# Rugular-Expressions

**正则表达式**是强大、便捷、高效的文本处理工具.正则表达式本身,加上如同一门袖珍编程语言的通用模式表示法,赋予使用者描述和分析文本的能力.本文利用**JavaScript**语言的正则功能说明如何**添加、删除、分离、叠加、插入和修整**各种类型的文本和数据.



## 1. JavaScript对正则的支持

### 1.1 `RegExp`实例

#### 1.1.1 `RegExp`实例对象的定义


##### 1.1.1.1 字面量法

``` javascript
var expression = / pattern / flags ;       //flags = g||i||m
```

>解析

``` javascript
 var pattern = /\[fc\]at/i;                //注意\[匹配[,这里的\是转义字符,总体匹配[fc]at
```

##### 1.1.1.2 构造函数法

``` javascript
var expression = new RegExp(pattern,flags);
```

>解析

``` javascript
var pattern = new RegExp("\\[fc\\]at","i");  //这里需要对[进行双转义
```
##### 1.1.1.3 两种定义方式的区别

在**ES3**中使用字面量法每次都是调用同一个`RegExp`实例对象,而**ES5**中规定即使使用的是字面量法,也必须像直接调用`RegExp`构造函数一样,每次都创建新的`RegExp`实例.

>解析

``` javascript
var pattern = null,
    i ;

for(i=0; i<3; i++) {
    pattern = /cat/g;                          //字面量法,在ES5中类似于构造函数法
    console.log(pattern.test('catfdaslkfj'));  //true true true
}

for(i=0; i<3; i++) {
    pattern = new RegExp("cat","g");           //构造函数法
    console.log(pattern.test('catfdaslkfj'));  //true true true
}
```

#### 1.1.2 `RegExp`实例对象的属性

>解析
``` javascript

/**
 * global:布尔值,表示是否设置了g标志
 * ignoreCase:布尔值,表示是否设置了i标志
 * lastIndex:整数,表示开始搜索下一个匹配项的字符位置,从0算起
 * multiline:布尔值,表示是否设置了m标志
 * source:正则表达式的字符串表示
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
- 该方法专门为**捕获组**设计.

>解析

``` javascript
/* 这个例子包含了两个捕获组 */

