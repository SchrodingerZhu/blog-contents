It is always a bad experience to debug all day and get no progress at all.  Unfortunately, it is exactly want I was doing yesterday and I am really out of patience now.



It all started with a [CI failure](https://github.com/SchrodingerZhu/snmalloc-rs/pull/66). Yes, as you can see from the following issues and commits:

- [linking with libatomic](https://github.com/SchrodingerZhu/snmalloc-rs/pull/23)
- [`-rdyamic` flag](https://github.com/microsoft/snmalloc/pull/217/commits/92866a3e917af0c9bb25df14cbed40e9920eb504)
- [different behavior on header files](https://github.com/microsoft/snmalloc/pull/217/commits/49b89b58e93f2ba2c045781abd7831d63c93bb1c)
- [imcompatible format symbol](https://github.com/microsoft/snmalloc/pull/217/commits/1172c34b5b5ec14d5ce30dba1eea6f4dcdff7d58)
- [different behavior of __builtin_[ctzl/clzl]](https://github.com/microsoft/snmalloc/pull/217/commits/9e784920c31fe8bd71feb03788ef200e752c5eb5)

to support MinGW seems to bring tons of troubles to me. After fixing all of them, I finally get into a bug: the destructors of `static thread_local` objects are not called at the MinGW environment. 

If you write something like: `static thread_local std::string some_str = "A bad day";`. The expected behavior is that `str` will be destroyed when the life-cycle of the thread ends. `snmalloc` uses this behavior to register a clean-up function as the destructor of an object so that it is called when the thread ends and returns the memory pool the global allocator. However, it is not functioning with MinGW.

Sadly, after a long git bisecting, I found that this bug occurs as long as the MinGW support is added and it should not be caused by the later added code.

Therefore, a reasonable explanation should be: **there is something wrong with MinGW toolchains**. Hence, I wrote the following code:
```c++
#include <iostream>
#include <mutex>
#include <thread>
#include <vector>
static std::mutex lock;

struct DtorTest {
	~DtorTest(){
		lock.lock();
      	std::cout << "dtor called " << std::this_thread::get_id() << std::endl;
        lock.unlock();	
	}
};

void _register() {
	static thread_local DtorTest test {};
}

const size_t TEST = 12;
int main() {
	std::vector<std::thread> threads;
	for (size_t i = 0; i < TEST; ++i) {
		threads.emplace_back(_register);
	}	
	for (auto& i : threads) {
		i.join();
	}
}
```

This is a very simple program that creates multiple threads and registers a `static thread_local` object in each thread duration. You can compile it with `g++ -lpthread <file.c> -o <output>` on linux and the output is something like:

```
dtor called 140122174142208
dtor called 140122165749504
dtor called 140122148964096
dtor called 140122157356800
dtor called 140122140571392
dtor called 140122132178688
dtor called 140122123785984
dtor called 140122115393280
dtor called 140121898743552
dtor called 140121890350848
dtor called 140121881958144
dtor called 140121873565440
```

However, you will get nothing with MinGW toolchains even though the program exits normally. Up to now, it is clear that there must be something wrong with MinGW.

Feeling angry about my **futile** debug work, I did feel like to do anything. So, I just decided to get even sad by investigating further on what is really going on here and learn more about TLS. In the following part, I will talk about what I have learned about TLS.

One may be curious about how static variables are created and destructed during the life cycle of a `c++` program.

GCC and clang follows `itanium ABI` to generate the code of C++ programs. In the specification of Itanium ABI, there is a section describing how the static variables are created and destructed:

- On construction, one should call the following function:

  ```c++
  extern "C" int __cxa_atexit ( void (*f)(void *), void *p, void *d ); 
  ```

  which registers a destructor `f` for object `p` on DSO `d`. (DSO, dynamic shared object, is the storage object for the program section; when the program section ends, `dlclose()` is called on the DSO). 

- When linking DSOs, the linker will define  a symbol `__dso_handle`. You can treat it as an special object contains the destructor list. The linker will also add a call the the following function:

  ```c++
  extern "C" void __cxa_finalize ( void *d ); 
  ```

  to the beginning of `FINI` list so that the destructors are called when program exits.

Now, let us have a look how thread local storage is implemented. You can treat this kind of storage as a special map where each thread can use their own identity to access different slots. On Windows, there is a whole set of low-level `TlsXxx` functions to help us manage the storage and in pthreads specification, there are also some functions like `pthread_key_create`, `pthread_key_delete`. Then, how to destruct those thread local objects on thread exit? This is a little bit more complex; but the idea is also to register destructors to a list and when a thread exits, a function is called to execute all the destructors.

In glibc, there is a function:

```c++
int __cxa_thread_atexit_impl (void (*dtor) (void *), void *obj,
                              void *dso_symbol);
```

doing this job. It also registers the destruction information into `__dso_handle` of a DSO but the DSO is marked with `DF_1_NODELETE`, which means it will not be unloaded on `dlclose`. When there is no reference to the `thread_local` variables within the DSO, the special flag will be cleared and the DSO will be unloaded. 

This is how final destruction is implemented in `winpthread`:

- use `_pthread_key_dest` array to collect all the destructors
- use `_pthread_v tv` array to collect all the values.
- on destruction, call a function named `_pthread_cleanup_dest` which invokes `_pthread_key_dest[i](tv[i])`  if needed.

It turns out that GCC code gen may cause trouble on TLS with MinGW targets ([Bug 80883](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=80883)). Therefore, I decided to check whether it is causing the problem here:

Let takes a look of a simple program (it is just a simplified version of the program above):

```c++
#include <thread>
static int i = 0;
struct A {
    ~A() {
        i += 1;
    }
};

void test() {
    static thread_local  A a;
}

int main() {
    auto a = std::thread {test};
    a.join();
}
```

In MSVC environment, the `test` function is translated into the following form:

```assembly
void test(void) PROC                                 ; test
$LN4:
        sub     rsp, 40                             ; 00000028H
        mov     eax, OFFSET FLAT:S1<`template-parameter-2',est,unsigned int, ?? &>
        mov     eax, eax
        mov     ecx, DWORD PTR _tls_index
        mov     rdx, QWORD PTR gs:88
        mov     rcx, QWORD PTR [rdx+rcx*8]
        mov     eax, DWORD PTR [rcx+rax]
        and     eax, 1
        test    eax, eax
        jne     SHORT $LN2@test
        mov     eax, OFFSET FLAT:S1<`template-parameter-2',est,unsigned int, ?? &>
        mov     eax, eax
        mov     ecx, DWORD PTR _tls_index
        mov     rdx, QWORD PTR gs:88
        mov     rcx, QWORD PTR [rdx+rcx*8]
        mov     eax, DWORD PTR [rcx+rax]
        or      eax, 1
        mov     ecx, DWORD PTR _tls_index
        mov     rdx, QWORD PTR gs:88
        mov     r8d, OFFSET FLAT:S1<`template-parameter-2',est,unsigned int, ?? &>
        mov     r8d, r8d
        mov     rcx, QWORD PTR [rdx+rcx*8]
        mov     DWORD PTR [r8+rcx], eax
        lea     rcx, OFFSET FLAT:void `void test(void)'::`2'::`dynamic atexit destructor for 'a''(void) ; `test'::`2'::`dynamic atexit destructor for 'a''
        call    __tlregdtor
$LN2@test:
        add     rsp, 40                             ; 00000028H
        ret     0
void test(void) ENDP    
```

Though we haven't discussed about how MSVC handle destructions; the assembly code is quite easy to understand: it prepares the TLS and register a destructor with:

```assembly
		lea     rcx, OFFSET FLAT:void `void test(void)'::`2'::`dynamic atexit destructor for 'a''(void) ; `test'::`2'::`dynamic atexit destructor for 'a''
        call    __tlregdtor
```

With GCC 10.1, we get the following assembly code:

```assembly
test():
        push    rbp
        mov     rbp, rsp
        mov     rax, QWORD PTR fs:0
        add     rax, OFFSET FLAT:guard variable for test()::a@tpoff
        movzx   eax, BYTE PTR [rax]
        test    al, al
        jne     .L12
        mov     rax, QWORD PTR fs:0
        add     rax, OFFSET FLAT:guard variable for test()::a@tpoff
        mov     BYTE PTR [rax], 1
        mov     rax, QWORD PTR fs:0
        add     rax, OFFSET FLAT:test()::a@tpoff
        mov     edx, OFFSET FLAT:__dso_handle
        mov     rsi, rax
        mov     edi, OFFSET FLAT:_ZN1AD1Ev
        call    __cxa_thread_atexit
.L12:
        nop
        pop     rbp
        ret
```

As we have already discussed, it registers the destructor with a function call to `__cxa_thread_atexit`.

With MinGW, you may see some calls to `__emutls_get_address` before `__cxa_thread_atexit`. It is the so-called **Emulating TLS**:

> For targets whose psABI does not provide Thread Local Storage via specific relocations and instruction sequences, an emulation layer is used.  A set of target hooks allows this emulation layer to be configured for the requirements of a particular target.  For instance the psABI may in fact specify TLS support in terms of an emulation layer.
>
> The emulation layer works by creating a control object for every TLS object.  To access the TLS object, a lookup function is provided which, when given the address of the control object, will return the address of the current thread’s instance of the TLS object.

So, it is just to get the TLS object in a different way and then do the destructor registration.

It turns out that at least at this simple program, the registration part is not to blame. Indeed, as **mati865** later suggested, if we compile the code use `compiler-rt` , then the destructor is [actually working](https://github.com/microsoft/snmalloc/pull/217#issuecomment-648286811). Notice that there is actually no difference in the assembly files for the source code, then the error should be in the MinGW's default language runtime.

To investigate further, I also tried [`mcfgthread`](https://github.com/lhmouse/mcfgthread) by **lhmouse**. Unfortunately, the problem is not resolved. 

I do not feel like looking into the MinGW runtime at this moment, so let us call it a day now!

So the bad day is not that bad. At least, I have learned something new:

- how static storage and TLS is implemented in general
- a simple demonstration of the pthread creation and destruction
- some C++ ABI details
- some reflections on MinGW ecosystem.

MinGW is a wonderful project that brings GNU environment to Windows. However, it has already brought too many troubles to me. Of course, I do hope the overall experience of MinGW can be improved over the time but I need to warn you that you must be careful to provide MinGW support for you program. I am considering drop the support for now since it will definitely causing memory leaks. Another thing to notice is that MinGW has accumulated some elder bugs that haven't been fixed for years; you may get some unexpected output when developing. 

At last, I want to give full thanks to the people helped me during this "learning process" and ... let me have a good sleep now xD. Hope there is no bug in dream!

