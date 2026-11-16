# 数字图像处理

相关链接：

- 冈萨雷斯《数字图像处理》
- [CV 算法工程师成长之路-数字图像处理](https://harleyszhang.github.io/cv_note/6-computer_vision/%E6%95%B0%E5%AD%97%E5%9B%BE%E5%83%8F%E5%A4%84%E7%90%86/0-%E6%95%B0%E5%AD%97%E5%9B%BE%E5%83%8F%E5%A4%84%E7%90%86%E7%AC%94%E8%AE%B0.html)
- [华为云《从零到壹•Python图像处理及识别》电子书](https://res-static.hc-cdn.cn/cloudbu-site/china/zh-cn/CloudBook/PythonCloudShare0712.pdf)


## 一、数字图像处理基础

### 1.1 **数字图像处理概述**

#### **1.2.1 什么是数字图像处理**

**数字图像处理（Digital Image Processing）**：借助计算机处理数字图像的过程。

- **图像**：一个二维函数 $f(x,y)$，其中 $x$ 和 $y$ 是空间（平面）坐标，任意一对空间坐标 $(x,y)$ 处的幅度值 $f$ 称为图像在该坐标点的强度或灰度。
- **数字图像**：$x,y$ 和幅度值 $f$ 均为**有限离散量**的图像，可用矩阵（数组）表示。
- **像素**：数字图像中每一离散坐标位置上的最小独立单元，其数值表示该点的强度或灰度。一幅 $M\times N$ 的数字图像对应一个 $M\times N$ 的矩阵，矩阵元素即像素值。

#### **1.2.2 数字图像的类别**

```
数字图像
│
├── 按成像波段
│   ├── 可见光图像
│   └── 非可见光图像（红外、X光、超声、MRI等）
│
├── 按亮度/通道层次
│   ├── 二值图像（1 bit）
│   ├── 灰度图像（单通道、多灰度级）
│   └── 彩色图像（多通道）
│
├── 按时间特性
│   ├── 静态图像
│   └── 动态图像（视频）
│
└── 按维数
    ├── 二维图像
    └── 三维图像（及高维图像）
```

#### **1.2.3 数字图像处理的方法/任务**


```
数字图像处理方法
│
├── 按处理域划分
│   ├── 空间域处理（spatial domain：直接作用于像素灰度）
│   │   ├── 点运算（灰度变换、直方图处理）
│   │   └── 邻域运算（空间滤波、平滑、锐化）
│   │
│   └── 频率域处理（frequency domain：作用于图像的频率成分）
│       ├── 傅里叶变换
│       ├── 频域滤波（低通、高通、同态滤波）
│       └── 其他变换（DCT、小波变换）
│
└── 按功能/处理层次划分
    │
    ├── 几何处理（改变像素坐标位置）
    │   │   几何变换：平移、旋转、缩放、仿射、透视、投影
    │   ├── 畸变校正
    │   ├── 图像配准
    │   └── 插值重采样
    │
    ├── 底层处理（Low-Level Processing）
    │   │   特点：图像 → 图像，像素级操作，无高层语义
    │   ├── 图像获取与数字化（采样、量化）
    │   ├── 图像增强（灰度变换、直方图处理、空域/频域滤波）
    │   ├── 图像复原（退化建模、逆滤波、维纳滤波）
    │   ├── 彩色图像处理（彩色空间变换、彩色增强）
    │   ├── 小波变换与多分辨率处理
    │   └── 图像压缩（无损/有损编码）
    │
    ├── 中层处理（Mid-Level Processing）
    │   │   特点：图像 → 属性/结构，提取边缘、区域、特征
    │   ├── 形态学图像处理（腐蚀、膨胀、开闭运算）
    │   ├── 图像分割（边缘检测、阈值分割、区域生长）
    │   └── 特征提取与表示（边界表示与描述、区域形状/纹理描述、角点、SIFT 等特征点检测）
    │
    └── 高层处理（High-Level Processing）
        │   特点：图像/特征 → 语义解释与决策，涉及认知与理解
        └── 图像识别与理解
            ├── 图像分类
            ├── 目标检测
            ├── 语义分割
            ├── 模式识别
            └── 场景理解
```

![图像处理相关的应用方法.png](images%2F%E5%9B%BE%E5%83%8F%E5%A4%84%E7%90%86%E7%9B%B8%E5%85%B3%E7%9A%84%E5%BA%94%E7%94%A8%E6%96%B9%E6%B3%95.png)

#### **1.2.4 像素间的基本关系**

数字图像中，像素并非孤立存在，其灰度值与空间位置均与邻域像素存在关联。

1. **像素间的邻域关系**
     - 设图像中某像素 \(p\) 的坐标为 \((x,y)\)。
     - 4 邻域（4-neighborhood）
$$
N_4(p) = \{(x+1,y),\ (x-1,y),\ (x,y+1),\ (x,y-1)\}
$$
     - 对角邻域（diagonal neighborhood）
$$
N_D(p) = \{(x+1,y+1),\ (x+1,y-1),\ (x-1,y+1),\ (x-1,y-1)\}
$$
     - 8 邻域（8-neighborhood）
$$
N_8(p) = N_4(p) \cup N_D(p)
$$
2. **像素间的邻接关系**：邻接（adjacency）描述两个像素在空间上是否相邻，是连通性分析的前提。
     - 设像素 \(p\) 与 \(q\) 的灰度值分别为 \(f(p)\)、\(f(q)\)，灰度值集合为 \(V\)（例如二值图像中 \(V=\{1\}\)，灰度图像中 \(V=\{0,1,\dots,255\}\) 的子集）。
     - 4 邻接：若 \(q \in N_4(p)\)，且 \(f(p), f(q) \in V\)，则称 \(p\) 与 \(q\) **4 邻接**。
     - 8 邻接：若 \(q \in N_8(p)\)，且 \(f(p), f(q) \in V\)，则称 \(p\) 与 \(q\) **8 邻接**。
     - m 邻接（混合邻接）
3. **像素间的距离度量**：
     - 设像素 \(p=(x,y)\)、\(q=(s,t)\)，常用的距离度量有三种。
     - 欧氏距离（Euclidean distance）
$$
D_e(p,q) = \sqrt{(x-s)^2 + (y-t)^2}
$$
     - 城市街区距离（\(D_4\) 距离）
$$
D_4(p,q) = |x-s| + |y-t|
$$
     - 棋盘距离（\(D_8\) 距离）
$$
D_8(p,q) = \max(|x-s|,\ |y-t|)
$$
4. **连通性**
     - 通路：从像素 \(p\) 到像素 \(q\) 的一条**通路（path）** 是一组像素序列，其中相邻像素 \(p_{i-1}\) 与 \(p_i\) 满足相应的邻接关系（4 邻接、8 邻接或 m 邻接）。
$$
p = p_0, p_1, \dots, p_n = q
$$
     - 连通：若通路中相邻像素均为 4 邻接（或 8 邻接、m 邻接），则称该通路为 4 连通（或 8 连通、m 连通）。若存在从 \(p\) 到 \(q\) 的通路，则称 \(p\) 与 \(q\) **连通**。
     - 连通分量：图像中满足连通关系的像素集合构成**连通分量（connected component）**。连通分量是区域标记、目标计数与形态学处理的基础。
5. **区域与边界**
     - **区域（region）**：图像中一个连通的像素集合。
     - **边界（boundary）**：区域中与区域外像素相邻的像素集合。
     - **边缘（edge）**：灰度值发生显著变化的像素集合，与边界概念相关但不等同。

> 注意区分“边界”与“边缘”：边界是区域意义上的拓扑概念，边缘是灰度变化意义上的微分概念。


---

### 1.2 **电磁波谱**

#### **1.2.1 电磁波**

电磁波在真空中的传播速度为光速 $c$：

$$
c = \lambda \cdot f
$$

其中：

- $c \approx 3 \times 10^8 \ \text{m/s}$（光速）
- $\lambda$ 为波长（m）
- $f$ 为频率（Hz）

光子能量与波长的关系为：

$$
E = hf = \frac{hc}{\lambda}
$$

其中 $h \approx 6.626 \times 10^{-34} \ \text{J·s}$ 为普朗克常数。波长越短，光子能量越高。

#### **1.2.2 电磁波谱**

电磁波谱（Electromagnetic Spectrum）是指所有电磁波按照**波长（或频率、能量）**连续排列所形成的谱系。

按波长从长到短（频率从低到高、能量从低到高）排列，电磁波谱主要包括：

| 波段 | 典型波长范围 | 典型频率范围 | 在图像处理中的典型应用 |
|------|-------------|-------------|----------------------|
| 无线电波 | $> 1\ \text{m}$ | $< 300\ \text{MHz}$ | 雷达成像、MRI（射频段）、遥感 |
| 微波 | $1\ \text{mm} \sim 1\ \text{m}$ | $300\ \text{MHz} \sim 300\ \text{GHz}$ | 合成孔径雷达（SAR）、微波遥感、毫米波安检成像 |
| 红外线 | $700\ \text{nm} \sim 1\ \text{mm}$ | $300\ \text{GHz} \sim 430\ \text{THz}$ | 热成像、夜视、遥感、医学诊断 |
| 可见光 | $400 \sim 700\ \text{nm}$ | $430 \sim 750\ \text{THz}$ | 普通摄影、工业检测、医学内窥镜、显微成像 |
| 紫外线 | $10 \sim 400\ \text{nm}$ | $750\ \text{THz} \sim 30\ \text{PHz}$ | 荧光显微成像、材料检测、天文观测 |
| X 射线 | $0.01 \sim 10\ \text{nm}$ | $30\ \text{PHz} \sim 30\ \text{EHz}$ | 医学 X 光、CT、工业无损检测、安检 |
| γ 射线 | $< 0.01\ \text{nm}$ | $> 30\ \text{EHz}$ | 核医学成像（PET/SPECT）、天文观测 |

![电磁波谱.png](images%2F%E7%94%B5%E7%A3%81%E6%B3%A2%E8%B0%B1.png)

#### **1.2.3 物理成像模型**

数字图像在物理上来源于**电磁波（或声波、电子束等）能量的空间分布**

**照射—反射模型**：

\[
f(x,y) = i(x,y)\, r(x,y)
\]

其中：

- \(f(x,y)\)：图像在点 \((x,y)\) 处的亮度（能量分布）；
- \(i(x,y)\)：**照射分量（illumination component）**，表示光源投射到场景表面的入射能量；
- \(r(x,y)\)：**反射分量（reflectance component）**，表示表面对入射能量的反射能力。

各分量取值范围：

\[
0 < i(x,y) < \infty
\]

\[
0 < r(x,y) < 1
\]

- 照射分量 \(i(x,y)\) 由光源决定，理论上非负且可大于 1（取决于光源强度与几何衰减）；
- 反射分量 \(r(x,y)\) 为反射率，取值在 0 与 1 之间：
    - \(r \to 0\)：全吸收（如黑色物体）；
    - \(r \to 1\)：全反射（如镜面或白色表面）。

由此得到图像亮度的取值范围：

\[
0 < f(x,y) < \infty
\]

经传感器量化后 \(f(x,y)\) 被限制为有限灰度级，例如 8 比特图像中 \(f \in [0, 255]\)。

---

### 1.3 **相机（可见光）成像原理**

#### **1.3.1 相机成像链**

相机成像的本质是**将三维场景中的光辐射信息记录为二维图像**。

一个完整的相机成像链可概括为**物理信号通路**与**几何数学建模**两个层次。

**（1）物理信号通路**：描述光辐射如何逐步转换为可视图像

$$
\text{场景辐亮度} \xrightarrow{\text{光学系统}} \text{像面辐照度} \xrightarrow{\text{光电转换}} \text{电荷/电压} \xrightarrow{\text{空间采样 + ADC 量化}} \text{RAW} \xrightarrow{\text{ISP}} \text{可视图像}
$$

* **场景辐亮度**：场景各方向单位面积、单位立体角的辐射通量，是成像的输入光能量分布。
* **光学系统**：通过镜头将光线聚焦到传感器感光面上，即把场景辐亮度映射为像面辐照度。
* **像面辐照度**：聚焦到感光面上的单位面积光功率，反映场景投影的明暗分布。
* **光电转换**：感光单元把入射光转为电荷，再读出为电压信号。
* **空间采样**：像素阵列对连续像面离散积分，得到各像素的电信号值。
* **ADC 量化**：把模拟电压离散化为数字码值，输出 RAW。
* **RAW**：未经处理的原始像素数据，是后续处理的输入。
* **ISP**：对 RAW 做去马赛克、白平衡、色彩校正、伽马等处理，得到可视图像。

**（2）几何数学建模**：通过针孔相机模型与畸变模型对成像几何进行抽象描述，为后续图像处理、标定与三维视觉提供数学基础。

$$
\text{针孔模型} + \text{畸变模型} \;\Longrightarrow\; \text{标定、三维视觉的数学基础}
$$

> 注意：几何建模不属于信号通路中的物理环节，而是对成像几何的数学抽象。

#### **1.3.2 相机的基本组成**

| 组成部分 | 主要功能 |
|---------|---------|
| 镜头（Lens） | 汇聚光线、成像，决定视场、焦距、光圈与像质 |
| 光圈（Aperture） | 控制进光量与景深 |
| 快门（Shutter） | 控制曝光时间 |
| 传感器（Sensor） | 将光能转换为电信号（CCD/CMOS） |
| 图像信号处理器（ISP） | 去马赛克、白平衡、伽马校正、降噪、锐化等 |
| 存储与显示 | 图像编码、保存与呈现 |

#### **1.3.3 光学成像**

**（1）小孔成像模型**

最简单的成像模型是**小孔成像（Pinhole Model）**：场景中一点发出的光线经小孔后，在像面上形成对应点，且物与像呈倒立关系。

设物距为 $u$，像距为 $v$，物体高度为 $h$，像高为 $h'$，则：

$$
\frac{h'}{h} = \frac{v}{u}
$$

小孔成像模型无聚焦模糊，但进光量极小。实际相机用透镜代替小孔以增加进光量。

**（2）薄透镜成像**

薄透镜成像满足**高斯成像公式**：

$$
\frac{1}{f} = \frac{1}{u} + \frac{1}{v}
$$

其中 $f$ 为焦距，$u$ 为物距，$v$ 为像距。横向放大率为：

$$
M = \frac{h'}{h} = -\frac{v}{u}
$$

负号表示像倒立。当 $u \to \infty$ 时，$v \to f$，即远景物体成像于焦平面附近。

**（3）视场与焦距**

镜头的视场角（FOV）与焦距、传感器尺寸有关。对于水平视场角 $\theta$：

$$
\theta = 2 \arctan\left(\frac{w}{2f}\right)
$$

其中 $w$ 为传感器感光面宽度。焦距越短，视场越大；焦距越长，视场越小（长焦）。

**（4）光圈与景深**

光圈大小用 **F 数（F-number）** 表示：

$$
N = \frac{f}{D}
$$

其中 $D$ 为光圈有效孔径。F 数越小，光圈越大，进光量越多。进光量与 $1/N^2$ 成正比。

**景深（Depth of Field, DOF）** 指成像清晰的前后物距范围。近似公式为：

$$
\text{DOF} \approx \frac{2 N c u^2}{f^2}
$$

其中 $c$ 为允许弥散圆直径。光圈越小（$N$ 越大）、焦距越短、物距越远，景深越大。

**（5）曝光量**

传感器接收的曝光量 $H$ 与场景照度 $E$、曝光时间 $t$ 有关：

$$
H = E \cdot t
$$

而像面照度与光圈、焦距、场景亮度相关：

$$
E \propto \frac{L \cdot \pi}{4 N^2}
$$

其中 $L$ 为场景亮度。综合可得曝光量与光圈、快门的关系，这即是摄影中**曝光三角**（光圈、快门、ISO）的物理基础。

#### **1.3.4 光电转换**

光电转换是传感器将光能转换为电信号的过程。

传感器感光单元（像素）吸收光子产生电子-空穴对，累积电荷量 $Q$ 与入射光子数近似成正比：

$$
Q = \eta \cdot \frac{P \cdot t}{h f} \cdot e
$$

- 其中 $\eta$ 为量子效率，$P$ 为入射光功率，$t$ 为积分时间，$hf$ 为单光子能量，$e$ 为电子电荷。

电荷经读出电路转换为电压：

$$
V = \frac{Q}{C}
$$

- 其中 $C$ 为像素电容。

#### **1.3.5 空间采样与 ADC 量化**

一幅连续图像 $f$，对坐标值进行数字化称为**空间采样**，对幅度值进行数字化称为**ADC 量化**。

* **空间采样**：由**像素阵列的几何排布**完成。传感器阵列对像面辐照度分布进行空间采样，每个像素对应一个采样点，从而将连续的空间分布离散化为 \(M \times N\) 的采样网格。
* **ADC 量化**：对电压进行**幅度量化**，发生在传感器读出之后，输出为**原始数字图像（RAW）**，而非最终可视图像。

$$
g(x,y) = \text{round}\left( \frac{V(x,y) - V_{\min}}{V_{\max} - V_{\min}} \cdot (2^b - 1) \right)
$$

其中 $b$ 为量化位数（如 8、10、12、16 bit），$g(x,y)$ 为数字灰度值。

#### **1.3.6 RAW 域与 ISP 处理**

ISP（图像信号处理器）主要对图像传感器经 ADC 输出的**数字 RAW 数据**进行处理，最终输出可视图像或中间图像格式。

ISP 典型流程：

1. **黑电平校正（Black Level Correction）**：减去暗电流偏置与光学黑电平。
2. **镜头阴影校正（Lens Shading Correction, LSC）**：补偿镜头渐晕与色彩阴影。
3. **坏点校正（Defect Pixel Correction）**：修复缺陷像素。
4. **RAW 域降噪**：在去马赛克前抑制传感器噪声。
5. **白平衡（White Balance）**：校正光源色温导致的色偏，可在 RAW 域或去马赛克后以通道增益形式实现。
6. **去马赛克（Demosaicing）**：由 CFA 数据恢复每像素 RGB。
7. **色彩校正矩阵（Color Correction Matrix, CCM）**：使颜色更接近人眼感知。
8. **色差校正（Chromatic Aberration Correction）**：抑制镜头色差。
9. **降噪与锐化**：通常分多级进行，包括去马赛克后降噪、YUV 域降噪、时域降噪以及锐化；锐化常在 YUV 域或输出前完成。
10. **伽马校正 / 色调映射**：将线性光强映射到显示域或压缩动态范围；现代系统常采用色调映射与传输函数，而非简单伽马。
11. **颜色空间转换**：如 RGB → YUV，便于显示、压缩或编码。
12. **输出**：可输出 8/10/12/16 bit 图像或 YUV/RAW 等中间格式；视频编码通常由后续编码器完成，不属于 ISP 核心职责。

此外，ISP 通常还包含 **3A 统计**（自动曝光 AE、自动白平衡 AWB、自动对焦 AF）、**HDR 合成/局部色调映射**、**去噪中的时域降噪**等功能。

> 说明：
> 
> 上述流程为教学性概述。实际 ISP 的模块划分、处理顺序、位深与输出格式因厂商和平台而异，部分步骤可合并、调序或省略。
> 
> 现代 ISP 链路前端通常还包含 CDS、模拟增益等**模拟/混合信号处理**，ADC 之后才进入数字域；因此“ISP 完全在数字域处理 RAW”是一种简化说法。
> 
> CFA（颜色滤波阵列）位于传感器前端，最典型的是 **Bayer 阵列**，而去马赛克位于 ISP 内，二者分属不同环节，不应混为一谈。

#### **1.3.7 相机几何模型**

**（1）针孔相机模型**

数字图像处理中最常用的相机模型为**针孔模型**。设世界点 $P=(X,Y,Z)$，相机坐标为 $(X_c,Y_c,Z_c)$，则投影到归一化像平面：

$$
x = f\frac{X_c}{Z_c}, \quad y = f\frac{Y_c}{Z_c}
$$

写成矩阵形式（齐次坐标）：

$$
\begin{bmatrix} x \\ y \\ 1 \end{bmatrix}
= \frac{1}{Z_c}
\begin{bmatrix} f & 0 & c_x \\ 0 & f & c_y \\ 0 & 0 & 1 \end{bmatrix}
\begin{bmatrix} X_c \\ Y_c \\ Z_c \end{bmatrix}
$$

其中 $c_x, c_y$ 为主点坐标。完整模型还包含世界坐标到相机坐标的旋转 $R$ 与平移 $t$：

$$
\begin{bmatrix} x \\ y \\ 1 \end{bmatrix}
= K [R \mid t]
\begin{bmatrix} X \\ Y \\ Z \\ 1 \end{bmatrix}
$$

$K$ 为内参矩阵，$[R\mid t]$ 为外参矩阵。

针孔相机（Pinhole Camera）通过投影变换，可以将三维相机（Camera）坐标转换为二维的图像坐标，这个变换矩阵是相机的内在属性，称为相机内参（Camera Intrinsic） K。

> yaw 航向角，pitch 俯仰角，roll 翻滾角。

**（2）镜头畸变**

实际镜头存在**径向畸变**与**切向畸变**。径向畸变模型为：

$$
\begin{aligned}
x_{\text{dist}} &= x(1 + k_1 r^2 + k_2 r^4 + k_3 r^6) \\
y_{\text{dist}} &= y(1 + k_1 r^2 + k_2 r^4 + k_3 r^6)
\end{aligned}
$$

其中 $r^2 = x^2 + y^2$。切向畸变由镜头与传感器不平行引起，常用 $p_1, p_2$ 参数描述。

---

### 1.4 **数字图像处理的基本数学工具**

1. **逐元素运算和矩阵运算**
2. **线性运算和非线性运算**（多幅图像）
3. **算术运算**（多幅图像）
     * 使用图像相加（平均）降低噪声。
     * 使用图像相减比较图像。
     * 使用图像相乘/相除校正阴影和模板。
4. **集合运算和逻辑运算**（多幅图像）：主要针对二值图像
     * 交集运算: $C = A\cap B$，满足交换律、结合律和分配律。
     * 并集运算: $C = A\cup B$。
5. **空间运算**（单幅图像）
     * **点运算（灰度变换）**：用一个变化函数 $T$ 改变图像中各个像素的灰度，即 $s = T(z)$
     * **邻域运算**：输出像素的值不仅取决于对应位置的输入像素值，还取决于该像素周围一定范围内（即邻域）的像素值。
     * **几何变换**

> 所谓的邻域运算就是对特定 roi 区域的所有像素做特定操作：以指定位置 $(x,y)$ 为中心，邻域范围为 $m\times n$，对这个范围内的像素取平均/求和/最大值/等得到输出像素。
>
> 典型的就是 CNN 模型中卷积层的滤波器操作。



## 二、OpenCV 图像基本操作

OpenCV 是一个轻量级高效的跨平台计算机视觉库，实现了图像处理和计算机视觉方面的多种通用算法。

Python 安装 OpenCV：`pip install opencv-python`

### 2.1 **OpenCV 常用数据结构**

OpenCV 用若干轻量结构表示点、颜色、尺寸、矩形与矩阵。Python 接口中，这些结构多以**元组或 NumPy 数组**表示；C++ 接口中则为对应的**类**。

- 点 Point

=== "Python"

    ```python
    p = (160, 160) # 元组
    ```

=== "C++"

    ```C++
    Point p(10, 20);               // 整型点 (x, y)
    Point2f pf(1.5f, 2.5f);        // 浮点
    Point2d pd(1.5, 2.5);          // 双精度
    // 访问
    int x = p.x, y = p.y;
    ```

- 颜色 Scalar

=== "Python"

    ```python
    color = (255, 0, 0)            # BGR 蓝色元组
    cv2.circle(img, (50, 50), 20, color, -1)
    ```

=== "C++"

    ```C++
    Scalar color(255, 0, 0);       // BGR 蓝色
    circle(img, Point(50,50), 20, color, -1);
    ```

- 尺寸 Size

=== "Python"

    ```python
    s = (640, 480)                 # (width, height)
    w, h = s
    # numpy 形状则是 (height, width, channels)
    h, w = img.shape[:2]
    ```

=== "C++"

    ```C++
    Size s(640, 480);              // width, height
    int w = s.width, h = s.height;
    ```

- 矩形 Rect

=== "Python"

    ```python
    cv2.rectangle(img, (20,20), (150,250), (255,0,0), 2)

    x, y, w, h = 10, 20, 100, 50
    roi = img[y:y+h, x:x+w]        # 直接用切片裁剪
    # 无内建 Rect 类，通常用元组 (x, y, w, h)
    ```

=== "C++"

    ```C++
    Rect r(10, 20, 100, 50);       // x, y, width, height
    Rect r2(p, s);                 // 点 + 尺寸
    // 属性
    r.x; r.y; r.width; r.height;
    r.tl(); r.br();                // 左上角、右下角
    r.area();                      // 面积
    Mat roi = img(r);              // 裁剪 ROI

    Rect rect = rect1 & rect2; #求两矩形交集
    Rect rect = rect1 | rect2; #求两矩形并集
    Rect rectShift = rect + point; #矩形平移
    Rect rect = rect1 + size; #矩形缩放
    ```

- 矩阵 Mat

=== "Python"

    ```python
    import cv2
    import numpy as np
   
    img = cv2.imread("test.jpg")           # BGR 三通道 ndarray
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
   
    m = np.zeros((3, 3), dtype=np.uint8)   # 全 0
    f = np.zeros((3, 3), dtype=np.float32)
    e = np.eye(3, dtype=np.float64)
   
    # 访问像素（注意顺序是 [y, x]）
    img[y, x, 0] = 255                     # B 通道
    gray[y, x] = 128
    ```

=== "C++"

    ```C++
    #include <opencv2/opencv.hpp>
    using namespace cv;
   
    Mat img = imread("test.jpg");          // 读图，BGR 三通道
    Mat gray;
    cvtColor(img, gray, COLOR_BGR2GRAY);   // 灰度图
   
    Mat m(3, 3, CV_8UC1, Scalar(0));       // 3x3 单通道，全 0
    Mat f = Mat::zeros(3, 3, CV_32F);      // 全 0 浮点
    Mat e = Mat::eye(3, 3, CV_64F);        // 单位矩阵

    # 访问像素（注意顺序是 [y, x]）
    img[y, x, 0] = 255                     # B 通道
    gray[y, x] = 128
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

#### **5.4.1 图像灰度化**

图像灰度化是将一幅彩色图像转换为灰度化图像的过程。彩色图像通常包括 R、G、B 三个分量，分别显示出红绿蓝等各种颜色，
灰度化就是使彩色图像的 R、G、B 三个分量相等的过程。

**加权平均法（亮度法，Luminance）—— 最标准**

基于人眼视觉特性（人眼对绿色最敏感、蓝色最不敏感）:

$$Gray = 0.299 R + 0.587 G + 0.114 B$$

这是 **ITU-R BT.601** 标准,也是 OpenCV `cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)` 实际采用的公式（注意 OpenCV 读入是 BGR 顺序,内部按 R/G/B 权重计算）。


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

## 八、图像灰度变换

### 8.1 **空间域图像处理：灰度变换、空间滤波**

空间域指的是图像平面本身，空间域中的图像处理方法是直接对图像中的像素进行处理。空间域图像处理的两个主要类别是:

* **灰度变换**: 如对比度处理、阈值处理等任务，直接对图像的每个像素进行操作。
* **空间滤波**: 如图像平滑和锐化，对图像中的每个像素的邻域进行操作。

> 空间域技术直接操作图像中的像素，而频率域技术操作的是图像的傅立叶变换而非图像本身。

### 8.2 **灰度变换：线性与非线性**

灰度变换（点运算）是对**灰度图像**中**每一个像素独立**进行操作，输出像素值只取决于该位置的输入像素值，与周围像素无关。

* 与几何运算的区别：不会改变图像内像素点之间的空间位置关系。
* 与局部（领域）运算的区别：输入像素和输出像素一一对应。

**数学形式**：

$$s = T(r)$$

其中：

- $r$：输入灰度值（原始像素值）
- $s$：输出灰度值（变换后像素值）
- $T$：灰度变换函数

**灰度变换方法分类**：

按**映射函数的数学性质**分类：

- **线性灰度变换**：映射函数为线性：$s = a \cdot r + b$

| 子类 | 参数条件 | 效果 |
|---|---|---|
| 恒等变换 | $a=1,\ b=0$ | 图像不变 |
| 反色（负像）变换 | $a=-1,\ b=255$ | 黑白反转 |
| 对比度拉伸 | $a>1$ | 扩展动态范围 |
| 对比度压缩 | $0<a<1$ | 压缩动态范围 |
| 灰度平移 | $a=1,\ b\neq0$ | 整体变亮/变暗 |

- **非线性灰度变换**：映射函数为非线性
    * **对数变换**：$s = c \cdot \log(1+r)$，扩展暗部、压缩亮部，用于显示频谱等
    * **幂律（伽马）变换**：$s = c \cdot r^\gamma$，$\gamma<1$ 提亮暗部，$\gamma>1$ 压暗亮部，最常用
    * **指数（反对数）变换**：与对数互补，扩展亮部、压缩暗部
    * **S 形变换**（Sigmoid）：增强中间灰度对比度，压缩两端
    * **阈值化**：二值化，用于图像分割
    * **直方图处理**：直方图均衡化、直方图规定化，以累积分布函数为映射，本质属非线性

按**分段结构**分类：

- **全局变换**：整幅图像使用统一映射函数
- **分段变换**：按灰度区间分段处理，如分段线性（对比度拉伸、灰度切分）、分段非线性

按**处理对象与实现方式**分类：

- **逐点运算**：仅依赖当前像素灰度值（上述全部属于此类）
- **查表变换（LUT）**：工程实现方式，预计算映射表，不改变分类本质

### 8.3 **线性灰度变换**

线性灰度变换是最基础的**点运算**（逐像素操作），通过一个线性函数把原灰度值映射到新灰度值，用于**调整图像对比度和亮度**。

$$
s = a \cdot r + b
$$

参数 $a$ 控制对比度：

| $a$ 取值 | 效果 |
|---------|------|
| $a > 1$ | 对比度**增强**(亮的更亮、暗的更暗),斜率变陡 |
| $0 < a < 1$ | 对比度**减弱**(灰度向中间压缩),斜率变缓 |
| $a = 1$ | 对比度不变 |
| $a < 0$ | 灰度**反转**(负片效果) |

参数 $b$ 控制亮度：

| $b$ 取值 | 效果 |
|---------|------|
| $b > 0$ | 整体**变亮**(灰度上移) |
| $b < 0$ | 整体**变暗**(灰度下移) |
| $b = 0$ | 亮度不变 |

> 约束：输出通常需裁剪到 $[0, 255]$,即 $g = \text{clip}(a f + b,\ 0, 255)$。


### 8.4 **对数变换**

对数变换是一类典型的**非线性灰度变换**，其映射函数为：

$$
s = c \cdot \log(1 + r)
$$

其中：

- $c$：尺度常数（归一化系数），用于控制输出动态范围
- $\log$：通常取自然对数 $\ln$，也可取以 10 为底的对数

**作用**：**扩展低灰度（暗部）细节，压缩高灰度（亮部）范围**。

**尺度常数 c 的确定**：

为保证输出落在有效灰度范围（如 8 位图像的 $[0,255]$），常取：

$$
c = \frac{255}{\log(1 + r_{\max})}
$$

其中 $r_{\max}$ 为输入图像的最大灰度值（8 位图像通常为 255）。这样可将 $r_{\max}$ 映射到 255，实现动态范围的充分利用。

**典型应用场景**：

- **频谱图显示**：傅里叶变换得到的幅度谱动态范围极大（中心亮、四周暗），直接显示几乎全黑，经对数变换后可清晰显示频谱结构，这是对数变换最经典的应用。
- **高动态范围（HDR）图像显示**：将大动态范围数据压缩到显示器可呈现的范围。
- **暗部细节增强**：医学影像、夜视图像、遥感图像等暗部信息丰富的场景。
- **光照不均校正的预处理**：与后续处理配合使用。

### 8.5 **幂律（伽马）变换**

幂律变换（又称伽马变换、幂次变换）是应用最广泛的**非线性灰度变换**之一，其映射函数为：

$$
s = c \cdot r^{\gamma}
$$

其中：

- $c$：尺度常数（归一化系数），用于控制输出动态范围
- $\gamma$：伽马参数（幂指数），控制变换曲线的弯曲方向和程度，是幂律变换的核心调节量

**作用**：**$\gamma < 1$ 扩展暗部、$\gamma > 1$ 扩展亮部**，$\gamma$ 可连续调节，过渡平滑。

**尺度常数 c 的确定**：

$$
c = \frac{255}{r_{\max}^{\gamma}}
$$

> 说明：当输入按 $[0,1]$ 归一化时（即 $r \in [0,1]$），通常取 $c=1$，此时 $s = r^{\gamma}$，形式最简洁，输出再按需乘以 255 还原；
> 
> 当处理 8 位图像（$r \in [0,255]$）时，需引入 $c$ 做尺度还原。

**典型应用场景：**

- **伽马校正（Gamma Correction）**：显示器、相机传感器对人眼亮度的响应是非线性的，用 $\gamma$ 变换做预补偿，使显示效果符合人眼感知，是图像显示与成像链路的标准环节。
- **图像增强**：对偏暗图像取 $\gamma<1$ 提亮暗部；对偏亮/雾感图像取 $\gamma>1$ 压暗、增强层次。
- **HDR 与色调映射**：将大动态范围数据压缩映射到显示范围。
- **医学影像、遥感图像**：调节暗部或亮部细节的可见性。
- **与对数变换互补使用**：根据目标区域（暗部或亮部）选择合适变换。

### 8.6 **指数（反对数）变换**

指数变换（又称反对数变换、反对数变换）是与对数变换**互补**的一类**非线性灰度变换**，其映射函数为：

$$
s = c \cdot \left( e^{k r} - 1 \right)
$$

其中：

- $c$：尺度常数（归一化系数），用于控制输出动态范围
- $k$：指数参数（增长系数），控制曲线弯曲程度，是变换的核心调节量

**作用**：**压缩低灰度（暗部）范围，扩展高灰度（亮部）细节**，与对数变换的作用方向完全互补。

**尺度常数 c 的确定**：

$$
c = \frac{255}{e^{k \cdot r_{\max}} - 1}
$$

**典型应用场景：**

- **显示端还原对数压缩数据**：数据在采集/传输端经对数变换压缩后，显示端用指数（反对数）变换还原，恢复亮部层次（如部分 HDR 显示流程）。
- **亮部细节增强**：对整体偏亮、亮部信息丰富的图像（如逆光场景中的高光区、某些医学影像）做增强。
- **曝光校正**：配合对数变换做双向调节，根据图像明暗分布选择合适的变换方向。
- **传感器/显示非线性响应的建模**：与伽马校正配合描述成像链路的非线性特性。

### 8.7 **阈值化（基于阈值的图像分割）**

阈值化（又称二值化）是一类特殊的**非线性灰度变换**，其映射函数为分段常值函数，将连续灰度压缩为两个（或多个）离散等级。

$$
s =
\begin{cases}
s_1, & r > T \\[4pt]
s_0, & r \le T
\end{cases}
$$

其中：

- $T$：阈值（分割门限），是变换的唯一核心参数
- $s_1, s_0$：输出高低两个等级（二值化通常取 $255$ 与 $0$）

> 注意：阈值化本质上是**图像分割**手段，而非增强手段。

**阈值 T 的确定方法**

阈值化效果几乎完全取决于 $T$ 的选取，常见方法包括：

| 方法 | 思路 | 特点 |
|---|---|---|
| 手动/经验阈值 | 人工指定固定 $T$ | 简单，但依赖先验，适应性差 |
| **Otsu 法（大津法）** | 最大化类间方差，自动选取使前景/背景分离度最大的 $T$ | 最经典的自适应单阈值方法，无需先验 |
| 迭代法 | 以均值迭代逼近最优阈值 | 计算简单，对双峰直方图有效 |
| 直方图双峰法 | 取直方图两峰之间谷底处灰度作为 $T$ | 直观，要求直方图呈明显双峰 |
| 自适应/局部阈值 | 按局部邻域统计量逐像素确定 $T$（如局部均值、Sauvola 法） | 适合光照不均图像 |
| 多阈值 | 设多个门限 $T_1<T_2<\cdots$，输出多等级 | 用于多类分割 |

> Otsu 法是最常用的自动阈值方法，其原理是遍历候选 $T$，使前景与背景两类像素的**类间方差最大**（等价于类内方差最小），在直方图双峰明显时效果良好。

#### **8.7.1 固定阈值化**

**固定阈值化函数**：`retval, dst = cv2.threshold(src, thresh, maxval, type)`

| 参数 | 含义 |
|---|---|
| `src` | 输入图像，**必须为单通道**（通常先转灰度） |
| `thresh` | 阈值 $T$ |
| `maxval` | 满足条件时赋予的最大值（二值化时通常为 255） |
| `type` | 阈值化类型（见下节），决定映射规则 |
| `retval` | 返回的实际使用阈值（用 Otsu/三角法时返回自动计算的 $T$） |
| `dst` | 输出图像，尺寸与类型同 `src` |

**阈值化类型（type 参数）**

OpenCV 提供 5 种基础类型，外加 2 种自动阈值标志：

| 类型常量 | 映射规则 | 说明 |
|---|---|---|
| `cv2.THRESH_BINARY` | $s = \begin{cases} \text{maxval}, & r > T \\ 0, & r \le T \end{cases}$ | 最常用二值化 |
| `cv2.THRESH_BINARY_INV` | $s = \begin{cases} 0, & r > T \\ \text{maxval}, & r \le T \end{cases}$ | 反相二值化 |
| `cv2.THRESH_TRUNC` | $s = \begin{cases} T, & r > T \\ r, & r \le T \end{cases}$ | 截断，超过阈值压为 $T$ |
| `cv2.THRESH_TOZERO` | $s = \begin{cases} r, & r > T \\ 0, & r \le T \end{cases}$ | 低于阈值置零 |
| `cv2.THRESH_TOZERO_INV` | $s = \begin{cases} 0, & r > T \\ r, & r \le T \end{cases}$ | 高于阈值置零 |
| `cv2.THRESH_OTSU` | 自动选 $T$（需与上述基础类型按位或组合） | 大津法，仅适用 8 位单通道 |
| `cv2.THRESH_TRIANGLE` | 自动选 $T$（三角法，需组合使用） | 适合直方图单峰情形 |

```python
import cv2

# 阈值化的输入为灰度图像
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

# Otsu 自动阈值（thresh 传 0 即可，返回值 ret 为自动算出的阈值）
ret, dst = cv2.threshold(img, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)

# 三角法自动阈值
ret, dst = cv2.threshold(img, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_TRIANGLE)
```

#### **8.7.2 自适应阈值化**

全局阈值对**光照不均**图像效果差，OpenCV 提供 `cv2.adaptiveThreshold()` 按局部邻域逐像素计算阈值：

**自适应阈值化函数**：`dst = cv2.adaptiveThreshold(src, maxValue, adaptiveMethod, thresholdType, blockSize, C)`

| 参数 | 含义 |
|---|---|
| `src` | 8 位单通道输入 |
| `maxValue` | 满足条件时赋予的值（同 `maxval`） |
| `adaptiveMethod` | `cv2.ADAPTIVE_THRESH_MEAN_C`（邻域均值）或 `cv2.ADAPTIVE_THRESH_GAUSSIAN_C`（邻域高斯加权） |
| `thresholdType` | 仅支持 `THRESH_BINARY` 或 `THRESH_BINARY_INV` |
| `blockSize` | 邻域窗口大小，**必须为奇数**（如 11、21） |
| `C` | 从邻域统计量中减去的常数，用于微调阈值 |

**自动求阈值 T 的原理**：根据参数 `adaptiveMethod` 的选取：

- 均值法：$T(x,y) = \text{mean}(\text{邻域}) - C$
- 高斯法：$T(x,y) = \text{GaussianWeightedMean}(\text{邻域}) - C$

```python
import cv2

img = cv2.imread("input.png")
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)   # 1. 转灰度（threshold 要求单通道）

# 2. 可选：滤波去噪，改善阈值稳定性
gray = cv2.GaussianBlur(gray, (5, 5), 0)

# 3a. 全局 + Otsu 自动阈值
ret, binary = cv2.threshold(gray, 0, 255,
      cv2.THRESH_BINARY + cv2.THRESH_OTSU)

# 3b. 或：光照不均时用自适应阈值
# binary = cv2.adaptiveThreshold(gray, 255,
#         cv2.ADAPTIVE_THRESH_GAUSSIAN_C, cv2.THRESH_BINARY, 21, 5)

# 4. 后续处理：形态学去噪、连通域分析、轮廓提取等
kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (3, 3))
binary = cv2.morphologyEx(binary, cv2.MORPH_OPEN, kernel)
```

### 8.7 **直方图处理**

#### **8.7.1 灰度直方图**

**灰度直方图**是图像灰度分布的统计描述：对每个灰度级 $k$（$k=0,1,\dots,L-1$，8 位图像 $L=256$），统计该灰度级出现的频次 $n_k$ 或频率。

**非归一化直方图（统计频次）**：

$$
h(r_k) = n_k
$$

**归一化直方图（统计频率）**：

$$
p(r_k) = \frac{n_k}{N}, \qquad \sum_{k=0}^{L-1} p(r_k) = 1
$$

其中 $N$ 为像素总数，$r_k$ 为第 $k$ 个灰度级。直方图只反映**各灰度出现频次**，丢失了像素的空间位置信息。

**4 种基本灰度特性的图像**：

![不同灰度特性的图像.png](images%2F%E4%B8%8D%E5%90%8C%E7%81%B0%E5%BA%A6%E7%89%B9%E6%80%A7%E7%9A%84%E5%9B%BE%E5%83%8F.png)

**直方图处理**：以直方图为依据、通过构造映射函数 $s = T(r)$ 改变灰度分布的一类方法。由于映射以**累积分布函数（CDF）**为基础，且一般不是线性函数，因此**本质上属于非线性灰度变换**。

$$
s = T(r)
$$

其中 $T$ 由输入直方图（或其 CDF）构造：

- **直方图均衡化（Histogram Equalization）**：以输入图像的 CDF 作为映射函数，使输出直方图**尽量均匀分布**
- **直方图规定化（Histogram Specification / Matching）**：指定目标直方图，构造映射使输出直方图**逼近目标分布**

#### **8.7.2 直方图计算与可视化**

**直方图计算函数**：`hist = cv2.calcHist(images, channels, mask, histSize, ranges)`

| 参数 | 含义 |
|---|---|
| `images` | 输入图像列表，如 `[gray]`（需加方括号） |
| `channels` | 参与统计的通道索引，灰度图为 `[0]`，BGR 为 `[0]`/`[1]`/`[2]` |
| `mask` | 掩膜，只统计掩膜非零区域；不需要时传 `None` |
| `histSize` | 直方图bin 数，如 `[256]` |
| `ranges` | 灰度取值范围，8 位图像为 `[0, 256]`（注意上界取 256） |
| 返回 `hist` | 形状为 `(histSize, 1)` 的数组，元素为该 bin 的像素数（float32） |

```python
import cv2
import numpy as np

# 计算灰度直方图
img = cv2.imread("input.png")
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

hist = cv2.calcHist([gray], [0], None, [256], [0, 256])

# 彩色图像分通道直方图
img = cv2.imread("input.png")
for i, color in enumerate(("b", "g", "r")):
   hist = cv2.calcHist([img], [i], None, [256], [0, 256])
   
# 带掩膜的直方图（只统计感兴趣区域）

mask = np.zeros(gray.shape, np.uint8)
mask[100:300, 100:300] = 255          # 只统计该矩形区域
hist = cv2.calcHist([gray], [0], mask, [256], [0, 256])
```

**直方图可视化**：Matplotlib 或 `cv2.line`/`cv2.polylines`

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

#### **8.7.3 直方图均衡化**

直方图均衡化（Histogram Equalization）是一种基于灰度直方图的**非线性灰度变换方法**。

**核心思想**：通过变换函数将原图像的灰度直方图重新映射，使其**近似均匀分布**，从而增强图像的全局对比度。

对归一化灰度 $r \in [0,1]$，连续形式的均衡化映射为：

$$
s = T(r) = \int_0^r p_r(w)\,dw = \text{CDF}(r)
$$

离散形式（8 位图像）为：

$$
s_k = (L-1)\sum_{j=0}^{k} p(r_j)
$$

即把每个灰度级映射为其**累积频率乘以 $(L-1)$**，再**取整**。

**直方图均衡化的计算步骤**：

1. **统计直方图**：统计每个灰度级 $r_k$ 出现的像素数 $n_k$，$k=0,1,\dots,L-1$。
2. **计算归一化直方图**：$p_r(r_k) = \frac{n_k}{N}$
3. **计算累积分布函数**：$\text{CDF}(k) = \sum_{j=0}^{k} p_r(r_j)$
4. **计算映射关系**：$s_k = \text{round}\left[(L-1)\cdot \text{CDF}(k)\right]$
5. **逐像素映射**：将原图中每个灰度值 $r_k$ 替换为 $s_k$，得到均衡化后的图像。

**直方图均衡化函数**：`dst = cv2.equalizeHist(src)`

- 全自动、无参数，对整体偏暗/偏亮、低对比度图像效果明显
- 可能**放大噪声**、改变整体亮度/色调；对含大片纯色背景（直方图尖峰）的图像易产生伪影
- 仅支持 8 位单通道；彩色图需先转灰度或对每个通道分别均衡化（分别均衡会改变色调，需谨慎）
- **全局**处理，对光照不均图像效果有限 → 改用 CLAHE

**自适应直方图均衡化函数**：`cv2.createCLAHE(clipLimit, tileGridSize)`

| 参数 | 含义 |
|---|---|
| `clipLimit` | 对比度限制阈值，越大增强越强、噪声放大越明显（默认 40.0 常见，`2.0` 为温和设置） |
| `tileGridSize` | 分块网格大小，图像被划分为该网格大小的子块分别均衡化（常用 8×8） |

**原理**：把图像分成若干子块，**各自做均衡化**，再做块间双线性插值消除块边界；`clipLimit` 用于裁剪直方图尖峰、**限制对比度增强幅度**，从而抑制噪声放大与伪影。

```python
gray = cv2.cvtColor(cv2.imread("input.png"), cv2.COLOR_BGR2GRAY)
clahe = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8, 8))
dst = clahe.apply(gray)
```

#### **8.7.4 直方图规定化（匹配）**

直方图规定化（Histogram Specification），又称直方图匹配（Histogram Matching）。

**核心思想**：通过变换函数将原图像的灰度直方图映射为用户指定的目标直方图，从而实现对图像对比度的灵活控制。

> 直方图均衡化是规定化的一个特例（目标直方图为均匀分布）

**步骤**：

1. 对输入做均衡化：$s = T(r) = \text{CDF}_r(r)$
2. 对目标直方图求其均衡化映射：$v = G(z) = \text{CDF}_z(z)$
3. 因均衡化后 $s$ 与 $v$ 都应服从均匀分布，令 $s = v$，得 $z = G^{-1}(s) = G^{-1}(T(r))$

即映射为 $T \to G^{-1}$ 的复合。离散实现中，通常对每个 $s_k$ 在目标 CDF 中找**最接近**的 $z$。

**用途**：使图像具有期望的灰度分布（如统一多幅图像色调、匹配参考图像风格），比均衡化更可控。

OpenCV 未直接提供规定化函数，可按"**均衡化 + 逆映射**"思路手动实现：

```python
import cv2
import numpy as np

