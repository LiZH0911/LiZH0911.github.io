# 数字图像处理

相关链接：

- 冈萨雷斯《数字图像处理》
- [CV 算法工程师成长之路-数字图像处理](https://harleyszhang.github.io/cv_note/6-computer_vision/%E6%95%B0%E5%AD%97%E5%9B%BE%E5%83%8F%E5%A4%84%E7%90%86/0-%E6%95%B0%E5%AD%97%E5%9B%BE%E5%83%8F%E5%A4%84%E7%90%86%E7%AC%94%E8%AE%B0.html)
- [华为云《从零到壹•Python图像处理及识别》电子书](https://res-static.hc-cdn.cn/cloudbu-site/china/zh-cn/CloudBook/PythonCloudShare0712.pdf)


## 一、数字图像处理基础知识

### 1.1 **什么是数字图像处理**

**数字图像处理（Digital Image Processing）**：又称为计算机图像处理（Computer Image Processing），指借助计算机处理数字图像的过程。

![图像处理相关的应用方法.png](images%2F%E5%9B%BE%E5%83%8F%E5%A4%84%E7%90%86%E7%9B%B8%E5%85%B3%E7%9A%84%E5%BA%94%E7%94%A8%E6%96%B9%E6%B3%95.png)

* 数字图像处理作为一门学科大约形成于 20 世纪 60 年代初期。早期的图像处理的目的是改善图像的质量，常用的处理方法包括图像增强、复原、编码、压缩等。
* 随着图像处理技术的深入发展，从 70 年代中期开始，计算机技术和人工智能、思维科学研究迅速发展，数字图像处理向更高、更深层次发展。人们已开始研究如何用计算机系统解释图像，实现类似人类视觉系统理解外部世界，这被称为图像理解或计算机视觉。

### 1.2 **图像处理基础**

**图像**：一个二维函数 $f(x,y)$，其中 $x$ 和 $y$ 是空间（平面）坐标，任意一对空间坐标 $(x,y)$ 处的幅度值 $f$ 称为图像在该坐标点的强度或灰度。

**数字图像**：$x,y$ 和灰度值 $f$ 都是有限离散量的图像。

**像素**：数字图像由有限数量的元素组成，每个元素都有一定的位置和数值，这些元素称为像素。

数字图像分类：二值图像、灰度图像和彩色图像。

- 二值图像又称为黑白图像，图像中任何一个点要么为白色（像素为 255），要么为黑色（像素为 0）。
- 灰度图像是指每个像素的信息由一个量化的灰度级来描述的图像，没有彩色信息。
- 彩色图像是 RGB 图像，RGB 表示红、绿、蓝三原色。

## 二、OpenCV 图像读取、显示、像素访问、保存

OpenCV 是一个轻量级高效的跨平台计算机视觉库，实现了图像处理和计算机视觉方面的多种通用算法。

Python 安装 OpenCV：`pip install opencv-python`

### 2.1 **常见数据类型**

- 点 Point

=== "Python"

    ```python
    points_list = [(160, 160), (136, 160)]
    ```

=== "C++"

    ```C++
    Point p; 
    p.x=1;
    p.y=2;
    Point p=Point(1, 2);
    ```

- 颜色 Scalar

=== "Python"

    ```python
    (0, 0, 255)
    ```

=== "C++"

    ```C++
    Scalar(b, g, r);·
    ```

- 尺寸 Size

=== "Python"

    ```python
    width, height = img.shape
    ```

=== "C++"

    ```C++
    Size(5, 5);
    Size_(_Tp _width, _Tp _height);
    ```

- 矩形 Rect

=== "Python"

    ```python
    cv2.rectangle(img, (20,20), (150,250), (255,0,0), 2)
    ```

=== "C++"

    ```C++
    Rect rect = rect1 & rect2; #求两矩形交集
    Rect rect = rect1 | rect2; #求两矩形并集
    Rect rectShift = rect + point; #矩形平移
    Rect rect = rect1 + size; #矩形缩放
    ```

- 矩阵 Mat

=== "Python"

    ```python
    np.zeros((256,256,3), np.uint8)
    ```

=== "C++"

    ```C++
    Mat M(3,2, CV_8UC3, Scalar(0,0,255));
    ```

### 2.2 **图像读取**

函数：`retval = cv2.imread(filename[, flags])`

### 2.3 **图像显示**

函数：`cv2.imshow(winname, mat)`

在显示图像过程中，通常还会调用操作窗口的函数：

- `retval = cv2.waitKey([, delay]))`：键盘绑定函数，共一个参数 `delay`，表示等待的毫秒数，看键盘是否有输入，返回值为 ASCII 值。
- `cv2.destroyAllWindows()`：删除所有建立的窗口
- `cv2.destroyWindow(winname)`，并在括号内输入要删除的窗口名。

```python
import cv2

#读取图片
img = cv2.imread("Lena.png")

#显示图像
cv2.imshow("Demo", img)

#等待显示
cv2.waitKey(0) # 等待键盘单击
cv2.destroyAllWindows()
```

在对比实验中，我们通常需要显示多张图片，此时可以调用 NumPy 和 Matplotlib 库辅助完成

### 2.4 **访问像素**

OpenCV 中读取的图像相当于二维（灰度）或三维（彩色）的 NumPy 矩阵，可以直接通过位置索引访问像素值。

```python
import cv2

#读取图片
img = cv2.imread("flower.png", cv2.IMREAD_UNCHANGED)
test = img[88,142]
print(test)
img[88,142] = [255, 255, 255]
print(test)

#分别获取 BGR 通道像素
blue = img[88,142,0]
print(blue)
green = img[88,142,1]
print(green)
red = img[88,142,2]
print(red)

#该区域设置为白色
img[100:200, 150:250] = [255,255,255]
```

### 2.5 **创建图像**

由于在 OpenCV 中没有 CreateImage 函数，如果需要创建图像，则需要使用 Numpy 库函数实现。

函数：`emptyImage = np.zeros(img.shape, np.uint8)`

### 2.5 **复制图像**

函数：`emptyImage2 = img.copy()`

### 2.6 **保存图像**

函数：`retval = cv2.imwrite(filename, img[, params])`

## 三、OpenCV 绘制几何图形

### 3.1 **绘制直线**

函数：`cv2.line(img, pt1, pt2, color, thickness)`

```python
import cv2
import numpy as np

# 创建一张黑色背景图 (高, 宽, 通道)
img = np.zeros((500, 500, 3), dtype=np.uint8)

# 绘制一条红色直线：从 (50, 50) 到 (450, 450)
cv2.line(img, (50, 50), (450, 450), (0, 0, 255), thickness=3)

# 显示
cv2.imshow("Line", img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### 3.2 **绘制矩形**

函数：`cv2.rectangle(img, pt1, pt2, color, thickness)`

```python
import cv2
import numpy as np

# 创建黑色背景图
img = np.zeros((500, 500, 3), dtype=np.uint8)

# 绘制绿色矩形边框：左上 (100, 100) 到右下 (400, 400)
cv2.rectangle(img, (100, 100), (400, 400), (0, 255, 0), thickness=3)

cv2.imshow("Rectangle", img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### 3.3 **绘制圆形**

函数：`cv2.circle(img, center, radius, color, thickness)`

```python
import cv2
import numpy as np

# 创建黑色背景图
img = np.zeros((500, 500, 3), dtype=np.uint8)

# 绘制绿色圆环：圆心 (250, 250)，半径 100
cv2.circle(img, (250, 250), 100, (0, 255, 0), thickness=3)

cv2.imshow("Circle", img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### 3.4 **绘制椭圆**

函数：`cv2.ellipse(img, center, axes, angle, startAngle, endAngle, color, thickness)`

```python
import cv2
import numpy as np

# 创建黑色背景图
img = np.zeros((500, 500, 3), dtype=np.uint8)

# 完整椭圆：中心 (250, 250)，半轴 (150, 80)，不旋转
cv2.ellipse(img, (250, 250), (150, 80),
            0, 0, 360, (0, 255, 0), thickness=3)

cv2.imshow("Ellipse", img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### 3.5 **绘制多边形**

函数：`cv2.polylines(img, pts, isClosed, color, thickness)`

```python
import cv2
import numpy as np

# 创建黑色背景图
img = np.zeros((500, 500, 3), dtype=np.uint8)

# 定义三角形顶点，必须是 int32 类型
pts = np.array([[250, 100], [100, 400], [400, 400]], dtype=np.int32)

# 绘制闭合多边形
cv2.polylines(img, [pts], isClosed=True,
              color=(0, 255, 0), thickness=3)

cv2.imshow("Triangle", img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### 3.6 **绘制文字**

函数：`cv2.putText(img, text, org, fontFace, fontScale, color, thickness)`

## 四、图像算术与逻辑运算

### 4.1 **图像加法**

图像加法运算主要有两种方法：

1. Numpy：`+` 或 `np.add()`，超过 255 的值取模 256，会发生溢出回绕
2. OpenCV（推荐）：`cv2.add()`，饱和截断

```python
import cv2
import numpy as np

# 创建两张测试图
img1 = np.full((300, 300, 3), 200, dtype=np.uint8)  # 值 200
img2 = np.full((300, 300, 3), 100, dtype=np.uint8)  # 值 100

# 饱和加法：200 + 100 = 300 → 截断为 255
result = cv2.add(img1, img2)
print(result[0, 0])  # [255 255 255]

cv2.imshow("Img1", img1)
cv2.imshow("Img2", img2)
cv2.imshow("cv2.add", result)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### 4.2 **图像减法**

1. Numpy：`-` 或 `np.subtract()`，小于 0 的值取模 256，会发生溢出回绕
2. OpenCV（推荐）：`cv2.subtract()`，饱和截断

```python
import cv2
import numpy as np

# 创建两张测试图
img1 = np.full((300, 300, 3), 100, dtype=np.uint8)  # 值 100
img2 = np.full((300, 300, 3), 200, dtype=np.uint8)  # 值 200

# 饱和减法：100 - 200 = -100 → 截断为 0
result = cv2.subtract(img1, img2)
print(result[0, 0])  # [0 0 0]

cv2.imshow("Img1", img1)
cv2.imshow("Img2", img2)
cv2.imshow("cv2.subtract", result)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### 4.3 **图像与运算**

图像的与运算是指两张图像（灰度图像或彩色图像均可）的每个像素值进行二进制“与”操作。

`cv2.bitwise_and(src1, src2[, dst[, mask]])`

```python
import cv2
import numpy as np

# 单个像素值运算
a = np.uint8(0b11001100)  # 204
b = np.uint8(0b10101010)  # 170

print(a & b)  # 0b10001000 = 136

# 用图像演示
img1 = np.full((200, 300, 3), 204, dtype=np.uint8)
img2 = np.full((200, 300, 3), 170, dtype=np.uint8)

result = cv2.bitwise_and(img1, img2)
print(result[0, 0])  # [136 136 136]
```

**应用场景**：掩膜抠图、颜色提取

![图像与运算.png](images%2F%E5%9B%BE%E5%83%8F%E4%B8%8E%E8%BF%90%E7%AE%97.png)

### 4.4 **图像或运算**

图像的或运算是指两张图像（灰度图像或彩色图像均可）的每个像素值进行二进制“或”操作。

`cv2.bitwise_or(src1, src2[, dst[, mask]])`

**应用场景**：掩膜合并

![图像或运算.png](images%2F%E5%9B%BE%E5%83%8F%E6%88%96%E8%BF%90%E7%AE%97.png)

### 4.5 **图像非运算**

图像非运算就是图像的像素反色处理，

`cv2.bitwise_not(src[, dst[, mask]])`

**应用场景**：掩膜取反

### 4.5 **图像异或运算**

图像的异或运算是指两张图像（灰度图像或彩色图像均可）的每个像素值进行二进制“异或”操作。

`cv2.bitwise_xor(src1, src2[, dst[, mask]])`

**应用场景**：二值掩膜差异对比

## 五、其他常见图像操作

### 5.1 **图像 ROI 区域定位**

ROI（Region of Interest）表示感兴趣区域，是指从被处理图像以方框、圆形、椭圆、不规则多边形等方式勾勒出需要处理的区域。
可以通过各种算子（Operator）和函数求得感兴趣 ROI 区域，被广泛应用于热点地图、人脸识别、图像分割等领域。

核心原理：NumPy 切片

`roi = img[y1:y2, x1:x2]`

### 5.2 **图像属性**

```python
import cv2

img = cv2.imread("photo.jpg")

print("形状 shape:", img.shape) # 返回元组 (height, width, channels)
print("尺寸 size:", img.size) # 像素总数（高 × 宽 × 通道）
print("数据类型 dtype:", img.dtype) # uint8 表示 0~255
print("维度 ndim:", img.ndim) # 2 表示单通道灰度图、3 表示多通道图
print("字节数 nbytes:", img.nbytes)
```

输出示例：

```
形状 shape: (720, 1280, 3)
尺寸 size: 2764800
数据类型 dtype: uint8
维度 ndim: 3
字节数 nbytes: 2764800
```

### 5.3 **图像通道分离、合并**


| 操作 | 函数 | 说明 |
|------|------|------|
| 分离通道 | `cv2.split(img)` | 拆成 B、G、R 三个单通道图 |
| 合并通道 | `cv2.merge([b, g, r])` | 把三个单通道合成彩色图 |
| NumPy 切片分离 | `img[:, :, 0]` 等 | 更高效，推荐大图使用 |

> ⚠️ OpenCV 默认通道顺序是 **BGR**，不是 RGB。

示例：

```python
import cv2

img = cv2.imread("photo.jpg")

# # cv2.split 分离成 B、G、R 三个单通道
# b, g, r = cv2.split(img)

# NumPy 方式分离（比 cv2.split 快）
b = img[:, :, 0]
g = img[:, :, 1]
r = img[:, :, 2]

print("原图形状:", img.shape)   # (h, w, 3)
print("单通道形状:", b.shape)    # (h, w)

# 合并回彩色图
merged = cv2.merge([b, g, r])

cv2.imshow("Original", img)
cv2.imshow("Merged", merged)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### 5.4 **图像颜色空间转换**

在日常生活中，我们看到的大多数彩色图像都是 RGB 类型，但是在图像处理过程中，常常需要用到灰度图像、二值图像、HSV、HSI 等颜色。

**图像颜色空间转换**是指将一种颜色空间转换到另一种颜色空间，比如彩色图像转换为灰度图像、BGR 图像转换为 RGB 图像。

图像颜色空间转换函数：`cv2.cvtColor`

```python
#encoding:utf-8
import cv2  
import matplotlib.pyplot as plt

#读取原始图像
img_BGR = cv2.imread('miao.jpg')

#BGR转换为RGB
img_RGB = cv2.cvtColor(img_BGR, cv2.COLOR_BGR2RGB)

#灰度化处理
img_GRAY = cv2.cvtColor(img_BGR, cv2.COLOR_BGR2GRAY)

#BGR转HSV
img_HSV = cv2.cvtColor(img_BGR, cv2.COLOR_BGR2HSV)

#BGR转YCrCb
img_YCrCb = cv2.cvtColor(img_BGR, cv2.COLOR_BGR2YCrCb)

#BGR转HLS
img_HLS = cv2.cvtColor(img_BGR, cv2.COLOR_BGR2HLS)

#BGR转XYZ
img_XYZ = cv2.cvtColor(img_BGR, cv2.COLOR_BGR2XYZ)

#BGR转LAB
img_LAB = cv2.cvtColor(img_BGR, cv2.COLOR_BGR2LAB)

#BGR转YUV
img_YUV = cv2.cvtColor(img_BGR, cv2.COLOR_BGR2YUV)

#调用matplotlib显示处理结果
titles = ['BGR', 'RGB', 'GRAY', 'HSV', 'YCrCb', 'HLS', 'XYZ', 'LAB', 'YUV']  
images = [img_BGR, img_RGB, img_GRAY, img_HSV, img_YCrCb,
          img_HLS, img_XYZ, img_LAB, img_YUV]  
for i in range(9):
   plt.subplot(3, 3, i+1), plt.imshow(images[i], 'gray')  
   plt.title(titles[i])  
   plt.xticks([]),plt.yticks([])  
plt.show()
```

## 六、图像几何变换

### 6.1 **基本概念**

图像几何变换的目的是**改变图像中像素的空间排列**。适当的几何变换可以最大程度地消除由于成像角度、透视关系乃至镜头自身原因所造成的几何失真所产生的负面影响。

图像几何变换由两种基本运算组成：

1. **空间变换**：包括平移、缩放、旋转和正平行投影等，需要用它来表示输出图像与输入图像之间的像素映射关系（通常为反向映射关系）。
2. **灰度插值**：为变换后的像素赋灰度值。因为空间变换可能会产生浮点坐标值。对于输出图像中的每一个整数坐标位置，需要进行灰度插值。
     * **最近邻插值**：直接取离目标点最近的输入像素的灰度值。
     * **双线性插值**（最常用）：利用目标点周围 2×2 的四个已知像素，分别在水平和垂直方向做两次线性插值。
     * **双三次插值**：利用目标点周围 4×4 的十六个已知像素，分别在水平和垂直方向做两次三次插值。



### 6.2 **图像平移**

图像平移就是将图像沿 x 轴、y 轴方向移动指定像素。

平移矩阵是一个 2×3 的仿射变换矩阵：

```
[1  0  tx]
[0  1  ty]
```

OpenCV 中使用 `cv2.warpAffine()` 函数实现图像平移。

语法：`cv2.warpAffine(src, M, dsize[, dst[, flags[, borderMode[, borderValue]]]])`

```python
import cv2
import numpy as np

img = cv2.imread('image.jpg')

# 获取图像尺寸
rows, cols = img.shape[:2]

# 定义平移矩阵:向右平移 100 像素,向下平移 50 像素
tx, ty = 100, 50
M = np.float32([[1, 0, tx],
                [0, 1, ty]])

# 执行平移
# dsize = (宽度, 高度)
translated = cv2.warpAffine(img, M, (cols, rows))
```

### 6.3 **图像缩放**

图像缩放（image scaling）用于改变图像尺寸。OpenCV 中主要使用 `cv2.resize()` 函数实现。

语法：`cv2.resize(src, dsize[, dst[, fx[, fy[, interpolation]]]])`

**（1）指定目标尺寸**

```python
import cv2

img = cv2.imread('image.jpg')

# 缩放到固定尺寸 (宽, 高)
resized = cv2.resize(img, (300, 200))
```

**（2）指定缩放比例**

```python
import cv2

img = cv2.imread('image.jpg')

# 宽高各缩小一半
resized = cv2.resize(img, None, fx=0.5, fy=0.5)
```

### 6.4 **图像旋转**

图像旋转是指图像以某一点为中心旋转一定的角度，形成一幅新的图像的过程。旋转中心一般为图像的中心。

OpenCV 中主要用 `cv2.getRotationMatrix2D()` 生成旋转矩阵，再用 `cv2.warpAffine()` 执行旋转。

语法：`cv2.getRotationMatrix2D(center, angle, scale)` 返回一个 2×3 的变换矩阵：

```
[ α   β   (1-α)·cx - β·cy ]
[ -β  α   β·cx + (1-α)·cy ]
```

其中 `α = scale·cos(angle)`，`β = scale·sin(angle)`。


```python
import cv2

img = cv2.imread('image.jpg')
h, w = img.shape[:2]

# 以图像中心为旋转中心,逆时针旋转 45 度,不缩放
center = (w // 2, h // 2)
M = cv2.getRotationMatrix2D(center, 45, 1.0)

# 执行旋转(输出尺寸保持原图大小)
rotated = cv2.warpAffine(img, M, (w, h))
```

> 上面的写法有个明显问题：旋转后四个角会被裁掉，因为输出画布仍是原图尺寸。
> 
> 解决思路：先计算旋转后图像的外接矩形尺寸，扩大输出画布，并调整旋转矩阵使图像居中。

### 6.6 **图像翻转（镜像）**

图像翻转（镜像）是沿某条轴对图像做对称翻转。OpenCV 中用 `cv2.flip()` 函数实现。

语法：`cv2.flip(src, flipCode[, dst])`

```python
import cv2

img = cv2.imread('image.jpg')

# 水平翻转(左右镜像,最常用)
flip_h = cv2.flip(img, 1)

# 垂直翻转(上下翻转)
flip_v = cv2.flip(img, 0)

# 双向翻转(等价于旋转 180°)
flip_both = cv2.flip(img, -1)
```

### 6.7 **图像仿射变换**

仿射变换（Affine Transform）是**线性变换（旋转、缩放、错切、镜像） + 平移**的组合，具有以下性质：

* 保持直线仍是直线（不会弯曲）
* 保持平行线仍平行
* 保持点的共线性比例关系

仿射变换用 2×3 矩阵表示：

```
[ a  b  tx ]
[ c  d  ty ]
```

对点 `(x, y)` 变换后：

```
x' = a·x + b·y + tx
y' = c·x + d·y + ty
```

其中：

* `a, b, c, d` 控制线性部分（旋转、缩放、错切、镜像）
* `tx, ty` 控制平移部分

写成矩阵形式（用**齐次坐标**统一）：

```
[x']   [a b tx] [x]
[y'] = [c d ty] [y]
[1 ]   [0 0 1 ] [1]
```

**常见仿射变换**：

![常见图像几何操作.png](images%2F%E5%B8%B8%E8%A7%81%E5%9B%BE%E5%83%8F%E5%87%A0%E4%BD%95%E6%93%8D%E4%BD%9C.png)


**（1）已知变换矩阵，直接变换**

**平移、缩放、旋转**都可以先构造矩阵再 `cv2.warpAffine(src, M, dsize[, dst[, flags[, borderMode[, borderValue]]]])`

```python
import cv2
import numpy as np

img = cv2.imread('image.jpg')
h, w = img.shape[:2]

# —— 平移：直接写矩阵 ——
M_translate = np.float32([[1, 0, 100],   # x 方向平移 100
                          [0, 1, 50]])   # y 方向平移 50

# —— 缩放：对角元素为缩放比例 ——
M_scale = np.float32([[1.5, 0, 0],
                      [0, 1.5, 0]])

# —— 旋转：用 getRotationMatrix2D 生成 ——
M_rotate = cv2.getRotationMatrix2D((w/2, h/2), 45, 1.0)

# —— 镜像（水平）：用负号 ——
M_flip = np.float32([[-1, 0, w],   # 注意 tx=w 把图像挪回来
                     [0, 1, 0]])

# 执行
out = cv2.warpAffine(img, M_translate, (w, h))
```

**（2）已知 3 个点对，自动求矩阵**

一些复杂的仿射变换很难直接构造变换矩阵。

OpenCV 提供了根据**变换前后三个点的对应关系**自动求矩阵的函数：`cv2.getAffineTransform(src, dst)`

仿射变换有 **6 个未知数**`（a,b,c,d,tx,ty）`，需要 **3 个点对**（6 个方程）即可唯一确定。

```python
import cv2
import numpy as np

img = cv2.imread('image.jpg')
h, w = img.shape[:2]

# 原图上的 3 个点（左上、右上、左下）
pts_src = np.float32([[0, 0], [w-1, 0], [0, h-1]])

# 目标图像中对应的 3 个点
pts_dst = np.float32([[50, 50], [w-1, 20], [30, h-1]])

# 自动计算变换矩阵
M = cv2.getAffineTransform(pts_src, pts_dst)

# 执行
out = cv2.warpAffine(img, M, (w, h))
```

### 6.8 **图像透视**

透视变换（Perspective Transform）也叫投影变换，比仿射变换更自由，本质是将图像投影到一个新的视平面，能处理"近大远小"的透视效果。

> **透视是仿射的超集**：仿射能做到的，透视都能做到；但透视能处理的"梯形→矩形"仿射做不到。

典型应用：文档矫正、车牌识别、鸟瞰图、AR 贴图等。

透视变换用 **3×3 齐次矩阵**表示：

```
[ a  b  c ]
[ d  e  f ]
[ g  h  1 ]
```

变换时要用**齐次坐标**，注意最后要**除以第三分量做归一化**：

```
[x']       [x]
[y'] = M · [y]
[w ]       [1]
```

实际坐标：

```
X = x' / w'
Y = y' / w'
```

> 这个除法是关键——正是它产生了"近大远小"的透视效果（w 随位置变化）

**基础示例：四点映射**

1. 由 4 个点对生成 3×3 透视矩阵：`M = cv2.getPerspectiveTransform(src, dst)`
2. 执行透视变换：`out = cv2.warpPerspective(src, M, dsize[, dst[, flags[, borderMode[, borderValue]]]])`

```python
import cv2
import numpy as np

img = cv2.imread('image.jpg')
h, w = img.shape[:2]

# 原图 4 个点(按 左上、右上、右下、左下 顺序)
pts_src = np.float32([[50, 50], [w-50, 50], [w-50, h-50], [50, h-50]])

# 目标 4 个点
pts_dst = np.float32([[0, 0], [w-1, 0], [w-1, h-1], [0, h-1]])

# 1. 生成透视矩阵
M = cv2.getPerspectiveTransform(pts_src, pts_dst)

# 2. 执行透视变换
out = cv2.warpPerspective(img, M, (w, h))
```

> 透视矩阵可求逆，实现反向变换


## 七、图像量化、采样

### 7.1 **图像量化处理**

图像量化（Image Quantization）是将图像的连续颜色值减少到有限个离散级别的过程，常用于减少颜色数量、压缩图像、实现油画/海报效果等。

量化等级越多，图像层次越丰富，灰度分辨率越高，图像的质量也越好。

**（1）灰度图量化**

一张 8 位灰度图有 256 个灰度级，量化就是把它们映射到更少的级别。公式：

```
quantized = (pixel // step) * step
```

其中 `step = 256 // levels`，`levels` 是想要保留的级别数。

```python
import cv2

img = cv2.imread('input.jpg', cv2.IMREAD_GRAYSCALE)

def quantize_gray(img, levels=4):
    """将灰度图量化到指定级别数"""
    step = 256 // levels
    # 方式1：整除取整
    q = (img // step) * step
    # 方式2：等价写法，用位运算（levels 为 2 的幂时更快）
    # bits = int(np.log2(levels))
    # q = (img >> bits) << bits
    return q

q4 = quantize_gray(img, 4)    # 4级量化
q8 = quantize_gray(img, 8)    # 8级量化
```

**（2）彩色图量化**

彩色图有 3 个通道（BGR），可以逐通道量化，也可以用 **K-Means 聚类**量化。

```python
import cv2
import numpy as np

def quantize_kmeans(img, k=8):
    # 转为浮点并 reshape 成 (N, 3)
    data = img.reshape((-1, 3)).astype(np.float32)
    
    # 定义中心
    criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 20, 1.0)
    #设置标签
    flags = cv2.KMEANS_PP_CENTERS 
    _, labels, centers = cv2.kmeans(data, k, None, criteria, 10, flags) # type: ignore
    
    centers = np.uint8(centers)
    result = centers[labels.flatten()].reshape(img.shape)
    return result

img = cv2.imread('input.jpg')
for k in [2, 4, 8, 16]:
    q = quantize_kmeans(img, k)
    cv2.imshow(f'k={k}', q)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### 7.2 **图像采样处理**

图像采样（Image Sampling）处理是将一幅连续图像在空间上分割成 M×N 个网格，每个网格用一个灰度值来表示。

图像采样的间隔越大，所得图像像素数越少，空间分辨率越低，图像质量越差，甚至出现**马赛克效应**；相反，图像采样的间隔越小，图像质量越好，但数据量会相应的增大。

### 7.3 **图像金字塔之降采样和上采样**

图像金字塔是同一幅图像的一组分辨率逐级递减（或递增）的图像集合，按尺寸从大到小（或从小到大）堆叠起来，形似金字塔，故得名。它是多尺度图像分析的基础工具。

金字塔就是用一组图像同时表达不同尺度，便于算法在多个尺度上处理图像。

* **大尺度**（底层,高分辨率）：细节丰富
* **小尺度**（顶层,低分辨率）：整体轮廓、概貌

```
        /\        ← 顶层(最小,分辨率最低)
       /  \
      /    \
     /      \
    /________\    ← 底层(最大,原始分辨率)
```

生成图像金字塔主要包括两种方式：

* 降采样：`cv2.pyrDown`
* 上采样：`cv2.pyrUp`

**（1）高斯金字塔（Gaussian Pyramid）—— 用于降采样**

设 $G_0 = I$，则：

$$
G_l = \text{pyrDown}(G_{l-1}), \quad l = 1,2,\dots,N
$$

降采样算子 `pyrDown` = 高斯低通滤波 + 隔行隔列取样：

```python
import cv2

img = cv2.imread('image.jpg')
G = [img]
for i in range(3):
    G.append(cv2.pyrDown(G[-1]))
# G[0] 原始, G[1] 1/2, G[2] 1/4, G[3] 1/8
```

**（2）拉普拉斯金字塔（Laplacian Pyramid）—— 用于保存细节**

拉普拉斯金字塔的每一层 = 高斯金字塔该层 − 该层上采样后的重建,即保存"丢失的高频细节":

$$
L_l = G_l - \text{pyrUp}(G_{l+1}), \quad l = 0,1,\dots,N-1
$$

顶层保留高斯层：

$$
L_N = G_N
$$

上采样算子 `pyrUp` = 插零放大 + 高斯滤波

```python
import cv2

def build_laplacian(img, levels=3):
    G = [img]
    for _ in range(levels):
        G.append(cv2.pyrDown(G[-1]))

    L = []
    for i in range(levels):
        up = cv2.pyrUp(G[i+1])
        # 尺寸对齐(奇数尺寸时 pyrUp 可能差 1 像素)
        up = cv2.resize(up, (G[i].shape[1], G[i].shape[0]))
        L.append(cv2.subtract(G[i], up))
    L.append(G[-1])   # 顶层保留高斯层
    return L
```

无损重建（逆过程）：

$$
G_l = L_l + \text{pyrUp}(G_{l+1})
$$

## 八、图像运算和图像增强

### 8.1 **图像点运算**

图像点运算（Point Operation）指对于一幅输入图像，将产生一幅输出图像，输出图像的每个像素点的灰度值由输入像素点决定。
点运算实际上是灰度到灰度的映射过程，通过映射变换来达到增强或者减弱图像的灰度。还可以对图像进行求灰度直方图、线性变换、非线性变换以及图像骨架的提取。

* 与几何运算的区别：不会改变图像内像素点之间的空间位置关系。
* 与局部（领域）运算的区别：输入像素和输出像素一一对应。

**数学形式**：

$$g(x, y) = T[f(x, y)]$$

其中：

- $f(x, y)$：输入图像
- $g(x, y)$：输出图像
- $T$：灰度变换函数

应用：光度学标定、对比度增强、对比度扩展、显示标定、轮廓线确定

### 8.2 **图像灰度化**

图像灰度化是将一幅彩色图像转换为灰度化图像的过程。彩色图像通常包括 R、G、B 三个分量，分别显示出红绿蓝等各种颜色，
灰度化就是使彩色图像的 R、G、B 三个分量相等的过程。

**加权平均法（亮度法，Luminance）—— 最标准**

基于人眼视觉特性（人眼对绿色最敏感、蓝色最不敏感）:

$$Gray = 0.299 R + 0.587 G + 0.114 B$$

这是 **ITU-R BT.601** 标准,也是 OpenCV `cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)` 实际采用的公式（注意 OpenCV 读入是 BGR 顺序,内部按 R/G/B 权重计算）。

### 8.3 **图像灰度线性变换**

灰度线性变换是最基础的**点运算**（逐像素操作），通过一个线性函数把原灰度值映射到新灰度值，用于**调整图像对比度和亮度**。

$$
g(x,y) = a \cdot f(x,y) + b
$$

其中:
- $f(x,y)$:原图像在 $(x,y)$ 处的灰度值
- $g(x,y)$:变换后的灰度值
- $a$:**斜率(增益 / 对比度控制)**
- $b$:**截距(偏移 / 亮度控制)**

> 约束:输出通常需裁剪到 $[0, 255]$,即 $g = \text{clip}(a f + b,\ 0, 255)$。

参数 $b$ 控制对比度：

| $a$ 取值 | 效果 |
|---------|------|
| $a > 1$ | 对比度**增强**(亮的更亮、暗的更暗),斜率变陡 |
| $0 < a < 1$ | 对比度**减弱**(灰度向中间压缩),斜率变缓 |
| $a = 1$ | 对比度不变 |
| $a < 0$ | 灰度**反转**(负片效果) |

参数 $b$ 控制亮度：

| $b$ 取值 | 效果 |
|---------|------|
| $b > 0$ | 整体**变亮**(灰度整体上移) |
| $b < 0$ | 整体**变暗**(灰度整体下移) |
| $b = 0$ | 亮度不变 |


### 8.4 **图像灰度非线性变换**

- **对数变换**：`s = c·log(1+r)`，压缩高灰度、扩展低灰度，用于显示傅里叶频谱
- **指数/伽马变换**：`s = c·r^γ`，γ<1 提亮暗部，γ>1 压暗亮部，用于显示校正
- **幂律变换**：伽马的一般形式
- **阈值化**：`s = 0 if r<T else 255`
- **分段线性/非线性拉伸**：对比度拉伸、S型曲线
- **直方图均衡化**：基于累积分布函数的非线性映射（严格说是全局非线性点运算）

### 8.5 **图像阈值化处理**

**固定阈值化**：`retval, dst = cv2.threshold(src, thresh, maxval, type)`

```python
import cv2

img = cv2.imread('input.jpg', cv2.IMREAD_GRAYSCALE)

# 固定阈值二值化
ret, th1 = cv2.threshold(img, 127, 255, cv2.THRESH_BINARY)

# 反二值化
ret, th2 = cv2.threshold(img, 127, 255, cv2.THRESH_BINARY_INV)

# 截断
ret, th3 = cv2.threshold(img, 127, 255, cv2.THRESH_TRUNC)

# 低于阈值置 0
ret, th4 = cv2.threshold(img, 127, 255, cv2.THRESH_TOZERO)

# 高于阈值置 0
ret, th5 = cv2.threshold(img, 127, 255, cv2.THRESH_TOZERO_INV)
```

**自适应阈值化**：`dst = cv2.adaptiveThreshold(src, maxValue, adaptiveMethod, thresholdType, blockSize, C)`

```python
import cv2

# 均值自适应
adap_mean = cv2.adaptiveThreshold(
    img, 255,
    cv2.ADAPTIVE_THRESH_MEAN_C,
    cv2.THRESH_BINARY,
    blockSize=11,
    C=2
)

# 高斯自适应（更平滑，常用）
adap_gauss = cv2.adaptiveThreshold(
    img, 255,
    cv2.ADAPTIVE_THRESH_GAUSSIAN_C,
    cv2.THRESH_BINARY,
    blockSize=11,
    C=2
)
```

### 8.6 **图像形态学处理：腐蚀、膨胀**

图像的腐蚀（Erosion）和膨胀（Dilation）是两种基本的形态学运算，主要用来寻找图像中的极小区域和极大区域。

#### **8.6.1 图像腐蚀**

- 作用：缩小图像中的白色区域（前景）
- 核心函数：`cv2.erode(src, kernel, dst=None, anchor=(-1,-1), iterations=1, borderType=..., borderValue=...)`
    - `kernel` 越大、`iterations` 越多，效果越明显
- 典型应用场景：去除噪点、断开物体连接、提取骨架

```python
import cv2
import numpy as np

img = cv2.imread('input.png', cv2.IMREAD_GRAYSCALE)

# 定义 5x5 结构元素
kernel = np.ones((5, 5), np.uint8)

# 腐蚀
eroded = cv2.erode(img, kernel, iterations=1)

cv2.imshow('Original', img)
cv2.imshow('Eroded', eroded)
cv2.waitKey(0)
```

常用结构元素：

```python
# 矩形
k1 = cv2.getStructuringElement(cv2.MORPH_RECT,    (5, 5))
# 椭圆（圆形）
k2 = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))
# 十字形
k3 = cv2.getStructuringElement(cv2.MORPH_CROSS,   (5, 5))
```

#### **8.6.2 图像膨胀**

* 作用：扩大图像中的白色区域（前景）
* 函数：`cv2.dilate(src, kernel, dst=None, anchor=(-1,-1), iterations=1, borderType=..., borderValue=...)`
    * 参数与 `cv2.erode` 完全一致，只是效果相反。
* 应用场景：填补孔洞、连接断裂物体、加粗线条

### 8.7 **图像形态学处理：开运算、闭运算、形态学梯度**

#### **8.7.1 图像开运算**

- 定义：**先腐蚀后膨胀**：`open = dilate(erode(img))`。
- 作用：去除小的白色噪点/毛刺，同时保持物体主体大小基本不变。 
- 函数：`cv2.morphologyEx(src, cv2.MORPH_OPEN, kernel, dst=None, anchor=(-1,-1), iterations=1, borderType=..., borderValue=...)`
    - 等价于：`cv2.dilate(cv2.erode(img, kernel, iterations), kernel, iterations)`

```python
import cv2

img = cv2.imread('input.png', cv2.IMREAD_GRAYSCALE)

# 5x5 结构元素
kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))

