##Node.js
1. node.js里面的并发
：是指在同一时间可以做很多事情，区别于操作系统的并发

#Node.js入门经典2013
##知识集（书上旧版本的更新）
1. jade 里面加载 script 脚本和包含文件
``` jade
h1= title
    //这是需要加一个.，否则加载不出来
    script.
      alert ("You can execute inline javascript through Jade")

  h2 jade include foot
```