/**
 *  返回一个数组,没有匹配项就返回null
 *  数组包含两个额外的属性,index和input
 *  index表示匹配项在字符串中的位置
 *  input表示应用正则表达式的字符串
 *  数组中第一项是与整个模式匹配的字符串,其他项是与模式中的捕获组匹配的字符串(如果没有捕获组,数组只包含一项)
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
>提示: 如果不是全局模式,`pattern.exec()`返回的都是第一个匹配项,而如果是全局模式,每次调用`pattern.exec()`都会返回字符串的下一个匹配项直至匹配结束.

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
var pattern = /(.)hort/g;                           //匹配任何字符后面跟hort,而且把第一个字符放在了一个捕获组中

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
var pattern = /(.)hort/g;                           //匹配任何字符后面跟hort,而且把第一个字符放在了一个捕获组中   
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
var text = "cat, bat, sat, fat";
var pattern = /.at/;
var matches = text.match(pattern);
console.log(matches);   //["cat", index: 0, input: "cat, bat, sat, fat"]
```

##### 1.2.2 `string.search(pattern)`
- **始终是字符串开头向后索引**,返回字符串中第一个匹配项的索引,没有找到匹配项则返回`-1`

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

##### 1.2.3 `string.replace(pattern/string,string)`

- 第一参数是要被替换的字符串或者匹配模式
- 第二个参数是要替换的字符串

``` javascript
var text = "cat,bat,sat,fat";
var pattern = /at/g;
var result = text.replace("at","ond");  //如果只是字符串,则是替换第一个子字符串
console.log(result); //cond,bat,sat,fat

result = text.replace(pattern,"ond");   //指定全局g标志
console.log(result); //cond,bond,sond,fond

pattern = /at/;
result = text.replace(pattern,"ond");   //正则不指定全局g标志
console.log(result); //cond,bat,sat,fat
```
如果第二个参数(要替换的字符串)是字符串(不是函数),还可以使用一些特殊的字符序列,将正则表达式操作得到的值插入到结果字符串中

``` javascript
var text = "cat,bat,sat,fat";
var pattern = /at/g;
var result = text.replace(/(.at)/g,"hello ($1)");  //$1是最近一次匹配结果中第一个捕获组的子字符串
console.log(result);                               //hello (cat),hello (bat),hello (sat),hello (fat)
```

如果第二个参数是函数

``` javascript
//函数本身接收三个参数:1.模式的匹配项 2.模式匹配项在字符串中的位置 3.原始字符串
function htmlReplace(text,pattern){
    return text.replace(pattern,function(match,pos,originalText){
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
console.log(htmlReplace("<p class=\"greeting\">hello world!</p>",pattern));
//&lt;p class=&quot;greeting&quot;&gt;hello world!&lt;/p&gt;
```

##### 1.2.4 `string.split(pattern/string, number)`
- 参数一 可以是字符串,也可以是RegExp对象(这个方法不会将字符串看成是一个正则表达式,不推荐使用)
- 第二个参数用于指定分割的数组的大小

``` javascript
var colorText = "red,blue,green,yellow";
var colors1 = colorText.split(",");
console.log(colors1); //["red", "blue", "green", "yellow"]
var colors2 = colorText.split(",",2);
console.log(colors2); //["red","blue"]
var colors3 = colorText.split(/blue/);
console.log(colors3); //["red,", ",green,yellow"]
```

## 2. 正则表达式入门

完整的正则表达式由小的**构建模块单元**组成,每个单独的构建模块都很简单,但是它们有无穷多种方式组合,将它们结合起来实现特殊目标必须依靠经验.



## 2. 1 元字符

完整的正则表达式由两种字符构成,特殊字符和普通文本字符. 其中特殊字符称为**元字符**.

### 2.1.1 行的起始和结束元字符

- **起始字符** `^`: 锚定一行的开头
- **结束字符** `$`: 锚定一行的结尾
- 两者的特别之处在于它们匹配的是位置而不是具体的文本

`/cat/`寻找的是一行文本中任意位置的`"cat"`

``` javascript
var pattern = /cat/,
    text = "bcat fat sat",
    matches = pattern.exec(text);

console.log(matches);  //[0:"cat" index:1 input:"bcat fat sat" length:1]
```
`/^cat/`寻找的是以`c`作为一行的第一个字符,紧接着是一个`a`,紧接着是一个`t`的文本(注意不要理解为以`cat`开头的行)
``` javascript
var pattern = /^cat/,
    text = "bcat fat sat",
    matches = pattern.exec(text);

console.log(matches);  //null

text = "cat fat sat";
matches = pattern.exec(text);
console.log(matches);  //[0:"cat" index:0 input:"cat fat sat" length:1]

pattern = /at$/;
matches = pattern.exec(text);
console.log(matches);  //[0:"at" index:9 input:"cat fat sat" length:1]
```

>提问:如何理解`/^cat$/`、`/^$/`、`/^/`呢? 


``` javascript
var pattern = /^cat$/,
    text = "bcat fat sat",
    matches = pattern.exec(text);

console.log(matches);  //null

pattern = /^$/;
matches = pattern.exec(text);
console.log(matches);  //null

pattern = /^/;
matches = pattern.exec(text);
console.log(matches);  //[0:"" index:0 input:"cat fat sat" length:1]
```


 `/^cat$/`匹配的是**行开头**,紧接着的字母`c`、`a`、`t`,最后是**行末尾**.`/^$/`匹配**行开头**,接着是**行末尾**,需要注意如果有空字符那肯定也是不匹配的,只是匹配了空行.`/^/`没有任何意义,因为每一行必定会有**行开头**,所以每一行都能匹配.



### 2.1.2 字符组"内部"的元字符

#### 2.1.2.1 连字符

- **字符组**`[]`: 同一个位置能够匹配若干字符,意思是**或**,需要注意的是多选结构只匹配一个字符.
- **字符组元字符**`-`(**连字符**):表示一个范围
- 只有在字符组**内部**连字符才可能是元字符,否则只能匹配普通的连字符号`-`,需要注意的是在字符组内部,如果连字符号出现在字符组的开头,它表示的仍然只是一个普通的字符,而不是一个范围,同样的原理,`?`和`.`通常被当做元字符,但是在字符组中则不是.

`/[cfs]at/g`是以全局的方式匹配任意位置的一个字符串,这个字符串的起始的第一个字符是`c`或`f`或`s`,紧接着的字符是`a`,紧接着的字符是`t`.

``` javascript
var pattern = /[cfs]at/g,
    text = "bcat fat sat",
    matches = pattern.exec(text);

console.log(matches);  //[0:"cat" index:1 input:"bcat fat sat" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"fat" index:5 input:"bcat fat sat" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"sat" index:9 input:"bcat fat sat" length:1]
```

> 提示: 如果不是全局模式,`pattern.exec()`返回的都是第一个匹配项,而如果是全局模式,每次调用`pattern.exec()`都会返回字符串的下一个匹配项直至匹配结束.

`/[0-9]/g`是以全局模式匹配一个字符,这个字符的范围是从`0`到`9`,类似于`/[0123456789]/g`
``` javascript
var pattern = /[0-9]/g,
    text = "a1236h",
    matches = pattern.exec(text);

console.log(matches);  //[0:"1" index:1 input:"a1236h" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"2" index:2 input:"a1236h" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"3" index:3 input:"a1236h" length:1]
```

>提问: 如何理解`/[0-9A-Z_!.?]/`?

``` javascript
var pattern = /[0-9A-Z_!.?]/g,
    text = "aA1_!.?",
    matches = pattern.exec(text);

console.log(matches);  //[0:"A" index:1 input:"aA1_!.?" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"1" index:2 input:"aA1_!.?" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"_" index:3 input:"aA1_!.?" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"!" index:4 input:"aA1_!.?" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"." index:5 input:"aA1_!.?" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"?" index:6 input:"aA1_!.?" length:1]
```

`/[0-9A-Z_!.?]/`中真正是特殊字符元字符的只有`0-9A-Z`中的两个连字符,而`!.?`都只是普通字符.

>提示: 不妨把字符组看作是独立的微型语言,在字符组内部和外部关于元字符的规定是不同的.某些字符在字符组内部是元字符,而某些字符在字符组内部是普通字符,在字符组外部则是元字符.


#### 2.1.2.2 排除型字符

- **排除型字符组**`[^...]`: 这个字符组匹配任何未列出的字符,这个字符组的开头`^`表示**排除**. 需要注意的是该字符和表示行首的**起始字符`^`**(详见`2.1.1`)是一样的字符,但是只有在字符组**内部**(而且是必须紧接在字符组的第一个方括号`[`之后)`^`才是元字符,而在字符组外部,`^`表示**起始字符**.


`/[^0-9A-Z_!.?]/g`是以全局模式匹配一个字符,这个字符是非数字、非大写字母、`_`、`!`、`.`和`?`.

``` javascript
var pattern = /[^0-9A-Z_!.?]/g,
    text = "aA1_!.?",
    matches = pattern.exec(text);

console.log(matches);  //[0:"a" index:0 input:"aA1_!.?" length:1]

matches = pattern.exec(text);
console.log(matches);  //null
```
`/c[^a]/g`与上面的例子不同的是这里需要匹配两个字符而不是一个字符,这两个字符是在需要匹配的字符串的任意位置中,以`c`开头,紧接着是**非`a`**的字符. 需要注意匹配非`a`的字符说明需要匹配一个字符,这个字符不是`a`. 如果`c`后面没有字符,那是不匹配的,因为**排除型字符**需要我们匹配一个未列出的字符,因此不会匹配`c`在行尾的情况. 当然如果`c`不在行尾,且`c`后面紧接着空格,那么也不是匹配的.

``` javascript
var pattern = /c[^a]/g,
    text = "cat bat cup c",
    matches = pattern.exec(text);

console.log(matches);  //[0:"cu" index:8 input:"cat bat cup c" length:1]

matches = pattern.exec(text);
console.log(matches);   //null 注意最后的c是不匹配的,因为c后面没有字符,这里c后面需要匹配一个非a的字符

text = "cat bat cup c ";
matches = pattern.exec(text);
console.log(matches);  //[0:"cu" index:8 input:"cat bat cup c" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"c " index:12 input:"cat bat cup c" length:1] 注意匹配的字符串c后面有空格
```

>提示: 一个字符组,即使是排除型字符组,也需要匹配一个字符.




### 2.1.3 匹配任意字符的元字符
- 元字符`.`: 表达式中可以使用的一个 "匹配任何字符" 的占位符.

`/11[_./]22[_./]33/g`可以匹配`11.22.33`、`11_22_33`、`11/22/33`. 

``` javascript
var pattern = /11[_./]22[_./]33/g,
    text = "11.22.33 11_22_33 11/22/33";

var matches = pattern.exec(text);
console.log(matches);  //[0:"11.22.33" index:0 input:"11.22.33 11_22_33 11/22/33" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"11_22_33" index:9 input:"11.22.33 11_22_33 11/22/33" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"11/22/33" index:18 input:"11.22.33 11_22_33 11/22/33" length:1]
```

如果只是模糊匹配,以上匹配方式也可以更改为`/11.22.33/g`(相对以上正则这个匹配的范围更广),其中的`.`表示可以匹配任意字符.

``` javascript
var pattern = /11.22.33/g,
    text = "11.22.33 11_22_33 11/22/33";

var matches = pattern.exec(text);
console.log(matches);  //[0:"11.22.33" index:0 input:"11.22.33 11_22_33 11/22/33" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"11_22_33" index:9 input:"11.22.33 11_22_33 11/22/33" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"11/22/33" index:18 input:"11.22.33 11_22_33 11/22/33" length:1]
```

>注意: 在`/11[_./]22[_./]33/g`中的点号`.`并不是元字符,因为在字符组内部(注意在字符组内外字符的定义和意义是不一样的),这里的连字符`_`同样也不是元字符(因为它出现在了字符组`[`或`[^`的开头,详见`2.1.2.1`).但是不在字符组内部的`/11.22.33/g`的点号`.`则表示元字符,可以匹配任意字符.

>提问: `[.-/]`中的`_`是什么意思?
>由于不是出现在字符组的开头,所以是元字符,但是在这里是错误的用法,因为这里无法明确表示范围.

### 2.1.4 多选结构
- 元字符`|`: 当我们把不同的子表达式组合成一个总表达式,它能够匹配任意的子表达式.例如`/apple|banana/`中有两个子表达式`apple`和`banana`,`/apple|banana/`可以匹配这两个子表达式中任意的一个子表达式,在这样的组合中,子表达式称为"**多选分支**".


`/1|2|3/g`可以以全局的方式匹配一个字符,这个字符是`1`或`2`或`3`.

``` javascript
var pattern = /1|2|3/g,
    text = "135";

var matches = pattern.exec(text);
console.log(matches);  //[0:"1" index:1 input:"135" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"3" index:3 input:"135" length:1]
```

可以用括号`()`来划定多选结构范围,注意`()`也是元字符,例如需要匹配`123`或者`125`时,可以使用`/12(3|5)/`来匹配.

``` javascript
var pattern = /12(3|5)/g,
    text = "123 124 125";

var matches = pattern.exec(text);
console.log(matches);  //[0:"123" index:0 input:"123 124 125" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"125" index:8 input:"123 124 125" length:1]
```

需要注意的是多选结构很容易和字符组产生混淆,例如`/12(3|5)/`和`/12[35]/`的功能都一样.

``` javascript
var pattern = /12[35]/g,
    text = "123 124 125";

var matches = pattern.exec(text);
console.log(matches);  //[0:"123" index:0 input:"123 124 125" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"125" index:8 input:"123 124 125" length:1]
```

但是如果我们要匹配`1234、1245、1256`时,使用多选结构可以使用表达式`/12(34|45|55)/`，而使用字符组都需要使用表达式`/12[3-5][4-6]/`. 

``` javascript
var pattern = /12(34|45|56)/g,
    text = "11231234";

var matches = pattern.exec(text);
console.log(matches);  //[0:"1234" 1:"34" index:4  input:"11231234" length:2]


pattern = /12[3-5][4-6]/g,
matches = pattern.exec(text);
console.log(matches);  //[0:"1234" index:4 input:"11231234" length:1]
```
>提示: 一个字符组只能匹配目标文本中的单个字符,而每个多选结构都可能是完整的正则表达式,可以匹配任意长度的文本. 字符组可以算是独立的一门微型语言(例如在字符组中对于元字符有不同的规定,和字符组外的正则表达式有区别),而多选结构是正则表达式语言主体的一部分.

>提问: `/^apple|banana|orange:/`和`/^(apple|banana|orange):/`的区别?
>前者匹配`^apple`、`banana`、`orange:`，后者先匹配一行的起始符号`^`，接着匹配`apple`或`banana`或`orange`，最后匹配`:`.


### 2.1.5 单词分界符

- `\b`: 匹配一个单词的边界,位于字符`\w`和`\W`之间的位置,或位于字符`\w`和字符串的开头或者结尾之间的位置,`\b`匹配的是退格符
- `\B`: 匹配非单词边界的位置

`\b`用来匹配单词的开头和结束位置,就像作为行锚点的`$`和`^`一样都用来匹配位置,在匹配的过程中并不对应到任何字符.`/\bbat\b/`先是匹配单词的开头位置,然后匹配`b`、`a`、`t`,最后匹配单词的结束位置,或者直接点说可以是匹配`bat`这个单词, `b`本身并不是元字符,只有于`\`结合起来整个序列才有特殊意义,称其为"**元字符序列**", 重要的是它特殊的意义,因此元字符和元字符序列大多数时候是等价的.

``` javascript
var pattern = /\bbat\b/g,   //匹配bat单词,
    text = "cat bat fat";

var matches = pattern.exec(text);
console.log(matches);       //[0:"bat" index:4  input:"cat bat fat" length:1]


pattern = /\BScript/;
text = "JavaScript";
matches = pattern.exec(text);
console.log(matches);       //[0:"Script" index:4  input:"JavaScript" length:1]
```

如果想要匹配单词`Java`,一种方法是匹配单词`Java`两边是否有空格

```javascript
var pattern = /\sJava\s/g,  //匹配Java单词
    text = "Java Java JavaScript Java";

var matches = pattern.exec(text);
console.log(matches);       //[0:"Java" index:4  input:"Java Java JavaScript Java" length:1]


matches = pattern.exec(text);
console.log(matches);       //null
```

但是从例子可以看出并不能匹配行首和行尾的单词`Java`,因此可以使用`\b`代替空格符`\s`

``` javascript
var pattern = /\bJava\b/g,  //匹配Java单词
    text = "Java Java JavaScript Java";

var matches = pattern.exec(text);
console.log(matches);  //[0:"Java" index:0  input:"Java Java JavaScript Java" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"Java" index:5  input:"Java Java JavaScript Java" length:1]

matches = pattern.exec(text);
console.log(matches);  //[0:"Java" index:21  input:"Java Java JavaScript Java" length:1]
```

>注意: 单词分界符只是匹配位置,而不会去识别匹配的字符串是不是一个真正的英语单词,它只是按照规则去匹配一系列字符和数字符号的开始位置和结束位置.

### 2.1.6 小结

| 元字符     |     名称  |   匹配对象|
| :-------- | :--------| :------ |
| `.`|   点号 |  单个任意字符 |
| `[...]`|  字符组 |  列出的任意字符 |
| `[^...]`|  排除型字符组 |  未列出的任意字符 |
| `^`|  脱字符 |  行的起始位置 |
| `$`|  美元符 |  行的结束位置 |
| `\b`|   | 单词的起始和结束位置  |
| `\|`| 竖线  | 匹配分隔两边的任意一个表达式  |
| `(...)`| 括号  | 匹配分隔两边的任意一个表达式,限制竖线的作用范围,其他功能下文讨论  |

### 2.1.7 可选项元素

 - `?`: 代表**可选项**,需要注意在字符组`[]`内部仍然代表普通字符.

`/u?/`是必然能够匹配成功的,它可以匹配一个`u`,也可以什么都不匹配,这个是必然能够匹配成功的.

``` javascript
var pattern = /u?/g,    //匹配Java单词
    text = "u a b";

var matches = pattern.exec(text);
console.log(matches);       //[0:"u" index:0  input:"u a b" length:1]

matches = pattern.exec(text);
console.log(matches);       //[0:" " index:1  input:"u a b" length:1]

matches = pattern.exec(text);
console.log(matches);       //[0:" " index:1  input:"u a b" length:1]

matches = pattern.exec(text);
console.log(matches);       //[0:" " index:1  input:"u a b" length:1]
```

因此`/(July|Jul)/`可以去掉括号使用`/July?/`表示. `/(July|Jul) (fourth|4th)/` 则可以使用`/July? four(th)?/`进行表示,需要注意的是其中的`(th)?`中的`?`现在作用的是整个括号了(把括号内的表达式看成一个整体).

### 2.1.8 量词:重复出现
- `+`: 之前紧邻的元素出现一次或多次. `[...+]`匹配尽可能多的次数,如果一次匹配都无法完成则报告失败.
- `*`: 之前紧邻的元素出现任意多次或者不出现. `[...*]`表示匹配尽可能多的次数,如果实在无法匹配也不要紧.

>注意: `?*+`这3个元字符统称为**量词**. 因为它们限定了所作用元素的重复出现次数. `[...?]`和`[...*]`永远都不会匹配失败.`/ ?/`和`/ */`的区别是前者能够匹配一个可能出现的空格,后者能够匹配任意多个空格.

例如匹配**HTML**中的`<H[1-6]>`标签, 按照**HTML**规范可以在标签结尾`>`字符之前添加多个空格或者没有空格,那么应该使用`*`而不是`?`

``` javascript
var pattern = /<H[1-6] *>/, 
    text = "<H1>",
    text2 = "<H1  >";