# 开运算
opened = cv2.morphologyEx(img, cv2.MORPH_OPEN, kernel)

cv2.imshow('Original', img)
cv2.imshow('Opened', opened)
cv2.waitKey(0)
```

#### **8.7.2 图像闭运算**

* 定义：**先膨胀后腐蚀**：`close = erode(dilate(img))`。
* 作用：填补物体内部的小孔洞、连接断裂的缝隙，同时保持物体主体大小基本不变。
* 函数：`cv2.morphologyEx(src, cv2.MORPH_CLOSE, kernel, dst=None, anchor=(-1,-1), iterations=1, borderType=..., borderValue=...)`

#### **8.7.3 图像形态学梯度**

- 定义：膨胀图减去腐蚀图：`gradient = dilate(img) - erode(img)`。
- 作用：提取物体的轮廓/边缘，得到的是物体的"厚度"边界。
- 函数：`cv2.morphologyEx(src, cv2.MORPH_GRADIENT, kernel, dst=None, anchor=(-1,-1), iterations=1, borderType=..., borderValue=...)`

### 8.8 **图像形态学处理：顶帽运算、底帽运算**

#### **8.8.1 顶帽运算**

- 定义：**原图减去开运算结果**：`tophat = img - open(img)`。
- 作用：提取比周围亮的细小物体/细节，即图像中"突出的小亮点、细线、局部高亮区域"。
- 原理：
    * 开运算（先腐蚀后膨胀）会去掉比 kernel 小的亮细节，只保留主体。
    * 原图 − 开运算结果 = 被开运算去掉的那些小亮细节
- 函数：`cv2.morphologyEx(src, cv2.MORPH_TOPHAT, kernel, dst=None, anchor=(-1,-1), iterations=1, borderType=..., borderValue=...)`
- 应用场景：
    * 提取图像中的小亮点/高光
    * 不均匀光照校正（背景估计）：顶帽能去掉大范围缓慢变化的背景，保留局部亮细节，常用于光照不均的图像预处理
    * 提取细小文字/笔画（OCR 预处理）
    * 检测缺陷/划痕（工业视觉）
    * 细胞/颗粒计数预处理

#### **8.8.2 底帽运算**

* 定义：**闭运算结果减去原图**：`blackhat = close(img) - img`。
* 作用：提取比周围暗的细小物体/细节，即图像中"隐藏在亮背景里的小暗点、孔洞、暗缝、局部阴影"
* 原理：
    * 闭运算（先膨胀后腐蚀）会填补比 kernel 小的暗细节（孔洞、缝隙），让暗区域变亮。
    * 闭运算结果 − 原图 = 被闭运算填掉的那些小暗细节
* 函数：`cv2.morphologyEx(src, cv2.MORPH_BLACKHAT, kernel, dst=None, anchor=(-1,-1), iterations=1, borderType=..., borderValue=...)`
* 应用场景：
    * 提取图像中的小暗点/孔洞
    * 暗缺陷/暗划痕检测（工业视觉）
    * 文字提取（暗文字、亮背景）
    * 不均匀光照下的暗细节增强

### 8.9 **图像直方图**

图像直方图（Image Histogram）用统计图表的形式，描述一幅图像中**各个灰度级（或颜色通道取值）出现的频率**。

#### **8.9.1 直方图的计算**

令 $r_k (k = 0,1,2...L-1)$ 表示一幅 $L$ 级灰度数字图像 $f(x,y)$ 的灰度。

$f$ 的**非归一化直方图**定义为：

$$h(r_{k}) = n_{k}$$

式中，$n_k$ 是 $f$ 中灰度为 $r_k$ 的像素的数量。。

类似地，**归一化直方图**定义为：

$$p(r_{k}) = \frac{h(r_{k})}{MN} = \frac{n_{k}}{MN}$$

式中，$M$ 和 $N$ 分别是图像的行数和列数。对 $k$ 的所有值，$p(r_{k})$ 的和总是 1.

**4 种基本灰度特性的图像**：

![不同灰度特性的图像.png](images%2F%E4%B8%8D%E5%90%8C%E7%81%B0%E5%BA%A6%E7%89%B9%E6%80%A7%E7%9A%84%E5%9B%BE%E5%83%8F.png)

#### **8.9.2 OpenCV 绘制直方图**

1. 直方图计算：`cv2.calcHist(images, channels, mask, histSize, ranges)`
2. 直方图绘制：Matplotlib 或 OpenCV 绘图函数

以 Matplotlib 绘制灰度图像的直方图为例：

```python
import cv2
import matplotlib.pyplot as plt

