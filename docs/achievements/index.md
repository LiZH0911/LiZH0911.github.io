# 个人经历

<p class="achievement-intro" markdown>
  记录竞赛与科研路上的阶段性成果，持续打磨，持续输出
</p>

## :material-trophy-outline: 竞赛经历

<div class="grid cards" markdown>

-   **全国大学生数学竞赛（非数学类） · 一等奖**

    ![全国大学生数学竞赛.png](images%2F%E5%85%A8%E5%9B%BD%E5%A4%A7%E5%AD%A6%E7%94%9F%E6%95%B0%E5%AD%A6%E7%AB%9E%E8%B5%9B.png){ width="100%" loading="lazy" }

    - **时间**：2021.12

-   **江苏省高等数学竞赛 · 一等奖**

    ![江苏省数学竞赛.png](images%2F%E6%B1%9F%E8%8B%8F%E7%9C%81%E6%95%B0%E5%AD%A6%E7%AB%9E%E8%B5%9B.png){ width="100%" loading="lazy" }

    - **时间**：2021.06

-   **华为海思嵌入式系统设计竞赛 · 校级三等奖**

    ![华为海思嵌入式系统设计竞赛.png](images%2F%E5%8D%8E%E4%B8%BA%E6%B5%B7%E6%80%9D%E5%B5%8C%E5%85%A5%E5%BC%8F%E7%B3%BB%E7%BB%9F%E8%AE%BE%E8%AE%A1%E7%AB%9E%E8%B5%9B.png){ width="100%" loading="lazy" }

    ![Taurus开发板.png](images%2FTaurus%E5%BC%80%E5%8F%91%E6%9D%BF.png){ loading="lazy" }

    ![牛羊图像分类.png](images%2F%E7%89%9B%E7%BE%8A%E5%9B%BE%E5%83%8F%E5%88%86%E7%B1%BB.png)

    - **时间**：2022.05 ~ 2022.07
    - **简介**：CNN 图像分类模型训练与嵌入式端部署

-   **华为软件精英挑战赛 · 江苏山东赛区 65 名**

    ![华为软件精英挑战赛.png](images%2F%E5%8D%8E%E4%B8%BA%E8%BD%AF%E4%BB%B6%E7%B2%BE%E8%8B%B1%E6%8C%91%E6%88%98%E8%B5%9B.png){ width="100%" loading="lazy" }
    
    ![华为软件精英挑战赛程序运行界面.png](images%2F%E5%8D%8E%E4%B8%BA%E8%BD%AF%E4%BB%B6%E7%B2%BE%E8%8B%B1%E6%8C%91%E6%88%98%E8%B5%9B%E7%A8%8B%E5%BA%8F%E8%BF%90%E8%A1%8C%E7%95%8C%E9%9D%A2.png)

    - **时间**：2023.03
    - **简介**：仓储机器人物流调度程序设计
    
</div>

## :material-flask-outline: 科研经历

### **基于机器视觉的伺服阀气隙测量装置研制**（课题组横向，2024.10 ~ 2024.11）

**1. 背景**

- **电液伺服阀**是电液伺服系统中的重要控制元件，能将电信号转换为液压信号。**力矩马达**将输入的电信号转换成力/力矩，使衔铁偏转，实现伺服阀前端的电-机转换，是电液伺服阀的重要组成部分。
力矩马达由导磁体、线圈、衔铁等组件组成。
- **气隙**是伺服阀力矩马达导磁体与衔铁间的工作空间，其均匀对称性对伺服阀性能影响显著。
- **传统气隙测量**主要依靠经验丰富的工人在显微镜下完成。测量过程存在操作复杂、效率不高、精度不足等问题，因此迫切需要引入**自动检测技术**。

**2. 主要工作**

