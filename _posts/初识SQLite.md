# 初识SQLite

## DBSqlite代码

### DBSqlite.pro中更改

```c++
QT += core gui sql
```

### mymodel.h

```c++
#ifndef MYMODEL_H
#define MYMODEL_H

#include <QAbstractItemModel>
#include <QSqlDatabase>
#include <QSqlRelationalTableModel>
class MyModel : public QSqlRelationalTableModel
{
    Q_OBJECT

public:
    explicit MyModel(QSqlDatabase db, QObject *parent = nullptr);

    void selectSome();

private:
    QSqlDatabase m_db;
};

#endif // MYMODEL_H
```

### myModel.cpp

```c++
#include "myModel.h"
#include <QSqlQuery>
MyModel::MyModel(QSqlDatabase db, QObject *parent)
    : QSqlRelationalTableModel(parent, db), m_db(db)
{
}

void MyModel::selectSome()
{
    QSqlQuery qs("SELECT Name, ID FROM PEOPLE", m_db);
    setQuery(qs);
}
```

### widget.h

```c++
#ifndef WIDGET_H
#define WIDGET_H

#include <QWidget>
#include <QDebug>
#include <QtSql/QSqlDatabase>
#include <QSqlQuery>
#include <QSqlRelationalTableModel>
#include <QTableView>
#include "myModel.h"

QT_BEGIN_NAMESPACE
namespace Ui { class Widget; }
QT_END_NAMESPACE

class Widget : public QWidget
{
    Q_OBJECT

public:
    Widget(QWidget *parent = nullptr);
    ~Widget();

    QSqlDatabase db;

private:
    Ui::Widget *ui;

    bool openDatabase();
    bool createTable();
    void insertData();
    void queryTable();
    void showDatabase();
    void callMyModel();

};
#endif // WIDGET_H
```

### widget.cpp

```c++
#include "widget.h"
#include "ui_widget.h"

Widget::Widget(QWidget *parent)
    : QWidget(parent)
    , ui(new Ui::Widget)
{
    ui->setupUi(this);

//检查qt支持的数据库驱动 QSQLITE就是指支持sqlite3，没有的话就得先编译对应的驱动
//    qDebug() << QSqlDatabase::drivers();

    db = QSqlDatabase::addDatabase("QSQLITE");

    openDatabase();
    createTable();
    insertData();
    queryTable();
    showDatabase();
//    callMyModel();
}



/***********************************************************************
Function:    // openDatabase()
Description: // 创建并打开sqlite数据库文件
Input:       // 无
Output:      // 无
Return:      // 无
Author:      // 马文水
Time:        // 2021-09-11
************************************************************************/
bool Widget::openDatabase()
{

    db.setDatabaseName("sqliteTest.sqlite");

    if(db.open())
    {
        qDebug() << db.databaseName() + " open success" << endl;
        return true;
    }

    return false;
}

/***********************************************************************
Function:    // createTable()
Description: // 创建一个表的PEOPLE表格，里面有NAME, ID, PHONE, CITY, EMAIL
Input:       // 无
Output:      // 无
Return:      // 无
Author:      // 马文水
Time:        // 2021-09-11
************************************************************************/
bool Widget::createTable()
{
    QSqlQuery q(db);
    QString Str = "CREATE TABLE PEOPLE "
                  "("
                  "Name char(10) NOT NULL,"
                  "ID char(50) NOT NULL,"
                  "Phone char(50) NULL,"
                  "City char(50) NULL,"
                  "Email char(5) NULL"
                  ");";

    if(q.exec(Str))
    {
        qDebug() << "Create Table success" << endl;
        return true;
    }
    return false;
}

/***********************************************************************
Function:    // insertData()
Description: // 在新建的表格PEOPLE中插入数据
Input:       // 无
Output:      // 无
Return:      // 无
Author:      // 马文水
Time:        // 2021-09-11
************************************************************************/
void Widget::insertData()
{
    QSqlQuery q(db);
    const char* format = "insert into PEOPLE values('name%d', '123%d', '101010%d', 'City%02d', '1111%d@email')";
    QString str = "";
    for(int i = 0; i < 10; i++)
    {
        str = "";
        str.sprintf(format, i, i, i, i, i);
        if(q.exec(str))
        {
            qDebug() << "insert ok" << endl;
        }
        else
        {
            qDebug() << "insert fail" << endl;
        }
    }
}

/***********************************************************************
Function:    // queryTable()
Description: // 访问数据库中某张表
Input:       // 无
Output:      // 无
Return:      // 无
Author:      // 马文水
Time:        // 2021-09-11
************************************************************************/
void Widget::queryTable()
{
    QSqlQuery q(db);
       if(q.exec("Select Name From PEOPLE"))    //Select 要查询的字段名 From 表名
       {
           qDebug() << "select ok";
       }
       else
       {
           qDebug() << "select fail";
       }
       while(q.next())
       {
           qDebug() << q.value(0).toString();
       }
}

/***********************************************************************
Function:    // showDatabase()
Description: // 使用Tableview显示数据库的信息
Input:       // 无
Output:      // 无
Return:      // 无
Author:      // 马文水
Time:        // 2021-09-11
************************************************************************/
void Widget::showDatabase()
{
    QSqlRelationalTableModel* model = new QSqlRelationalTableModel(this, db);
    model->setTable("PEOPLE");
    model->select();
    QTableView* view = new QTableView(this);
    view->resize(600, 200);
    view->setModel(model);
    model->setEditStrategy(QSqlTableModel::OnFieldChange);
}

/***********************************************************************
Function:    // callMyModel()
Description: // 调用MyModel显示表中一部分字段
Input:       // 无
Output:      // 无
Return:      // 无
Author:      // 马文水
Time:        // 2021-09-11
************************************************************************/
void Widget::callMyModel()
{
    MyModel* model = new MyModel(db, this);
    model->setTable("PEOPLE");
    model->selectSome();

    //修改第0列的表头 这句话要放在执行完查询语句之后，这里是放在selectsome之后
    model->setHeaderData(0, Qt::Horizontal, tr("NewHeader"));

    QTableView* view = new QTableView(this);

    view->resize(600, 200);
    view->setModel(model);

    model->setFilter("Phone");//存在某个字段的记录

}

Widget::~Widget()
{
    delete ui;
}
```

### main.cpp

```c++
#include "widget.h"

#include <QApplication>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    Widget w;
    w.show();
    return a.exec();
}
```

