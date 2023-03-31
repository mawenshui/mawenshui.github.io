# QTreeWidget全部遍历节点

```c++
QTreeWidgetItemIterator it(ui_Platform->treeWidget_Platform);
while (*it)
{
    //对(*it)进行处理，(*it)就是QTreeWidgetItem *类型的，比如(*it)->text(0)
    (*it)->text(0)
       ++it;
}//这个循环会对所有item进行遍历，方式为 先序遍历
```

