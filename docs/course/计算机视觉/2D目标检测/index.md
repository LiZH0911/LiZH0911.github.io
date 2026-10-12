# 2D 目标检测

相关链接：

- [AIGC-Interview-Book-经典模型-目标检测高频知识点](https://github.com/WeThinkIn/AIGC-Interview-Book/blob/main/%E7%BB%8F%E5%85%B8%E6%A8%A1%E5%9E%8B/%E7%9B%AE%E6%A0%87%E6%A3%80%E6%B5%8B%E9%AB%98%E9%A2%91%E7%9F%A5%E8%AF%86%E7%82%B9.md)

## 一、目标检测核心概念

### 1.1 **交并比（IOU）**

**IoU（Intersection over Union）**即交并比，是 GT bbox 与 pred bbox **交集的面积 / 并集的面积**。

下面我们用坐标（top，left，bottom，right），即左上角坐标，右下角坐标。从而可以在给定的两个矩形中计算IOU值。

```python
def compute_iou(rect1,rect2):
    """
    计算两个矩形的 IoU。
    坐标格式: (y0, x0, y1, x1) = (top, left, bottom, right)
    """
    # 各自面积
    S_rect1 = (rect1[2] - rect1[0]) * (rect1[3] - rect1[1])
    S_rect2 = (rect2[2] - rect2[0]) * (rect2[3] - rect2[1])
    sum_area = S_rect1 + S_rect2
    
    # 交集边界
    left_line = max(rect1[1],rect2[1])
    right_line = min(rect1[3],rect2[3])
    top_line = max(rect1[0],rect2[0])
    bottom_line = min(rect1[2],rect2[2])
    
    # 若无交集
    if left_line >= right_line or top_line >= bottom_line:
        return 0
    
    # 交集面积
    intersect = (right_line - left_line) * (bottom_line - top_line)
    return (intersect / (sum_area - intersect)) * 1.0
```

### 1.2 **非极大值抑制（NMS）**

非极大值抑制的流程：

### 1.3 **One-stage 目标检测与 Two-stage 目标检测的区别**

**Two-stage 目标检测算法**：先进行区域生成（region proposal，RP）（一个有可能包含待检物体的预选框），再通过卷积神经网络进行样本分类。

* 特点：精度较高，但速度较慢。
* 主要逻辑：特征提取 —> 生成 RP —> 分类/定位回归
* 常见算法：Faster R-CNN 系列和 R-FCN 等。

**One-stage 目标检测算法**：直接在网络中提取特征来预测物体分类和位置。 

* 特点：速度较快，精度比起 Two-stage 算法稍低。
* 主要逻辑：特征提取 —> 分类/定位回归。
* 常见算法：YOLO系列、SSD 和 RetinaNet 等。

### 1.4 **提升小目标检测效果的方法**

* **提高图像分辨率**。小目标在边界框中可能只包含几个像素，那么能通过提高图像的分辨率以增加小目标的特征的丰富度。
* **提高模型的输入分辨率**。这是一个效果较好的通用方法，但是会带来模型 inference 速度变慢的问题。
* 平铺图像
* **数据增强**。小目标检测增强包括随机裁剪、随机旋转和镶嵌增强等.
* 自动学习 anchor
* 类别优化

### 1.5 **Focal Loss 的作用**

Focal Loss 是解决了分类问题中类别不均衡、分类难度差异的一个损失函数，使得模型在训练过程中更加聚焦在困难样本上。

## 二、目标检测评价指标

## 三、目标检测优化策略

## 四、YOLO 系列

## 五、DETR 系列

## 六、其他经典模型和 Backbone

## 七、前沿检测技术