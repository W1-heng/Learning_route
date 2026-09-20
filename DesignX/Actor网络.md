__init__
├── 超参数存储（embedding_dim=64, hidden_dim=16, n_heads=4, 3 层）
├── sep_state 嵌入：算子(16→16) + 状态(9→16) → concat → embedder(32→64)
├── 位置编码（可选，learnt 或 sin）
├── 编码器：3 层 MultiHeadEncoder 或 MLP
└── 解码器：MLP_for_actor(64→32→12)，输出 (μ,σ) 对

forward
├── detach_state? → 切断梯度
├── sep_state 切分：pe_id + x_ind + x_fla → 分别嵌入 → 融合
├── 位置编码（可选）
├── encoder(x, q_length) → 64维编码
├── only_critic? → 返回编码
├── decoder → 12维 [0,1]
├── 奇数索引缩放 σ: [0,1] → [0.1, 0.7]
└── to_critic? → 返回 (decoded, logits) 否则 decoded

get_logp
├── 对每个优化器 → 每个控件 → 每个参数
├── Normal(μ, σ).log_prob(action) → 对数概率
└── 返回累加的 logp + 熵列表

logits：指的是还没归一化的分数，这个项目中采用的是高斯策略的参数对，偶数位：均值，奇数位：标准差