# 以灰度模式读取
img = cv2.imread('lena.jpg', cv2.IMREAD_GRAYSCALE)

# 计算直方图
hist = cv2.calcHist([img], [0], None, [256], [0, 256])

# 绘制
plt.figure(figsize=(10, 4))
plt.subplot(1, 2, 1)
plt.imshow(img, cmap='gray')
plt.title('Image')
plt.axis('off')

plt.subplot(1, 2, 2)
plt.plot(hist, color='black')
plt.title('Grayscale Histogram')
plt.xlabel('Pixel Value')
plt.ylabel('Count')
plt.xlim([0, 256])
plt.tight_layout()
plt.show()
```


## 二、数字图像基础

**相机成像的原理**：针孔相机( Pinhole Camera )通过投影变换，可以将三维相机（Camera）坐标转换为二维的图像坐标，这个变换矩阵是相机的内在属性，称为相机内参（Camera Intrinsic） K。

> yaw 航向角，pitch 俯仰角，roll 翻滾角。

### 2.2 **电磁波谱**

![电磁波谱.png](images%2F%E7%94%B5%E7%A3%81%E6%B3%A2%E8%B0%B1.png)

* 感知的物体颜色由物体反射的光的性质决定。
* 没有颜色的光称为单色光或无色光。
* 彩色光源的三个属性：频率、辐射、光通量和亮度。

### 2.3 **图像感知与获取**

将照射能量转换为数字图像主要由三种传感器配置：

* **单个传感器**
* **条带传感器**：如磁共振成像（MRI）和正电子发射断层成像（PET）等。
* **阵列传感器**: 如单反相机和手机相机。

![图像感知与获取.png](images%2F%E5%9B%BE%E5%83%8F%E6%84%9F%E7%9F%A5%E4%B8%8E%E8%8E%B7%E5%8F%96.png)

### 2.4 **图像取样和量化**

**2.4.1 取样和量化的概念**

一幅连续图像 $f$，对坐标值进行数字化称为**取样**（或采样），对幅度值进行数字化称为**量化**。

**2.4.2 数字图像表示**

在计算机中，数字图像可用一个 $M\times N$矩阵表示，图像长为 $M$，宽为 $N$，矩阵中的每个元素即为图像的像素。

![数字图像表示.png](images%2F%E6%95%B0%E5%AD%97%E5%9B%BE%E5%83%8F%E8%A1%A8%E7%A4%BA.png)

### 2.5 **像素间的一些基本关系**

* 像素的相邻像素
* 邻接、连通、区域和边界
* 距离测度：两个像素的距离，通过欧几里得（欧式）距离计算：$D(p,q) = \sqrt{[(x-u)^2 + (y-v)^2]}$

### 2.6 **数字图像处理的基本数学工具**

**2.6.1 逐元素运算和矩阵运算**

**2.6.2 线性运算和非线性运算（一般两个图像之间）**

![线性运算和非线性运算.png](images%2F%E7%BA%BF%E6%80%A7%E8%BF%90%E7%AE%97%E5%92%8C%E9%9D%9E%E7%BA%BF%E6%80%A7%E8%BF%90%E7%AE%97.png)

线性运算更为重要，包含了大量适用于图像处理的理论与实践成果；非线形运算范围比较有限。

**2.6.3 算术运算（一般两个图像之间）**

算术运算常用在特定的天文、医学等领域，将两幅图像经过算术运算从而得到更为清晰的图像。

![算术运算.png](images%2F%E7%AE%97%E6%9C%AF%E8%BF%90%E7%AE%97.png)

这些加减乘除运算都是对应的像素运算，算术运算一般有以下应用：

* 使用图像相加（平均）降低噪声。
* 使用图像相减比较图像。
* 使用图像相乘/相除校正阴影和模板。

**2.6.4 集合运算和逻辑运算（一般两个图像之间）**

集合运算主要针对二值图像。

假设有两个集合 A 和 B，在数字图像处理中常见的集合运算有：

* 交集运算: $C = A\cap B$，满足交换律、结合律和分配律。
* 并集运算: $C = A\cup B$。

如果想要知道一幅二值图像中的两个目标 A 和 B 是否重叠，可通过计算 $A\cap B$。如果结果不是空集，则可确定两个目标的某些元素是有重叠的。

**2.6.5 空间运算（单幅图像）**

空间运算是直接对单幅图像的像素执行数学操作，分为三类：

1. **点运算**
2. **邻域运算**
3. **几何变换**

**2.6.5.1 点运算**：用一个变化函数 $T$ 改变图像中各个像素的灰度，即 $s = T(z)$

**2.6.5.2 邻域运算**：输出像素的值不仅取决于对应位置的输入像素值，还取决于该像素周围一定范围内（即邻域）的像素值。

> 所谓的邻域运算就是对特定 roi 区域的所有像素做特定操作：以指定位置 $(x,y)$ 为中心，邻域范围为 $m\times n$，对这个范围内的像素取平均/求和/最大值/等得到输出像素。
>
> 典型的就是 CNN 模型中卷积层的滤波器操作。

## 三、灰度变换与空间滤波

### 3.1 **空间域图像处理概述**

空间域指的是图像平面本身，空间域中的图像处理方法是直接对图像中的像素进行处理。空间域图像处理的两个主要类别是:

* **灰度变换**: 如对比度处理、阈值处理等任务，直接对图像的每个像素进行操作。
* **空间滤波**: 如图像平滑和锐化，对图像中的每个像素的邻域进行操作。

> 空间域技术直接操作图像中的像素，而频率域技术操作的是图像的傅立叶变换而非图像本身。
> 
> 尽管灰度变换和空间滤波的应用范围广泛，但本书中的大多数例子是关于图像增强的。所谓图像增强技术，是为了某些特定应用对原图像进行加工的技术，不具备通用性。


### 3.2 **灰度变换**

**3.2.1 灰度变换的定义**

灰度变换是对图像中**每一个像素独立**进行操作，输出像素值只取决于该位置的输入像素值，与周围像素无关。

**数学形式**：

$$g(x, y) = T[f(x, y)]$$

其中：

- $f(x, y)$：输入图像
- $g(x, y)$：输出图像
- $T$：灰度变换函数

**3.2.2 灰度变换的常见类型**

| 类型 | 变换函数 | 作用 |
| :--- | :--- | :--- |
| 图像反转 | $g = 255 - f$ | 负片效果，黑变白、白变黑 |
| 对数变换 | $g = c\log(1+f)$ | 压缩动态范围，扩展暗部细节 |
| 幂律（伽马）变换 | $g = c \cdot f^\gamma$ | 伽马校正，调整亮度感知 |
| 对比度拉伸 | 分段线性函数 | 扩展灰度范围，增强对比度 |
| 阈值处理 | $g = 0$ 或 $255$ | 二值化，用于图像分割 |
| 直方图均衡化 | 累积分布函数映射 | 增强整体对比度 |

### 3.3 **直方图处理**



**3.3.1 直方图均衡化**

直方图均衡化（Histogram Equalization）是一种**基于灰度直方图的灰度变换方法**。

**核心思想**：通过变换函数将原图像的灰度直方图重新映射，使其**近似均匀分布**，从而增强图像的全局对比度。

设输入图像灰度级为 $r$，范围 $[0, L-1]$（8 位图像 $L=256$）。

**直方图均衡化的变换函数**为：

$$s = T(r) = (L-1)\sum_{j=0}^{r} p_r(j)$$

其中：

- $s$：输出灰度值
- $p_r(j)$：第 $j$ 级灰度的概率（归一化直方图）
- $\sum_{j=0}^{r} p_r(j)$：累积分布函数（CDF）

**直方图均衡化变换函数的概率密度形式**（连续情况）：

$$s = T(r) = (L-1)\int_0^r p_r(w)\,dw$$

**直方图均衡化的计算步骤**：

1. **统计直方图**：统计每个灰度级 $r_k$ 出现的像素数 $n_k$，$k=0,1,\dots,L-1$。
2. **计算归一化直方图**：$p_r(r_k) = \frac{n_k}{N}$
3. **计算累积分布函数**：$\text{CDF}(k) = \sum_{j=0}^{k} p_r(r_j)$
4. **计算映射关系**：$s_k = \text{round}\left[(L-1)\cdot \text{CDF}(k)\right]$
5. **逐像素映射**：将原图中每个灰度值 $r_k$ 替换为 $s_k$，得到均衡化后的图像。

**3.3.2 直方图匹配（规定化）**

直方图规定化（Histogram Specification），又称直方图匹配（Histogram Matching），是一种基于灰度直方图的灰度变换方法。

**核心思想**：通过变换函数将原图像的灰度直方图映射为用户指定的目标直方图，从而实现对图像对比度的灵活控制。

> 直方图均衡化是规定化的一个特例（目标直方图为均匀分布）

**3.3.3 局部直方图处理**

直方图均衡化和直方图规定化的直方图处理方法都是全局性的，因为像素是由基于整个图像的灰度分布的变换函数修改的。

当目的是为了解决增强图像中几个小区域的细节时，解决方法是设计**基于像素邻域的灰度分布的变换函数**。

**3.3.4 使用直方图统计增强图像**

直接从图像直方图得到的统计量信息可用于增强图像。

1，**均值是平均灰度的测度**:

$$m = \sum_{i=0}^{L-1} r_{i}p(r_i)$$

2，**方差（或标准差$\sigma$）是图像对比度的测度**：

$$\sigma^2 = \mu_2 = \sum_{i=0}^{L-1}(r_{i}-m)^{2}p(r_i)$$

> 简单理解：均值越大，图像越亮；方差越大，图像对比度越高

### 3.4 **空间滤波基础**

> 本节内容讨论如何使用空间滤波器进行图像处理。滤波有时要分多个阶段完成。

滤波器一词来自频率域处理（第四章），滤波的意思是指通过修改或抑制图像的规定分量。例如，通过低频的滤波器称为低通滤波器。低通滤波器的作用是通过模糊图像来平滑图像，使用空间滤波器可以直接对图像本身进行类似效果的平滑处理。

**3.4.1 线性空间滤波的原理**

**3.4.2 空间相关与卷积**

### 3.5 **平滑（低通）空间滤波器**

平滑（也称平均）空间滤波器用于降低灰度的急剧过渡，因为随机噪声通常就是由灰度的急剧过渡组成，所以平滑的一个明显应用就是**降噪**。

本节介绍基于可分离盒式核和高斯核的低通滤波器。

![可分离盒式核和高斯核.png](images%2F%E5%8F%AF%E5%88%86%E7%A6%BB%E7%9B%92%E5%BC%8F%E6%A0%B8%E5%92%8C%E9%AB%98%E6%96%AF%E6%A0%B8.png)

**3.5.1 基于可分离盒式核的低通滤波器**

最简单的可分离低通滤波器核是**盒式核**，其系数的值相同（通常为1）。

**盒式滤波器的局限性**：

* 对透镜模糊特性的近视能力较差。
* 盒式滤波器往往沿垂直方向模糊图像，不适合精细细节或具有强几何分量的图像应用。

**opencv 函数**：

```C++
void boxFilter(InputArray f, OutputArray dst, int ddepth,
                             Size ksize, Point anchor=Point(-1,-1),
                             bool normalize=true,
                             int borderType=BORDER_DEFAULT);
