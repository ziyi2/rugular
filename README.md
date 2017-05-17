# Rugular-Expressions

**正则表达式**是强大、便捷、高效的文本处理工具.正则表达式本身，加上如同一门袖珍编程语言的通用模式表示法，赋予使用者描述和分析文本的能力.本文利用**JavaScript**语言的正则功能说明如何**添加、删除、分离、叠加、插入和修整**各种类型的文本和数据.



## 1. JavaScript对正则的支持

### 1.1 `RegExp`实例

#### 1.1.1 `RegExp`实例对象的定义


##### 1.1.1.1 字面量法

``` javascript
var expression = / pattern / flags ;       //flags = g||i||m
```

>解析

``` javascript
 var pattern = /\[fc\]at/i;                //注意\[匹配[，这里的\是转义字符，总体匹配[fc]at
```

##### 1.1.1.2 构造函数法

``` javascript
var expression = new RegExp(pattern，flags);
```

>解析

``` javascript
var pattern = new RegExp("\\[fc\\]at"，"i");  //这里需要对[进行双转义
```
##### 1.1.1.3 两种定义方式的区别

在**ES3**中使用字面量法每次都是调用同一个`RegExp`实例对象，而**ES5**中规定即使使用的是字面量法，也必须像直接调用`RegExp`构造函数一样，每次都创建新的`RegExp`实例.

>解析

``` javascript
var pattern = null，
    i ;

for(i=0; i<3; i++) {
    pattern = /cat/g;                          //字面量法，在ES5中类似于构造函数法
    console.log(pattern.test('catfdaslkfj'));  //true true true
}

for(i=0; i<3; i++) {
    pattern = new RegExp("cat"，"g");           //构造函数法
    console.log(pattern.test('catfdaslkfj'));  //true true true
}
```

#### 1.1.2 `RegExp`实例对象的属性

>解析
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

pattern = new RegExp("\\[fc\\]at"，"i");

console.log(pattern.global);        //false
console.log(pattern.ignoreCase);    //true
console.log(pattern.multiline);     //false
console.log(pattern.lastIndex);     //0
console.log(pattern.source);        //\[fc\]at 需要注意source属性保存的是字面量形式所用的字符串
```

#### 1.1.3 `RegExp`实例对象的方法

##### 1.1.1.3  `pattern.exec(string)`
- 该方法专门为**捕获组**设计.

>解析

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
var pattern = /mom( and dad( and baby)?)?/gi;   //gi 全局匹配，不区分大小写
var matches = pattern.exec(text);
console.log(matches);
// [
//      "mom and dad and baby"，         //整个模式匹配的字符串
//      " and dad and baby"，            //捕获组1
//      " and baby"，                    //捕获组2
//      index: 0，                       //匹配项在字符串中的位置
//      input: "mom and dad and baby"   //应用正则表达式的字符串
// ]

matches = pattern.exec('123');
console.log(matches);                   //null 匹配失败
```

全局标志的作用

``` javascript
var string = "cat，bat，sat，fat";
var pattern = /.at/;

for(var i=0; i<4; i++) {
    console.log(pattern.exec(string));
    console.log(pattern.lastIndex);
    //["cat"， index: 0， input: "cat，bat，sat，fat"]
    //["cat"， index: 0， input: "cat，bat，sat，fat"]
    //["cat"， index: 0， input: "cat，bat，sat，fat"]
    //["cat"， index: 0， input: "cat，bat，sat，fat"]
    //lastIndex 0 0 0 0
}

pattern = /.at/g;
for(var i=0; i<4; i++) {
    console.log(pattern.exec(string));
    console.log(pattern.lastIndex);
    //["cat"， index: 0， input: "cat，bat，sat，fat"]
    //["bat"， index: 4， input: "cat，bat，sat，fat"]
    //["sat"， index: 8， input: "cat，bat，sat，fat"]
    //["sat"， index: 8， input: "cat，bat，sat，fat"]
    //lastIndex 3 7 11 15
}
```
>提示： 如果不是全局模式，`pattern.exec()`返回的都是第一个匹配项，而如果是全局模式，每次调用`pattern.exec()`都会返回字符串的下一个匹配项直至匹配结束.

##### 1.1.1.4  `pattern.test(string)`
- 该方法在只想对目标字符串进行模式匹配时非常方便(不需要知道目标字符串的文本内容).

``` javascript
var text = "000-00-0000";
var pattern = /\d{3}-\d{2}-\d{4}/;

if(pattern.test(text)){ //true
    document.write("The pattern was matched." + "<br/>");
}
```


#### 1.1.4 `RegExp`构造函数的属性

