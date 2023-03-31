QGroupBox设置了border之后会造成title下沉，即使在designer 中设置了title向上或者居中，都没有效果。

如下是解决办法:

1.

```css
QGroupBox{
border:1px solid;
margin-top:6px;
}

QGroupBox{
padding: -16 6px 0 6px;
left:10px;
}
```

2.

```css
QGroupBox{
border:1px solid;
margin-top:6px;
}

QGroupBox{
padding: -16 6px 0 6px;
left:10px;
}

QGroupBox::title { 
subcontrol-origin: margin;
subcontrol-position: top left;
left:10px; //线的偏离度
margin-left: 0px;
padding:0 1px; //文字在方框中位置的偏离度
}
```

