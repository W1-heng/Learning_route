# Agent-2 多智能体 PBT 通信改造计划书

  

## 目标

  

在 Agent-1 生成算法后，将同一个算法复制给多个 Agent-2 learner。每个 learner 拥有独立的 ConfigX 调参策略，独立用 PPO 训练；每轮通信选出“归一化累计提升最高”的策略，让较差 learner 向最优策略插值，通信采用前松后紧。

  

> 计划范围：本轮只做方案设计，不写代码。

  

---

  

## 0. 关键概念澄清

  

### 0.1 Agent-2 到底在训练什么？

  

**结论：Agent-2 训练的是“调参策略”，不是一组固定超参。**

  

ConfigX 不是一次性找到 `F=0.5, CR=0.9` 然后全程不变，而是一个 PPO 策略网络：

  

- 每一步观察当前优化器状态。

- 输出当前这一步应该使用哪些超参。

- 同一个算法在不同阶段可以使用不同超参。

  

所以多智能体通信“最好的超参”，最自然的形式是通信表现最好 ConfigX 的 actor/critic 权重，而不是通信某个具体数值。

  

### 0.2 Agent-1 只生成一个算法，怎么做多智能体？

  

**结论：Agent-1 只生成算法结构，多智能体放在 Agent-2 层。**

  

方案 B 的做法不是让 Agent-1 生成多个不同算法，而是：

  

```text

对每个 problem：

  Agent-1 生成一个算法 A

  复制 K 份 A

  K 个 Agent-2 learner 独立训练调参策略

  这 K 个 learner 组成一个通信组

```

  

也就是说，Agent-2 的多智能体是“同一算法结构、多个调参策略”。

  

### 0.3 `gbest` 是什么？

  

`gbest` 是当前算法运行到目前为止找到的全局最优函数值。

  

- 问题是最小化问题，所以 `gbest` 越小越好。

- `init_gb` 是算法开始前的最优值。

- `final_gbest` 是 rollout 结束时的 `gbest`。

  

对应的代码位置：

  

- `cbest`：当前这一代种群的最优值。

- `gbest`：从运行开始到现在的历史最优值。

- `lbest`：每个子种群自己的局部最优值。

  

### 0.4 为什么“越小越好”？

  

黑箱优化问题通常写成：

  

```text

min f(x)

```

  

`f(x)` 是目标函数，也叫 cost / fitness / loss，它给每个候选解打分，分数越低表示解越好。很多现实问题天然是最小化问题，例如成本最低、误差最小、能量最低、距离最短。

  

### 0.5 最小化黑箱优化研究什么？

  

黑箱优化研究的是：

  

> 当 `f(x)` 的数学表达式不可知、没有梯度、可能非凸、不可微、甚至带噪声时，如何用尽量少的函数评估次数找到尽量好的解。

  

在 DesignX 中：

  

- 每个 benchmark problem 就是黑箱函数。

- 调用 `problem.eval(x)` 得到 `cost`。

- 一次 eval 算一次 FEs，`MaxFEs` 是总预算。

- Agent-1 设计算法，Agent-2 控制算法参数，目标是在有限 FEs 内把 `gbest` 压得越低越好。

  

### 0.6 fitness 为什么可能是负数？

  

之前的公式写成：

  

```text

fitness = (init_gb - final_gbest) / init_gb

```

  

分子方向没有错：

  

- 最小化问题中，算法变好意味着 `final_gbest < init_gb`。

- 所以 `init_gb - final_gbest > 0`。

  

但如果 `init_gb` 本身是负数，分母是负数，整个 fitness 就会变成负数，甚至符号反转。因此要改成用绝对值归一化。

  

---

  

## 1. 总体设计

  

```text

Agent-1 冻结，rollout 生成每个 problem 的算法 A_p

对每个 problem p：

  K 个 Agent-2 learner，共享同一个 A_p

  每个 learner 有独立 ConfigX actor/critic

  本地 PPO 训练若干轮

通信时：

  用固定种子评估每个 learner 的归一化累计提升 fitness

  组内选 best，bottom 一部分向 best 插值并加小噪声

  通信间隔由 fitness 停滞状态自适应缩短

```

  

---

  

## 2. 新增配置参数

  

加在 `configx_cfg.py`，因为这是 Agent-2 的训练配置：

  

- 种群规模：每个 problem 组的 learner 数 `K`

- 通信间隔：初始、最小、最大

- 停滞判断：连续多少轮不进步就缩短间隔

- 缩短因子

- 插值强度：`tau`

- 更新比例：只更新 bottom 的比例

- 评估 repeat 次数

- 混合后噪声强度

- 是否只混合 actor，不混合 critic

  

---

  

## 3. 需要改动的文件

  

### `configx_cfg.py`

  

新增 Agent-2 种群通信参数。

  

### `nets/actor_network.py`

  

- 给 `ConfigX` 增加权重插值接口。

- 支持只混 actor，或同时混 actor/critic。

- 支持混合后对 actor 参数加高斯噪声。

  

### `rollout.py`

  

- 扩展评估接口，让单次 rollout 返回 `init_gb`、`final_gbest`、重复次数等评估所需数据。

- 复用现有 `configx_rollout_ray_v2`，补充初始最优值信息，便于计算归一化提升。

  

