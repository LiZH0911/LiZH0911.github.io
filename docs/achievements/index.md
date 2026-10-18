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


### **基于深度学习的红外和可见光图像融合方法研究（硕士课题，2025.01 ~ 2026.07）**

**1. 背景**

针对恶劣天气（雨、雾、雪）场景中因红外和可见光图像退化导致的图像融合效果不佳的问题，提出一种端到端融合方法以同时实现图像退化去除与互补信息整合。

**2. 主要工作**：

- 针对现有基于 Transformer 的自监督融合框架，引入混合专家模型（MoE）与有监督训练范式。
- **技术栈**：**Python · Pytorch · OpenCV · Git**

**3. 成果**

在 AWMM-100k 数据集上完成模型训练和评估，在三种恶劣天气条件下图像融合的 QAB/F、SCD、VIF 等指标均有提升。

<figure markdown="span">
  ![网络框架.png](images%2F%E7%BD%91%E7%BB%9C%E6%A1%86%E6%9E%B6.png){ width="640" loading="lazy" }
  <figcaption>所提红外和可见光图像融合方法的网络框架</figcaption>
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

**1. 背景**

针对复杂城市环境下智能汽车定位的精度和频率难以兼顾的问题，提出一种基于激光雷达和 IMU 融合的高频高精定位方法。

**2. 主要工作**：

1. **纯激光 SLAM 建图**：采用改进的 LeGO-LOAM 算法实现高精建图；
2. **NDT 重定位**：利用 NDT 算法将当前帧点云与先验点云地图进行匹配，实现高精定位；
3. **融合定位**：将重定位观测位姿与IMU预测位姿进行卡尔曼滤波融合，实现高频高精定位。

- **技术栈**：**C++ · linux · ROS · PCL 点云库**

**3. 成果**

在 KITTI 数据集上完成对比实验，相比 LeGO-LOAM，所提方法在 KITTI 00 序列上的 ATE RMSE 由 2.84 m 降至 2.10 m，降低约 26%；位姿输出频率从 10Hz提升到了 100Hz。该本科毕业设计被评为院级优秀。


<figure markdown="span">
  ![整体方案.png](images%2F%E6%95%B4%E4%BD%93%E6%96%B9%E6%A1%88.png){ width="640" loading="lazy" }
  <figcaption>整体方案</figcaption>
</figure>

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

<figure markdown="span">
  ![NDT重定位.png](images%2FNDT%E9%87%8D%E5%AE%9A%E4%BD%8D.png){ width="640" loading="lazy" }
  <figcaption>基于 NDT 算法的单帧点云-先验地图匹配，实现高精定位</figcaption>
</figure>

<figure markdown="span">
  ![卡尔曼滤波融合.png](images%2F%E5%8D%A1%E5%B0%94%E6%9B%BC%E6%BB%A4%E6%B3%A2%E8%9E%8D%E5%90%88.png){ width="640" loading="lazy" }
  <figcaption>基于误差状态卡尔曼滤波的 NDT 低频高精观测位姿与 IMU 高频低精预测位姿融合</figcaption>
</figure>
