---
title: 关于捕获异常处理注意点
tags: notes
categories:
- Android
- 开发雷区
---
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在开发中经常需要写一些 try catch，不是我们有意去捕获可能存在的异常，而是 IDE 提示我们此处需要 try catch，不然编译不通过。于是我们按照 IDE 的提示写了 try catch，习惯性地也没多考虑直接在捕获体里面写了大量的逻辑交互。示例代码如下：
```
try {
  JSONObject json = new JSONObject();
  String name = JSONObject.getString("name");
  ............
  // do some thing
}catch(Exception e) {
  e.printStack();
}

```
如果 name 字段不存在就会直接到 catch 中，下面所有的代码都会被跳过，如果你之后的操作需要依赖被跳过的代码，那么后果你懂得。所以尽量在 try catch 中只保留真正需要 catch 的代码，如果真的需要写在 try catch 中，请检查如果某处抛出了异常保证不影响后面的执行。