src = cv2.cvtColor(cv2.imread("input.png"), cv2.COLOR_BGR2GRAY)

# 1. 输入图像 CDF（均衡化映射）
hist_src = cv2.calcHist([src], [0], None, [256], [0, 256]).ravel()
cdf_src = np.cumsum(hist_src) / src.size

# 2. 目标直方图（此处示例：均匀分布；也可自定义/取自参考图）
hist_ref = np.ones(256) / 256
cdf_ref = np.cumsum(hist_ref)

# 3. 构造查找表：对每个灰度级，在目标 CDF 中找最接近的 z
lut = np.zeros(256, np.uint8)
for k in range(256):
  z = np.argmin(np.abs(cdf_ref - cdf_src[k]))
  lut[k] = z

# 4. 应用映射
matched = cv2.LUT(src, lut)
```

> 要点：`cv2.LUT()` 用于按查找表做逐像素映射，是实现任意灰度变换（含规定化）的通用工具；目标直方图可来自参考图像（先 `calcHist` 再归一化），实现"风格/色调匹配"。


## 九、空间滤波

### 9.1 **空间滤波基础**

**空间滤波（spatial filtering）** 是数字图像处理中最基本的一类**邻域运算**：
用一个**滤波核（kernel / mask / 模板）** 在图像上逐像素滑动，对核覆盖的邻域像素值按某种规则加权组合，得到输出图像对应位置的像素值。
其本质是利用**像素与其邻域的空间关系**来改变图像（平滑、锐化、去噪、增强边缘等）。

**基本模型**：

设输入图像为 \(f(x,y)\)，滤波核为 \(w(s,t)\)（大小为 \(m \times n\)，通常 \(m,n\) 为奇数以便有中心），输出图像为 \(g(x,y)\)。线性空间滤波的一般形式为：

$$
g(x,y) = \sum_{s=-a}^{a}\sum_{t=-b}^{b} w(s,t)\, f(x+s,\, y+t)
$$

其中 \(a=(m-1)/2\)、\(b=(n-1)/2\)。这一运算在数学上就是**离散卷积（更准确地说是相关）**：核与图像局部区域逐元素相乘再求和，结果作为中心像素的新值。

> 冈萨雷斯书中把“核滑动 + 加权求和”称为**空间滤波**，把核称为**空间滤波器（spatial filter）**；线性情形对应卷积/相关，非线性情形（如中值滤波）则按排序等规则取值。
 
**空间滤波分类**：

- 线性空间滤波：输出是邻域像素的线性加权和，核心是卷积。
    - **平滑（低通）**：均值滤波、高斯滤波、加权平均滤波——抑制噪声、模糊细节。
    - **锐化（高通）**：拉普拉斯核、Sobel/Prewitt、非锐化掩蔽——增强边缘与细节。
- 非线性空间滤波：输出不是邻域像素的线性组合。
    - 基于排序/集合：中值滤波、最大/最小值滤波、形态学处理
    - 基于自适应权重：双边滤波


### 9.2 **图像平滑**

#### **9.2.1 均值滤波（归一化盒式滤波）**

均值滤波输出图像的每一个像素值是其周围 M×M 个像素值的平均值。

**均值滤波（\(3\times3\)）**：

$$
\frac{1}{9}\begin{bmatrix}1&1&1\\1&1&1\\1&1&1\end{bmatrix}
$$

* 优点：计算速度较快，对周期性的干扰噪声有很好的抑制作用，
* 缺点：不能很好地保护图像的细节，在图像去噪的同时，也破坏了图像的细节部分，从而使图像变得模糊。

**均值滤波函数**：`dst = cv2.blur(src, ksize[, anchor[, borderType]])`

```python
dst = cv2.blur(img, (5, 5))
```

#### **9.2.2 盒式滤波（归一化可选）**

**盒式滤波**的滤波核内所有系数相等。

**盒式滤波函数**：`dst = cv2.boxFilter(src, ddepth, ksize[, anchor[, normalize[, borderType]]])`

- `normalize=True`：等价于 `blur`
- `normalize=False`：核不除以面积，用于计算邻域和（如积分图替代、光流）

```python
dst = cv2.boxFilter(img, -1, (3, 3), normalize=True)
```

#### **9.2.3 高斯滤波**

实际应用中要求卷积核是各向同性的（圆对称），其响应与方向无关。

高斯核是唯一可分离的圆对称核，对于**抑制服从正态分布的噪声**非常有效。

**数学原理**：

$$w(s,t) = G(s,t) = Ke^{-\frac{s^2+t^2}{2\sigma^2}}$$

令$r=[s^2+t^2]^{1/2}$，上式可写为

$$G(r) = Ke^{-\frac{r^2}{2\sigma^2}}$$

这个函数依然是圆对称的，变量 $r$ 表示从中心到函数 $G$ 上任意一点的距离，它必须是正数且是奇数。

希望产生**更均匀的平滑结果**时，通常使用高斯核平滑；盒式核平滑则是硬过渡。

**高斯滤波核（\(3\times3\)，近似）**：

$$
\frac{1}{16}\begin{bmatrix}1&2&1\\2&4&2\\1&2&1\end{bmatrix}
$$

**高斯滤波函数**：`dst = cv2.GaussianBlur(src, ksize, sigmaX[, sigmaY[, borderType]])`

- `ksize`：核大小，必须为正奇数，如 `(5, 5)`；也可 `(0,0)` 由 `sigmaX` 自动推算
- `sigmaX`、`sigmaY`：X/Y 方向标准差；`sigmaY=0` 时等于 `sigmaX`

```python
dst = cv2.GaussianBlur(img, (5, 5), sigmaX=1.5)
```

#### **9.2.4 中值滤波**

中值滤波是一种**非线性空间滤波**，用邻域内像素值的**中位数**替代中心像素值。它对**椒盐噪声（脉冲噪声）**有极好的抑制效果，同时能较好保留边缘。

> 注意：中值滤波**不属于线性滤波**，不能用卷积核 / `filter2D` 实现，需用专门的 `cv2.medianBlur()`。

**数学原理**

设邻域窗口大小为 $m \times n$（通常为奇数），窗口内像素值排序后取中间值：

$$
g(x,y) = \operatorname{median}\big\{\, f(x+i,\; y+j) \;\big|\; i \in [-a,a],\; j \in [-b,b] \,\big\}
$$

其中 $m = 2a+1$，$n = 2b+1$。

**中值滤波函数**：`cv2.medianBlur(src, ksize)`

#### **9.2.5 双边滤波**

双边滤波是一种**非线性保边平滑滤波**，同时考虑**空间距离**和**像素值差异**两个因素。它在平滑图像的同时能**保留边缘**，是"保边去噪"的经典方法。

> 与高斯滤波只考虑空间距离不同，双边滤波额外引入**值域（range）权重**，使值差异大的像素（边缘两侧）不被混合。

**数学原理**

双边滤波的输出为加权平均：

$$
g(x,y) = \frac{1}{W_p} \sum_{i,j} f(i,j) \cdot w_s(i,j) \cdot w_r(i,j)
$$

其中归一化因子：

$$
W_p = \sum_{i,j} w_s(i,j) \cdot w_r(i,j)
$$

**两个权重核：**

1. **空间域权重**（spatial，高斯）：

$$
w_s(i,j) = \exp\left(-\frac{(i-x)^2 + (j-y)^2}{2\sigma_s^2}\right)
$$

2. **值域权重**（range，高斯）：

$$
w_r(i,j) = \exp\left(-\frac{\|f(i,j) - f(x,y)\|^2}{2\sigma_r^2}\right)
$$

**直观理解：**

- $w_s$：离中心越远，权重越小（同高斯滤波）。
- $w_r$：像素值差越大，权重越小 → **边缘处值差大，权重趋近 0，边缘不被模糊**。

**双边滤波函数**：`dst = cv2.bilateralFilter(src, d, sigmaColor, sigmaSpace[, dst[, borderType]])`

```python
import cv2

