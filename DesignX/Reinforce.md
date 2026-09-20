# **train__batch**
每一批次的训练是这里的最小训练单位
一个 batch 的训练过程：
┌─────────────────────────────────────────────────┐
│ ① 对每个问题随机采样 → 算 ELA 特征（Ray 并行）   │
│ ② 把 ELA 特征喂给 Actor → 生成算法模块序列        │
│ ③ 把算法模块装进 Optimizer（优化环境）             │
│ ④ 让 ConfigX 控制优化器跑完 → 得到 reward          │
│ ⑤ loss = -reward × log_prob → 反向传播            │
│ ⑥ 梯度裁剪 → 优化器更新权重 → 写日志               │
└─────────────────────────────────────────────────┘

输入: 一个 batch 的优化问题 (如 128 个 Sphere 函数)
              │
    ┌─────────▼──────────┐
    │ ① 随机采样 + 评估   │  rng.rand() → sample → eval() → sample_y
    │    (串行)          │
    └─────────┬──────────┘
              │ Xs, Ys, seds
    ┌─────────▼──────────┐
    │ ② Ray 并行 求ELA 特征    │  ray_ela.remote() × N → 9 维 ELA 特征
    └─────────┬──────────┘
              │ elas + problem_info → [batch, 13]
    ┌─────────▼──────────┐
    │ ③ Actor 生成算法   │  agent.actor(ela_features) → modules, logp, entropy
    └─────────┬──────────┘
              │ modules[i] + 问题
    ┌─────────▼──────────┐
    │ ④ 构建 Optimizer   │  Optimizer(problem, modules) × N
    └─────────┬──────────┘
              │ optimizers
    ┌─────────▼──────────┐
    │ ⑤ ConfigX 驱动运行 │  configx_rollout_ray_v2() → rewards, costs
    └─────────┬──────────┘
              │ rewards, logp
    ┌─────────▼──────────┐
    │ ⑥ REINFORCE 更新   │  loss = -(reward × logp).mean()
    │    反向传播        │  loss.backward() → clip → optimizer.step()
    └─────────┬──────────┘
              │
    ┌─────────▼──────────┐
    │ ⑦ 写日志 + 存模块  │  TensorBoard + pickle
    └────────────────────┘



# train__configX
**为什么全循环复用同一批 `optimizers`？**

`train_configx` 的假设是 Actor 已固定（生成的算法模块不再变），那 `optimizers` 就不需要重建。ConfigX 每 epoch 在同一批优化器上练习动态调参，相当于"同一个考场反复模拟直到熟练"。


**主逻辑和train差不多，有一些不同点可在注释中看到**




