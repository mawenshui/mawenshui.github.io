# Qt通过经纬度计算两点间的距离

[原文链接](https://my.oschina.net/110NotFound/blog/3042418)

项目里有一个需求是计算两台机器之间的距离，有了这两台机器的经纬度，距离就很好计算了。

有一个 ***球面余弦定律*** 可以用来可以计算球面两点距离，但是根据这个反余弦函数公式会有较大的舍入误差，所以最好选用 ***半正矢公式*** 是最好的，航海上运用广泛的也是半正矢公式。

根据  ***半正矢函数（半正矢公式）*** 的定义和两角和的余弦函数展开式求出使用半正矢函数计算大圆距离的公式。

首先 半正矢公式 的权威介绍可以参考维基百科： [https://en.wikipedia.org/wiki/Haversine_formula](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FHaversine_formula)

关于求大圆距离也就是从球面的一点A出发到达球面上另一点B，所经过的最短路径的长度，可以参考： [https://en.wikipedia.org/wiki/Great-circle_distance](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FGreat-circle_distance)

知道了数学理论基础，接下来就是要把计算方法翻译成计算机的计算逻辑。

如图所示 d 就是我们要求的距离，

![image-202204221544348](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204221544348.png)

代码如下：

```c++
/**
* 根据经纬度计算两点间的距离距离
* @param long1 经度1
* @param lat1  纬度1
* @param long2 经度2
* @param lat2  纬度2
* @return double 距离，单位是 米
*/
double findDistance(double long1, double lat1, double long2, double lat2) 
{
    // 地球半径的平均值，
    double R = 6371393;
    lat1 = lat1 * M_PI / 180.0;
    lat2 = lat2 * M_PI / 180.0;
    double a = lat1 - lat2;
    double b = (long1 - long2) * M_PI / 180.0;
    double sa2 = sin(a / 2.0);
    double sb2 = sin(b / 2.0);
    return 2 * R * asin( sqrt(sa2 * sa2 + cos(lat1) * cos(lat2) * sb2 * sb2));
}


测试数据记录：

深圳桃园地铁站  22.5323483070,113.9248001575
深圳大新地铁站   22.5322492086,113.9152836800
实际距离：985
程序运行结果：977

深圳市  22.5277797987,114.0682983398
惠州市  23.1024705550,114.4198608398
实际距离：73368
程序运行结果：73366

深圳市 22.544300,114.065379
北京市 39.908057,116.409885
实际距离：1943.381
程序运行结果：1943.501
```

 **这样得到的数据在数学的严谨上来说不算是最准确的，更准确的方法，应该是考虑地球离心率的 Vincenty 公式或其他有关地理距离的论文所给出方法来得到半径值，因为地球并不是真正完全的球形的，所以使用地球的平均半径只能算是一个折中值。**