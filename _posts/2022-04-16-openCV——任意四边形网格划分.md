# [opencv——任意四边形网格划分](https://blog.csdn.net/Sun_tian/article/details/104197446)

## 1 算法介绍

**算法功能：用于对任意四边形进行网格化处理**
**输入：四边形的四个角点（类型为cv::Point），网格行数，网格列数**
**输出：网格交叉点点集或网格中心点点集（类型为std::vector< cv::Point > ）**

效果如下：
		vector gridret=Gridding(TEST_P0,TEST_P1,TEST_P2,TEST_P3,3,5,1);

![测试图](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111015585.png)

vector gridret=Gridding(TEST_P0,TEST_P1,TEST_P2,TEST_P3,3,5,2);

![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111015150.png)

vector gridret=Gridding(TEST_P0,TEST_P1,TEST_P2,TEST_P3,3,5,1,1,1,1,1);

![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111015285.png)

## 2 源码

### 2.1 命名空间声明和函数声明

```c++
using namespace std; 
using namespace cv;

//网格化函数
// P0到P3为任意四边形角点，顺时针或逆时针排布均可 
// rows为四边形内想要计算的网格行数，cols为列数。
// mode为返回参数格式：mode=1 时函数返回网格角点，mode=2 时函数返回网格中心点
// 最后四个参数为网格向外扩展参数，缺省值全为0，表示计算的网格全部位于四边形内部
vector<Point> Gridding(Point P0,Point P1,Point P2,Point P3,\
                                int rows,int cols,int mode,\
         int upex=0,int downex=0,int leftex=0,int rightex=0);  
```



### 2.2 函数实现

```c++
//四边形网格化
vector<Point> Gridding(Point P0,Point P1,Point P2,Point P3,\
                       int rows,int cols,int mode,\
                       int upex,int downex,int leftex,int rightex)
{
    vector<Point> pointvector01; //在P0和P1方向上创建等距点
    vector<Point> pointvector12; //在P1和P2方向上创建等距点
    vector<Point> pointvector32; //在P3和P2方向上创建等距点
    vector<Point> pointvector03; //在P0和P3方向上创建等距点
    vector<Point> pointvector; //
    for(int j=0-leftex;j<cols+1+rightex;j++){
        Point point;
        point.x = P0.x+(P1.x-P0.x)*j/cols;
        point.y = P0.y+(P1.y-P0.y)*j/cols;
        pointvector01.push_back(point);
    }
    for(int j=0-upex;j<rows+1+downex;j++){
        Point point;
        point.x = P1.x+(P2.x-P1.x)*j/rows;
        point.y = P1.y+(P2.y-P1.y)*j/rows;
        pointvector12.push_back(point);}
    for(int j=0-leftex;j<cols+1+rightex;j++){
        Point point;
        point.x = P3.x+(P2.x-P3.x)*j/cols;
        point.y = P3.y+(P2.y-P3.y)*j/cols;
        pointvector32.push_back(point);
    }
    for(int j=0-upex;j<rows+1+downex;j++){
        Point point;
        point.x = P0.x+(P3.x-P0.x)*j/rows;
        point.y = P0.y+(P3.y-P0.y)*j/rows;
        pointvector03.push_back(point);
    }
    for(int i=0;i<rows+1+downex+upex;i++) //依次求得交叉点
    {
        for(int j=0;j<cols+1+rightex+leftex;j++)
        {
            Point point=CrossPoint(pointvector01.at(j),pointvector32.at(j),\
pointvector03.at(i),pointvector12.at(i));
            pointvector.push_back(point);
        }
    }

    return pointvector;
}

//计算两直线交点
Point CrossPoint(Point P1, Point P2, Point P3, Point P4)
{
    Point pt;
    double x1=P1.x,y1=P1.y;
    double x2=P2.x,y2=P2.y;
    double x3=P3.x,y3=P3.y;
    double x4=P4.x,y4=P4.y;
    double D = (x1-x2)*(y3-y4)-(y1-y2)*(x3-x4);
    if (D == 0){
        pt.x=0;
        pt.y=0;
    }
    else{
        pt.x = ((x1*y2-y1*x2)*(x3-x4)-(x1-x2)*(x3*y4-y3*x4))/D;
        pt.y = ((x1*y2-y1*x2)*(y3-y4)-(y1-y2)*(x3*y4-y3*x4))/D;
    }
    return pt;
}
```