var matches = pattern.exec(text);
console.log(matches);       //[0:"<H1>" index:0  input:"<H1>" length:1]

matches = pattern.exec(text2);
console.log(matches);       //[0:"<H1  >"" index:0  input:"<H1  >" length:1]
```

例如需要匹配`<div width=14>`这个`div`标签时, 允许`>`之前有任意多个空格或没有空格,而`div`和`width`之间至少有一次空格,而`=`两边则也可以由任意多个空格或者没有空格,则可以使用表达式`/<div +width *= *14 *>/`,其中`/ +/`表示至少要匹配一次空格

``` javascript
var pattern = /<div +width *= *14 *>/,  
    text = "<div width   = 14         >",
    text2 = "<divwidth= 14>";
    text3 = "<div width=14>";

var matches = pattern.exec(text);
console.log(matches);       //[0:"<div width   = 14         >" index:0  input:"<div width   = 14         >" length:1]

matches = pattern.exec(text2);
console.log(matches);       //null

matches = pattern.exec(text3);
console.log(matches);       //[0:"<div width=14>"" index:0  input:"<div width=14>" length:1]
```

`/<div +width *= *14 *>/`如果还需要匹配范围更通用,可以改成`/<div +width *= *[0-9]+ *>/`,其中`[0-9]+`表示至少匹配数字一次,当然`00`也是会被匹配的,需要注意的是一个字符组就是一个"元素",可以对它直接使用`+*`而不需要括号. 在以上例子中`*`和`+`的作用对象都是空格,因此要习惯把空格当做普通字符.

>提问: 如果`width`属性是可选的, 也可以只匹配`<div>`该如何实现呢?
>解答: 可选项当然要用`?`元字符,因此应该是`/<div( +width *= *[0-9]+)? *>/`,这里需要注意把`/ +/`包含在可选项的范围内,不然`<div>`就不匹配了,因为有了`/ +/`至少需要匹配一个空格了.而结尾的`/ */`则不应该包含在可选项之内,因为需要匹配`<div  >`这样的情况.

问号`?`星号`*`和加号`+`的重要区别

| 元字符类型|     次数下限|   次数上限| 次数上限|
| :-------- | :--------| :------ |:------ |
| `?` |   0 |  1| 可以不出现,也可以只出现一次 |
| `*` |   0 |  n| 可以不出现,也可以出现任意多次 |
| `+` |   1 |  n| 不可以不出现,可以出现一次或任意多次 |

### 2.1.9 区间量词:规定重现次数的范围

- `{min,max}`: 允许重复出现的次数范围为`[min,max]`.

`/^[a-zA-Z]{1,5}$/`用于匹配一个字符串,字符串可以由`1~5`个字母组成

``` javascript
var pattern = /^[a-zA-Z]{1,5}$/,    
    text = "abcdefg",
    text2 = "abcd";
    

