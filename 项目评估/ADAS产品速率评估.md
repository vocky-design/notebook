# ADAS产品速率评估

对其速率的评估包含 CSI-2 接口的 **Bandwidth** 以及 FPD-LINK 高速线上的 **line rate** 。

## CSI-2接口的bandwidth计算

CSI-2 接口有最多四组 data lane， lane 的数量以及每组 data lane 的最大速率决定了当前 CSI-2 接口的最大 bandwidth  。

不同于 DVP 接口有专门的行场同步信号， CSI-2 的行场同步需要用特定的 CSI-2 短包来表征，此外 CSI-2 的长包也需要相应的帧头帧尾，在包与包之间还需要切换到 LPS 状态，如图 13 所示，因此 CSI-2 接口比 DVP 接口需要更多额外总线开销。图像消隐时间加上 CSI-2 协议总共需要约 **10%~25%**的开销。 计算出来的数据量满足 FPD-LINK 的 CSI-2 接口定义的最大 bandwidth即可。  

要分别考虑串行器以及解串器的CSI-2的带宽能力。

我方产品的CSI-2带宽计算（）

960 * 720 * 8 * 60 *  1.25 = 396Mbps

## FPD-LINK Line rate计算

DVP 接口产品中， FPD-LINK 串行线上 Line rate 与实际运行的 PCLK 存在固定的比例关系，而 CSI-2接口中， Line rate 与 CSI-2 实际运行的 Bandwidth 不存在固定关系， line rate 取决于固定的参考时钟频率。 不论 CSI-2 上运行的数据量是多少， FPD-LINK 串行线上的速率始终是固定的 。（具体的 line rate 计算也和芯片的工作模式有关  ）

