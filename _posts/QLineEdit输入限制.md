

# QLineEdit输入限制

在使用QLineEdit输入数值时，经常遇到限制其范围的需要，比如角太阳高度角范围为[-90,90]，经度值范围[-180,180]，方位角范围[0,360]。Qt提供了QIntValidator和QDoubleValidator可以限定数值输入范围，如使用QIntValidator限制整数的数值范围：

例1：

```c++
lineEdit->setValidator(new QIntValidator(0, 1000, this)); 
```



例2：

```c++
lineEdit->setValidator(new QDoubleValidator(-180.0,180.0,6,this)); 
```

​    对于浮点数，使用QDoubleValidator时，发现只能限制只输入小数，但是无法设定数值范围，因此有必要对这个问题做一番研究。

​    除了QIntValidator和QDoubleValidator，Qt提供另一种校验器是正则表达式校验器：QRegExpValidator，下面是一些常用的利用正则表达式校验器限定数值范围的用法：

**限制浮点数输入范围为[-999999.9999,999999.9999]** 

```c++
QRegExp rx("^(-?[0]|-?[1-9][0-9]{0,5})(?:\\.\\d{1,4})?$|(^\\t?$)"); 
QRegExpValidator *pReg = new QRegExpValidator(rx, this); 
lineEdit->setValidator(pReg); 
```

**限制浮点数输入范围为[-180,180]**

```c++
QRegExp rx("(^-?180$)|(^-?1[0-7]\\d$)|(^-?[1-9]\\d$)|(^-?[1-9]$)|^0$"); 
QRegExpValidator *pReg = new QRegExpValidator(rx, this); 
lineEdit->setValidator(pReg); 
```



**限制浮点数输入范围为[-180,180]**

```c++
QRegExp rx("^-?(180|1?[0-7]?\\d(\\.\\d+)?)$"); 
QRegExpValidator *pReg = new QRegExpValidator(rx, this); 
lineEdit->setValidator(pReg); 
```



**限制浮点数输入范围为[-180,180]并限定为小数位后4位**

```c++
QRegExp rx("^-?(180|1?[0-7]?\\d(\\.\\d{1,4})?)$"); 
QRegExpValidator *pReg = new QRegExpValidator(rx, this); 
lineEdit->setValidator(pReg); 
```



**限制浮点数输入范围为[-90,90]并限定为小数位后4位**

```c++
QRegExp rx("^-?(90|[1-8]?\\d(\\.\\d{1,4})?)$"); 
QRegExpValidator *pReg = *new** QRegExpValidator(rx, **this**); 
lineEdit->setValidator(pReg); 
```



简单说明一下这几个正则表达式：

```c++
^(-?[0]|-?[1-9][0-9]{0,5})(?:\.\d{1,4})?$|(^\t?$)

(^-?180$)|(^-?1[0-7]\d$)|(^-?[1-9]\d$)|(^-?[1-9]$)|^0$

^-?(180|1?[0-7]?\d(\.\d+)?)$

^-?(180|1?[0-7]?\d(\.\d{1,4})?)$

^-?(90|[1-8]?\d(\.\d{1,4})?)$
```



```c++
1.  式子中开头的^和结尾的$限定字符串的开始和结尾；
2.  "-?" 表示一个或0个负号，这里面的问号表示其前面的字符重复0次或1次；
3.  管道符“|”表示平行分组，比如后三个，表示180或其它形式；
4.  [1-9] 表示限定数字范围为1到9，其余类似，如果是有限几个值，还可以用枚举的方式，比如限定-255到255时，第一个数字2的限定，应该表达为[1,2]，这表示这个位置只允许是1或者2；
5.  "\d"是一个转义字符，表示匹配一位数字；
6.  “\.” 表示匹配小数点；
7.  "\d+"，这里面的+表示其前面的\d重复一次或多次；
8.  "\d{1,4}"，里面的{1,4}表示重复1到4次；
```





有了以上知识，下面我们可以很快的写出限定[-255,255]的正则表达式：

```c++
[-255,255]整数：^-?(255|[1,2]?[0-4]?\d|[1,2]?5[0-4]?)$

[-255,255]小数：^-?(255|([1,2]?[0-4]?\d|[1,2]?5[0-4]?)(\.\d)?)$
```

**参考：**

1.[Qt限制文本框输入的方法](http://blog.csdn.net/rabinsong/article/details/8932713)

2.[怎么让QLineEdit中只能输入数字](http://bbs.csdn.net/topics/330184617)

3.[用正则表达式配出-180到180该怎么写](http://wenwen.soso.com/z/q324681274.html)

4.[求正则表达式，在-180到180之间的数字，包括浮点数](http://zhidao.baidu.com/link?url=t2OLnpy18tWBVaxreopq3XHhwEI2rGpEc-5xfg2yBWbV-bhz9kAAHbh2Wq0mYdIOUEe9vU-tdekkWqjwQJBbNK)