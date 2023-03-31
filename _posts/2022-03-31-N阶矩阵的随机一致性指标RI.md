# N阶矩阵的随机一致性指标RI

## 随机一致性指标求解

```c++
#include <iostream>
#include <cmath>
#include <ctime>
#include <cstdlib>
#include <iomanip>

using namespace std;

int getNameta(double a[], int n);
void createMatrix(double a[], int n);
int getMax(double b[], int n);
void Mifa(double a[], double b[], int n);

int main()
{
	int n;
	int m = 3000;
	int all = 0;
	int tem = 0;
	int ri = 0;
	cout << "please enter the size of the matrix:" << endl;
	cout << "n=";
	cin >> n;
	if (n > 2 && n <= 30) {
		for (int i = 0; i < m; i++)
		{
			double *a = new double[n*n];
			for (int j = 0; j < n*n; j++)
			{
				a[j] = 1;
			} 
			createMatrix(a, n);
			all += getNameta(a, n);
		}
		tem = all / m;

		ri = (tem - n * 100);
		ri = ri / (n - 1);

		int baiwei = ri / 100;
		int shiwei = (ri - 100 * baiwei) / 10;
		int gewei = ri - 100 * baiwei - 10 * shiwei;

		cout << "The RI of the" << n << "size matrix is" << baiwei << "." << shiwei << gewei << endl;
	}
	else if (n = 1)
	{
		cout << "The RI of the" << n << "size of matrix is" << 0 << endl;
	}
	else
		cout << "The size of n you input is wrong" << endl;
}


int getNameta(double a[], int n)//获取当前随机正互反矩阵
{
	int tem = 0;
	double *b = new double[n];
	for (int i = 0; i < n; i++)
	{
		b[i] = 1.0 / n;
	}
	Mifa(a, b, n);
	int max = getMax(b, n);
	while(tem != max)
	{
		tem = max;
		for (int d = 0; d < n; d++)
		{
			b[d] = b[d] * 100 / max;
		}
		Mifa(a, b, n);
		max = getMax(b, n);
	}
	int nameta;
	nameta = tem;
	return nameta;
}

void createMatrix(double a[], int n)//遍历随机正互反矩阵
{
	double sample[17] = { 1.0,2.0,3.0,4.0,5.0,6.0,7.0,8.0,9.0,1.0 / 2.0,1.0 / 3.0,1.0 / 4.0,1.0 / 5.0,1.0 / 6.0,1.0 / 7.0,1.0 / 8.0,1.0 / 9.0 };

	for (int i = 0; i < n; i++)
	{
		for (int j = i; j < n; j++)
		{
			int t;
			t = rand() % 17;
			if (j == i) 
			{
				a[n*i + j] = 1.0;
			}
			else 
			{
				a[n*i + j] = sample[t];
				a[n*j + i] = 1.0 / sample[t];
			}
		}
	}
}

int getMax(double b[], int n)//获取b矩阵中的最大值
{
	int tem = 0;
	double *b = new double[n];
	for (int i = 0; i < n; i++)
	{
		b[i] = 1.0 / n;
	}
	Mifa(a, b, n);
	int max = getMax(b, n);
	while (tem != max)
	{
		tem = max;
		for (int d = 0; d < n; d++)
		{
			b[d] = b[d] * 100 / max;
		}
		Mifa(a, b, n);
		max = getMax(b, n);
	}
	int nameta;
	nameta = tem;
	return nameta;
}

void Mifa(double a[], double b[], int n)//幂法过程
{
	double *c = new double[n];
	for (int r = 0; r < n; r++)
	{
		c[r] = 0.0;
	}
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < n; j++)
		{
			c[i] = a[n*i + j] * b[j];
		}
	}
	for (int k = 0; k < n; k++)
	{
		b[k] = c[k];
	}
}

int getMax(double b[],int n)
{
	int a = 0;
	for (int i = 0; i < n; i++)
	{
		if(100*b[i]>a)
		{
			a = 100 * b[i];
		}
	}
	int tem;
	tem = a;
	return tem;
}
```

![image-20210726160057617](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311418732.png)



![](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311414840.jpeg)



![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311414899.jpeg)

![image-20210729095114770](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311419026.png)

![image-20210729112752217](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311416900.png)

![image-20210729143609779](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311415381.png)