### `REINFORCE.py`

  

- 把 `train_configx` 中“冻结 Agent-1、生成各问题算法、构造 Optimizer”的逻辑抽成可复用函数。

- 单智能体和多种群都使用同一套算法生成流程。

  

### 新增 `population_configx.py`

  

- 实现 Agent-2 种群训练主循环。

- 实现 `ConfigXLearner` 包装类。

- 实现 `ConfigXPopulation` 通信器。

- 复用或泛化 `population.py` 的 `AdaptiveCommScheduler`。

  

### `main.py` 或 `REINFORCE.py` 入口

  

- 当 `train_cfx=True` 且 `K>1` 时，走新的 `train_configx_population`。

- 否则保持原 `train_configx` 单智能体逻辑。

  

---

  

## 4. 核心数据结构

  

### ConfigXLearner

  

每个 learner 持有：

  

- 一个独立 `ConfigX` 实例。

- 自己的 run_name。

- 自己那份 `Optimizer` 训练集。

- 最近一次评估 fitness。

  

### 问题组

  

```text

problem_p -> [learner_1, learner_2, ..., learner_K]

```

  

组内共享同一个 Agent-1 生成的算法结构。

  

### 通信器

  

- 按 problem 分组评估 fitness。

- 每组内部选出 best learner。

- 对 bottom 比例的 learner 执行权重插值。

- 维护全局或组内停滞计数，控制通信间隔。

  

---

  

## 5. 训练主循环

  

1. 加载或生成训练和验证问题。

2. 冻结 Agent-1，用 `rollout=True` 为每个问题生成算法。

3. 为每个问题构建基础 `Optimizer`。

4. 创建 `K` 个 learner，各自深拷贝算法或环境并初始化 ConfigX。

5. 每个 epoch：

   - 每个 learner 在本地 `Optimizer` 上做若干轮 PPO。

   - 判断是否到通信 epoch。

   - 若通信：

     - 固定种子评估每个 learner 的 fitness。

     - 每个 problem 组内选 best。

     - bottom 部分向 best 插值。

     - 加小噪声。

     - 更新停滞计数和通信间隔。

   - 保存 checkpoint 和 TensorBoard 日志。

  

---

  

## 6. fitness 定义

  

对每个 learner：

  

```text

fitness = (1 / R) * sum_r

          [ max(init_gb_r - final_gbest_r, 0) / max(|init_gb_r|, eps) ]

```

  

- 同一组内必须同 problem、同算法。

- 评估时使用固定初始种子，保证差异只来自 ConfigX 策略。

- 用多次 repeat 降低随机性。

- 分母用 `|init_gb|` 而不是 `init_gb`，避免 `init_gb` 为负数时符号反转。

  

> 如果只在同一 problem、同一算法、同一初始种子内部比较，直接用 `final_gbest` 也能正确排序；但归一化提升公式更符合“评价提升量”的初衷。

  

---

  

## 7. 通信细节

  

- 只对每组 fitness 较差的 bottom 比例 learner 更新。

- best 或 top learner 保持不动，保留探索性。

- 第一版只插值 actor，critic 不动。

- 插值后给 actor 权重加小高斯噪声，防止过早趋同。

- 初始通信间隔大，fitness 停滞时缩短间隔，后期可适当增大 `tau`。

  

> “通信最好的超参”在这里实现为：通信表现最好 ConfigX 的 actor 权重，而不是通信某一步的具体 F/CR 数值。

  

---

  

## 8. 分阶段实施

  

### 阶段 1：参数和接口准备

  

- 加配置项。

- 给 `ConfigX` 增加 actor 插值和噪声接口。

- 扩展 rollout 返回 `init_gb`。

  

### 阶段 2：重构算法生成

  

- 从 `train_configx` 抽出生成算法和构造 `Optimizer` 的函数。

- 保证原有单智能体训练行为不变。

  

### 阶段 3：实现种群训练

  

- 新增 `population_configx.py`。

- 实现 learner 创建、本地 PPO、评估、选优、插值、噪声、自适应通信。

  

### 阶段 4：主流程接入

  

- 在 `main.py` 或 `REINFORCE.py` 增加分支。

- 保证 `K=1` 时走原逻辑。

  

### 阶段 5：验证和消融

  

- 先验证 `K=1` 和原版结果一致。

- 再跑 `K=2/3/4`，比较收敛速度和最终 `avg_best`。

- 调 `tau`、噪声、通信间隔、repeat 次数。

  

---

  

## 9. 验证指标

  

- 测试集最终 `avg_best` 和收敛曲线。

- 组内 learner 策略的差异度，防止过早趋同。

- 通信间隔随训练的变化。

- 每组 best learner 的 fitness 提升轨迹。

- 与单 ConfigX baseline 的 seed 稳定性对比。

  

---

  

## 10. 主要风险

  

- 计算量约 `K` 倍，需要控制 learner 数和 rollout 规模。

- 权重直接插值可能不稳定，`tau` 必须小。

- best learner 可能只是评估噪声造成的偶然好结果，必须多次 repeat。

- 过早通信会使所有 learner 趋同，需用 bottom-only 更新和噪声缓解。

- critic 不同步可能影响 PPO 更新，第一版先只混 actor。