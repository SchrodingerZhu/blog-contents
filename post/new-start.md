It has been long since my last blog server being removed brutally without any backup due to some unhappy reasons. It did hurt me a lot that I stopped any blogging activity since then. Recently, however, I feel an urgent need for a place to keep my thinkings and my thoughts. It is always unsatisfactory to live without a playground to share my ideas with others and receive feedbacks. What's more, by writing some learning reflections, I do hope I can push myself to keep going with play study plans. Hence, I restarted the blog.



It took my several days to write this blog system. My original idea was to use a static blog generator written in `Clojure`; but I do not like its comment integration and finally gave it up. Then, it suddenly occurred to me that I could write a dynamic blog system in `rust` , practicing my database skills and  getting more familiar with asynchronous web server at the same time. Therefore, I wrote this with the power of `async-std` and some crates depending on it (i.e. `tide`, `xactor`and `surf`). After exploring  for a little while, I finally managed to put all pieces together.



It was also my first time to write some real front-end code. As you can see, the blog layout is primitive and it can be very inconvenient in some places. Please forgive my bad front-end skills. I will appreciate it if you could offer me some necessary suggestions on how to improve my blog system. The git repo is public at [blog-system](https://github.com/schrodingerzhu/blog-system).



According to my current plan, the blog will be maintained in English. I will put some learning notes, technical reflections and important pieces of my life in this blog. Comments are welcomed and I will try my best to reply to them. Please do not try to comment some malicious code. They will definitely be filtered away but it may result in some meaningless blocks. :)