- 采用**机器视觉**方案，通过工业相机配合镜头采集图像数据，并传输到计算机中，利用图像处理算法对图像进行分析（包括相机标定、图像滤波、图像增强、基于灰度阈值的气隙边缘检测、尺寸计算）
- **硬件选型**：
    - **显微镜镜头**：镜头的视野范围覆盖检测区域，镜头接口类型（C口）
    - **工业相机**：像素分辨率满足测量精度要求，输出颜色（黑白），输出方式（以太网），镜头接口类型（C口）
    - **光源**：保证镜头视野内的光照均匀，可采用环形光源，并用点光源辅助补光。
- **技术栈**：**Python · OpenCV 图像处理 · shell 自动化 · 工业相机图像采集 SDK**

**3. 成果**

- 研制了一台伺服阀气隙测量装置，能够对伺服阀力矩马达的气隙进行实时检测和尺寸测量，单帧耗时约0.2 ~ 0.5 s
- 以人工测量作为真值（通过螺旋测微器读数计算），该装置的测量偏差在 3 微米的允许范围内

<figure markdown="span">
  ![气隙试验台研制方案示意图.png](images%2F%E6%B0%94%E9%9A%99%E8%AF%95%E9%AA%8C%E5%8F%B0%E7%A0%94%E5%88%B6%E6%96%B9%E6%A1%88%E7%A4%BA%E6%84%8F%E5%9B%BE.png){ width="640" loading="lazy" }
  <figcaption>气隙试验台研制方案示意图</figcaption>
</figure>

<figure markdown="span">
  ![气隙测量算法流程.png](images%2F%E6%B0%94%E9%9A%99%E6%B5%8B%E9%87%8F%E7%AE%97%E6%B3%95%E6%B5%81%E7%A8%8B.png){ width="640" loading="lazy" }
  <figcaption>气隙测量算法流程</figcaption>
</figure>

<figure markdown="span">
  ![基于机器视觉的伺服阀气隙测量装置研制-硬件实物图1.png](images%2F%E5%9F%BA%E4%BA%8E%E6%9C%BA%E5%99%A8%E8%A7%86%E8%A7%89%E7%9A%84%E4%BC%BA%E6%9C%8D%E9%98%80%E6%B0%94%E9%9A%99%E6%B5%8B%E9%87%8F%E8%A3%85%E7%BD%AE%E7%A0%94%E5%88%B6-%E7%A1%AC%E4%BB%B61.png){ width="640" loading="lazy" }
  <figcaption>硬件实物图 1</figcaption>
</figure>

<figure markdown="span">
  ![基于机器视觉的伺服阀气隙测量装置研制-硬件2.png](images%2F%E5%9F%BA%E4%BA%8E%E6%9C%BA%E5%99%A8%E8%A7%86%E8%A7%89%E7%9A%84%E4%BC%BA%E6%9C%8D%E9%98%80%E6%B0%94%E9%9A%99%E6%B5%8B%E9%87%8F%E8%A3%85%E7%BD%AE%E7%A0%94%E5%88%B6-%E7%A1%AC%E4%BB%B62.png){ width="640" loading="lazy" }
  <figcaption>硬件实物图 2</figcaption>
</figure>

<figure markdown="span">
  ![基于机器视觉的伺服阀气隙测量装置研制-软件.png](images%2F%E5%9F%BA%E4%BA%8E%E6%9C%BA%E5%99%A8%E8%A7%86%E8%A7%89%E7%9A%84%E4%BC%BA%E6%9C%8D%E9%98%80%E6%B0%94%E9%9A%99%E6%B5%8B%E9%87%8F%E8%A3%85%E7%BD%AE%E7%A0%94%E5%88%B6-%E8%BD%AF%E4%BB%B6.png){ width="640" loading="lazy" }
  <figcaption>程序实时运行界面</figcaption>
</figure>


### **基于深度学习的红外和可见光图像融合方法研究**（硕士课题，2025.01 ~ 2026.07）

**1. 背景**

**红外和可见光图像融合（Infrared and visible image fusion，IVIF）**是一种多传感器信息融合方法，通过整合多源图像中的互补信息生成兼具显著目标信息和丰富纹理细节的融合图像，
对于克服单一类型传感器的局限性、提升成像系统在复杂场景中的感知能力具有重要的理论意义和实际应用价值，已广泛应用于安防监控、自动驾驶和军事侦察等领域。

