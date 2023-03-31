# OpenCV学习笔记②——图像缩放

## 1.打开图片文件

```c++
QString fileName = QFileDialog::getOpenFileName(this, tr("请选择一张图片"), ".", tr("Image Files (*.png *.jpg *.bmp)"));
m_InputImage = cv::imread(fileName.toLocal8Bit() .data());
imshow("InputImage", m_InputImage);
```

## 2.按比例缩放图片

```c++
if (m_InputImage.cols > 640)
{
	cv::resize(m_InputImage, m_ResizeImage, Size(640, 640 * m_InputImage.rows / m_InputImage.cols));
}
imshow("m_ResizeImage", m_ResizeImage);
```

