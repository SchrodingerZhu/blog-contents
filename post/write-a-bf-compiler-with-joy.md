This post is written to be a tutorial and show case of PEG parser, LLVM and C++ practices.

## Before the Journey

`C++` is the main language used in this post and `cmake` is the build tool. I will assume you have learned basic knowledge of `c++(17)` and `cmake`.

The related repo is public on Github at [simplebf](https://github.com/SchrodingerZhu/simplebf).

We will use two other repos as submodules:

- [PEGTL](https://github.com/taocpp/PEGTL): PEG parser generator library
- [cxxopts](https://github.com/jarro2783/cxxopts): for command interface

We also need LLVM installed. If also things are ready, you should be able to use something like:

```cmake
cmake_minimum_required(VERSION 3.17)
project(simplebf)

set(CMAKE_CXX_STANDARD 17)
option(PEGTL_BUILD_TESTS OFF)
option(PEGTL_BUILD_EXAMPLES OFF)
find_package(LLVM REQUIRED CONFIG)
message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")

add_subdirectory(PEGTL EXCLUDE_FROM_ALL)
include_directories(cxxopts/include)
add_executable(simplebf main.cpp)
target_compile_definitions(simplebf PRIVATE ${LLVM_DEFINITIONS})
target_link_libraries(simplebf taocpp::pegtl LLVM)
set_property(TARGET "simplebf" APPEND_STRING PROPERTY LINK_FLAGS "${LLVM_LINK_FLAGS}")
```

to setup the project and from then on the include relations mentioned in this post are based on this cmake configuration. 

## What is BrainF\*\*k?

BrainF\*\*k is a very simple **esolang**. It is so simple that it may not be worthy to use a parser for it. However, I really want to go through a relatively complete process of compiling and introduce PEG in the same time, so I will use a PEG library anyway.

BrainF\*\*k has only a small set of invalid characters (each of which has a corresponding `c` translation):

- (Program Start): `char ptr[30000] = {0};`
- `>`: `++ptr;`
- `<` : `--ptr;`
- `+`: `++*ptr;`
- `-`: `--*ptr;`
- `.`: `putchar(*ptr);` 
- `,`: `*ptr=getchar();`
- `[`: `while (*ptr) {`
- `]`: `}`

Indeed, based on the rules above; one can already scan the whole program and translate it into `C`. It is just that simple. but the language is  fully [Turing complete](https://en.wikipedia.org/wiki/Turing_completeness), which means it is capable to do most logical operations. For example, the following program will output `Hello, World!`:

```brainfuck
++++++++[>++++[>++>+++>+++>+<<<<-]>+>+>->>+[<]<-]>>.>---.+++++++..+++.>>.<-.<.+++.------.--------.>>+.>++.
```

## What is PEG (PEGTL)?

If you learned about parsers before, I think you must know CFGs (Context Free Grammars). PEGs (Parsing Expression Grammars) is yet another way to organize target grammars. The expressions of PEGs is much similar to Regular Expressions, but it is powerful enough to express computer languages. Moreover, because PEG support regex-like expressions, we can make out language scannerless. In my opinion, it is much more friendly that traditional lexer + CFG parser way.

There are also some drawbacks of PEGs like:

- Parsers usually has a higher memory consumption
- Programmers must makes sure that there are no (implicit) left-recursions. 

With PEG, we can first define some basic tokens with ascii rules (or unicode rules); after that, we use powerful combinators to collect all rules together.

Basic combinators are:

- and-predicate: `&rules`: succeeds when `rules` match the input, but consumes nothing
- not-predicate: `!rules`: succeeds when `rules` fail to match the input, but comsumes nothing
- optional `rules?`: try to match input with `rules`, succeed regardless of the match result
- one-or-more `rules+`: match `rules` as many times as possible and succeed if at least one trial succeeds.
- sequence `a ~ b ~ ...`: match `a, b, ...` in order, succeed if all matchings succeed.
- ordered choice `a | b | ...`: match `a, b, ...` in order, succeed at the first time one rule succeed.
- zero-or-more `rules*`, match `rules` as many times as possible but always succeed.

There are many PEG parser gens. For example, [pest.rs](https://pest.rs) is a great parser gen in rust language. Let me use the example from its homepage to give you a first glance of PEG:

```pest
alpha = { 'a'..'z' | 'A'..'Z' }
digit = { '0'..'9' }

ident = { (alpha | digit)+ }

ident_list = _{ !digit ~ ident ~ (" " ~ ident)+ }
          // ^
          // ident_list rule is silent (produces no tokens or error reports)
```

It should be easy to understand what is going on here. I am not going to explain the details of pest, but as you can see, it is very human readable and powerful.

In C++, there are also some good candidates. I am going to use `PEGTL` in this post. The latest version of the library requires `c++17`. It uses templates to derive the parser. The above example can be written as (in fact, there are built-in rules for alpha, digit and identity):

```cpp
#include <tao/pegtl.hpp>
using namespace tao::pegtl;
struct alpha : sor<range<'a', 'z'>, range<'A', 'Z'>> {};
struct digit : range<'0', '9'> {};
struct ident : plus<sor<alpha, digit>> {};
struct ident_list : seq<not_at<digit>, ident, plus<blank, ident>> {};
```

`PEGTL` has a wonderful document at its repo, you can check it with [this link](https://github.com/taocpp/PEGTL/blob/master/doc/README.md).

Here,

- `sor<A, B, ...>` means **ordered choice**. The parser will try to match the parameters from left to right, and succeeds at the first time when a rule matches.
- `range<L, R>` is an ASCII rule,  which means a range of characters (from `L` to `R`).
- `seq<A, ...>` will try to match of sequence of rules `A, ...` in order and only succeeds if all rules are matched. 
- `plus<K, ...>` will try to match  `seq<K, ...>` for at least one time.
- `not_at<K, ...>` succeeds when `seq<K, ...>` fails.

There is no direct silent rule here, but `PEGTL` support custom selector, actions and transforms, you can add your own selector as a template parameter so that the final parse tree will only keep the nodes you wanted.

## Grammar Definition and the Parser

Now, it is high time to check out how to write a brainf\*\*k compiler in PEG form:

```c++
namespace grammar {
    using namespace tao::pegtl;
    struct ShiftR : one<'>'> {};
    struct ShiftL : one<'<'> {};
    struct Inc : one<'+'> {};
    struct Dec : one<'-'> {};
    struct Dot : one<'.'> {};
    struct Comma : one<','> {};
    struct Loop;
    struct NonSense : plus<not_one<'<', '>', '+', '-', '.', ',', '[', ']'>> {};
    struct Statement : plus<sor<ShiftR, ShiftL, Inc, Dec, Dot, Comma, Loop, NonSense>> {};
    struct Loop : seq<one<'['>, Statement, one<']'>> {};
    struct Module : must<Statement> {};
}
```

Notice that we do not suppose empty loop or empty program here.

- `one<K, ...>` succeeds if one of the characters of `K, ...` matches the input.
- `must<K, ...>` is the same as `seq<K, ...>`, but it will raise errors on failure. (Error behaviors can also be customized, but we will not cover that in this post)

I think I do not need to explain much of the grammar. The first six structures correspond to basic tokens. Basic tokens and loops forms the statements and loops are statements enclosed by square brackets and module represents the whole program.

I have said that implicit left-recursions are not allowed in PEGs. `PEGTL` provides some facilities to check this problem. You can include ` <tao/pegtl/contrib/analyze.hpp>`, and run the function:

```c++
tao::pegtl::analyze<grammar::Module>();
```

The function returns the number of error it found and it will also output some information to `stdout`.

For example, If we change the grammar to something like:

```c++
    struct Statement : plus<sor<Loop, ShiftR, ShiftL, Inc, Dec, Dot, Comma, NonSense>> {};
    struct Loop : seq<Statement> {};
```

Then, there is a trivial left-recursion here. And, indeed, you will get some errors:

```c
problem: cycle without progress detected at rule class grammar::Loop
problem: cycle without progress detected at rule class grammar::Statement
problem: cycle without progress detected at rule class grammar::Statement
problem: cycle without progress detected at rule class grammar::Statement
problem: cycle without progress detected at rule class grammar::Statement
problem: cycle without progress detected at rule class tao::pegtl::opt<grammar::Statement>
problem: cycle without progress detected at rule class tao::pegtl::sor<grammar::Loop, grammar::ShiftR, grammar::ShiftL, grammar::Inc, grammar::Dec, grammar::Dot, grammar::Comma, grammar::NonSense>
problem: cycle without progress detected at rule class grammar::Statement
```

The parser is ready. How can we get the parse tree? `PEGTL` again has handy built-in functions.

All we have to do is to include `tao/pegtl/contrib/parse_tree.hpp` and then use something like

```c++
auto res  = tao::pegtl::parse_tree::parse<grammar::Module, transform::selector>(in);
```

where `in` is the input part. To test it, you can just use argument input:

```c++
int main(int argc, char* argv[]) {
    auto in = tao::pegtl::argv_input(argv, 1);
    // ... other stuffs
} 
```

What's more, you can even output the dot graph of the parse tree

```c++
if (res) {
        tao::pegtl::parse_tree::print_dot(std::cout, *res)
}

```

Now you should be able to build the program and run it as

```bash
./simplebf '+' | dot -Tpng -o test.png
xdg-open test.png
```

You should get a graph like:

<?xml version="1.0" encoding="UTF-8" standalone="no"?>

<div class="svg-scroll">
<svg width="2534pt" height="493pt"
 viewBox="0.00 0.00 2534.41 492.70" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<g id="graph0" class="graph" transform="scale(1 1) rotate(0) translate(4 488.7)">
<title>parse_tree</title>
<polygon fill="white" stroke="transparent" points="-4,4 -4,-488.7 2530.41,-488.7 2530.41,4 -4,4"/>
<!-- x0x559f0eedf290 -->
<g id="node1" class="node">
<title>x0x559f0eedf290</title>
<ellipse fill="none" stroke="black" cx="1263.2" cy="-466.7" rx="34.39" ry="18"/>
<text text-anchor="middle" x="1263.2" y="-463" font-family="Times,serif" font-size="14.00">ROOT</text>
</g>
<!-- x0x559f0eedf340 -->
<g id="node2" class="node">
<title>x0x559f0eedf340</title>
<ellipse fill="none" stroke="black" cx="1263.2" cy="-385.83" rx="83.38" ry="26.74"/>
<text text-anchor="middle" x="1263.2" y="-389.63" font-family="Times,serif" font-size="14.00">grammar::Module</text>
<text text-anchor="middle" x="1263.2" y="-374.63" font-family="Times,serif" font-size="14.00">&quot;+\n&quot;</text>
</g>
<!-- x0x559f0eedf290&#45;&gt;x0x559f0eedf340 -->
<g id="edge1" class="edge">
<title>x0x559f0eedf290&#45;&gt;x0x559f0eedf340</title>
<path fill="none" stroke="black" d="M1263.2,-448.59C1263.2,-441.1 1263.2,-432.01 1263.2,-423.13"/>
<polygon fill="black" stroke="black" points="1266.7,-422.91 1263.2,-412.91 1259.7,-422.91 1266.7,-422.91"/>
</g>
<!-- x0x559f0eedf3f0 -->
<g id="node3" class="node">
<title>x0x559f0eedf3f0</title>
<ellipse fill="none" stroke="black" cx="1263.2" cy="-296.09" rx="91.85" ry="26.74"/>
<text text-anchor="middle" x="1263.2" y="-299.89" font-family="Times,serif" font-size="14.00">grammar::Statement</text>
<text text-anchor="middle" x="1263.2" y="-284.89" font-family="Times,serif" font-size="14.00">&quot;+\n&quot;</text>
</g>
<!-- x0x559f0eedf340&#45;&gt;x0x559f0eedf3f0 -->
<g id="edge2" class="edge">
<title>x0x559f0eedf340&#45;&gt;x0x559f0eedf3f0</title>
<path fill="none" stroke="black" d="M1263.2,-358.51C1263.2,-350.54 1263.2,-341.65 1263.2,-333.16"/>
<polygon fill="black" stroke="black" points="1266.7,-333.09 1263.2,-323.09 1259.7,-333.09 1266.7,-333.09"/>
</g>
<!-- x0x559f0eedf4b0 -->
<g id="node4" class="node">
<title>x0x559f0eedf4b0</title>
<ellipse fill="none" stroke="black" cx="627.2" cy="-206.35" rx="627.41" ry="26.74"/>
<text text-anchor="middle" x="627.2" y="-210.15" font-family="Times,serif" font-size="14.00">tao::pegtl::sor&lt;grammar::ShiftR, grammar::ShiftL, grammar::Inc, grammar::Dec, grammar::Dot, grammar::Comma, grammar::Loop, grammar::NonSense&gt;</text>
<text text-anchor="middle" x="627.2" y="-195.15" font-family="Times,serif" font-size="14.00">&quot;+&quot;</text>
</g>
<!-- x0x559f0eedf3f0&#45;&gt;x0x559f0eedf4b0 -->
<g id="edge3" class="edge">
<title>x0x559f0eedf3f0&#45;&gt;x0x559f0eedf4b0</title>
<path fill="none" stroke="black" d="M1181.36,-283.8C1089.83,-271.17 938.49,-250.29 817.08,-233.54"/>
<polygon fill="black" stroke="black" points="817.3,-230.04 806.91,-232.14 816.34,-236.98 817.3,-230.04"/>
</g>
<!-- x0x559f0eedf620 -->
<g id="node5" class="node">
<title>x0x559f0eedf620</title>
<ellipse fill="none" stroke="black" cx="1899.2" cy="-206.35" rx="627.41" ry="26.74"/>
<text text-anchor="middle" x="1899.2" y="-210.15" font-family="Times,serif" font-size="14.00">tao::pegtl::sor&lt;grammar::ShiftR, grammar::ShiftL, grammar::Inc, grammar::Dec, grammar::Dot, grammar::Comma, grammar::Loop, grammar::NonSense&gt;</text>
<text text-anchor="middle" x="1899.2" y="-195.15" font-family="Times,serif" font-size="14.00">&quot;\n&quot;</text>
</g>
<!-- x0x559f0eedf3f0&#45;&gt;x0x559f0eedf620 -->
<g id="edge4" class="edge">
<title>x0x559f0eedf3f0&#45;&gt;x0x559f0eedf620</title>
<path fill="none" stroke="black" d="M1345.05,-283.8C1436.58,-271.17 1587.92,-250.29 1709.33,-233.54"/>
<polygon fill="black" stroke="black" points="1710.07,-236.98 1719.5,-232.14 1709.11,-230.04 1710.07,-236.98"/>
</g>
<!-- x0x559f0eedf540 -->
<g id="node6" class="node">
<title>x0x559f0eedf540</title>
<ellipse fill="none" stroke="black" cx="627.2" cy="-116.61" rx="65.11" ry="26.74"/>
<text text-anchor="middle" x="627.2" y="-120.41" font-family="Times,serif" font-size="14.00">grammar::Inc</text>
<text text-anchor="middle" x="627.2" y="-105.41" font-family="Times,serif" font-size="14.00">&quot;+&quot;</text>
</g>
<!-- x0x559f0eedf4b0&#45;&gt;x0x559f0eedf540 -->
<g id="edge5" class="edge">
<title>x0x559f0eedf4b0&#45;&gt;x0x559f0eedf540</title>
<path fill="none" stroke="black" d="M627.2,-179.03C627.2,-171.06 627.2,-162.17 627.2,-153.68"/>
<polygon fill="black" stroke="black" points="630.7,-153.61 627.2,-143.61 623.7,-153.61 630.7,-153.61"/>
</g>
<!-- x0x559f0eedf6b0 -->
<g id="node7" class="node">
<title>x0x559f0eedf6b0</title>
<ellipse fill="none" stroke="black" cx="1899.2" cy="-116.61" rx="91.85" ry="26.74"/>
<text text-anchor="middle" x="1899.2" y="-120.41" font-family="Times,serif" font-size="14.00">grammar::NonSense</text>
<text text-anchor="middle" x="1899.2" y="-105.41" font-family="Times,serif" font-size="14.00">&quot;\n&quot;</text>
</g>
<!-- x0x559f0eedf620&#45;&gt;x0x559f0eedf6b0 -->
<g id="edge6" class="edge">
<title>x0x559f0eedf620&#45;&gt;x0x559f0eedf6b0</title>
<path fill="none" stroke="black" d="M1899.2,-179.03C1899.2,-171.06 1899.2,-162.17 1899.2,-153.68"/>
<polygon fill="black" stroke="black" points="1902.7,-153.61 1899.2,-143.61 1895.7,-153.61 1902.7,-153.61"/>
</g>
<!-- x0x559f0eedf740 -->
<g id="node8" class="node">
<title>x0x559f0eedf740</title>
<ellipse fill="none" stroke="black" cx="1899.2" cy="-26.87" rx="212.68" ry="26.74"/>
<text text-anchor="middle" x="1899.2" y="-30.67" font-family="Times,serif" font-size="14.00">tao::pegtl::ascii::not_one&lt;&#39;&lt;&#39;, &#39;&gt;&#39;, &#39;+&#39;, &#39;&#45;&#39;, &#39;.&#39;, &#39;,&#39;, &#39;[&#39;, &#39;]&#39;&gt;</text>
<text text-anchor="middle" x="1899.2" y="-15.67" font-family="Times,serif" font-size="14.00">&quot;\n&quot;</text>
</g>
<!-- x0x559f0eedf6b0&#45;&gt;x0x559f0eedf740 -->
<g id="edge7" class="edge">
<title>x0x559f0eedf6b0&#45;&gt;x0x559f0eedf740</title>
<path fill="none" stroke="black" d="M1899.2,-89.29C1899.2,-81.32 1899.2,-72.43 1899.2,-63.94"/>
<polygon fill="black" stroke="black" points="1902.7,-63.87 1899.2,-53.87 1895.7,-63.87 1902.7,-63.87"/>
</g>
</g>
</svg>
</div>


But it is such a mess! We only want to preserve those semantically meaningful nodes. Here is the right place to introduce our selectors:

```c++
namespace transform {

    template <class T>
    struct selector : std::false_type {};

#define select(X) \
    template <> \
    struct selector<grammar::X> : std::true_type {}

    select(Dot);
    select(Comma);
    select(Inc);
    select(Dec);
    select(ShiftL);
    select(ShiftR);
    select(Statement);
    select(Loop);
    select(Module);
}
```

Selector is easy to write. The default selector inherits `std::false_type`, which means nodes are disabled on default. However, we also provide specializations for those meaningful language units, the selector of whom inherits `std::true_type` and they will be stored in the final parse tree. I did this by using macros, because it is just some repetitions.

How to use the selectors? Simply pass it as a template parameter:

```c++
auto res  = tao::pegtl::parse_tree::parse<grammar::Module, transform::selector>(in);
```

With the same commands, now we can get:

<?xml version="1.0" encoding="UTF-8" standalone="no"?>

<div class="svg-scroll">
<svg width="192pt" height="313pt"
 viewBox="0.00 0.00 191.85 313.22" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<g id="graph0" class="graph" transform="scale(1 1) rotate(0) translate(4 309.22)">
<title>parse_tree</title>
<polygon fill="white" stroke="transparent" points="-4,4 -4,-309.22 187.85,-309.22 187.85,4 -4,4"/>
<!-- x0x5594c9931290 -->
<g id="node1" class="node">
<title>x0x5594c9931290</title>
<ellipse fill="none" stroke="black" cx="91.92" cy="-287.22" rx="34.39" ry="18"/>
<text text-anchor="middle" x="91.92" y="-283.52" font-family="Times,serif" font-size="14.00">ROOT</text>
</g>
<!-- x0x5594c9931340 -->
<g id="node2" class="node">
<title>x0x5594c9931340</title>
<ellipse fill="none" stroke="black" cx="91.92" cy="-206.35" rx="83.38" ry="26.74"/>
<text text-anchor="middle" x="91.92" y="-210.15" font-family="Times,serif" font-size="14.00">grammar::Module</text>
<text text-anchor="middle" x="91.92" y="-195.15" font-family="Times,serif" font-size="14.00">&quot;+\n&quot;</text>
</g>
<!-- x0x5594c9931290&#45;&gt;x0x5594c9931340 -->
<g id="edge1" class="edge">
<title>x0x5594c9931290&#45;&gt;x0x5594c9931340</title>
<path fill="none" stroke="black" d="M91.92,-269.11C91.92,-261.62 91.92,-252.53 91.92,-243.65"/>
<polygon fill="black" stroke="black" points="95.42,-243.43 91.92,-233.43 88.42,-243.43 95.42,-243.43"/>
</g>
<!-- x0x5594c99313f0 -->
<g id="node3" class="node">
<title>x0x5594c99313f0</title>
<ellipse fill="none" stroke="black" cx="91.92" cy="-116.61" rx="91.85" ry="26.74"/>
<text text-anchor="middle" x="91.92" y="-120.41" font-family="Times,serif" font-size="14.00">grammar::Statement</text>
<text text-anchor="middle" x="91.92" y="-105.41" font-family="Times,serif" font-size="14.00">&quot;+\n&quot;</text>
</g>
<!-- x0x5594c9931340&#45;&gt;x0x5594c99313f0 -->
<g id="edge2" class="edge">
<title>x0x5594c9931340&#45;&gt;x0x5594c99313f0</title>
<path fill="none" stroke="black" d="M91.92,-179.03C91.92,-171.06 91.92,-162.17 91.92,-153.68"/>
<polygon fill="black" stroke="black" points="95.42,-153.61 91.92,-143.61 88.42,-153.61 95.42,-153.61"/>
</g>
<!-- x0x5594c9931540 -->
<g id="node4" class="node">
<title>x0x5594c9931540</title>
<ellipse fill="none" stroke="black" cx="91.92" cy="-26.87" rx="65.11" ry="26.74"/>
<text text-anchor="middle" x="91.92" y="-30.67" font-family="Times,serif" font-size="14.00">grammar::Inc</text>
<text text-anchor="middle" x="91.92" y="-15.67" font-family="Times,serif" font-size="14.00">&quot;+&quot;</text>
</g>
<!-- x0x5594c99313f0&#45;&gt;x0x5594c9931540 -->
<g id="edge3" class="edge">
<title>x0x5594c99313f0&#45;&gt;x0x5594c9931540</title>
<path fill="none" stroke="black" d="M91.92,-89.29C91.92,-81.32 91.92,-72.43 91.92,-63.94"/>
<polygon fill="black" stroke="black" points="95.42,-63.87 91.92,-53.87 88.42,-63.87 95.42,-63.87"/>
</g>
</g>
</svg>
</div>


It is much clear, isn't it?

## What is LLVM?

LLVM (Low Level Virtual Machine) is kind of one-stop shop of language back-ends. Basically, it provides almost everything you need to transform your languages into executable machine code. In this post, we will find a way to transform our programs into LLVM IR and let LLVM do the remaining jobs.

There are tons of header files to use:

```c++
#include <llvm/IR/LLVMContext.h>
#include <llvm/IR/IRBuilder.h>
#include <llvm/Support/raw_ostream.h>
#include <llvm/IR/LegacyPassManager.h>
#include <llvm/Transforms/InstCombine/InstCombine.h>
#include <llvm/Transforms/Scalar.h>
#include <llvm/Support/TargetRegistry.h>
#include <llvm/Support/TargetSelect.h>
#include <llvm/Target/TargetOptions.h>
#include <llvm/Target/TargetMachine.h>
#include <llvm/IR/Verifier.h>
```

It is okay if you do not know much LLVM, I will not use fancy features here and I will provide detailed explanations of what I am going to do. However, it is better to grab the basic concept of [SSA IR](https://en.wikipedia.org/wiki/Static_single_assignment_form).

## Abstract Syntax Tree and Code Generation

With selectors, our parse tree is already in the shape of AST (Abstract Syntax Tree), it should be easy to transform it into the final AST; but before that. let us first define our AST nodes.

### ASTNode Basic Interface

To begin with, the general interface looks like:

```c++
namespace ast {
    using namespace llvm;
    struct ASTNode {
        static LLVMContext context;
        static std::unique_ptr<Module> module;
        static IRBuilder<> builder;
        static Function* toplevel;
        static Function* getchar;
        static Function* putchar;
        static AllocaInst* pointer;
        static AllocaInst* array;

        virtual Value* codegen() = 0;

    };

    LLVMContext ASTNode::context {};
    IRBuilder<> ASTNode::builder {context};
    std::unique_ptr<Module> ASTNode::module = std::make_unique<Module>("simple", context);
    Function* ASTNode::toplevel;
    Function* ASTNode::getchar;
    Function* ASTNode::putchar;
    AllocaInst* ASTNode::pointer;
    AllocaInst* ASTNode::array;
}
```

- `LLVMContext ASTNode::context` is the global context of LLVM IR code generation, it stores the global states. 

- `IRBuilder<> ASTNode::builder` is the helper to emit IR code.
- `std::unique_ptr<Module> ASTNode::module` is the module represents our program.
- We will compile the whole program into one top-level function (`Function* ASTNode::toplevel`) named `main`, in this way, we can use c language runtime and let clang to do the linking job.
- `Function* ASTNode::getchar` and `Function* ASTNode::putchar` is the declaration of extern functions in `libc`. We will use them for `.` and `,`.
- `AllocaInst* ASTNode::pointer` represents the cursor of in the BrainF\*\*k language. We store it in an `alloca` memory block as it is a mutable variable. We should know that `alloca` is the way to preserve space on the stack. In fact, mutable variables and arrays in computer languages are usually implemented in this way.
- `AllocaInst* ASTNode::array` is the "tape" of the BrainF\*\*k language. It is also stored in an `alloca` area.

We assume the compiler compile one program at one time, so we just store these things as static variables.

Each instance of `ASTNode` should provide a function with prototype `virtual Value* codegen();`. This function is in charge of the code generation. The return value is not useful here: I simply tried to return every last instruction at each step of code generation. It was useful to debug.

### Module

Let us move on to our first node: `ast::Module`:

```c++
namespace ast {
	struct Module : ASTNode {
        std::unique_ptr<ASTNode> child;
        explicit Module(std::unique_ptr<ASTNode> child) : child(std::move(child)) {}
        Value* codegen() override {
            auto int_type = IntegerType::get(context, 32);
            {
                auto func_type = FunctionType::get(int_type, {int_type}, false);
                putchar = Function::Create(func_type, Function::ExternalLinkage, "putchar", module.get());
            }
            {
                auto func_type = FunctionType::get(int_type, {}, false);
                getchar = Function::Create(func_type, Function::ExternalLinkage, "getchar", module.get());
            }
            auto func_type = FunctionType::get(int_type, {}, false);
            toplevel = Function::Create(func_type, Function::ExternalLinkage, "main", module.get());
            auto basic_block = BasicBlock::Create(context, "", toplevel);
            builder.SetInsertPoint(basic_block);
            array = builder.CreateAlloca(int_type, ConstantInt::get(context , APInt(32, StringRef("10000"), 10)));
            pointer = builder.CreateAlloca(int_type, nullptr);
            if(child) child->codegen();
            auto zero  = ConstantInt::get(context , APInt(32, StringRef("0"), 10));
            builder.CreateRet(zero);
            return toplevel;
        }
	};
}
```

In our design of the grammar, `Module` node will have a single child to a statement node. This relation is represented by `std::unique_ptr<ASTNode> child`.

We know that `Module` will the very beginning node for code generation, so we run the initialization here. 

The line

```c++
auto int_type = IntegerType::get(context, 32);
```

creates the type information of 32-bit integer. It will be the type for the cursor and the array element.

```c++
{
   	auto func_type = FunctionType::get(int_type, {int_type}, false);
    putchar = Function::Create(func_type, Function::ExternalLinkage, "putchar", module.get());
}
{
    auto func_type = FunctionType::get(int_type, {}, false);
    getchar = Function::Create(func_type, Function::ExternalLinkage, "getchar", module.get());
}
```

Then, it declares two external functions: `putchar` and `getchar`. Take a look of `FunctionType::get(int_type, {int_type}, false)`:

- The first argument represents the return type.
- The second argument is the vector of types. Each of which represents the type in the argument list of the target function.
- The third argument mark whether the function supports variable argument list length.

`Function::Create(func_type, Function::ExternalLinkage, "putchar", module.get())` creates the function we want. 

- The first argument represents the type of the function
- The second argument set the linkage type of the function. Here we use external linkage, which means
  - The function can be declared in another module (or object file in the final output);
  - or it is declared in the current module but exposed to the outside
- The third argument is the name of the function
- The fourth argument is the module of the function to be registered in

After that, it declares the `toplevel function` with name `main`. Unlike the previous two functions, we should define the function by ourselves. Therefore, we create a basic block as the function body and move the IR generation cursor onto that block.

When entering the function, we need to first initialize the "runtime" of our language. So we allocate the space of the array and pointer on the stack. This is done by:

```c++
builder.CreateAlloca(Type*, Value*)
```

- The first argument is the type of the variable
- The second argument is a pointer to the size of the array. You can use `nullptr` if it is a single variable.

Here we use a constant value for array size, this constant integer can be created by:

```
ConstantInt::get(context , APInt(32, StringRef("10000"), 10))
```

`APInt` takes 3 arguments: integer bits, integer literal and integer radix.

After initialization, we call the `codegen()` function of the child to emit other code of the program. And finally, we return 0 at the end of the function.

### ShiftR and ShiftL

`ShiftR` and `ShiftL` represents `>` and `<` correspondingly. These kinds of AST nodes do not have any child. Basically, `ShiftR` and `ShiftL` operations are quite the same: the only difference in code generation is the difference between add instruction and sub instruction.

```c++
namespace ast {
        struct ShiftR : ASTNode {
        Value* codegen() override {
            auto cur = builder.CreateLoad(pointer);
            auto one  = ConstantInt::get( context , APInt(32, StringRef("1"), 10));
            auto next = builder.CreateAdd(cur, one);
            return builder.CreateStore(next, pointer);
        }
    };

    struct ShiftL : ASTNode {
        Value* codegen() override {
            auto cur = builder.CreateLoad(pointer);
            auto one  = ConstantInt::get( context , APInt(32, StringRef("1"), 10));
            auto next = builder.CreateSub(cur, one);
            return builder.CreateStore(next, pointer);
        }
    };
}
```

Notice that when we use `AllocaInst` to preserve the space, the return value is actually in the type of `i32 *` (pointer to 32-bit integer). However, this means we need an extra load instruction to get the value and an extra store instruction to change to value. So the procedure goes like this:

1. Load the current value of `pointer`

2. Add/Sub constant value $1$ to/from the current value

3. Store the new value back to the storage.

There are some new functions here:

- `builder.CreateLoad(ptr)`: create a load instruction on `ptr`.
-  `builder.CreateSub(A, B)/builder.CreateAdd(A, B)` emit the instruction of $C = A - B$ or $C = A + B$
- `builder.CreateStore(value, ptr)` store `value` to the address represented by `ptr`.

### Inc and Dec

`Inc` and `Dec` correspond to `+` and `-`. They also have no child. Actually, the logic here is quite the same as `ShiftR` and `ShiftL`; but now the changes happen in th array and we need to figure out a way to access elements in the array with index.

```c++
namespace ast {
        struct Inc : ASTNode {
        Value* codegen() override {
            auto cur = builder.CreateLoad(pointer);
            auto ptr = builder.CreateGEP(array, cur);
            auto val = builder.CreateLoad(ptr);
            auto one  = ConstantInt::get( context , APInt(32, StringRef("1"), 10));
            auto next = builder.CreateAdd(val, one);
            return builder.CreateStore(next, ptr);
        }
    };

    struct Dec : ASTNode {
        Value* codegen() override {
            auto cur = builder.CreateLoad(pointer);
            auto ptr = builder.CreateGEP(array, cur);
            auto val = builder.CreateLoad(ptr);
            auto one  = ConstantInt::get( context , APInt(32, StringRef("1"), 10));
            auto next = builder.CreateSub(val, one);
            return builder.CreateStore(next, ptr);
        }
    };
}
```

LLVM provides a special instruction called `getelementptr` which accept a pointer to the array and a index and return a pointer to the array element at the index position. With our `IRBuilder` we can use `builder.CreateGEP(array, index)` to emit the target instruction. Therefore, we implement `Inc` (`Dec`) in the following way:

1. Load the current value of `pointer` as index
2. Get the pointer to the array element at the current index
3. Load the array element at the current index
4. Add/Sub $1$ to/from the current value of that element
5. Store the new value back to the place pointed by the element pointer.

### Dot and Comma

At `Dot` and `Comma`, we need to handle the I/O operations, which means that we need to emit calls to foreign functions. Let's see how we can do that.

```c++
namespace ast {
   struct Dot : ASTNode {
        Value* codegen() override {
            auto index = builder.CreateLoad(pointer);
            auto cur_ptr = builder.CreateGEP(array, index);
            auto ele = builder.CreateLoad(cur_ptr);
            return builder.CreateCall(putchar, {ele});
        }
    };

    struct Comma : ASTNode {
        Value* codegen() override {
            auto cur = builder.CreateLoad(pointer);
            auto input = builder.CreateCall(getchar, {});
            auto ptr = builder.CreateGEP(array, cur);
            return builder.CreateStore(input, ptr);
        }
    };
}
```

- For `Dot`, the procedure goes like
  1. Load current index from `pointer`
  2. Shift array pointer with `GEP` instruction to get the pointer to current cell.
  3. Load the value from current cell.
  4. Call `putchar` with the value
- For `Comma`, the procedure goes like
  1. Load current index from `pointer`
  2. Call `getchar` to get the input
  3. Shift array pointer with `GEP` instruction to get the pointer to current cell.
  4. Store the input to current cell.
- `builder.CreateCall` is exactly what we need to emit the call instruction. It takes two arguments: the first one is the function to call, the second argument is the array of the functions' inputs.

### Loop

It is high time to introduce the only control flow of the language, the loop. How can we implement the loop? Consider two basic blocks $A$ and $B$, where $A$ is the loop body and $B$ is the block right after $A$. Before $A$ and at the end of $A$, we insert a conditional branch such that if the current pointed value is $0$, the program jumps to the beginning of $B$; otherwise, it jumps to the beginning of $A$. In this way, we can construct a while loop.

```c++
 namespace ast {
	struct Loop : ASTNode {
        std::unique_ptr<ASTNode> child;
        explicit Loop(std::unique_ptr<ASTNode> child) : child(std::move(child)) {}
        Value* codegen() override {
            auto loop = BasicBlock::Create(context, "", toplevel);
            auto after = BasicBlock::Create(context, "", toplevel);
            auto zero  = ConstantInt::get( context , APInt(32, StringRef("0"), 10));
            {
                auto index = builder.CreateLoad(pointer);
                auto cur_ptr = builder.CreateGEP(array, index);
                auto cur = builder.CreateLoad(cur_ptr);
                auto flag = builder.CreateICmpEQ(cur, zero);
                builder.CreateCondBr(flag, after, loop);
            }
            builder.SetInsertPoint(loop);
            if(child) child->codegen();
            {
                auto index = builder.CreateLoad(pointer);
                auto cur_ptr = builder.CreateGEP(array, index);
                auto cur = builder.CreateLoad(cur_ptr);
                auto flag = builder.CreateICmpEQ(cur, zero);
                builder.CreateCondBr(flag, after, loop);
            }
            builder.SetInsertPoint(after);
            return after;
        }
    };
}
```

As you can see, we first prepare two basic blocks. At each check point, we fetch the value to check and emit a comparison instruction with `builder.CreateICmpEQ` (which performs a integral equality compare of its two operands). Using the comparison results, we create the conditional branches with `builder.CreateCondBr` (which takes three arguments: condition, true branch, false branch).

### Statement

Statement is just a collection of basic operations and the loop. We just need to generate the code of its children in order:

```c++
 namespace ast{
	struct Statement : ASTNode {
        std::vector<std::unique_ptr<ASTNode>> children;
        Statement(std::vector<std::unique_ptr<ASTNode>>&& children) : children(std::move(children)) {}
        Value* codegen() override {
            Value* end = nullptr;
            for (auto& i: children) {
                end = i->codegen();
            }
            return end;
        }
    };
}
```

### Transform Parse Tree to AST

As you can see, our parse tree should have the same structure as the AST. Hence, it should be every easy to do the transform.

How can check the corresponding grammar type at each node? `PEGTL` provides a `is_type<T>` function in its default parse tree node structure.

To begin with, let us define two macros:

```c++
#define trans(type, block) \
    if (n->is_type<grammar::type>()) block
#define trans_simple(type) trans(type, {return std::make_unique<ast::type> (); })
```

The first one is easy to understand, it is just to check the type and do corresponding operations on match.

The second one is for those AST nodes without any child. We do not need to do anything special for these kinds of nodes; we just construct the corresponding AST node directly and return the pointer.

```c++
namespace transform {	
	using node = tao::pegtl::parse_tree::node;
    std::unique_ptr<ast::ASTNode> transform(const std::unique_ptr<node>& n) {
        trans(Module, {
            auto stmt = transform(n->children[0]);
            return std::make_unique<ast::Module>(std::move(stmt));
        })

        trans(Statement, {
            std::vector<std::unique_ptr<ast::ASTNode>> children;
            for (const auto& i : n->children) {
                children.emplace_back(transform(i));
            }
            return std::make_unique<ast::Statement>(std::move(children));
        })

        trans(Loop, {
            auto stmt = transform(n->children[0]);
            return std::make_unique<ast::Loop>(std::move(stmt));
        })

        trans_simple(Dot)
        trans_simple(Comma)
        trans_simple(Inc)
        trans_simple(Dec)
        trans_simple(ShiftR)
        trans_simple(ShiftL)
        __builtin_unreachable();
    }
}
```

So this is a recursive function. For those simple node, we directly return the pointer; for those node with child/children, we first transform the children , attach it to new node and return the pointer. That `__builtin_unreachable` at the end of the function is used to mark that the function cannot fall through as we have already iterate all possible cases.

Now, let us generate the code for the parse tree:

```c++
auto ast = transform::transform(res->children[0]);
ast->codegen();
```

Notice that the very beginning node is the parse root, and its only child is the real program. Therefore, what we need to transform is actually `res->children[0]`

### Print IR

Now, the most important part of our compiler is finished and we can check what can we get with LLVM IR. LLVM provide good facilities for us to debug th program. One can use :

```c++
ast::ASTNode::module->print(llvm::outs(), nullptr);
```

to output the IR of the module. For example, `+[-],.` will generate the following code:

```llvm
; ModuleID = 'simple'
source_filename = "simple"

declare i32 @putchar(i32)

declare i32 @getchar()

define i32 @main() {
  %1 = alloca i32, i32 10000
  %2 = alloca i32
  %3 = load i32, i32* %2
  %4 = getelementptr i32, i32* %1, i32 %3
  %5 = load i32, i32* %4
  %6 = add i32 %5, 1
  store i32 %6, i32* %4
  %7 = load i32, i32* %2
  %8 = getelementptr i32, i32* %1, i32 %7
  %9 = load i32, i32* %8
  %10 = icmp eq i32 %9, 0
  br i1 %10, label %20, label %11

11:                                               ; preds = %11, %0
  %12 = load i32, i32* %2
  %13 = getelementptr i32, i32* %1, i32 %12
  %14 = load i32, i32* %13
  %15 = sub i32 %14, 1
  store i32 %15, i32* %13
  %16 = load i32, i32* %2
  %17 = getelementptr i32, i32* %1, i32 %16
  %18 = load i32, i32* %17
  %19 = icmp eq i32 %18, 0
  br i1 %19, label %20, label %11

20:                                               ; preds = %11, %0
  %21 = load i32, i32* %2
  %22 = call i32 @getchar()
  %23 = getelementptr i32, i32* %1, i32 %21
  store i32 %22, i32* %23
  %24 = load i32, i32* %2
  %25 = getelementptr i32, i32* %1, i32 %24
  %26 = load i32, i32* %25
  %27 = call i32 @putchar(i32 %26)
  ret i32 0
}
```

I will not go through the [LLVM IR](https://llvm.org/docs/LangRef.html), but you should be able to understand what is going on here. Basically, there are some built-in instructions and unlimited number of registers. The type of each instruction operand is provided together with the register name.

For instance,

```llvm
%3 = load i32, i32* %2
%4 = getelementptr i32, i32* %1, i32 %3
%5 = load i32, i32* %4
%6 = add i32 %5, 1
```

is the IR code emitted for the first `+` statement. The code is just doing what we have talked about for the AST node. And as you can see, `%1` and `%2` are the pointer to the allocated space and their type `i32*` is marked clearly at the places they are used.

### IR Verification

LLVM IR is nothing but just a few lines of code of another language called LLVM IR. There can be mistakes in the IR code just as there can be errors in the C++ code.

When we construct IR using the builder, we are just passing `Value*` to the builder. This does not guarantee that the thing behind the `Value*` is exactly a valid operand. It is true that basic type checking is already performed (in the debug mode, obvious errors will result in assertion failures) but it is not careful enough. There are situations when invalid IR are generated by the builder.

Fortunately, LLVM provides a function for us to check the IR:

```c++
if (llvm::verifyFunction(*ast::ASTNode::toplevel, &llvm::errs())) {
    llvm::errs() << "failed to verify toplevel";
    return 1;
};
```

With the above code, details of the errors will be output to `stderr` if there is any.

## Function Passes and Optimizations

You can regard the compile process as an assembly line. After the IR code is generated, we can add a few passes for it to go through; each pass perform some operations to the IR: some of the operations optimize the code, some of the operations do statistics and some of the operations are in charge of the final binary output.

Here we just use a very simple example to show how we can optimize the code with function passes.

```c++
using namespace llvm;
auto FPM = std::make_unique<legacy::FunctionPassManager>(ast::ASTNode::module.get());

// Do simple "peephole" optimizations and bit-twiddling optzns.
FPM->add(createInstructionCombiningPass());
// Reassociate expressions.
FPM->add(createReassociatePass());
// Remove extra values.
FPM->add(createNewGVNPass());
// Simplify the control flow graph (deleting unreachable blocks, etc).
FPM->add(createCFGSimplificationPass());

FPM->doInitialization();
FPM->run(*ast::ASTNode::toplevel);
FPM->doFinalization();
```

At the very beginning, we create a `FunctionPassManager` to manage all the passes.

Then we add four passes:

- `InstructionCombiningPass` will do the peephole optimizations, which analysis each basic block and compress the instructions.  You can check out [wikipedia](https://en.wikipedia.org/wiki/Peephole_optimization) for a better understanding.

- `ReassociatePass`  can reassociate expressions for better optimization.

  For example, changing `1 + (x + 4)` to ` x+ (1 + 4)` will give a change to fold more constants.

- `GVNPass` partitions the variables created in the functions into congruent classes. This enables the optimizer to understand which values are guaranteed to be the same in the runtime and therefore can be represented by a single value

-   `CFGSimplificationPass` will simplify the control flow

After the passes are added, we heat up the the assembly line, input the sources, get the product and shut it down.

## Compile to Object File

Last but not the least, let us see how we can get a real executable.

If you check the compiling process of C programs, you can see that each file are first compiled into object files (usually with `.o/.obj` as extension names). After that, object files are passed to linker and the linker will fill the holes in the object file (such as the calls to external functions) and finally generate an executable.

We are going to use `clang`  as the linker (this is not quite accurate, we actually keeps the ABIs of the program as the same as C programs; therefore, `clang` can recognize the format and link our programs with the c library and c language runtime using the system linker; in this way, we can get a runnable ELF). So, we only need to provide the object file.

```c++
int main (...) {
    // parse and code gen
    
	auto target_triple = llvm::sys::getDefaultTargetTriple();

    std::string Error;
    llvm::InitializeAllTargetInfos();
    llvm::InitializeAllTargets();
    llvm::InitializeAllTargetMCs();
    llvm::InitializeAllAsmParsers();
    llvm::InitializeAllAsmPrinters();
    auto target = llvm::TargetRegistry::lookupTarget(target_triple, Error);

    if (!target) {
        llvm::errs() << Error;
        return 1;
    }

    auto CPU = "generic";
    auto Features = "";

    llvm::TargetOptions opt;
    auto RM = llvm::Optional<llvm::Reloc::Model>();
    auto machine = target->createTargetMachine(target_triple, CPU, Features, opt, RM);
    ast::ASTNode::module->setDataLayout(machine->createDataLayout());
    ast::ASTNode::module->setTargetTriple(target_triple);
    auto filename = result["output"].as<std::string>();
    std::error_code EC;
    llvm::raw_fd_ostream dest(filename, EC);

    if (EC) {
        llvm::errs() << "Could not open file: " << EC.message();
        return 1;
    }

    llvm::legacy::PassManager pass;
    auto file_type = llvm::CGFT_ObjectFile;

    if (machine->addPassesToEmitFile(pass, dest, nullptr, file_type)) {
        llvm::errs() << "TargetMachine can't emit a file of this type";
        return 1;
    }

    pass.run(*ast::ASTNode::module);
    dest.flush();

    llvm::outs() << "written to file\n";
    return 0;
}
```

LLVM targets are specified by triples. For example, `x86-64 GNU/Linux` targets are represents by `x86_64-unknown-linux-gnu`. This information is useful for the generation of machine code. `getDefaultTargetTriple` will get the triple of the current running system automatically.

Then, we just run some initialization steps. LLVM can also optimize the program for specific CPUs. For example, some modern intel chips may support `avx512` instructions, so LLVM can run special SIMD optimization for it. Here we do not care much about these micro-structure optimizations, so we just set CPU to `generic` and provide no specific feature.

At last, we use a pass to emit the file with the provided information.

After the the object file is generated, we can use:

```bash
clang <object-file.o> -o <executable name>
```

to get the executable. If there is `.` or `,` in the program, `clang` will handle the linking to `getchar` or `putchar` automatically.

Now, check the example at the beginning of this post and you will see the warm greetings:

```
Hello, World!
```

