# Rugular-Expressions

**正则表达式**是强大、便捷、高效的文本处理工具。正则表达式本身,加上如同一门袖珍编程语言的通用模式表示法,赋予使用者描述和分析文本的能力。本文利用**JavaScript**语言的正则功能说明如何**添加、删除、分离、叠加、插入和修整**各种类型的文本和数据。



## 1. JavaScript对正则的支持

### 1.1 `RegExp`实例

#### 1.1.1 `RegExp`实例对象的定义


##### 1.1.1.1 字面量法

``` javascript
var expression = / pattern / flags ; //flags = g||i||m
```

>例

``` javascript
 var pattern = /\[fc\]at/i;  //注意\[匹配[,这里总体匹配[fc]at
```

##### 1.1.1.2 构造函数法

``` javascript
var expression = new RegExp(pattern,flags);
```

>例

``` javascript
var pattern = new RegExp("\\[fc\\]at","i");  //这里需要对[进行双转义
```
##### 1.1.1.3 两种定义方式的区别

在**ES3**中使用字面量法每次都是调用同一个`RegExp`实例对象，而**ES5**中规定即使使用的是字面量法，也必须像直接调用`RegExp`构造函数一样，每次都创建新的`RegExp`实例。

>例

``` javascript
var pattern = null,
        i ;

for(i=0; i<3; i++) {
    pattern = /cat/g;  //字面量法,在ES5中类似于构造函数法
    console.log(pattern.test('catfdaslkfj'));  //true true true
}

for(i=0; i<3; i++) {
    pattern = new RegExp("cat","g"); //构造函数法
    console.log(pattern.test('catfdaslkfj'));
}
```

#### 1.1.2 `RegExp`实例对象的属性

``` javascript

/**
 * global:布尔值，表示是否设置了g标志
 * ignoreCase:布尔值，表示是否设置了i标志
 * lastIndex：整数，表示开始搜索下一个匹配项的字符位置，从0算起
 * multiline：布尔值，表示是否设置了m标志
 * source：正则表达式的字符串表示
 */

var pattern = /\[fc\]at/i;

console.log(pattern.global);        //false
console.log(pattern.ignoreCase);    //true
console.log(pattern.multiline);     //false
console.log(pattern.lastIndex);     //0
console.log(pattern.source);        //\[fc\]at

pattern = new RegExp("\\[fc\\]at","i");

console.log(pattern.global);        //false
console.log(pattern.ignoreCase);    //true
console.log(pattern.multiline);     //false
console.log(pattern.lastIndex);     //0
console.log(pattern.source);        //\[fc\]at 需要注意source属性保存的是字面量形式所用的字符串
```

#### 1.1.3 `RegExp`实例对象的方法

##### 1.1.1.3  `pattern.exec(string)`

该方法专门为**捕获组**设计.


``` javascript
/* 这个例子包含了两个捕获组 */

/**
 *  返回一个数组，没有匹配项就返回null
 *  数组包含两个额外的属性，index和input
 *  index表示匹配项在字符串中的位置
 *  input表示应用正则表达式的字符串
 *  数组中第一项是与整个模式匹配的字符串，其他项是与模式中的捕获组匹配的字符串（如果没有捕获组，数组只包含一项）
 */

var text = "mom and dad and baby";
var pattern = /mom( and dad( and baby)?)?/gi;   //gi 全局匹配,不区分大小写
var matches = pattern.exec(text);
console.log(matches);
// [
//      "mom and dad and baby",         //整个模式匹配的字符串
//      " and dad and baby",            //捕获组1
//      " and baby",                    //捕获组2
//      index: 0,                       //匹配项在字符串中的位置
//      input: "mom and dad and baby"   //应用正则表达式的字符串
// ]

matches = pattern.exec('123');
console.log(matches);                   //null 匹配失败
```

全局标志的作用

``` javascript
var string = "cat,bat,sat,fat";
var pattern = /.at/;

for(var i=0; i<4; i++) {
    console.log(pattern.exec(string));
    console.log(pattern.lastIndex);
    //["cat", index: 0, input: "cat,bat,sat,fat"]
    //["cat", index: 0, input: "cat,bat,sat,fat"]
    //["cat", index: 0, input: "cat,bat,sat,fat"]
    //["cat", index: 0, input: "cat,bat,sat,fat"]
    //lastIndex 0 0 0 0
}

pattern = /.at/g;
for(var i=0; i<4; i++) {
    console.log(pattern.exec(string));
    console.log(pattern.lastIndex);
    //["cat", index: 0, input: "cat,bat,sat,fat"]
    //["bat", index: 4, input: "cat,bat,sat,fat"]
    //["sat", index: 8, input: "cat,bat,sat,fat"]
    //["sat", index: 8, input: "cat,bat,sat,fat"]
    //lastIndex 3 7 11 15
}
```
>提示： 如果不是全局模式，`exec()`返回的都是第一个匹配项，而如果是全局模式，每次调用`exec()`都会返回字符串的下一个匹配项直至匹配结束。



### 2.3 `String`类型










## 2. 元字符

完整的正则表达式由两种字符构成，特殊字符和普通文本字符. 其中特殊字符称为**元字符**。

### 2. 1 行的起始和结束

- 起始字符 `^`
- 结束字符 `$`








