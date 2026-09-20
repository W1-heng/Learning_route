**==PPO是离线策略训练，先交互n步得到数据memory，然后再根据memory更新==**


强化学习交互流程：
①保存当前状态到 memory
② Actor 看状态 → 输出动作（μ, σ）
③ 环境执行动作 → 返回 下一状态、奖励、是否结束
④ Critic 看状态 → 输出价值估计
⑤ 把 (状态, 动作, log概率, 奖励, 是否结束) 全部存入 memory
⑥ 重复，直到收集 n_step 步

PPO流程
while 优化器还没全部结束:
    ① 收集 n_step 步交互数据 (状态→动作→奖励→新状态) 存入 memory
    ② 倒序计算折扣累积奖励 R = r + γ·r_next + ...
    ③ 重复 K_epochs=3 轮:
        ├─ 算新旧概率比 ratio = exp(new_logp - old_logp)
        ├─ 算优势 advantage = R - Critic基线
        ├─ Actor损失 = -min(ratio×adv, clip(ratio)×adv)
        ├─ Critic损失 = (V(s)-R)²
        ├─ 总损失 = Actor损失 + Critic损失
        └─ 反向传播 → 梯度裁剪 → 参数更新
    ④ 清空 memory，继续下一段轨迹

gamma：折扣因子
traj_len:轨迹长度
entropy：### 在强化学习里

- **高熵**：Agent 很迷茫，什么都可能选 → **探索多**。
- **低熵**：Agent 很自信，几乎只选一个 → **探索少**。

### 为什么日志要记录熵？

```
训练开始：entropy 高（Agent 在乱试）
训练后期：entropy 应该逐渐降低（Agent 学会稳定策略）
```

如果熵**一直很高**：说明 Agent 没学到东西。  
如果熵**瞬间掉到 0**：说明策略过早收敛，可能陷入局部最优。

所以熵是一个"健康指标"——**只监控，不参与损失**。
