# OpenCV 图像切分与图像合并

[原文链接](https://blog.csdn.net/ETNthrough/article/details/104112802?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_antiscanv2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_antiscanv2&utm_relevant_index=2)

### 将一张图片切分成多个小图片并将小图片合并为原图

**前言**

最近用到一个功能，需要将一张原图切分成多个小图像，然后对小图像进行处理，处理之后再将其整合成一张大图像。达到对原图进行处理的目的，这样做的好处是将一个大任务划分为多个小任务，分别进行处理以节约时间（当然需要多线程进行协助，效果才会更明显）。

下面，就以2个模块进行介绍，分别为

①图像切分。

②图像合并。

```
/*
图像切分（我是按列进行切分的，按照行也是同样的原理。亦或是按块）
核心代码如下:
*/
//用于存储切分后的小图像
vector<Mat> imgs;
//src:待切分原图像 splitCols:切分的每个小图像列数
void imgSplit(Mat src,int splitCols)
{
	//设置分割后图像存储路径
	string outpath = ".\\split\\";

	int col = src.cols, row = src.rows;
	//切分后图像数量
	int sum = 0;
	//被整除
	if ((col%num == 0))
	{
		sum = col / num;
		//迭代器ceil_img存储子图像
		//vector<Mat> ceil_img;
		//迭代器name存储子图像的名字，从0到m*n-1
		vector<int> name;
		for (int i = 0; i < sum; i++)
		{
			name.push_back(i);
		}

		Mat image_cut, roi_img, tim_img;
		//存储完整图像
		for (int i = 0; i < sum; i++)
		{
			Rect rect(i*num, 0, num, row);
			image_cut = Mat(src, rect);
			roi_img = image_cut.clone();
			imgs.push_back(roi_img);
		}
		//写入到指定文件夹
		for (int i = 0; i < sum; i++)
		{
			imwrite(outpath + to_string(long long((name[i]))) + ".jpg", imgs[i]);
		}
	}
	else //不能整除
	{
		sum = col / num + 1;

		//迭代器ceil_img存储子图像
		//vector<Mat> ceil_img;
		//迭代器name存储子图像的名字，从0到m*n-1
		vector<int> name;
		for (int i = 0; i < sum; i++)
		{
			name.push_back(i);
		}

		Mat image_cut, roi_img, tim_img;
		//存储完整图像
		for (int i = 0; i < sum - 1; i++)
		{
			Rect rect(i*num, 0, num, row);
			image_cut = Mat(src, rect);
			roi_img = image_cut.clone();
			imgs.push_back(roi_img);
		}

		//留余图像(因为有时候原图像总列数不能被整除，但又不能有损原图）
		Rect rect((sum - 1)*num, 0, col%num, row);
		image_cut = Mat(src, rect);
		roi_img = image_cut.clone();
		imgs.push_back(roi_img);

		//写入到指定文件夹
		for (int i = 0; i < sum; i++)
		{
			imwrite(outpath + to_string(long long((name[i]))) + ".jpg", imgs[i]);
		}
	}
}
```

```
/*
图像合并
只要学会合并两幅图像，那么多幅图像合并就不在话下了
*/
//按列合并两幅图像
Mat mergeCols(Mat src1, Mat src2)
{
	int totalCols = src1.cols + src2.cols;
	Mat dst(src1.rows, totalCols, src1.type());
	Mat submat = dst.colRange(0, src1.cols);
	src1.copyTo(submat);
	submat = dst.colRange(src1.cols, totalCols);
	src2.copyTo(submat);
	return dst;
}

//多幅图像合并
void imgMerge()
{
	int imgSum = imgs.size();
	Mat dst = imgs[0];
	for (int i = 1; i < imgSum; i++)
	{
		dst = mergeCols(dst, imgs[i]);
	}
}
```

​	执行下面代码可以验证（当然，你要根据自己的需求进行个别修改，比如切分后图像保存路径这些细节）

```
void split_mergeRun()
{
	Mat src = imread("img.jpg");
	imgSplit(src, 100);
	imgMerge();
}
```

***友情提示***

万变不离其宗，上述我分了两个模块分别为图像切分、合并。你也可以结合其它资料或者自己对代码进行理解，达到自己的最终目的，如果以上描述有什么有待改进之处，希望能够留言指正。

