# Qt连接mysql数据库、数据库开启远程连接，实现QQ登录、注册、修改密码功能（已实现），后续继续更新中...

> 视频示例：如下[直通车](https://www.bilibili.com/video/BV1ZK4y1T74T?share_source=copy_web)



<iframe id="WZjuOXVW-1616734656465" src="https://player.bilibili.com/player.html?aid=887349880" allowfullscreen="true" data-mediaembed="bilibili" style="box-sizing: border-box; outline: 0px; user-select: auto !important; margin: 0px; padding: 0px; font-weight: normal; overflow-wrap: break-word; display: block; width: 730px; height: 365px;"></iframe>

Qt连接数据库



> 个人博客[直达](https://jcs-blog.gitee.io/posts/ee69.html)

# 一、安装Mysql数据库软件

### 1.下载安装连接：[点击连接](https://dev.mysql.com/downloads/windows/installer/5.7.html)

![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/20210326124929591.png)

### 2.解压，打开如下

![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/2021032612494191.png)

### 3.设置环境变量

![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/20210326124951758.png)

### 4.修改添加环境变量

![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/20210326125018870.png)

### 5.新建配置文件my.ini（新建一个空白文件）

![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/2021032612521594.png)

```ini
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=D:\\Mysql\\mysql-5.7.33-win32
# 设置mysql数据库的数据的存放目录
#datadir=D:\\Mysql\\mysql-5.7.33-win32\\Data 
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。
max_connect_errors=10
# 服务端使用的字符集默认为utf8mb4
character-set-server=utf8mb4
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
#mysql_native_password
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8mb4
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8mb4
12345678910111213141516171819202122232425
```

详细安装过程请参考：[压缩包安装mysql](https://blog.csdn.net/qq_42244038/article/details/112441200)

### 6.检测本机QT是否有mysql驱动

没有mysql驱动时出现如下错误：

```c
QSqlDatabase: QMYSQL driver not loaded
1
```

检测程序：

```c
#include "widget.h"
#include "ui_widget.h"
#include <QSqlDatabase>
#include <QDebug>
#include <QMessageBox>
#include <QSqlQuery>
#include <QSqlError>

Widget::Widget(QWidget *parent)
    : QWidget(parent)
    , ui(new Ui::Widget)
{
    ui->setupUi(this);
    QSqlDatabase db = QSqlDatabase::addDatabase("QMYSQL");//添加驱动
    db.setHostName("localhost");
    db.setUserName("root");
    db.setPassword("123456");
    db.setDatabaseName("sys");
    if(!db.open()){
        QMessageBox::warning(this,"错误",db.lastError().text());
        return;
    }
    QSqlQuery query;
    qDebug()<<QSqlDatabase::drivers();
}

Widget::~Widget()
{
    delete ui;
}

12345678910111213141516171819202122232425262728293031
```

出现报错，就按照如下教程手动编译mysql驱动：[qt连接mysql报错：QSqlDatabase: QMYSQL driver not loaded QSqlDatabase: available drivers: QSQLITE QODBC…](https://blog.csdn.net/weixin_46288017/article/details/111592772)

按照教程修改后将会出现如下效果：
![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/20210326125112456.png)

进而将驱动以及库文件放到相应的地方！

> 小插曲：
>
> 手动编译仍未出现上面那个文件夹有可能就是qt出现了问题，建议重装！我的就是编译以后没有出现那个文件夹，百度了两天仍未解决，于是乎。。。我就重装了，然后就解决了！
>
> 注意：mysql的版本要与qt对应，这里用的是32位的
> ![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/20210326125251561.png)

**至此，配置成功！**

# 二、开启连接远程数据库

### 法一：修改表格

- 在bin目录下打开数据库
  ![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/20210326125305835.png)

**输入：**

```mysql
MySQL>update user set host = '%' where
user = 'root';
12
MySQL>select host, user from user;
1
MySQL>GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY
'mypassword' WITH GRANT OPTION;
//解释：开启全部权限，'root'自定义的用户名，可以是别的；'%'是代表对任意的ip地址开放；'mypassword' 是自定义的密码。
123
MySQL>flush privileges;
1
MySQL>EXIT
1
```

### **法二：直接修改（我采用这个）**

```mysql
mysql -u root -p
1
GRANT ALL PRIVILEGES ON *.* TO ‘root'@'%' IDENTIFIED BY
‘MyPassword' WITH GRANT OPTION;
//和上面一样，当然这里可以把%替换为你指定的ip也可以不指定
123
MySQL>flush privileges;//开启权限
1
```

**连接测试：**

![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/20210326125318516.png)
![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/20210326125327182.png)

**连接成功，无报错！**

**远程连接已开启，目前可以实现本地连接和远程连接！**

> 注意：在进行连接数据库时请在.pro中添加：`QT += core gui sql`