``` javascript
var text = "this has been a short summer";
var pattern = /(.)hort/g;                           //匹配任何字符后面跟hort，而且把第一个字符放在了一个捕获组中

if(pattern.test(text)){
    /* 最近一次要匹配的字符串 */
    document.write(RegExp.input + "<br/>");         //this has been a short summer   
    /* 最近一次的匹配项 */
    document.write(RegExp.lastMatch + "<br/>");     //short      
    /* lastMatch之后的文本 */ 
    document.write(RegExp.rightContext + "<br/>");  //summer      
    /* lastMatch之前的文本 */                    
    document.write(RegExp.leftContext + "<br/>");   //this has been a               
    /* 最近一次匹配的捕获组 */                     
    document.write(RegExp.lastParen + "<br/>");     //s    
    /* 表示是否所有表达式都使用了多行模式 */                           
    document.write(RegExp.multiline + "<br/>");     //undefined(chrome)          
}
```

简写形式

``` javascript
var text = "this has been a short summer";
var pattern = /(.)hort/g;                           //匹配任何字符后面跟hort，而且把第一个字符放在了一个捕获组中   
document.write(RegExp.$_ + "<br/>");            //this has been a short summer    
document.write(RegExp["$'"] + "<br/>");         //summer                          
document.write(RegExp["$`"] + "<br/>");         //this has been a                
document.write(RegExp["$&"] + "<br/>");         //short                          
document.write(RegExp["$+"] + "<br/>");         //s                              
document.write(RegExp["$*"] + "<br/>");         //undefined(chrome)      

var pattern = /(..)or(.)/g;
var text = "this has been a short summer";

if(pattern.test(text)) {
    document.write(RegExp.$1 + "<br/>");    //sh    $1捕获组1
    document.write(RegExp.$2 + "<br/>");    //t     $2捕获组2 一直到捕获组9
}
```

### 1.2 `String`类型

##### 1.2.1 `string.match(pattern)`

- 和`pattern.exec(string)`功能类似

``` javascript
var text = "cat， bat， sat， fat";
var pattern = /.at/;
var matches = text.match(pattern);
console.log(matches);   //["cat"， index: 0， input: "cat， bat， sat， fat"]
```

##### 1.2.2 `string.search(pattern)`
- **始终是字符串开头向后索引**，返回字符串中第一个匹配项的索引，没有找到匹配项则返回`-1`

``` javascript
var text = "hello cat";
var pattern = /at/;
var pos = text.search(pattern);
console.log(pos);     //7
pos = text.search(pattern);
console.log(pos);     //7

text = "cat bat";
pattern = /at/g;
pos = text.search(pattern);
console.log(pos);     //1
pos = text.search(pattern);
console.log(pos);     //1
```

##### 1.2.3 `string.replace(pattern/string，string)`

- 第一参数是要被替换的字符串或者匹配模式
- 第二个参数是要替换的字符串

``` javascript
var text = "cat，bat，sat，fat";
var pattern = /at/g;
var result = text.replace("at"，"ond");  //如果只是字符串，则是替换第一个子字符串
console.log(result); //cond，bat，sat，fat

result = text.replace(pattern，"ond");   //指定全局g标志
console.log(result); //cond，bond，sond，fond

pattern = /at/;
result = text.replace(pattern，"ond");   //正则不指定全局g标志
console.log(result); //cond，bat，sat，fat
```
如果第二个参数(要替换的字符串)是字符串(不是函数)，还可以使用一些特殊的字符序列，将正则表达式操作得到的值插入到结果字符串中

``` javascript
var text = "cat，bat，sat，fat";
var pattern = /at/g;
var result = text.replace(/(.at)/g，"hello ($1)");  //$1是最近一次匹配结果中第一个捕获组的子字符串
console.log(result);                               //hello (cat)，hello (bat)，hello (sat)，hello (fat)
```

如果第二个参数是函数

``` javascript
//函数本身接收三个参数：1.模式的匹配项 2.模式匹配项在字符串中的位置 3.原始字符串
function htmlReplace(text，pattern){
    return text.replace(pattern，function(match，pos，originalText){
        switch(match){
            case "<":
                return "&lt;";
            case ">":
                return "&gt;";
            case "&":
                return "&amp;";
            case "\"":
                return "&quot;";
        }
    });
}

