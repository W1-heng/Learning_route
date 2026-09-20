**了解概念，知道有这些东西为主，暂时不用记接口**
# 图像的读取及显示
```python
import cv2

print(cv2.getVersionString())#打印OpenCV版本号

image=cv2.imread("图片url")#读取图片

print(image.shape)#打印图片的像素和通道数

cv2.imshow("窗口名",image)#生成一个窗口展示指定图片

cv2.waitkey(等待时间（默认是一直等待）)#程序会停滞，等待按下任意按键。

```


# 图像的彩色通道

在OpenCV中，一张图片相当于存储三张灰度图，他们存储在图像中的最后一个维度中，**且OpenCV对颜色的存储顺序是BGR**
刚好和RGB相反

![[Pasted image 20260908194159.png]]

==彩色图像显示逻辑：当我们查看图像时，计算机会依次取出蓝绿红的灰度图，然后分别给屏幕的蓝色绿色红色芯片，这样就可以显示彩色图案了==


```python
import cv2
image=cv2.imread("图片url")
cv2.imshow("blue",image[:,:,0])
cv2.imshow("green",image[:,:,1])
cv2.imshow("red",image[:,:,2])#会生成三个分别显示三个灰度图的窗口

gray=cv2.cvtColor(image,cv2.COLOR_BGR2GRAY)
cv2.imshow("gray"，gray)#三个灰度图拼接起来，灰度深度的不同反应了相机cmos感光采集光子数分布的不同，这个灰度图也是平时视觉训练常提到用到的灰度图

cv2.waitkey()
```


# 图片裁剪

```python
crop=image[10:170,40:200]#直接切片就行
```


# 简单绘制

```python
import cv2
import numpy as np
image=np.zeros([300,300,3],dtype=np.uint8)#OpenCV图像本质是numpy的一个数组，所以可以用np创建全黑画布
cv2.line(image,.......)#在image画布中画线
cv2.rectangle(image,.....)#在image中画矩形
cv2.circle(image,......)#在image中画圆
cv2.putText(image,....)#在image中放置文本
```


# 均值滤波处理
**作用：减少图像中的一些噪点，副作用是可能会减少一些图片的细节

```python
gauss=cv2.GaussianBlur(image,(5,5),0)#高斯滤波器
median=cv2.medianBlur(image,5)#中值滤波器
```

# 图像特征的提取

```python
import cv2
image=cv2.imread()
gray=cv2.cvtColor(image,cv2.COLOR_BGR2GRAY)

corners=cv2.goodFeaturesToTrack(....,提供特定算法)#提取特征

```


# 匹配算法

```python
import cv2
import numpy as np
image=imread(...)
gray=cv2.cvtColor(image,cv2.COLOR_BGR2GRAY)

template=gray[.....]#传入需要匹配的一个图形的确切的像素位置作为匹配模板

match=cv2.matchTemplate(gray,template,匹配算法)

```


# 梯度算法

**==图像中的梯度反映的是灰度图中图像的明暗变化程度，所以梯度算法常常用于计算边缘==**

```python
import cv2
gray=imread("图片url",cv2.IMREAD_GRAYSCALE)#直接读取灰度图的方法
laplacian=cv2.laplacian(gray,cv2.CV_64F)#拉普拉斯梯度算子
canny=cv2.Canny(gray,min,max)#canny边缘检测算法，指定梯度区间，如果梯度小于min则不是边缘，如果梯度大于min小于max，如果它和已知的边缘像素相连，那么它是边缘，否则不是，如果梯度大于max则它是边缘
```

![[Pasted image 20260908204907.png]]


# 阈值算法

它把灰度图分为黑与白，简单来说，**在阈值下为黑，阈值上为白**
，也叫**二值化**，它把无限连续的灰度图化成非黑即白的图



例子：
```python
ret,binary=cv2.threshold(gray,10,255,cv2.THRESH_BINARY)#binary是二值化后的算法
```


# 形态学算法（腐蚀和膨胀）

腐蚀：让白色区域变小变细
膨胀：让白色区域变大变粗
**算法需要基于二值化后的图像**











例子
```python
import cv2
import numpy as np

gray =cv2.imread(...,cv2.IMREAD_GRAYSCALE)
_,binary=cv2.threshold(gray,200,255,cv2.THRESH_BINARY_INV)#根据具体图片决定要不要反转黑白，重要的是目标对象必须是白色区域
kernel =np.ones((5,5),np.uint8)#内核
erosion=cv2.erode(binary,kernel)#腐蚀
dilation=cv2.dilate(binary,kernel)#膨胀
```
![[Pasted image 20260908211917.png]]

                     腐蚀                    膨胀



# OpenCV调用摄像头

与获取静态图片不同，需要获取摄像头的每一帧的图片并展示，这需要循环

```python
import cv2
capture=cv2.VideoCapture(输入摄像头的序号)#获取摄像头的指针
while true:
	ret,image=capture.read()
	cv2.imshow("camera",image)
	key =cv2.waitkey(1)#等待一秒
	if key !=-1:
	break
	
	
capture.release()
```