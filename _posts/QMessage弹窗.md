



**自定义QMessageBox：适用于提示用户做出选择的场景，两个选择按钮均可以有对应操作**

```c++
QPushButton *determinePushButton = new QPushButton("确定");
QPushButton *cancelPushButton = new QPushButton("取消");

// "是否保存" 弹窗
QMessageBox *mymsgbox = new QMessageBox;
mymsgbox->setIcon(QMessageBox::Warning);
mymsgbox->setWindowTitle("修改尚未保存");
mymsgbox->setText("是否保存修改的内容？");
mymsgbox->addButton(determinePushButton, QMessageBox::AcceptRole);
mymsgbox->addButton(cancelPushButton, QMessageBox::RejectRole);
mymsgbox->show();
mymsgbox->exec();//阻塞等待用户操作

if(mymsgbox->clickedButton() == determinePushButton)
{
    on_preservationPushButton_clicked();
    mymsgbox->close();
}
```

