[cotrain.md](https://github.com/user-attachments/files/33179001/cotrain.md)
Encoder:
1. Baseline
State: 多个数值，用来描述机器人、方块和目标的状态
obs_horizon: 观测帧数, 观测 x 步动作， （动作就是Obervation）
pred_horizon： 预测帧数，根据观测，预测接下来 y 步 action

假设采用训练使用的batch size: 64,
Encoder 会把input数据[64, 2, 42] flatten 之后，作为Unet的输入。

Unet的输入由 Observation State, 带噪动作，扩散步数 组成。

2. Cotraining:
Cotrain 需要统一维度的输出作为Unet的input,
所以对于每一个task 不同的State 加一个MLP 将 每帧统一映射成 128维的output, 再按照顺序拼接。

Task Embedding (Optional)
同时加上32维的task embedding向量，拼接上一步的数据，所以是[Batch Size, 256 + 32] 的数据作为input
每个task有自己的task embedding, 对应任务训练时，拼接该任务自己的embedding. Task Embedding的作用在于为模型提供任务条件


Adapter:
在两个残差块之后，分别接入一个adapter（残差块）学习中间特征并调整。
这里每个任务有自己的adapter.

同时为了与Task Adapter做对比，加上了所有任务公用一个adapter的版本，保持对比公平性。
