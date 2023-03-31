#  QTableWidget创建右键菜单

 **（1）创建菜单、菜单项**

```
void DlgPlatformProperties::createActions()
{
   //创建菜单项
   pop_menu = new QMenu();
   action_name = new QAction(this);
   action_size = new QAction(this);
   action_type = new QAction(this);
   action_date = new QAction(this);

   action_open = new QAction(this);  
   action_download = new QAction(this);
   action_flush = new QAction(this);

   action_delete = new QAction(this);
   action_rename = new QAction(this);
   action_create_folder = new QAction(this);

   action_open->setText(QString("打开"));

   action_download->setText(QString("下载"));

   action_flush->setText(QString("刷新"));

   action_delete->setText(QString("删除"));

   action_rename->setText(QString("重命名"));

   action_create_folder->setText(QString("新建文件夹"));

   action_name->setText(QString("名称"));
   action_size->setText(QString("大小"));
   action_type->setText(QString("项目类型"));
   action_date->setText(QString("修改日期"));

   //设置快捷键
   action_flush->setShortcut(QKeySequence::Refresh);

   //设置文件夹图标
   action_create_folder->setIcon(icon);

   QObject::connect(action_create_folder, SIGNAL(triggered()), this, SLOT(createFolder()));
} 
```

 **（2）重新实现contextMenuEvent**

```
void DlgPlatformProperties::contextMenuEvent(QContextMenuEvent *event)
{
    pop_menu->clear(); //清除原有菜单
    QPoint point = event->pos(); //得到窗口坐标

    QTableWidgetItem *item = this->itemAt(point);

    if(item != NULL)
    {
        pop_menu->addAction(action_download);
        pop_menu->addAction(action_flush);
        pop_menu->addSeparator();
        pop_menu->addAction(action_delete);
        pop_menu->addAction(action_rename);
        pop_menu->addSeparator();
        pop_menu->addAction(action_create_folder);

        sort_style = pop_menu->addMenu("排序");
        sort_style->addAction(action_name);
        sort_style->addAction(action_size);
        sort_style->addAction(action_type);
        sort_style->addAction(action_date);

        //菜单出现的位置为当前鼠标的位置
        pop_menu->exec(QCursor::pos());
        event->accept();
	} 

}
```