<figure markdown="span">
  ![红外和可见光图像融合.png](images%2F%E7%BA%A2%E5%A4%96%E5%92%8C%E5%8F%AF%E8%A7%81%E5%85%89%E5%9B%BE%E5%83%8F%E8%9E%8D%E5%90%88.png){ width="640" loading="lazy" }
  <figcaption>红外和可见光图像融合全貌</figcaption>
</figure>

然而，**恶劣天气条件下的源图像退化问题**对于红外和可见光图像融合仍是一个严峻的挑战。

现有大多数**仅关注互补信息整合**的融合方法通常假设输入为理想天气条件下的干净图像对，而在**降雨、降雪或雾霾等恶劣天气条件**下通常会继承源图像的退化特性，导致融合图像的视觉质量较差。

<figure markdown="span">
  ![恶劣天气条件下的红外和可见光图像融合.png](images%2F%E6%81%B6%E5%8A%A3%E5%A4%A9%E6%B0%94%E6%9D%A1%E4%BB%B6%E4%B8%8B%E7%9A%84%E7%BA%A2%E5%A4%96%E5%92%8C%E5%8F%AF%E8%A7%81%E5%85%89%E5%9B%BE%E5%83%8F%E8%9E%8D%E5%90%88.png){ width="500" loading="lazy" }
  <figcaption>恶劣天气条件下图像融合的效果示例</figcaption>
</figure>

针对恶劣天气（雨、雾、雪）场景中因红外和可见光图像退化导致的图像融合效果不佳的问题，提出一种基于深度学习的端到端融合方法以同时实现图像退化去除与互补信息整合。

**2. 主要工作**：

**目标**：

1. **互补信息整合**：要求融合图像中既有红外热目标，也有可见光纹理
2. **恢复有效信息（图像退化去除）**：要求融合图像中没有源图像的雨、雾、雪退化特性

**如何实现？两种策略**：

1. **先复原再融合**：
     - **图像复原**（去噪，相对客观的过程）通常采用**有监督训练范式**，构建**输出图像与无退化参考图像之间的损失函数**。输入输出通常为三通道彩色图，去退化的过程需要关注色彩
     - **图像融合** 通常采用**无监督/自监督训练范式**，构建**输出融合图像与输入多源图像之间的损失函数**。输入输出通常为单通道灰度图，仅需关注亮度通道（YCbCr 的 Y 分量），输出叠加可见光源图像的 Cr、Cb 通道得到 3 通道彩色图

2. **端到端融合**
     - **将无监督融合改为有监督**，构建**输出融合图像与无退化多源参考图像之间的损失函数**。输入输出均三通道彩色图
     - 对参数量、计算量的要求更高，可引入**混合专家模型（MoE）**以适应多种恶劣天气场景（主要创新点）

**基座模型（Base Model）**：Restormer（一种 Vision Transformer 变体）风格 U-Net

1. 块嵌入
2. 双分支编码器（Transformer block + 下采样）
3. 特征融合层（Cross-Attention）
4. 解码器（Transformer block + 上采样 + skip connection）
5. 通道维度调整

**改进后的网络框架**

<figure markdown="span">
  ![网络框架.png](images%2F%E7%BD%91%E7%BB%9C%E6%A1%86%E6%9E%B6.png){ width="640" loading="lazy" }
  <figcaption>所提红外和可见光图像融合方法的网络框架</figcaption>
</figure>

**基于混合专家模型（MoE）的改进 Transformer 解码块（主要创新点）**：

<figure markdown="span">
  ![基于MoE的改进Transformer解码块.png](images%2F%E5%9F%BA%E4%BA%8EMoE%E7%9A%84%E6%94%B9%E8%BF%9BTransformer%E8%A7%A3%E7%A0%81%E5%9D%97.png){ width="640" loading="lazy" }
  <figcaption>基于 MoE 的改进 Transformer 解码块</figcaption>
</figure>