img = cv2.imread('input.jpg')

# 双边滤波保边去噪
dst = cv2.bilateralFilter(img, d=9, sigmaColor=75, sigmaSpace=75)

# 对比：高斯滤波会模糊边缘
gauss = cv2.GaussianBlur(img, (9, 9), 2.0)
```


### 9.3 **图像锐化与边缘检测**

图像锐化是一种**增强图像边缘和细节**的处理技术，通过突出图像中的高频成分（边缘、纹理），提升局部对比，使图像看起来更清晰。

> 锐化的本质是**高频增强**：边缘处像素值变化剧烈，锐化放大这种变化，使边缘更"陡峭"。

**图像锐化方法**：

- **基于微分算子的锐化**：利用一阶微分（梯度，如 Roberts、Prewitt、Sobel 的梯度响应）或二阶微分（Laplacian）构造高频信号并叠加回原图。
    - 其中，Laplacian 叠加是最典型的微分锐化；
    - 一阶梯度算子主要用于边缘检测，其响应也可用于增强边缘。
- **非锐化掩蔽**：基于原图与模糊图的差值增强细节，是实际应用中最常用的锐化方法之一。

**图像锐化与边缘检测的区别**：同源于"微分/高频增强"这一数学基础，但目标不同：

- 边缘检测：目标是"定位"——找出边缘的位置，通常输出二值或稀疏的边缘图。
- 图像锐化：目标是"增强"——把边缘和细节叠加回原图，输出仍是完整图像。

#### **9.3.1 Roberts 算子**

罗伯特交叉梯度（Roberts）算子是最早的边缘检测算子之一，采用 **2×2** 的局部差分算子，利用对角线方向相邻两像素之差来近似梯度幅值。

两个卷积核分别为：

$$G_x = \begin{bmatrix} 1 & 0 \\ 0 & -1 \end{bmatrix}, \quad G_y = \begin{bmatrix} 0 & 1 \\ -1 & 0 \end{bmatrix}$$

梯度幅值为：

$$G(x,y) = |G_x| + |G_y|$$

或使用平方和开方：

$$G(x,y) = \sqrt{G_x^2 + G_y^2}$$

特点：

- 计算简单、速度快。
- 对噪声较敏感，抗噪能力差。
- 边缘定位精度较高，但容易丢失部分边缘。
- 适用于边缘明显且噪声较少的图像。

OpenCV 没有直接的 Roberts 函数，需用 `filter2D` 手动实现。

```python
import cv2
import numpy as np

