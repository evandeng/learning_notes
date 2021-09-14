# mmdet推荐超参数

## 1. learning rate

**Important**: The default learning rate in config files is for 8 GPUs and 2 img/gpu (batch size = 8*2 = 16). According to the [linear scaling rule](https://arxiv.org/abs/1706.02677), you need to set the learning rate proportional to the batch size if you use different GPUs or images per GPU, e.g., `lr=0.01` for 4 GPUs * 2 imgs/gpu and `lr=0.08` for 16 GPUs * 4 imgs/gpu.