<figure markdown="span">
  ![路由策略和路由专家.png](images%2F%E8%B7%AF%E7%94%B1%E7%AD%96%E7%95%A5%E5%92%8C%E8%B7%AF%E7%94%B1%E4%B8%93%E5%AE%B6.png){ width="640" loading="lazy" }
  <figcaption>路由策略和路由专家</figcaption>
</figure>

**损失函数**

<figure markdown="span">
  ![损失函数.png](images%2F%E6%8D%9F%E5%A4%B1%E5%87%BD%E6%95%B0.png){ width="640" loading="lazy" }
  <figcaption>损失函数</figcaption>
</figure>

**训练细节**

<figure markdown="span">
  ![训练细节.png](images%2F%E8%AE%AD%E7%BB%83%E7%BB%86%E8%8A%82.png){ width="640" loading="lazy" }
  <figcaption>训练细节</figcaption>
</figure>

**对比方法（基线模型，baseline）**

<figure markdown="span">
  ![对比方法.png](images%2F%E5%AF%B9%E6%AF%94%E6%96%B9%E6%B3%95.png){ width="640" loading="lazy" }
  <figcaption>对比方法</figcaption>
</figure>

**评估指标**

<figure markdown="span">
  ![评估指标.png](images%2F%E8%AF%84%E4%BC%B0%E6%8C%87%E6%A0%87.png){ width="640" loading="lazy" }
  <figcaption>评估指标</figcaption>
</figure>

**3. 成果**

在 AWMM-100k 数据集上完成模型训练和评估，在三种恶劣天气条件下图像融合的 QAB/F、SCD、VIF 等指标均有提升。

<figure markdown="span">
  ![恶劣天气条件下的实验结果.png](images%2F%E6%81%B6%E5%8A%A3%E5%A4%A9%E6%B0%94%E6%9D%A1%E4%BB%B6%E4%B8%8B%E7%9A%84%E5%AE%9E%E9%AA%8C%E7%BB%93%E6%9E%9C.png){ width="640" loading="lazy" }
  <figcaption>恶劣天气条件下的实验结果（AWMM-100k数据集 降雨天气）</figcaption>
</figure>

<figure markdown="span">
  ![理想天气实验结果.png](images%2F%E7%90%86%E6%83%B3%E5%A4%A9%E6%B0%94%E5%AE%9E%E9%AA%8C%E7%BB%93%E6%9E%9C.png){ width="640" loading="lazy" }
  <figcaption>理想天气条件下的实验结果（MSRS 数据集），评估模型泛化性</figcaption>
</figure>

<figure markdown="span">
  ![CJSI2026会议.jpg](images%2FCJSI2026%E4%BC%9A%E8%AE%AE.jpg){ width="640" loading="lazy" }
  <figcaption>在 CJSI 2026 会议上做报告</figcaption>
</figure>

**其他相关产出**：基于 PyQt 的海康机器人可见光相机联合高德红外相机同步图像抓取工具