| 阶数 | 16      | 17      | 18      | 19      | ***20***      | 21      | 22     | 23      | 24      | 25      | 26      | 27      | 28      | 29      |  ***30***   |
| ---- | ------- | ------- | ------- | ------- | :------------ | ------- | ------ | ------- | ------- | ------- | ------- | ------- | ------- | ------- | :---------: |
| RI   | 1.60226 | 1.61082 | 1.61446 | 1.62808 | ***1.60941*** | 1.64159 | 1.6481 | 1.65872 | 1.66552 | 1.65854 | 1.66113 | 1.66682 | 1.67931 | 1.67724 | ***1.673*** |



| 阶数 | 1    | 2    | 3        | 4        | 5       | 6       | 7       | 8       | 9       | 10      | 11      | 12      | 13      | 14      | 15      |
| ---- | ---- | ---- | -------- | -------- | ------- | :------ | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- |
| RI   | 0    | 0    | 0.582638 | 0.940667 | 1.16921 | 1.31649 | 1.35919 | 1.42081 | 1.49027 | 1.49906 | 1.53246 | 1.56928 | 1.57698 | 1.59887 | 1.60588 |



```c++
/***********************************************************************
Function:   //Maxlameda( QVector<QVector<double>> array)
Description://计算最大特征根，并返回特征向量
Input:      //初始判断矩阵
Output:     //返回特征向量QList
Return:     //QList<double> list;
Author:     //温晋杰
Time:       //%{CurrentDate:2021-02-22}
************************************************************************/
QList<double> AnalyticHierarchyProcess::Maxlameda( QVector<QVector<double>> array)
{
    QList<double> l_listWeight;//临时存放计算结果，并返回
    l_listWeight.clear();
    //array按列存储矩阵
    int length = array.length();//数组的长度，也就是矩阵的列数
    //定义权重向量数组和
    double w[length], arrayTemp[length][length];
    //定义中间的和
    double l_dbSum;
    //列向量归一化
    for(int j = 0; j<length; j++)//列循环
    {
        l_dbSum = 0;
        for(int i = 0; i<length; i++)//行循环
        {
             l_dbSum += array.at(i).at(j);
        }

        //按列求和，按公式计算
        for(int i = 0; i<length; i++)//行循环
        {
             arrayTemp[i][j] = array.at(i).at(j)/l_dbSum;
        }
    }
    //按行求和，并归一化
    l_dbSum = 0;
    for(int i = 0; i<length; i++)//行循环
    {
        for(int j = 0; j<length; j++)//列循环
        {
            w[i] += arrayTemp[i][j];
        }
        l_dbSum += w[i];
    }
    for(int j = 0; j<length; j++)//列循环
    {
        w[j] = w[j]/l_dbSum;//特征向量
        l_listWeight.push_back(w[j]);
    }
    //计算特征向量和原始判断矩阵的乘积
    double Matrix[length];
    for(int i = 0; i<length; i++)//行循环
    {
        Matrix[i] = 0;
        for(int j = 0; j<length; j++)//列循环
        {
           Matrix[i] += w[j]*(array.at(i).at(j));
        }
    }
    //计算最大特征根
    m_lameda = 0;
    for(int i = 0; i<length; i++)//列循环
    {
       m_lameda +=  (Matrix[i]/w[i])/length;
    }
    return l_listWeight;
}

/***********************************************************************
Function:   //checkCR(double lameda)
Description://一致性检验
Input:      //最大特征根
Output:     //是否通过检验的布尔值
Return:     //false//true
Author:     //温晋杰
Time:       //%{CurrentDate:2021-02-22}
************************************************************************/
bool  AnalyticHierarchyProcess::checkCR(double lameda)
{
    double l_dbCR = 0, l_dbCI = 0;

    //矩阵的列数length
    int length = m_array.length();
    //按公式计算
    l_dbCI =(lameda - m_array.length())/(m_array.length()-1);
    l_dbCR = l_dbCI/(g_RI[length-1]);

    if (l_dbCR >= 0.1)
    {
        QMessageBox::information(NULL, "提示", "未通过一致性检验，请重新输入！", QMessageBox::Yes | QMessageBox::No, QMessageBox::Yes);
        return false;
    }
    else{
        return true;
    }
}
```

按顺序前六个验证无误

