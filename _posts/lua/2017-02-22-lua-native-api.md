---
layout: post
title:  "lua 原生api解读"
categories: lua
tags: lua
---

* content
{:toc}

## 简介

研究lua也有一段时间了，对lua也算是小有了解，对于lua的api可能和其他脚本语言不是
很相同，使用lua来编写，lua原生api都采用C语言开发的，理解这些api含义对于理解lua
语言设计的本身又起着至关重要的作用，故此在这里记录下，lua-api具体含义。

## LuaAPI

#### 1.创建新表
```
void lua_createtable(lua_State *L, int narr, int nrec)

创建一个新表，并将它放在栈顶，narr和nrec分别制定该table的array和hash部分的预分配
元素的数量。
无返回值
栈高度+1，栈顶元素是新的table。
 #define lua_newtable(L) lua_createtable(L, 0, 0)
 创建表的另一个api，常用这个api,上面的宏一定很清楚不多介绍。
 ```



#### 2.取表中元素
```
void lua_getfield (lua_State *L, int index, const char *k)
操作:   arr = Stack[index]
        Stack.push( arr[k] )
取表中键为k的元素, 这里的表是由index指向的栈上的一个表
无返回值
栈高度+1, 栈顶元素是(Stack[index])[k]
注意, 该操作将触发 __index 元方法
```

#### 3.表中的元素赋值
```
void lua_setfield (lua_State *L, int index, const char *k)
操作:   arr = Stack[index]
        arr[k] = Stack.top()
        Stack.pop()
给表中键为k的元素赋值value(value就是栈顶元素), 这里的表是由index指向的栈上的一个表
无返回值
栈高度-1, 被弹出的是value
注意, 该操作将触发 __newindex 元方法
```

#### 4.取表元素
```
void lua_gettable (lua_State *L, int index)
操作:   ele  = Stack[index]
        key = Stack.top()
        Stack.pop()
        value = ele[key]
        Stack.push(value)
根据index指定取到相应的表; 取栈顶元素为key, 并弹出栈; 获取表中key的值压入栈顶.
无返回值
栈高度不变, 但是发生了一次弹出和压入的操作, 弹出的是key, 压入的是value
注意, 该操作将触发 __index 元方法
```

#### 4.表元素赋值
```
void lua_settable (lua_State *L, int index)
操作:   ele = Stack[index]
        value = Stack.top()
        Stack.pop()
        key = Stack.top()
        Stack.pop()
        ele[key] = value
根据index指定取到相应的表; 取栈顶元素做value, 弹出之; 再取当前栈顶元素做key, 亦弹出之; 然后将表的键为key的元素赋值为value
无返回值
栈高度-2, 第一次弹出value, 第二次弹出key
注意, 该操作将触发 __newindex 元方法
```

#### 5.对table的一些操作[不引发原方法]
```
void lua_rawget (lua_State *L, int index)和lua_gettable操作一样，但是不触发相应的元方法 
void lua_rawgeti(lua_State *L, int index, int n)
操作:   ele = Stack[index]
        value = ele[n]
        Stack.push(value)
无返回值
栈+1， 栈顶新增元素就是 value
不触发相应的元方法

void lua_rawset (lua_State *L, int index) 和lua_settable操作一样，但是不触发相应的原方法
void lua_rawseti (lua_State *L, int index, int n) 
操作:   ele = Stack[index]
        value = Stack.top()
        Stack.pop()
        ele[n] = value
无返回值
栈-1, 栈顶将value弹出
不触发相应的元方法
```

#### 6.复制栈上元素并压入栈
```
void lua_pushvalue (lua_State *L, int index)
操作:   value = Stack[index]       
        Stack.push(value)
无返回值
栈+1 
```

## 参考资料

[lua源码](https://github.com/lua/lua)

[UniLua源码](https://github.com/xebecnan/UniLua)

[Csharp-lua源码](https://github.com/abaojin/csharp-lua)

[lua-api论坛](http://www.cnblogs.com/ringofthec/archive/2010/10/22/lua.html)



	