- [https://github.com/Rabbits-sys/steroCameraGUI](https://github.com/Rabbits-sys/steroCameraGUI)

<figure markdown="span">
  ![双目相机实物图.jpg](images%2F%E5%8F%8C%E7%9B%AE%E7%9B%B8%E6%9C%BA%E5%AE%9E%E7%89%A9%E5%9B%BE.jpg){ width="640" loading="lazy" }
  <figcaption>双目相机（海康机器人可见光相机 + 高德红外相机）实物图及其图像采集界面</figcaption>
</figure>


### 基于激光雷达和 IMU 融合的高频高精定位方法研究（本科毕业设计，2023.9 ~ 2024.6）

**1. 动机**

针对复杂城市环境下智能汽车定位的精度和频率难以兼顾的问题，提出一种基于激光雷达和 IMU 融合的高频高精定位方法。

- 纯激光雷达定位方案：定位精度高，但一帧点云的处理通常要几十到上百毫秒，输出只有 10Hz 左右，在动态遮挡或退化场景会失效
- 纯 IMU 积分定位方案：能到 100Hz 以上，但加速度计/陀螺仪会累积漂移。
- 所以核心思路是用 IMU 做高频递推，用激光雷达做低频高精校正。

**2. 主要工作**：

```text
离线建图（一次性）:   改进 LeGO-LOAM  →  全局点云地图 (pcd)
在线定位（低频 10Hz）:  NDT 扫描匹配    →  高精位姿观测
在线融合（高频 100Hz）: IMU 递推 + ESKF   →  100Hz 高频高精位姿
```

<figure markdown="span">
  ![整体方案.png](images%2F%E6%95%B4%E4%BD%93%E6%96%B9%E6%A1%88.png){ width="640" loading="lazy" }
  <figcaption>整体方案</figcaption>
</figure>

**（1）纯激光 SLAM 建图**：采用改进的 LeGO-LOAM 算法实现高精建图；

- baseline： LeGO-LOAM（Levenberg–Marquardt 优化的轻量级 LOAM，特征点分地面/非地面）。
- 主要改进点：用 LiDAR Iris 回环描述子替换了原算法基于欧氏距离半径搜索的回环检测，以更好地校正运动估计漂移。
    - **基于欧氏距离半径搜索的回环检测**：若历史帧与当前帧的距离比较接近，且时间差大于阈值，则认为形成一个回环，但长距离运行后累积误差可能远超搜索半径，此时容易漏检。
    - **基于 LiDAR Iris 的回环检测**：把一帧点云投影成方位角图像，用 LoG-Gabor 滤波 + 二值化得到二值特征图，用汉明距离匹配、靠傅里叶变换实现旋转不变性，所以不依赖位置、对视角旋转鲁棒，能检出反向回环。
    - 另外做了**工程适配**：Ubuntu 20.04 + ROS Noetic、KITTI 64 线雷达、新增 TUM 格式实时位姿/全局位姿输出。

<figure markdown="span">
  ![回环检测优化.png](images%2F%E5%9B%9E%E7%8E%AF%E6%A3%80%E6%B5%8B%E4%BC%98%E5%8C%96.png){ width="640" loading="lazy" }
  <figcaption>改进 LeGO-LOAM 的回环检测模块流程图</figcaption>
</figure>

<figure markdown="span">
  ![可视化轨迹.png](images%2F%E5%8F%AF%E8%A7%86%E5%8C%96%E8%BD%A8%E8%BF%B9.png){ width="640" loading="lazy" }
  <figcaption>改进 LeGO-LOAM 算法在 KITTI 00 序列上的轨迹</figcaption>
</figure>

<figure markdown="span">
  ![全局点云地图.png](images%2F%E5%85%A8%E5%B1%80%E7%82%B9%E4%BA%91%E5%9C%B0%E5%9B%BE.png){ width="640" loading="lazy" }
  <figcaption>改进 LeGO-LOAM 算法在 KITTI 00 序列上的全局点云地图</figcaption>
</figure>

**（2）NDT 重定位**：利用 NDT 算法将当前帧点云与先验点云地图进行匹配，实现高精定位；

- 用 PCL 的正态分布变换（NDT）把当前帧点云和建好的地图配准，输出实时高精位姿。
    - NDT 的优点：NDT 不依赖点对点对应，比 ICP 更鲁棒
    - 缺点：依赖初值，需要在 Rviz 里给一个大致初始位姿——IMU 可以提供帧间初值、缓解这个问题。

<figure markdown="span">
  ![NDT重定位.png](images%2FNDT%E9%87%8D%E5%AE%9A%E4%BD%8D.png){ width="640" loading="lazy" }
  <figcaption>基于 NDT 算法的单帧点云-先验地图匹配，实现高精定位</figcaption>
</figure>

**（3）ESKF 融合定位**：将重定位观测位姿与 IMU 预测位姿进行卡尔曼滤波融合，实现高频高精定位。

- 这是高频高精的关键。参考了经典 GPS/INS 组合导航的 15 维 ESKF，把 GPS 观测换成 NDT 的位姿观测，坐标系对齐到 KITTI 真值坐标系。
- 15 维误差状态 = 位置误差(3) + 速度误差(3) + 姿态误差(3) + 加速度计偏置(3) + 陀螺仪偏置(3)。**用误差状态而非全状态，是因为误差是小量，可以在流形上做小角度线性化，姿态用四元数表达避免万向锁，数值更稳**。
- 融合前需要写数据读取脚本，把 KITTI 真值、NDT 位姿、100Hz IMU 原始数据对齐成**统一时间戳**的数据结构。
- **输出**：100Hz 的位姿，和真值对比计算三维位置误差 RMS。

<figure markdown="span">
  ![卡尔曼滤波融合.png](images%2F%E5%8D%A1%E5%B0%94%E6%9B%BC%E6%BB%A4%E6%B3%A2%E8%9E%8D%E5%90%88.png){ width="640" loading="lazy" }
  <figcaption>基于误差状态卡尔曼滤波的 NDT 低频高精观测位姿与 IMU 高频低精预测位姿融合</figcaption>
</figure>

- **技术栈**：**C++ · linux · ROS · PCL 点云库**

**3. 成果**

在 KITTI 数据集上完成对比实验，相比 LeGO-LOAM，所提方法在 KITTI 00 序列上的 ATE RMSE 由 2.84 m 降至 2.10 m，降低约 26%；位姿输出频率从 10Hz提升到了 100Hz。该本科毕业设计被评为院级优秀。

**4. 问答总结**

1. 为什么用 ESKF 而不是普通 EKF？ 
     - 误差状态是围绕名义状态的小量，可在切空间做小角度近似，姿态用四元数/旋转向量处理避免万向锁，数值稳定、线性化误差小，也方便融合 IMU 偏置。
2. 为什么选 NDT 而不是 ICP？
     - NDT 把点云建模成高斯分布网格，优化目标是概率密度最大化，不需要显式点对应，对噪声和初始偏差更鲁棒；缺点是依赖初值、网格分辨率敏感。
3. 频率具体是多少？为什么？
     - IMU 100Hz，NDT 约 10Hz（每 8 个 IMU 周期做一次更新）。10Hz 是点云匹配的耗时决定的，100Hz 是 IMU 采样决定的，ESKF 把两者桥接起来。
4. 观测只用了 NDT 的位置，没用姿态，为什么？
     - 代码里观测矩阵 `H = [eye(3) zeros(3,12)]`，只观测量位置，这是当时的简化设计，姿态主要靠 IMU 递推 + 陀螺仪偏置估计维持；更完整的做法是把 NDT 的 6 自由度位姿都作为观测，这也是后续可改进的方向。
5. LiDAR Iris 的原理？
     - 点云投影成「方位角 × 距离」的图像 → LoG-Gabor 滤波器提取纹理 → 二值化得到 iris 特征图 → 用汉明距离衡量相似度，用行列循环移位 + 傅里叶变换实现旋转不变匹配。
6. KITTI 坐标系有什么坑？
     - KITTI 是相机/雷达前向 x、左 y、上 z（特定坐标约定），和 IMU 惯用的 NED/ENU 不一样，需要做坐标变换；代码里把 NDT 观测和真值都对齐到统一坐标系再比较。
7. 激光退化 / 没有回环时怎么办？
     - 这正是 IMU 的价值：短时间靠 IMU 递推兜底，等 NDT 恢复观测再校正；长期退化则依赖建图质量。
8. 怎么评估的？ 
     - KITTI 05 和 KITTI 00 序列上分别对比，计算三维位置误差 RMS，画轨迹对比图和 x/y/z 误差曲线。
9. NDT 依赖初值怎么解决？ 
     - 手动在 Rviz 给初始位姿（代码 README 里也写了），实际部署中可用上一帧位姿或 IMU 递推做帧间初值。
10. 如果重做，怎么改进？ 
     - 把 NDT 的完整 6 自由度位姿纳入观测、引入因子图/滑窗优化、处理动态物体、换 IMU-LiDAR 紧耦合（如 LIO-SAM/FAST-LIO 思路）。