var matches = pattern.exec(text);
console.log(matches);       //null
matches = pattern.exec(text2);
console.log(matches);       //[0:"abcd" index:0  input:"abcd" length:1]
```

### 2.1.10 括号及反向引用

- `()`: 之前所说的括号主要作用有两个,第一个是限制多选项`|`的范围,第二个是将若干字符组合为一个单元,受`?`或者`*`之类的量词的作用. 第三个作用就是**反向引用**. `()`可以把它包含的子表达式匹配的文本记忆起来,同时使用反向引用就可以匹配与表达式先前部分匹配的同样的本文.

如果要匹配`/the the/`,可以使用`/(the) +\1/`进行代替,其中`\1`就是反向引用,它引用的是`()`中记忆的表达式

``` javascript
var pattern = /(the) +\1/,  
    text = "the the apple";
    

var matches = pattern.exec(text);
console.log(matches);       //[0:'the the' 1:'the' index:0 inpout:'the the apple' length:2]
```

如果要匹配重复的单词,更通用的方法当然是使用`/\b([a-zA-Z]+) +\1\b/`来匹配重复的单词

``` javascript
var pattern = /\b([a-zA-Z]+) +\1\b/g,   
    text = "the the apple apple";
    

var matches = pattern.exec(text);
console.log(matches);       //[0:'the the' 1:'the' index:0 inpout:'the the apple apple' length:2]

