此md记录实现过程中新增的细节
# 复制agent1算法

1.关键点：记得确定好种子，包括初始化时，每个agent2种子设为：`seed = opts.seed + batch_id * batch_size + i`，保证了每个算法初始化相同，并且每次每个agent2PPO训练之前也都设置seed=opts.seed+k,独立初始化，保证策略会分化但是也可以复现
(**确保每个agent2获得的同一问题的算法是完全一样的，但是每个agent2的网络权重参数不同，因为要让不同agent2的探索方向不一样，但是这里只和seed和k有关系，也保证了结果可复现**)

2.修改了optimizer的签名，使其和调用点对齐

3.引入build_specs存每个问题的算法的构建所需参数，agent2训练时只要循环k次_make_optimizers(opts,build_specs)就能创建k个agent2进行训练