img = cv2.imread('input.jpg', cv2.IMREAD_GRAYSCALE)

# Roberts 两个核
gx = np.array([[ 1,  0],
               [ 0, -1]], np.float32)
gy = np.array([[ 0,  1],
               [-1,  0]], np.float32)

# 分别卷积
rx = cv2.filter2D(img, cv2.CV_32F, gx)
ry = cv2.filter2D(img, cv2.CV_32F, gy)

# 梯度幅值（两种方式任选）
mag = cv2.magnitude(rx, ry)                     # 精确：sqrt(gx^2+gy^2)
mag_abs = cv2.convertScaleAbs(rx) + cv2.convertScaleAbs(ry)  # 近似：|gx|+|gy|

mag = np.clip(mag, 0, 255).astype(np.uint8)
cv2.imshow('Roberts', mag)
```


#### **9.3.2 Prewitt 算子**

Prewitt 算子采用 **3×3** 的卷积核，分别在水平和垂直方向上计算差分，并引入一定的平滑作用。

$$G_x = \begin{bmatrix} -1 & 0 & 1 \\ -1 & 0 & 1 \\ -1 & 0 & 1 \end{bmatrix}, \quad G_y = \begin{bmatrix} -1 & -1 & -1 \\ 0 & 0 & 0 \\ 1 & 1 & 1 \end{bmatrix}$$

梯度幅值：

$$G(x,y) = \sqrt{G_x^2 + G_y^2}$$

特点：

- 相比 Roberts 算子，具有更好的抗噪能力。
- 计算量适中，实现简单。
- 边缘定位较准确，但对细边缘的响应不如 Sobel。

OpenCV 没有直接的 Prewitt 函数（只有 Sobel/Scharr），需用 `filter2D` 手动实现。

```python
import cv2
import numpy as np