matches = pattern.exec(text);
console.log(matches);       //[0:'apple apple' 1:'apple' index:8 inpout:'the the apple apple' length:2]
```

如果有多个括号,当然可以使用`\2`甚至`\3`来引用第二组括号或者第三组括号匹配的文本. 

### 2.1.11 转义字符
- `\`:  如果要匹配元字符本身,就需要对元字符进行转义,使正则表达式可以把元字符当做普通字符看待,`\`就是用作对元字符进行转义使用的字符.

例如要匹配`www.ziyi2.cn`,使用`/www.ziyi2.cn/`是可以匹配到该网址的,但是也可以匹配诸如`wwwaziyi2bcn`这种非网址的情况,因为其中的`.`在这里表示元字符,可以匹配任意的字符,所以为了匹配`.`本身,需要使用表达式`/www\.ziyi2\.cn/`,其中`\.`就是对`.`的转义,使其不表示元字符

``` javascript
var pattern = /www\.ziyi2\.cn/g,    
    text = "www.ziyi2.cn";
    text1 = "www.ziyi2 cn";
    

var matches = pattern.exec(text);
console.log(matches);       //[0:'www.ziyi2.cn' index:0 inpout:'www.ziyi2.cn' length:1]

matches = pattern.exec(text);
console.log(matches);       //null
```

需要注意的是`JavaScript`语言支持在字符组内部的转义,某些语言或者工具会把字符组内部的`\`看成是普通字符处理

``` javascript
var pattern = /www[\.]ziyi2[\.]cn/g,    
    text = "www.ziyi2.cn";

