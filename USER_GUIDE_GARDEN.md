# Semantic Gaussians "Garden" 场景零基础操作指南

这份文档将手把手教您如何在 Garden 场景上复现 Semantic Gaussians 的全流程。

## 0. 环境准备 (已就绪)

当前环境已经为您配置好了所有必要的依赖库和数据集。
- **数据集位置**: `/root/projects/semantic-gaussians/dataset/mipnerf360/garden`
- **预训练权重**: SAM 和 CLIP 模型已准备好。

---

## 1. 训练 RGB 高斯模型 (Training)

第一步是训练基础的 3D 高斯模型，重建场景的几何和颜色。

**命令**:
```bash
python train.py --config config/train_garden.yaml
```

*   **耗时**: 约 30-60 分钟 (7000 次迭代)。
*   **输出**: 训练结果保存在 `output/garden_replication/point_cloud/iteration_7000/point_cloud.ply`。
*   **注意**: 这一步我们已经完成了！

---

## 2. 语义融合 (Fusion)

第二步是将 2D 图片的语义特征（来自 SAM + CLIP 模型）融合到 3D 点云上。

**命令**:
```bash
python fusion.py --config config/fusion_garden.yaml
```

*   **功能**: 提取多视角的 2D 语义特征，并将其投影到 3D 高斯球上。
*   **输出**: 生成一个巨大的特征文件 `output/garden_replication/0.pt` (约 3-4GB)。
*   **注意**: 这一步我们也已经完成了！

---

## 3. 语义蒸馏 (Distillation) [正在进行中]

由于融合生成的特征文件太大且推理慢，我们需要训练一个轻量级的 3D 网络（MinkowskiEngine）来学习这些特征。

**命令**:
```bash
python distill.py --config config/distill_garden.yaml
```

*   **功能**: 训练一个 3D 稀疏卷积网络 (MinkUNet)，让它学会预测语义特征。
*   **优势**: 训练完成后，推理速度更快，且不再需要加载巨大的 `.pt` 文件。
*   **状态**: 目前正在后台运行中...

---

## 4. 可视化 (Visualization)

最后一步是查看结果。您可以启动一个网页服务器来交互式地查看 3D 场景。

**命令**:
```bash
python view_viser.py --config config/view_garden.yaml
```

*   **如何访问**: 命令运行后，打开浏览器访问 `http://localhost:8080`。
*   **操作说明**:
    *   **Render mode**:
        *   `RGB`: 查看原始颜色。
        *   `Semantic`: 查看语义分割颜色（PCA降维）。
        *   `Relevancy`: **最好玩的功能！** 在文本框输入物体名称（如 "tree", "grass", "table"），场景中对应的物体会变亮。
        *   `Depth`: 查看深度图。

---

## 常见问题 (FAQ)

1.  **为什么 `distill.py` 报错 `collections.Iterable`?**
    *   这是一个 Python 版本兼容性问题，我已经为您修复了代码 (`dataset/fusion_utils.py`)。

2.  **可视化时报错 `is_fg`?**
    *   这是代码对属性检查不严谨导致的，我已经为您修复了 `view_viser.py`。

3.  **显存不足 (OOM) 怎么办?**
    *   我们在 `fusion.py` 中已经做了大量优化（如将张量移至 CPU），通常情况下不会再出现 OOM。如果出现，可以尝试减小 `img_dim`。
