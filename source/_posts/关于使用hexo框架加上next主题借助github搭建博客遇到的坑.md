---
title: 关于使用hexo框架加上next主题借助github搭建博客遇到的坑
date: 2018-03-09 11:45:10
tags: 坑走的越多，处理事情就越能够游刃有余，当然，要时刻保持自信
---

1. 坑first

   这个坑是在搭建完之后，新建第一个博客文章的时候出现的

   编辑博客使用的工具是Typora,语言是markdown

   编辑完了之后hexo g 生成的时候就会报错，报错内容如下

   ~~~
   YAMLException: can not read a block mapping entry; a multiline key may not be an implicit key at line 4, column 1:
       ^
       at generateError (/Users/shucan/blog/node_modules/js-yaml/lib/js-yaml/loader.js:165:10)
       at throwError (/Users/shucan/blog/node_modules/js-yaml/lib/js-yaml/loader.js:171:9)
       at readBlockMapping (/Users/shucan/blog/node_modules/js-yaml/lib/js-yaml/loader.js:1046:9)
       at composeNode (/Users/shucan/blog/node_modules/js-yaml/lib/js-yaml/loader.js:1332:12)
       at readDocument (/Users/shucan/blog/node_modules/js-yaml/lib/js-yaml/loader.js:1492:3)
       at loadDocuments (/Users/shucan/blog/node_modules/js-yaml/lib/js-yaml/loader.js:1548:5)
       at Object.load (/Users/shucan/blog/node_modules/js-yaml/lib/js-yaml/loader.js:1569:19)
       at parseYAML (/Users/shucan/blog/node_modules/hexo-front-matter/lib/front_matter.js:80:21)
       at parse (/Users/shucan/blog/node_modules/hexo-front-matter/lib/front_matter.js:56:12)
       at Promise.all.spread (/Users/shucan/blog/node_modules/hexo/lib/plugins/processor/post.js:52:20)
       at tryCatcher (/Users/shucan/blog/node_modules/bluebird/js/release/util.js:16:23)
       at Promise._settlePromiseFromHandler (/Users/shucan/blog/node_modules/bluebird/js/release/promise.js:509:35)
       at Promise._settlePromise (/Users/shucan/blog/node_modules/bluebird/js/release/promise.js:569:18)
       at Promise._settlePromise0 (/Users/shucan/blog/node_modules/bluebird/js/release/promise.js:614:10)
       at Promise._settlePromises (/Users/shucan/blog/node_modules/bluebird/js/release/promise.js:693:18)
       at Promise._fulfill (/Users/shucan/blog/node_modules/bluebird/js/release/promise.js:638:18)
       at PromiseArray._resolve (/Users/shucan/blog/node_modules/bluebird/js/release/promise_array.js:126:19)
       at PromiseArray._promiseFulfilled (/Users/shucan/blog/node_modules/bluebird/js/release/promise_array.js:144:14)
       at PromiseArray._iterate (/Users/shucan/blog/node_modules/bluebird/js/release/promise_array.js:114:31)
       at PromiseArray.init [as _init] (/Users/shucan/blog/node_modules/bluebird/js/release/promise_array.js:78:10)
       at Promise._settlePromise (/Users/shucan/blog/node_modules/bluebird/js/release/promise.js:566:21)
       at Promise._settlePromise0 (/Users/shucan/blog/node_modules/bluebird/js/release/promise.js:614:10)
   ~~~

   在网上百般搜索，一直以为是各种配置文件语法有误，所以一直在看配置文件，当然最后也没有解决问题

   就在万般无奈之际，我尝试着打开了自己所编辑的博客，用Typora打开的

   <img src="https://raw.githubusercontent.com/ShuCan-git-up/ShuCan-git-up.github.io/master/images/photo/source/bug-001.png"/>

   在新建的page里面，文章最前面的三个标签title,date,tags的值得填写也需要遵守和配置文件一样的格式，在标签和值之间，也就是冒号之后需要有一个空格。

问题不大，只是有时候很容易被忽略，而且编译的时候报错了也不能够直接定位到这个错误的地方。所以记录一下以防以后再碰到。也希望能为大家所用！