![image-20210802163244652](C:/Users/mws/Desktop/Qt5.14.2/002Qt%E5%85%A5%E9%97%A8%E7%AC%94%E8%AE%B0/N%E9%98%B6%E7%9F%A9%E9%98%B5%E7%9A%84%E9%9A%8F%E6%9C%BA%E4%B8%80%E8%87%B4%E6%80%A7%E6%8C%87%E6%A0%87RI.assets/image-20210802163244652.png)

任意取六组数据进行验证

![image-20210802173437996](C:/Users/mws/Desktop/Qt5.14.2/002Qt%E5%85%A5%E9%97%A8%E7%AC%94%E8%AE%B0/N%E9%98%B6%E7%9F%A9%E9%98%B5%E7%9A%84%E9%9A%8F%E6%9C%BA%E4%B8%80%E8%87%B4%E6%80%A7%E6%8C%87%E6%A0%87RI.assets/image-20210802173437996.png)

2021.08.02	14:57 PM 备份代码

```c++
/***********************************************************************
Function:    // generatingCI(int l_iRank)  l_iRank矩阵阶数
Description: // 计算并生成CI
Input:       // 无
Output:      // 无
Return:      // 无
Author:      // 马文水
Time:        // 2021-07-27
************************************************************************/
void MainWindow::generatingCI(int l_iRank)
{
    double l_dbRI = 0.0;
    double CISum = 0.0;

    //定义中间的和
    double l_dbSum;
    //新建矩阵
    MatrixXd l_A;

    //重复步骤10000次
    for (int var = 0; var < 5; var++)
    {
        l_A = generatingMatrix(l_iRank);//接收随机矩阵
        cout<<"A="<<l_A<<endl;

        //定义权重向量、数组和
        double l_dbC[l_iRank];
        double w[l_iRank];
        double arrayTemp[l_iRank][l_iRank];

        //定义CI
        double CI = 0.0;

        //列向量归一化
        for(int j = 0; j<l_iRank; j++)//列循环
        {
            //按列求和，按公式计算
            l_dbSum = 0;
            for(int i = 0; i<l_iRank; i++)//行循环
            {
                l_dbSum += l_A(i,j);
            }

            //归一化
            for(int i = 0; i<l_iRank; i++)//行循环
            {
                arrayTemp[i][j] = l_A(i,j) / l_dbSum;
                cout<<"arrayTemp["<<i<<"]["<<j<<"]="<<arrayTemp[i][j]<<endl;
            }
        }

        //按行求和
        l_dbSum = 0;
        for(int i = 0; i<l_iRank; i++)//行循环
        {
            for(int j = 0; j<l_iRank; j++)//列循环
            {
                l_dbC[i] += arrayTemp[i][j];
            }
            cout<<"l_C["<<i<<"]="<<l_dbC[i]<<endl;
            l_dbSum += l_dbC[i];
        }
        cout<<"l_dbSum="<<l_dbSum<<endl;
        //并归一化
        for(int j = 0; j<l_iRank; j++)//列循环
        {
            w[j] = l_dbC[j] / l_dbSum;//特征向量
            cout<<"w["<<j<<"]="<<w[j]<<endl;
        }

        //计算特征向量和原始判断矩阵的乘积
        double Matrix[l_iRank];
        for(int i = 0; i<l_iRank; i++)//行循环
        {
            Matrix[i] = 0;
            for(int j = 0; j<l_iRank; j++)//列循环
            {
                Matrix[i] += w[j] * l_A(i,j);
            }
            cout<<"AW["<<i<<"]="<<Matrix[i]<<endl;
        }

        //计算最大特征根
        double l_dbLambdaMax = 0;
        for(int i = 0; i<l_iRank; i++)//列循环
        {
            l_dbLambdaMax +=  (Matrix[i] / w[i]) / l_iRank;
        }
        cout<<"l_dbLambdaMax="<<l_dbLambdaMax<<endl;

        //计算CI
        CI = (l_dbLambdaMax - l_iRank) / (l_iRank - 1);
        cout<<"CI="<<CI<<endl;
        CISum += CI;
    }

    //计算RI
    l_dbRI = CISum / SAMPLESIZE;
    cout<<"RI="<<l_dbRI<<endl;
}
```

```c++
        //新建矩阵
        MatrixXd l_A;
        l_A = generatingMatrix(l_iRank);//接收随机矩阵

        MatrixXd l_B;//A列归一化后得到的矩阵

//        l_B = l_A;
```

如果不初始化矩阵B，则会报错如下：

![image-20210802155221039](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311419201.png)