img = cv2.imread('input.jpg', cv2.IMREAD_GRAYSCALE)

# Prewitt 两个核
kx = np.array([[-1, 0, 1],
               [-1, 0, 1],
               [-1, 0, 1]], np.float32)
ky = np.array([[-1, -1, -1],
               [ 0,  0,  0],
               [ 1,  1,  1]], np.float32)

px = cv2.filter2D(img, cv2.CV_32F, kx)
py = cv2.filter2D(img, cv2.CV_32F, ky)

mag = cv2.magnitude(px, py)
mag = np.clip(mag, 0, 255).astype(np.uint8)
cv2.imshow('Prewitt', mag)
```

#### **9.3.3 Sobel 算子**

Sobel 算子同样是 **3×3** 卷积核，但在 Prewitt 的基础上对中心像素赋予更大权重，从而具有更强的平滑和抗噪能力。

$$G_x = \begin{bmatrix} -1 & 0 & 1 \\ -2 & 0 & 2 \\ -1 & 0 & 1 \end{bmatrix}, \quad G_y = \begin{bmatrix} -1 & -2 & -1 \\ 0 & 0 & 0 \\ 1 & 2 & 1 \end{bmatrix}$$

梯度幅值：

$$G(x,y) = \sqrt{G_x^2 + G_y^2}$$

特点：

- 抗噪能力优于 Roberts 和 Prewitt。
- 能较好地检测边缘方向信息。
- 边缘较粗，定位精度略低于 Roberts。
- 广泛应用于图像边缘检测与锐化预处理。

```python
sx = cv2.Sobel(img, cv2.CV_32F, 1, 0, ksize=3)
sy = cv2.Sobel(img, cv2.CV_32F, 0, 1, ksize=3)
```

#### **9.3.4 Scharr 算子**

Scharr 算子是对 Sobel 算子的改进，同样是一阶微分算子，采用 **3×3** 卷积核。其设计目标是在保持 3×3 小核的前提下，获得更好的**旋转对称性**和**梯度方向精度**。

Sobel 算子对中心行/列赋予权重 1、2、1，而 Scharr 算子的权重为 3、10、3，从而更精确地逼近理想微分：

$$G_x = \begin{bmatrix} -3 & 0 & 3 \\ -10 & 0 & 10 \\ -3 & 0 & 3 \end{bmatrix}, \quad G_y = \begin{bmatrix} -3 & -10 & -3 \\ 0 & 0 & 0 \\ 3 & 10 & 3 \end{bmatrix}$$

梯度幅值：

$$G(x,y) = \sqrt{G_x^2 + G_y^2}$$

特点:

- **精度高于 Sobel**：在 3×3 核中具有更优的梯度近似，尤其对细小边缘和斜向边缘响应更准确。
- **旋转对称性更好**：对任意方向边缘响应更一致。
- **计算量与 Sobel 相同**：核大小一致，速度几乎相同。
- **抗噪性**：与 Sobel 相近，仍对噪声较敏感。
- **典型用途**：当需要比 Sobel 更精确的边缘/梯度、但又不希望增大核尺寸时使用（如光流、特征点方向估计）。

```python
gx = cv2.Scharr(img, cv2.CV_32F, 1, 0)  # x 方向
gy = cv2.Scharr(img, cv2.CV_32F, 0, 1)  # y 方向
```

#### **9.3.4 Laplacian 算子与 Laplacian 锐化**

拉普拉斯（Laplacian）算子是二阶微分算子，**各向同性**（旋转不变），对边缘点和孤立点响应更强。

$$\nabla^2 f(x,y) = \frac{\partial^2 f}{\partial x^2} + \frac{\partial^2 f}{\partial y^2}$$

离散形式常用 **3×3** 卷积核表示：

$$L = \begin{bmatrix} 0 & 1 & 0 \\ 1 & -4 & 1 \\ 0 & 1 & 0 \end{bmatrix}$$

或包含对角方向的扩展形式：

$$L = \begin{bmatrix} 1 & 1 & 1 \\ 1 & -8 & 1 \\ 1 & 1 & 1 \end{bmatrix}$$

> 注意：核的符号可整体取反（中心为 4/8，周围为 -1），两者等价，只是输出的正负号相反。

特点：

- 各向同性，对任意方向的边缘均有响应。
- 对噪声非常敏感，通常会先进行高斯平滑（LoG 算子）。
- 能增强细节，但容易放大噪声。
- 常用于图像细节增强和边缘提取。

Laplacian 函数：`dst = cv2.Laplacian(src, ddepth[, ksize[, scale[, delta[, borderType]]]])`

```python
import cv2
import numpy as np