```

* `normalize = true`：盒式滤波就变成了**均值滤波**。也就是说，均值滤波是盒式滤波归一化（normalized）后的特殊情况。
    * 计算速度快，对**周期性的干扰噪声**有很好的抑制作用。
    * 不能很好地保护图像细节，在图像去噪的同时，也破坏了图像的细节部分，从而使图像变得模糊，不能很好地去除噪声点。
* `normalize = false`：为非归一化的盒式滤波，用于计算每个像素邻域内的积分特性，比如密集光流算法（dense optical flow algorithms）中用到的图像倒数的协方差矩阵（covariance matrices of image derivatives）。


**3.5.2 基于高斯核的低通滤波器**

实际应用中要求卷积核是各向同性的（圆对称），其响应与方向无关。

高斯核是唯一可分离的圆对称核，对于**抑制服从正态分布的噪声**非常有效。

高斯核定义:

$$w(s,t) = G(s,t) = Ke^{-\frac{s^2+t^2}{2\sigma^2}}$$

令$r=[s^2+t^2]^{1/2}$，上式可写为

$$G(r) = Ke^{-\frac{r^2}{2\sigma^2}}$$

这个函数依然是圆对称的，变量 $r$ 表示从中心到函数 $G$ 上任意一点的距离，它必须是正数且是奇数。下图显示了不同大小的核的 $r$ 值。

希望产生**更均匀的平滑结果**时，通常使用高斯核平滑；盒式核平滑则是硬过渡。

盒式核与高斯核平滑特性的比较对比图如下：

![高斯核和盒式核平滑特性的比较对比图.png](images%2F%E9%AB%98%E6%96%AF%E6%A0%B8%E5%92%8C%E7%9B%92%E5%BC%8F%E6%A0%B8%E5%B9%B3%E6%BB%91%E7%89%B9%E6%80%A7%E7%9A%84%E6%AF%94%E8%BE%83%E5%AF%B9%E6%AF%94%E5%9B%BE.png)

### 3.6 **锐化（高通）空间滤波器**

**锐化**的作用是突出灰度中的过渡。前面讲的平滑（低通）滤波（图像模糊）通过积分运算实现的，那可以推断出图像锐化可以用微分实现。

3.6.1 导数基础

略

3.6.2 拉普拉斯：二阶导数锐化图像

![拉普拉斯核.png](images%2F%E6%8B%89%E6%99%AE%E6%8B%89%E6%96%AF%E6%A0%B8.png)

3.6.3 钝化掩蔽和高提升滤波

从原图像减去一幅钝化（平滑后）图像，是 20 世纪 30 年代以来印刷和出版业一直用来锐化图像的方法，这个过程称为**钝化掩蔽**，步骤如下：

1. 模糊原图像。
2. 从原图像减去模糊后的图像（产生的差称为模板）。
3. 将模板与原图像相加。

3.6.4 梯度：一阶导数锐化图像

包括罗伯特交叉梯度算子和 `Sobel` 算子，使用一阶梯度算子锐化图像常用在工业缺陷检测。

![梯度.png](images%2F%E6%A2%AF%E5%BA%A6.png)

### 3.7 **低通、高通、带阻核带通滤波器**

![低通、高通、带阻核带通滤波器.png](images%2F%E4%BD%8E%E9%80%9A%E3%80%81%E9%AB%98%E9%80%9A%E3%80%81%E5%B8%A6%E9%98%BB%E6%A0%B8%E5%B8%A6%E9%80%9A%E6%BB%A4%E6%B3%A2%E5%99%A8.png)

### 3.8 **组合使用图像增强方法**

例如使用拉普拉斯来**增强细节**，使用梯度来**增强边缘**。

## 四、频率域滤波

### 4.1 **背景**

* **傅里叶级数**: 任何**周期函数**都可表示为不同频率的正弦函数和/或余弦函数之和，其中每个正线函数和/或余弦函数都乘以不同的系数（我们现在称该和为傅里叶级数）。
* **傅里叶变换**: 任何**非周期函数**都可表示为正弦函数和/或余弦函数乘以加权函数的积分。 

略。

### 4.2 **基本概念**

1. 复数
2. 傅里叶级数
3. 冲激函数及其取样（筛选）性质
4. 连续单变量函数的傅里叶变换
5. 卷积

### 4.3 **取样和取样函数的傅里叶变换**

略。

### 4.8 **使用低通频率域滤波器平滑图像**

图像中的边缘和其他急剧的灰度变化（如噪声）主要影响其傅里叶变换的高频内容，因此，在频率域中是通过衰减高频（即低通滤波）来实现平滑（模糊）的。

## 五、图像复原与重建

图像增强主要是一种主观处理，而图像复原很大程度上是一种**客观处理**技术。

## 六、彩色图像处理

### 6.1 **彩色基础**

光的三原色：红（R）、绿（G）、蓝（B）

区分不同颜色的特性通常是**亮度、色调和饱和度**，色调与饱和度一起称为**色度**。

* **亮度**：亮度体现的是发光强度（灰度级）的消色概念。
* **色调**：色调是混合光波中与主导波长相关的属性，表示被观察者感知的主导色。即，当我们说一个物体颜色说红色、橙色或红色时，说的就是物体的色调。
* **饱和度**：饱和度指的是相对的纯度（纯色被白光稀释的程度），或与一种色调混合的白光量。纯光谱颜色是完全饱和的，非光谱颜色如淡紫色（紫色加白色）是不饱和度，饱和度与所加白光量成反比。

### 6.2 **彩色模型**

彩色模型（也称彩色空间或彩色系统）的目的是以某种标准的方式来方便地规定颜色。彩色模型本质上规定：（1）坐标系；（2）坐标系内的字空间，模型内的每种颜色都可由字空间内包含的一个点来表示。数字图像处理中的面向硬件的彩色模型常用有以下几种：

* 针对彩色显示器和彩色摄像机开发的 **RGB（红、绿、蓝）**模型。
* 针对彩色打印机开发的 **CMY（青、深红、黄）**模型和 **CMYK（青、深红色、黄、黑）**模型。
* 针对人们描述和解释颜色的方式开发的 **HSI（色调、饱和度、亮度）**模型。HSI 模型的优点：它能够解除图像中颜色和灰度级信息的联系。

**6.2.1 RGB 彩色模型**

在 RGB 模型中，每种颜色都以其红色、绿色和蓝色光谱成分显示，该模型是根据笛卡尔坐标系建立的。

全彩色图像通常是指一幅 24 比特的 RGB 彩色图像（每幅分量图像都是 8 位，值域是 `[0, 255]`），颜色总数自然是 $(2^8)^3$=16777216。

**6.2.2 HSI 彩色模型**

RGB 、CMY 和 CMYK 模型适合硬件实现但是并不能很好的描述人类世纪解释的颜色。

观察彩色物体时，我们会用色调、饱和度和亮度来描述这个物体，由此提出了 HSI 彩色模型（色调、饱和度、亮度）。

从 RGB 到 HSI 的彩色变换的公式如下：

![RGB和HSI彩色变换.png](images%2FRGB%E5%92%8CHSI%E5%BD%A9%E8%89%B2%E5%8F%98%E6%8D%A2.png)

### 6.4 **全彩色图像处理基础**

全彩色图像处理方法主要分为两种：

* 首先分布处理每幅灰度级分量图像，然后将处理后的各幅分量图像合成为一幅彩色图像。
* 直接处理彩色像素。因为全彩色图像至少有 3 个分量，因此彩色像素是向量。

### 6.5 **彩色变换**

### 6.6 **彩色图像平滑和锐化**

## 八、图像压缩和水印

图像压缩是一种减少表示一幅图像所需数据量的技术。

### 8.1 **基础**

### 8.2 **霍夫曼编码**

### 8.12 **数字图像水印**