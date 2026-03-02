# 复现对齐清单 (Reproduction Alignment Checklist)

## 环境 (Environment)
- [x] 环境搭建 (用户已确认)
- [x] 依赖项 (假设环境搭建已覆盖，但需注意如果涉及前端需使用 `pnpm`)

## 数据准备 (Data Preparation)
- [ ] ScanNet 数据集 (需申请，跳过)
- [x] Mip-NeRF 360 数据集 (Garden 场景)
  - [x] 下载/挂载: 已通过软链接 `/mnt/f/mipnerf360` 挂载
  - [x] 放置位置: `/root/projects/semantic-gaussians/dataset/mipnerf360`
- [x] 数据格式: COLMAP (已验证)

## 预训练模型 (Pretrained 2D Vision-Language Models)
- [x] CLIP (ViT-L/14@336px) - 自动下载/已链接
- [ ] OpenSeg (Default) - 跳过，使用 SAM+CLIP
- [ ] LSeg (Demo) - 跳过
- [x] SAM (ViT-H) - 已链接 `/root/projects/LangSplat/ckpts/sam_vit_h_4b8939.pth`
- [ ] VLPart (Swin-Base) - 跳过

## 执行目标 (Execution Goals)
- [x] 运行完整训练流程
  - [x] RGB 训练 (`train.py`) - Garden 场景 (7000 iters)
  - [x] 语义融合 (`fusion.py`) - SAM+CLIP, 修复 OOM 问题
  - [x] 可视化 (`view_viser.py`) - 修复 `is_fg` 报错
  - [ ] 语义蒸馏 (`distill.py`) - **下一步**
- [ ] 仅运行推理/演示 (Inference/Demo)
- [ ] 复现特定论文结果 (Table/Figure)