img = cv2.imread('input.jpg', cv2.IMREAD_GRAYSCALE)

# 用 CV_16S 得到有符号结果
lap = cv2.Laplacian(img, cv2.CV_16S, ksize=3)

# 取绝对值并转回 uint8 显示
lap_abs = cv2.convertScaleAbs(lap)
cv2.imshow('Laplacian', lap_abs)
```

**典型用法：Laplacian 锐化**

Laplacian 锐化通过将拉普拉斯算子与原图叠加实现：

$$g(x,y) = f(x,y) - c \cdot \nabla^2 f(x,y)$$

其中 $c$ 为锐化强度系数。当使用中心为负的核时，取减号；使用中心为正的核时取加号。

由于 Laplacian 对噪声敏感，通常**先高斯平滑再求 Laplacian**，即 **LoG（Laplacian of Gaussian）**：

```python
def Laplacian_of_laplacian(img, c=0.5, blur_ksize=5):
    img_f = img.astype(np.float32)
    smooth = cv2.GaussianBlur(img_f, (blur_ksize, blur_ksize), 0)
    log = cv2.Laplacian(smooth, cv2.CV_32F, ksize=1)  # ksize=1 用标准3x3核
    sharp = img_f - c * log # 注意符号，取决于核约定。如果换成 ksize=3 的 Sobel 式 Laplacian（核符号相反），- c*log 就会变成模糊而不是锐化。
    return np.clip(sharp, 0, 255).astype(np.uint8)
```

#### **9.3.5 非锐化掩蔽**

非锐化掩蔽（Unsharp Masking，USM）是一种经典的锐化技术，中文也常译作反锐化掩模/反锐化掩蔽、钝化掩蔽、模糊掩蔽。

**核心思想**：**原始图像减去其模糊版本得到高频细节（掩蔽信号），再将细节叠加回原图**。

**步骤**：

1. 对原图 $f(x,y)$ 进行低通滤波（如高斯模糊），得到模糊图像 $f_{\text{blur}}(x,y)$。
2. 计算掩蔽信号（高频细节）：$\text{mask}(x,y) = f(x,y) - f_{\text{blur}}(x,y)$
3. 将掩蔽信号按强度系数 $k$ 叠加回原图：$g(x,y) = f(x,y) + k \cdot \text{mask}(x,y)$
     - 其中 $k$ 为锐化强度（通常 $k > 0$，$k$ 越大锐化越强）。

```python
def unsharp_mask(image, sigma=1.0, k=1.0):
    """
    非锐化掩蔽（Unsharp Masking）

    参数:
        image : np.ndarray, 输入图像（BGR 或灰度，uint8）
        sigma : float, 高斯模糊的标准差，控制细节尺度（越大细节越粗）
        k     : float, 锐化强度系数（k>0 锐化，k<0 柔化）

    返回:
        sharpened : np.ndarray, 锐化后的图像（uint8）
    """
    # 1. 低通滤波（高斯模糊）
    blurred = cv2.GaussianBlur(image, (0, 0), sigma)

    # 2. 计算掩蔽信号（高频细节）
    #    注意：用 float32 避免 uint8 减法下溢
    mask = image.astype(np.float32) - blurred.astype(np.float32)

    # 3. 叠加回原图
    sharpened = image.astype(np.float32) + k * mask

    # 4. 裁剪并转回 uint8
    sharpened = np.clip(sharpened, 0, 255).astype(np.uint8)

    return sharpened
```

#### **9.3.6 Canny 边缘检测**

Canny 边缘检测由 John Canny 于 1986 年提出，是**工业界最经典、最常用的边缘检测算法**之一。它不是一个单一卷积核，而是一个**多阶段处理流程**。

Canny 提出了边缘检测应满足的**三个最优准则**：

1. **低错误率（Good Detection）**：尽可能检测真实边缘，不漏检、不误检。
2. **良好定位（Good Localization）**：检测到的边缘位置应尽可能接近真实边缘。
3. **单一响应（Single Response）**：每个真实边缘只应产生一个响应，避免多重边缘。

**算法流程**：

1. **高斯平滑（降噪）**：用高斯滤波器对图像卷积，抑制噪声。噪声是微分运算的大敌，平滑是后续梯度计算可靠的前提。
2. **计算梯度幅值与方向**：通常用 Sobel 算子（也可用 Scharr、Prewitt）求 $x$、$y$ 方向梯度。
3. **非极大值抑制（NMS）**：沿梯度方向比较，仅保留局部极大值，把宽边缘"瘦身"为单像素宽。
     - **方向量化**：将梯度方向量化到 4 个方向（0°、45°、90°、135°），对应比较左右、两对角、上下邻域。
     - **判定规则**：当前像素幅值若大于其梯度方向上前后两个相邻像素幅值，则保留；否则置零。
4. **双阈值检测**：设高阈值 $T_H$、低阈值 $T_L$（一般 $T_H \approx 2\sim3\,T_L$），对 NMS 后的幅值图分类。
     - **强边缘**：$|G| > T_H$，确定为边缘，保留。
     - **弱边缘**：$T_L < |G| \le T_H$，待定，交给下一步连通性判断。
     - **非边缘**：$|G| \le T_L$，直接抑制。
     - **作用**：高阈值保证可靠性（低误检），低阈值保证不漏检弱边缘，二者平衡误检与漏检。
5. **边缘连接（滞后阈值 / Hysteresis）**：对弱边缘做连通性判断，把断续边缘接成连续边缘链。
     - **判定规则**：弱边缘像素若与任一强边缘像素在 8 邻域内连通，则升级为边缘保留；否则丢弃。
     - **实现方式**：常以强边缘为种子做连通域/BFS 生长，仅保留与种子连通的弱边缘。

```python
import cv2

img = cv2.imread("input.jpg", cv2.IMREAD_GRAYSCALE)

# 可选：先自己高斯平滑，再送入 Canny（Canny 内部也会平滑）
blurred = cv2.GaussianBlur(img, (5, 5), 1.4)

