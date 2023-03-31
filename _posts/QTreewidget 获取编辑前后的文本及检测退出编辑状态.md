# QTreeWidget 获取编辑前后的文本及检测退出编辑状态

#### **写两个信号连接即可**



#### **一，**

##### **首先再双击时设置QTreeWidget 为可编辑，**

##### **双击时 -进入编辑状态记录编辑前的文本**

##### **设置全局变量dcstr**

```c++
QString dcstr;
dcstr=item->text(0);
connect(ui->treeWidget,&QTreeWidget::itemDoubleClicked,this,[=](QTreeWidgetItem *item, int column)
{
ui->treeWidget->currentItem()->setFlags(ui->treeWidget->currentItem()->flags()|Qt::ItemIsEditable);
});
```



#### 相关信息：

```c++
flags（）返回控件当前信息
setflags() 设置控件信息
setflags的参数如下
Constant Value Description
Qt::NoItemFlags 			0 It does not have any properties set.
Qt::ItemIsSelectable 		1 It can be selected.
Qt::ItemIsEditable 			2 It can be edited.
Qt::ItemIsDragEnabled 		4 It can be dragged.
Qt::ItemIsDropEnabled 		8 It can be used as a drop target.
Qt::ItemIsUserCheckable 	16 It can be checked or unchecked by the user.
Qt::ItemIsEnabled 			32 The user can interact with the item.
Qt::ItemIsAutoTristate 		64 The item’s state depends on the state of its children. This enables automatic management of 									the state of parent items in QTreeWidget (checked if all children are checked, unchecked if 								all children are unchecked, or partially checked if only some children are checked).
Qt::ItemIsTristate ItemIsAutoTristate This enum value is deprecated. Use Qt::ItemIsAutoTristate instead.
Qt::ItemNeverHasChildren 	128 The item never has child items. This is used for optimization purposes only.
Qt::ItemIsUserTristate 		256 The user can cycle through three separate states.

参数之间平列关系： 如 item ->setflags(Qt::NoItemFlags|Qt::ItemIsSelectable|Qt::ItemIsDragEnabled);
```

#### 二，

##### 设置控件文本变化的信号关系

##### 控件发出文本变化信号且文本与双击时不同，则文本改变完成编辑，

##### 也就是退出编辑状态。

```c++
connect(ui->treeWidget,&QTreeWidget::itemChanged,this,[=](QTreeWidgetItem item, int column)
{
if(dcstr!=item->text(0))
{
//dcstr编辑前的文本
//item->text(0)退出编辑后的文本
//相关操作。。。
}
/*

*/
});
```

