Sacred内部机制概述

本节旨在为Sacred开发者提供参考。它将对Sacred更复杂的内部机制进行高级描述。

配置过程
配置过程在启动实验时执行，确定应该用于运行的最终配置：

确定运行Ingredient的顺序
拓扑排序
按照添加的顺序
对于每个Ingredient：
收集所有适用的配置更新（需要config_updates）
收集要使用的所有命名配置（需要named_configs）
从子运行程序中收集所有适用的回退配置（需要subrunners.config）
将回退配置设置为只读
运行所有命名配置，并将结果用作额外的配置更新，但优先级低于全局配置（需要named_configs，config_updates）
运行所有普通配置
更新全局配置
运行配置钩子
更新全局配置更新