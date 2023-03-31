# 双击控件添加 QLineEdit 进行编辑

[TOC]



## 1.头文件定义函数

```c++
private slots:
void on_treeWidget_itemDoubleClicked();//双击树节点槽函数

void slotFinishEdit();//编辑完成后,移除widget,还原树对应的槽函数
```

## 2.树节点双击响应槽函数

```c++
/********************************************************************
Function:    // on_treeWidget_itemDoubleClicked
Description: // 树节点双击响应槽函数
Input:       // 无
Output:      // 无
Return:      // 无
Author:      // 马文水
Time:        // 2021-10-11
********************************************************************/
void Widget::on_treeWidget_itemDoubleClicked()
{
    // 使用QTextEdit的信号
    QLineEdit *pLineEdit=new QLineEdit(this);

    // 设置widget
    ui->treeWidget->setItemWidget(ui->treeWidget->currentItem(),0,pLineEdit);

    // 给QLineEdit设置初始值
    pLineEdit->setText(ui->treeWidget->currentItem()->text(0));
    
    connect(pLineEdit,SIGNAL(editingFinished()),this,SLOT(slotFinishEdit()));
    
}
```

## 3.编辑完成后,移除widget,还原树对应的槽函数

```c++
/********************************************************************
Function:    // slotFinishEdit
Description: // 编辑完成后,移除widget,还原树对应的槽函数
********************************************************************/
void Widget::slotFinishEdit()
{
    QLineEdit *pEdit=qobject_cast<QLineEdit*>(ui->treeWidget->itemWidget(ui->treeWidget->currentItem(),0));

    if(!pEdit)
    {
        return;
    }

    // 编辑结束，移除widget
    ui->treeWidget->removeItemWidget(ui->treeWidget->currentItem(),0);
}
```