edges = cv2.Canny(
    blurred,
    threshold1=50,        # 低阈值 T_L
    threshold2=150,       # 高阈值 T_H
    apertureSize=3,       # 内部 Sobel 核大小（3/5/7）
    L2gradient=True       # True 用平方和开方，更精确；False 用 |Gx|+|Gy|
)
```

#### **9.3.7 LOG 边缘检测**

LoG（Laplacian of Gaussian，高斯拉普拉斯）边缘检测是一种**二阶微分边缘检测方法**。
它把**高斯平滑**与**拉普拉斯二阶微分**结合，解决了纯 Laplacian 算子对噪声极度敏感的问题，又称**墨西哥帽算子（Mexican Hat Operator）**

**算法流程**；

1. **高斯平滑（降噪）**：用高斯滤波器对图像卷积，抑制噪声。二阶微分对噪声极其敏感，平滑是 LoG 可靠的前提。
2. **拉普拉斯二阶微分**：对平滑图求二阶导，得到 LoG 响应。
3. **等效 LoG 核（可选优化）**：由于卷积与微分可交换，可直接用 $\nabla^2 G_\sigma$ 对原图卷积，一次完成平滑+微分。
     - **数学依据**：$\nabla^2 (G_\sigma * f) = (\nabla^2 G_\sigma) * f$。
     - **解析形式**：$\nabla^2 G_\sigma(x,y) = \frac{x^2+y^2-2\sigma^2}{\sigma^4} e^{-\frac{x^2+y^2}{2\sigma^2}}$
     - **常用 5×5 离散近似核**：$\text{LoG} \approx \begin{bmatrix} 0 & 0 & -1 & 0 & 0 \\ 0 & -1 & -2 & -1 & 0 \\ -1 & -2 & 16 & -2 & -1 \\ 0 & -1 & -2 & -1 & 0 \\ 0 & 0 & -1 & 0 & 0 \end{bmatrix}$
4. **过零点检测（Zero-Crossing）**：LoG 响应为零的位置对应边缘，通过检测过零点定位边缘。
     - **判定规则**：对每个像素，检查其 3×3 邻域内是否存在符号相反的相邻像素对；若存在，且两侧差值（过零斜率）足够大，则标记为边缘。
     - **阈值控制**：用过零斜率阈值抑制弱响应（噪声引起的伪过零）。
     - **作用**：把 LoG 的连续响应转化为二值边缘图，完成边缘定位。

```python
import cv2
import numpy as np

def log_edge(img, sigma=1.0, zero_cross_thresh=0):
    """
    LoG 边缘检测

    参数:
        img               : 灰度图 (uint8)
        sigma             : 高斯标准差，控制检测尺度
        zero_cross_thresh : 过零斜率阈值，越大边缘越少
    """
    # 1. 高斯平滑
    smooth = cv2.GaussianBlur(img, (0, 0), sigma).astype(np.float32)

    # 2. 拉普拉斯（ksize=1 用固定 3x3 四邻域核，中心为负）
    log = cv2.Laplacian(smooth, cv2.CV_32F, ksize=1)

    # 3. 过零点检测
    h, w = log.shape
    edges = np.zeros((h, w), np.uint8)
    for y in range(1, h - 1):
        for x in range(1, w - 1):
            patch = log[y-1:y+2, x-1:x+2]
            p = log[y, x]
            # 邻域内是否存在符号相反的像素
            if (patch.min() < 0 < patch.max()):
                # 过零斜率（两侧极差）需超过阈值
                if (patch.max() - patch.min()) > zero_cross_thresh:
                    edges[y, x] = 255
    return edges
```

**补充**：工程中常直接用 DoG（高斯差分） 近似 LoG，效率更高：

```python
def dog(img, sigma1=1.0, sigma2=1.6):
    g1 = cv2.GaussianBlur(img, (0, 0), sigma1).astype(np.float32)
    g2 = cv2.GaussianBlur(img, (0, 0), sigma2).astype(np.float32)
    return g1 - g2
```

#### **9.3.8 二值图像轮廓提取**

**轮廓**是连接所有连续点（沿边界）的曲线，是形状分析、目标提取、图像分割的基础工具。

`cv2.findContours()` 用于在**二值图像**中查找轮廓（contour）。

```python
import cv2
import numpy as np

img = cv2.imread("input.jpg")
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

# 1. 二值化
_, thresh = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY)