var pattern = /[<>"&]/g;
console.log(htmlReplace("<p class=\"greeting\">hello world!</p>"，pattern));
//&lt;p class=&quot;greeting&quot;&gt;hello world!&lt;/p&gt;
```

##### 1.2.4 `string.split(pattern/string， number)`
- 参数一 可以是字符串，也可以是RegExp对象（这个方法不会将字符串看成是一个正则表达式，不推荐使用）
- 第二个参数用于指定分割的数组的大小

``` javascript
var colorText = "red，blue，green，yellow";
var colors1 = colorText.split("，");
console.log(colors1); //["red"， "blue"， "green"， "yellow"]
var colors2 = colorText.split("，"，2);
console.log(colors2); //["red"，"blue"]
var colors3 = colorText.split(/blue/);
console.log(colors3); //["red，"， "，green，yellow"]
```

## 2. 正则表达式入门

完整的正则表达式由小的**构建模块单元**组成，每个单独的构建模块都很简单，但是它们有无穷多种方式组合，将它们结合起来实现特殊目标必须依靠经验.



## 2. 1 元字符

完整的正则表达式由两种字符构成，特殊字符和普通文本字符. 其中特殊字符称为**元字符**.

### 2.1.1 行的起始和结束

- **起始字符** `^`： 锚定一行的开头
- **结束字符** `$`： 锚定一行的结尾
- 两者的特别之处在于它们匹配的是位置，而不是具体的文本

`/cat/`寻找的是一行文本中任意位置的`"cat"`

``` javascript
var pattern = /cat/，
    text = "bcat fat sat"，
    matches = pattern.exec(text);

console.log(matches);  //[0:"cat" index:1 input:"bcat fat sat"，length:1]
```
`/^cat/`寻找的是以`c`作为一行的第一个字符，紧接着是一个`a`，紧接着是一个`t`的文本（注意不要理解为以`cat`开头的行）
``` javascript
var pattern = /^cat/，
    text = "bcat fat sat"，
    matches = pattern.exec(text);

console.log(matches);  //null

text = "cat fat sat";
matches = pattern.exec(text);
console.log(matches);  //[0:"cat" index:0 input:"cat fat sat"，length:1]

pattern = /at$/;
matches = pattern.exec(text);
console.log(matches);  //[0:"at" index:9 input:"cat fat sat"，length:1]
```

>提问：如何理解`/^cat$/`、`/^$/`、`/^/`呢? 


``` javascript
var pattern = /^cat$/，
    text = "bcat fat sat"，
    matches = pattern.exec(text);

console.log(matches);  //null

pattern = /^$/;
matches = pattern.exec(text);
console.log(matches);  //null

pattern = /^/;
matches = pattern.exec(text);
console.log(matches);  //[0:"" index:0 input:"cat fat sat"，length:1]
```


 `/^cat$/`匹配的是**行开头**，紧接着的字母`c`、`a`、`t`，最后是**行末尾**.`/^$/`匹配**行开头**，接着是**行末尾**，需要注意如果有空字符那肯定也是不匹配的，只是匹配了空行.`/^/`没有任何意义，因为每一行必定会有**行开头**，所以每一行都能匹配.



### 2.1.2 字符组

- **字符组**`[]`: 同一个位置能够匹配若干字符，意思是**“或”**.
- **字符组元字符**`-`（**连字符**）：表示一个范围
- 只有在字符组内部连字符才可能是元字符，否则只能匹配普通的连字符号（`-`），需要注意的是在字符组内部，如果连字符号出现在字符组的开头，它表示的仍然只是一个普通的字符，而不是一个范围，同样的原理，`?`和`.`通常被当做元字符，但是在字符组中则不是.

`/[cfs]at/g`是以全局的方式匹配任意位置的一个字符串，这个字符串的起始的第一个字符是`c`或`f`或`s`，紧接着的字符是`a`，紧接着的字符是`t`.

``` javascript
var pattern = /[cfs]at/g，
    text = "bcat fat sat"，
    matches = pattern.exec(text);

console.log(matches);  //[0:"cat" index:1 input:"bcat fat sat"，length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"fat" index:5 input:"bcat fat sat"，length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"sat" index:9 input:"bcat fat sat"，length:1]
```

> 提示： 如果不是全局模式，`pattern.exec()`返回的都是第一个匹配项，而如果是全局模式，每次调用`pattern.exec()`都会返回字符串的下一个匹配项直至匹配结束.

`/[0-9]/g`是以全局模式匹配一个字符，这个字符的范围是从`0`到`9`，类似于`/[0123456789]/g`
``` javascript
var pattern = /[0-9]/g，
    text = "a1236h"，
    matches = pattern.exec(text);

console.log(matches);  //[0:"1" index:1 input:"a1236h"，length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"2" index:2 input:"a1236h"，length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"3" index:3 input:"a1236h"，length:1]
```

>提问: 如何理解`/[0-9A-Z_!.?]/`?

``` javascript
var pattern = /[0-9A-Z_!.?]/g，
    text = "aA1_!.?"，
    matches = pattern.exec(text);

console.log(matches);  //[0:"A" index:1 input:"aA1_!.?"，length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"1" index:2 input:"aA1_!.?"，length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"_" index:3 input:"aA1_!.?"，length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"!" index:4 input:"aA1_!.?"，length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"." index:5 input:"aA1_!.?"，length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"?" index:6 input:"aA1_!.?"，length:1]
```

`/[0-9A-Z_!.?]/`中m，在字符组中真正是特殊字符元字符的只有`0-9A-Z`中的两个连字符，而`!`、`.`、`?`在字符组中都只是普通字符.