var matches = pattern.exec(text);
console.log(matches);       //[0:'www.ziyi2.cn' index:0 inpout:'www.ziyi2.cn' length:1]
```


### 2.1.12 元字符总结

#### 2.1.12.1 匹配对象

| 元字符     |     名称  |   匹配对象|
| :-------- | :--------| :------ |
|  |
| `.`|   点号 |  **单个**任意字符 |
| `[...]`|  字符组 |  **单个**列出的任意字符 |
| `[^...]`|  排除型字符组 |  **单个**未列出的任意字符 |
| `\char`|  转义字符 | 若`char`是元字符,匹配`char`对应的普通字符 |

#### 2.1.12.2 提供计数功能的量词

| 元字符     |     名称  |   匹配对象|
| :-------- | :--------| :------ |
| `?` |  问号 | 可以不出现,也可以只出现一次 |
| `*` |  星号 |   可以不出现,也可以出现任意多次 |
| `+` |  加号 |  不可以不出现,可以出现一次或任意多次 |
| `{min,max}` |  区间量词 |  至少出现`min`次,至多出现`max`次 |

#### 2.1.12.3 匹配位置
| 元字符     |     名称  |   匹配对象|
| :-------- | :--------| :------ |
| `^`|  脱字符 |  行的起始位置 |
| `$`|  美元符 |  行的结束位置 |
| `\b`|   | 单词的起始和结束位置  |

#### 2.1.12.4 其他元字符

| 元字符     |     名称  |   匹配对象|
| :-------- | :--------| :------ |
| `()`|  括号 |  限定多选结构的范围,标注量词作用的范围,为**反向引用**捕获文本 |
| `\1,\2,...\n`|  反向引用 |  捕获之前的第一个括号,第二个括号乃至第n个括号的文本 |

>注意

- 使用括号的3个理由: 限制多选结构,分组和捕获文本.
- 多选结构和字符组功能完全不同,在有限的情况下表现相同.
- 排除型字符组仍然需要匹配一个字符.
- 转义字符的情况: `\`加上元字符表示匹配元字符对应的普通字符,`\`加上非元字符组成一种由具体实现方式规定其意义的元字符序列(例如`\b`),`\`加上任意其他字符默认情况就是匹配此字符.
- 字符组的特殊性在它的规定是完全独立于正则表达式语言"主体"的.

## 3.入门示例拓展

### 3.1 使用正则表达式匹配文本

#### 3.1.1 简单实用的程序

>提问: 如何匹配一个数,这个数既可以是负数也可以是正数,可以是整数也可以是浮点数?

如果要匹配正负数,首先需要匹配符号,可以使用`/-?/`或者`/[-+]?/`来匹配可能出现的符号,`?`表示可以出现一次或者不出现. 接下来是匹配整数或者浮点数,如果要匹配整数那么可以使用`/[0-9]+/`来表示整数部分,需要注意的是也可以匹配`00009`这样的数,接下来如果有小数部分,则可以使用`/\.[0-9]+/`来表示小数部分,需要注意的是一旦有了`.`那么后面必须要跟数字,所以这里使用`+`(至少匹配一次)而不是`*`(可以不匹配),把它合起来就是`/[-+]?[0-9]+\.[0-9]+/`,但是需要注意的是这种情况无法匹配整数,因此可以给小数点以及之后的部分加上`?`,让小数点以及之后的数字可以出现一次或者不出现,因此最终的匹配表达式是`/[-+]?[0-9]+(\.[0-9]+)?/`

``` javascript
var pattern = /^[-+]?[0-9]+(\.[0-9]+)?$/,   
    num = 123,
    num2 = -123;
    num3 = 123.0009
    num4 = "123.009.09",
    num5 = "-000123.000",
    num6 = "123.";
    num7 = ".123";