# 2. 查找轮廓
contours, hierarchy = cv2.findContours(
    thresh, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

print("轮廓数量:", len(contours))

# 3. 遍历轮廓，按面积筛选并绘制
result = img.copy()
for cnt in contours:
    area = cv2.contourArea(cnt)
    if area > 500:                                   # 过滤小噪点
        # 绘制轮廓
        cv2.drawContours(result, [cnt], -1, (0, 255, 0), 2)
        # 外接矩形
        x, y, w, h = cv2.boundingRect(cnt)
        cv2.rectangle(result, (x, y), (x + w, y + h), (255, 0, 0), 2)
        # 最小外接圆
        (cx, cy), r = cv2.minEnclosingCircle(cnt)
        cv2.circle(result, (int(cx), int(cy)), int(r), (0, 0, 255), 2)

cv2.imshow("Contours", result)
cv2.waitKey(0)
```

### 9.4 **形态学处理**

图像的腐蚀（Erosion）和膨胀（Dilation）是两种基本的形态学运算，主要用来寻找图像中的极小区域和极大区域。

#### **9.4.1 腐蚀**

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

#### **9.4.2 膨胀**

* 作用：扩大图像中的白色区域（前景）
* 函数：`cv2.dilate(src, kernel, dst=None, anchor=(-1,-1), iterations=1, borderType=..., borderValue=...)`
    * 参数与 `cv2.erode` 完全一致，只是效果相反。
* 应用场景：填补孔洞、连接断裂物体、加粗线条

#### **9.4.3 开运算**

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

#### **9.4.4 闭运算**

* 定义：**先膨胀后腐蚀**：`close = erode(dilate(img))`。
* 作用：填补物体内部的小孔洞、连接断裂的缝隙，同时保持物体主体大小基本不变。
* 函数：`cv2.morphologyEx(src, cv2.MORPH_CLOSE, kernel, dst=None, anchor=(-1,-1), iterations=1, borderType=..., borderValue=...)`

#### **9.4.5 形态学梯度**

- 定义：膨胀图减去腐蚀图：`gradient = dilate(img) - erode(img)`。
- 作用：提取物体的轮廓/边缘，得到的是物体的"厚度"边界。
- 函数：`cv2.morphologyEx(src, cv2.MORPH_GRADIENT, kernel, dst=None, anchor=(-1,-1), iterations=1, borderType=..., borderValue=...)`

#### **9.4.6 顶帽运算**

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

#### **9.4.7 底帽运算**

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

## 十、传统图像分割

### 10.1 **图像分割概述**

图像分割（Image Segmentation）是将数字图像划分为若干**互不重叠的区域**，使同一区域内像素具有相似属性（如灰度、颜色、纹理），不同区域间差异明显的过程。
它是图像处理与计算机视觉中的核心基础任务，是从"像素级图像"到"语义级理解"的桥梁。

**分割的本质**：给每个像素分配一个标签（区域编号或类别），即像素级分类/聚类问题。

**输出形式**：

- 二值掩膜（前景/背景）
- 区域标签图（每像素一个区域 ID）
- 语义分割图（每像素一个类别）
- 实例分割图（区分同类不同个体）

分割的分类体系

| 层次 | 类型 | 说明 |
|------|------|------|
| 传统方法 | 基于阈值、边缘、区域、聚类、图论 | 依赖手工特征与规则 |
| 深度学习 | 语义分割、实例分割、全景分割 | 端到端学习特征 |


### 10.2 **基于阈值的图像分割（同 8.7）**

基于阈值的图像分割利用灰度直方图，根据像素灰度值与一个或多个阈值的比较，将像素划分到不同类别（前景/背景或多个区域）。

- **固定阈值化**
- **自适应阈值**：按局部邻域统计量定阈值，适应光照不均。
- **多阈值**：多个阈值分成多类。

#### **10.2.1 Otsu 大津法**

**原理**：遍历所有可能阈值，选取使**类间方差最大**（等价于类内方差最小）的阈值。

设阈值 $T$ 将像素分为 $C_0$（灰度 $\le T$）与 $C_1$（灰度 $> T$），两类概率为 $\omega_0, \omega_1$，均值为 $\mu_0, \mu_1$，全局均值为 $\mu_T$。类间方差：

$$\sigma_B^2(T) = \omega_0 (\mu_0 - \mu_T)^2 + \omega_1 (\mu_1 - \mu_T)^2$$

最优阈值：

$$T^* = \arg\max_T \sigma_B^2(T)$$

**特点**：

- 全自动、无需人工干预，计算快（可用直方图累加加速）。
- 对双峰直方图效果最好。
- 对噪声、光照不均、目标占比悬殊敏感。
- 是工业界最常用的自动全局阈值方法。

```python
import cv2
import numpy as np

img = cv2.imread("input.jpg", cv2.IMREAD_GRAYSCALE)

# 1. 手动全局阈值
_, th_manual = cv2.threshold(img, 127, 255, cv2.THRESH_BINARY)

# 2. Otsu 自动阈值（与 THRESH_BINARY 组合）
t_otsu, th_otsu = cv2.threshold(img, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
print("Otsu 阈值:", t_otsu)

# 3. 自适应阈值（均值 / 高斯）
th_mean = cv2.adaptiveThreshold(
    img, 255, cv2.ADAPTIVE_THRESH_MEAN_C, cv2.THRESH_BINARY, blockSize=11, C=2)
th_gauss = cv2.adaptiveThreshold(
    img, 255, cv2.ADAPTIVE_THRESH_GAUSSIAN_C, cv2.THRESH_BINARY, blockSize=11, C=2)
```


### 10.3 **基于边缘检测的图像分割**

**图像中的边缘对应区域之间的边界**，基于边缘检测的图像分割先检测边缘（Sobel、Canny、LoG 等），再连接边缘形成闭合轮廓，轮廓内部即区域。

**基本逻辑**：

1. 边缘检测（Sobel、Canny、LoG 等）得到边缘像素/边缘图。
2. 边缘连接 或 轮廓提取（`cv2.findContours()`），把离散边缘连成闭合轮廓。
3. 轮廓内部填充（或轮廓外部分离），得到分割区域。

**前提假设**：不同区域之间存在明显的灰度/颜色突变，边缘能反映区域边界。

```python
import cv2
import numpy as np

img = cv2.imread("input.jpg")
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

# 1. 预处理：降噪
blur = cv2.GaussianBlur(gray, (5, 5), 1.4)

# 2. 边缘检测（Canny）
edges = cv2.Canny(blur, 50, 150)

# 3. 形态学连接断裂边缘
kernel = np.ones((3, 3), np.uint8)
edges_closed = cv2.morphologyEx(edges, cv2.MORPH_CLOSE, kernel, iterations=2)

# 4. 查找轮廓
contours, hierarchy = cv2.findContours(
    edges_closed, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

# 5. 按面积筛选并填充为分割掩膜
mask = np.zeros_like(gray)
for cnt in contours:
    if cv2.contourArea(cnt) > 500:          # 过滤小轮廓
        cv2.drawContours(mask, [cnt], -1, 255, thickness=cv2.FILLED)

# 6. 用掩膜提取分割结果
segmented = cv2.bitwise_and(img, img, mask=mask)
```

### 10.4 **基于区域的图像分割**

基于区域的图像分割**利用图像的空间局部特性，将具有相似属性（灰度、颜色、纹理等）的相邻像素合并成同一区域**，使区域内一致、区域间差异明显。

与基于阈值/边缘的方法不同，区域法**显式利用像素的空间邻域关系与连通性**，输出的是连通的区域，而非孤立的像素分类或边界。

**基本假设**：同一目标内部像素属性相似且空间连通；不同目标之间属性存在差异。

**常见方法**：

- 区域生长（Region Growing）：选种子点，按相似性准则逐步合并邻域像素。
- 区域分裂与合并（Split and Merge）：四叉树分裂再合并相似区域。
- 分水岭算法（Watershed）：把灰度图视为地形，模拟注水找分水岭，适合粘连目标分离（常配合标记点避免过分割）。

**区域生长算法流程**

1. 选取**种子点**（一个或多个）。
2. 定义**相似性准则**（如灰度差小于阈值 $T$、颜色距离、纹理相似）。
3. 从种子出发，检查其邻域像素，满足准则则并入区域，并以新并入像素继续扩张。
4. 直到无新像素可并入，区域生长结束。

### 10.5 **基于聚类的图像分割**

基于聚类的图像分割把**像素看作数据样本**，把像素的灰度/颜色/纹理等属性看作**特征向量**，通过聚类算法将特征空间中相近的像素归为同一类，再把类别标签映射回图像空间，得到分割区域。

与区域生长不同，聚类法通常**不需要种子点**，而是从全局特征分布出发划分像素。

**基本流程**：

1. 特征提取：为每个像素构造特征向量（如 RGB、Lab、灰度 + 位置坐标 $(x,y)$）。
2. 聚类：用 K-means、Mean Shift、FCM 等算法对特征向量聚类。
3. 标签映射：把每个像素的聚类标签还原为分割图。
4. 后处理：形态学去噪、连通域过滤、小区域合并等。

**常用方法**：

- **K-means**：预先指定类数 $K$，迭代最小化类内平方误差，按颜色/灰度特征聚类成 $K$ 类。
- **均值漂移（Mean Shift）**：基于核密度估计寻找密度峰值，**无需预设类数**，适合颜色分布复杂、类数未知的场景。
- **模糊 C 均值（FCM）**：允许像素以**隶属度**属于多类，适合边界模糊、存在部分容积效应的图像。

**K-means 目标函数**：

$$J = \sum_{k=1}^{K} \sum_{x \in C_k} \| x - \mu_k \|^2$$

其中 $C_k$ 为第 $k$ 类像素集合，$\mu_k$ 为第 $k$ 类中心。迭代执行“分配像素到最近中心”与“更新类中心”直至收敛。

**Mean Shift 要点**：对每个像素在其特征空间邻域内计算均值漂移向量，沿密度梯度方向迭代移动，最终收敛到同一模态的像素归为一类；带宽 $h$ 决定邻域尺度，直接影响分割粒度。

**FCM 目标函数**：

$$J_m = \sum_{i=1}^{N} \sum_{k=1}^{K} u_{ik}^m \| x_i - \mu_k \|^2, \quad \sum_{k=1}^{K} u_{ik} = 1$$

其中 $u_{ik}$ 为像素 $i$ 属于第 $k$ 类的隶属度，$m>1$ 为模糊指数（常取 2）。

```python
import cv2
import numpy as np

img = cv2.imread("input.jpg")
# 转为 Lab 颜色空间，更符合人眼感知
lab = cv2.cvtColor(img, cv2.COLOR_BGR2LAB)
data = lab.reshape((-1, 3)).astype(np.float32)

# 1. K-means 聚类（K=4）
K = 4
criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 20, 1.0)
_, labels, centers = cv2.kmeans(
    data, K, None, criteria, attempts=5, flags=cv2.KMEANS_PP_CENTERS)

# 2. 标签映射回图像
labels = labels.flatten()
seg = centers[labels].reshape(lab.shape).astype(np.uint8)
seg_bgr = cv2.cvtColor(seg, cv2.COLOR_LAB2BGR)

# 3. Mean Shift 分割（无需预设类数）
shifted = cv2.pyrMeanShiftFiltering(img, sp=15, sr=30)

cv2.imshow("K-means", seg_bgr)
cv2.imshow("Mean Shift", shifted)
cv2.waitKey(0)
```


### 10.6 **基于图论的图像分割**

**基于图论的图像分割**（Graph-based Image Segmentation）是一类将图像建模为图结构，并利用图论算法实现像素或区域划分的方法。其核心思想：

- 将图像的每个像素（或超像素）视为图的**顶点**（node/vertex）；
- 将像素之间的相似性（颜色、亮度、纹理、空间距离等）视为**边**（edge）及其**权重**（weight）；
- 图像分割问题转化为图上的**顶点划分问题**——使同一子图内连接权重较大（相似度高），不同子图间连接权重较小（相似度低）。

形式化地，给定无向图 $G=(V,E)$，其中 $V$ 为顶点集，$E$ 为边集，边权 $w(u,v)$ 表示顶点 $u,v$ 的相似度。目标是将 $V$ 划分为若干不相交子集 $V_1,V_2,\dots,V_k$，使得子图内部相似性高、子图间相似性低。

典型算法：

- **图割（Graph Cut）**：把图像建成图，最小割对应最优分割，可融合先验。
- **GrabCut**：交互式前景分割，用户框选后迭代优化。
- **归一化割（Normalized Cut）**：考虑区域间与区域内相似性。
- ……

#### **10.6.1 GrabCut**

GrabCut 是 Microsoft Research 于 2004 年提出的**交互式前景分割算法**，是 Graph Cut 的改进版。用户只需用矩形框圈出目标（或涂几笔标记前景/背景），算法即可自动迭代优化，分离前景与背景。

**核心思想**：用**高斯混合模型（GMM）** 建模前景/背景的颜色分布，用**图割（Graph Cut）** 最小化能量函数实现分割，并通过**迭代**不断更新 GMM 参数与分割结果，直至收敛。

**基本假设**：同一区域（前景或背景）内颜色分布可用 GMM 描述；相邻且颜色相近的像素更可能属于同一类。

**Graph Cut 与 GrabCut 对比**：

| 对比项 | Graph Cut | GrabCut |
|--------|-----------|---------|
| 用户交互 | 需人工标注前景/背景种子点 | 只需矩形框（或少量涂抹） |
| 颜色建模 | 直方图 | 高斯混合模型（GMM） |
| 迭代优化 | 单次求解 | 迭代更新 GMM + 图割 |
| 适用性 | 简单场景 | 更复杂、更自动 |

GrabCut 在 Graph Cut 基础上做了三件事：**用 GMM 替代直方图**、**用矩形框自动初始化**、**引入迭代优化**。

**原理**：

1. **区域标记**：用矩形框初始化时，框外像素标记为**确定背景**，框内像素标记为**可能前景**。用户也可额外涂抹标记：
     - $T_B$：确定背景
     - $T_F$：确定前景
     - $T_U$：未知区域（待分割）
2. **GMM 颜色建模**：分别为前景和背景各建一个含 $K$ 个高斯分量的 GMM（通常 $K=5$）：$p(x) = \sum_{k=1}^{K} \pi_k \cdot \mathcal{N}(x \mid \mu_k, \Sigma_k)$，每个像素根据其颜色被分配到最可能的高斯分量。
3. **能量函数**：GrabCut 最小化 Gibbs 能量：$E(\alpha, \theta, z) = U(\alpha, \theta, z) + V(\alpha, z)$
     - **数据项 $U$**：像素属于前景/背景的负对数似然（由 GMM 给出）：$U(\alpha, \theta, z) = \sum_n -\log p(z_n \mid \alpha_n, \theta)$
     - **平滑项 $V$**：惩罚相邻像素标签不一致，鼓励边界平滑：$V(\alpha, z) = \gamma \sum_{(m,n) \in \mathcal{N}} [\alpha_m \neq \alpha_n] \cdot \exp\left(-\beta \|z_m - z_n\|^2\right)$
     - 其中 $\alpha$ 为标签，$\theta$ 为 GMM 参数，$z$ 为像素颜色，$\mathcal{N}$ 为邻域对，$\gamma, \beta$ 为权重参数。
4. **迭代优化**（核心）：GrabCut 交替执行：
      1. **固定分割 $\alpha$，更新 GMM 参数 $\theta$**：用当前前景/背景像素重新估计各高斯分量。
      2. **固定 GMM $\theta$，用图割求最优 $\alpha$**：构建 s-t 图，源点连前景、汇点连背景，边权由数据项与平滑项给出，求最小割。
      3. 重复直到能量收敛或达到最大迭代次数。

```python
import cv2
import numpy as np

img = cv2.imread("input.jpg")
mask = np.zeros(img.shape[:2], np.uint8)

# 初始化背景/前景 GMM 模型（OpenCV 内部使用，占位数组）
bgd_model = np.zeros((1, 65), np.float64)
fgd_model = np.zeros((1, 65), np.float64)

# 矩形框 (x, y, w, h)，框外为确定背景
rect = (50, 50, 400, 300)

# 1. 矩形框初始化 + 迭代分割
cv2.grabCut(img, mask, rect, bgd_model, fgd_model,
            iterCount=5, mode=cv2.GC_INIT_WITH_RECT)

# 2. 生成二值掩膜（前景 + 可能前景）
mask2 = np.where((mask == cv2.GC_FGD) | (mask == cv2.GC_PR_FGD), 255, 0).astype(np.uint8)

# 3. 提取前景
result = cv2.bitwise_and(img, img, mask=mask2)

cv2.imshow("GrabCut", result)
cv2.waitKey(0)
```

### 10.7 **基于特定理论的图像分割**

- 主动轮廓模型（Snake、Level Set）
- 马尔可夫随机场（MRF）
- 遗传算法、蚁群算法等优化方法

## 十一、傅里叶变换

### 11.1 **图像傅里叶变换和逆变换**

### 11.2 **OpenCV 实现**

### 11.3 **OpenCV 实现**

## 十二、霍夫变换

## 十三、图像复原与重建

图像增强主要是一种主观处理，而图像复原很大程度上是一种**客观处理**技术。

### 13.1 **图像去雾**

## 十四、彩色图像处理

### 14.1 **彩色基础**

光的三原色：红（R）、绿（G）、蓝（B）

区分不同颜色的特性通常是**亮度、色调和饱和度**，色调与饱和度一起称为**色度**。

* **亮度**：亮度体现的是发光强度（灰度级）的消色概念。
* **色调**：色调是混合光波中与主导波长相关的属性，表示被观察者感知的主导色。即，当我们说一个物体颜色说红色、橙色或红色时，说的就是物体的色调。
* **饱和度**：饱和度指的是相对的纯度（纯色被白光稀释的程度），或与一种色调混合的白光量。纯光谱颜色是完全饱和的，非光谱颜色如淡紫色（紫色加白色）是不饱和度，饱和度与所加白光量成反比。

### 14.2 **彩色模型**

彩色模型（也称彩色空间或彩色系统）的目的是以某种标准的方式来方便地规定颜色。彩色模型本质上规定：（1）坐标系；（2）坐标系内的字空间，模型内的每种颜色都可由字空间内包含的一个点来表示。数字图像处理中的面向硬件的彩色模型常用有以下几种：

* 针对彩色显示器和彩色摄像机开发的 **RGB（红、绿、蓝）**模型。
* 针对彩色打印机开发的 **CMY（青、深红、黄）**模型和 **CMYK（青、深红色、黄、黑）**模型。
* 针对人们描述和解释颜色的方式开发的 **HSI（色调、饱和度、亮度）**模型。HSI 模型的优点：它能够解除图像中颜色和灰度级信息的联系。

#### **14.2.1 RGB 彩色模型**

在 RGB 模型中，每种颜色都以其红色、绿色和蓝色光谱成分显示，该模型是根据笛卡尔坐标系建立的。

全彩色图像通常是指一幅 24 比特的 RGB 彩色图像（每幅分量图像都是 8 位，值域是 `[0, 255]`），颜色总数自然是 $(2^8)^3$=16777216。

#### **14.2.2 HSI 彩色模型**

RGB 、CMY 和 CMYK 模型适合硬件实现但是并不能很好的描述人类世纪解释的颜色。

观察彩色物体时，我们会用色调、饱和度和亮度来描述这个物体，由此提出了 HSI 彩色模型（色调、饱和度、亮度）。

从 RGB 到 HSI 的彩色变换的公式如下：

![RGB和HSI彩色变换.png](images%2FRGB%E5%92%8CHSI%E5%BD%A9%E8%89%B2%E5%8F%98%E6%8D%A2.png)

### 14.4 **全彩色图像处理基础**

全彩色图像处理方法主要分为两种：

* 首先分布处理每幅灰度级分量图像，然后将处理后的各幅分量图像合成为一幅彩色图像。
* 直接处理彩色像素。因为全彩色图像至少有 3 个分量，因此彩色像素是向量。



## 十一、图像分类

## 十一、图像特效

## 十一、OpenGL 入门

## 十一、OpenCV 文字图像区域定位及提取分析

## 十一、OpenCV 车牌检测及区域识别

## 十一、OpenCV 人脸检测及视频人脸动态识别