var matches = pattern.exec(num);
console.log(matches);       //[0:'123' 1:undefined index:0 inpout:'123' length:2]

matches = pattern.exec(num2);
console.log(matches);       //[0:'-123' 1:undefined index:0 inpout:'-123' length:2]

matches = pattern.exec(num3);
console.log(matches);       //[0:'123.0009' 1:'.0009' index:0 inpout:'123.0009' length:2]

matches = pattern.exec(num4);
console.log(matches);       //null

matches = pattern.exec(num5);
console.log(matches);       //[0:'-000123.000' 1:'.000' index:0 inpout:'-000123.000' length:2]

matches = pattern.exec(num6);
console.log(matches);       //null

matches = pattern.exec(num7);
console.log(matches);       //null
```

#### 3.1.2 成功匹配的副作用

在之前所有的`JavaScript`例子中,我们可以看到使用`pattern.exec()`函数匹配时返回的数组中第一项是与整个模式匹配的字符串,其他项是与模式中的捕获组匹配的字符串(如果没有捕获组,数组只包含一项),捕获组就是匹配的副作用. 反向引用使用`\1 \2 ... \n`来指代正则表达式之前的括号内的子表达式, 和此类似的是在`JavaScript`语言中支持捕获组的概念,一旦文本匹配成功后,匹配文本所对应的正则表达式如果有`()`包裹的子表达式,那么子表达式所匹配的文本内容就能被捕获.在`JavaScript`中使用`RegExp.$1`、`RegExp.$2`...表示匹配`()`内子表达式的文本.

例如`/^[0-9]+[ms]$/`表示匹配单位是`m`或者`s`的数

``` javascript
var pattern = /^[0-9]+[ms]$/,   
    num = '123m',
    num2 = '123s';


var matches = pattern.exec(num);
console.log(matches);       //[0:'123m' index:0 inpout:'123m' length:1]

matches = pattern.exec(num2);
console.log(matches);       //[0:'123s'  index:0 inpout:'123s' length:1]
```
但是`/^([0-9]+)([ms])$/`也可以匹配同样的文本,但是它还可以捕获额外的子文本,同时返回的数组长度不在是`1`,还包含了捕获组的文本

``` javascript
var pattern = /^([0-9]+)([ms])$/,   
    num = '123m',
    num2 = '123s';


var matches = pattern.exec(num);
console.log(matches);       //[0:'123m' 1:'123' 2:'m' index:0 inpout:'123m' length:3]

console.log(RegExp.$1);     //123 第一个括号所匹配的子文本
console.log(RegExp.$2);     //m   第二个括号所匹配的自文本

matches = pattern.exec(num2);
console.log(matches);       //[0:'123m' 1:'123' 2:'s' index:0 inpout:'123m' length:3]

console.log(RegExp.$1);     //123 第一个括号所匹配的子文本
console.log(RegExp.$2);     //s   第二个括号所匹配的自文本
```

