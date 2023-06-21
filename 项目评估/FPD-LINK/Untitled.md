![image-20230620143516238](Y:\github_local\notebook\项目评估\FPD-LINK\.Untitled.assets\image-20230620143516238.png)

软硬件修改方案：

1. 硬件修改953的strap，使得MODE_SEL (Address 0x03)  的MODE选择“010: CSI-2 Non-synchronous external clock Mode (Requires a local clock source)”
1. 软件修改954的BCC_CONFIG (Address 0x58)的BC_FREQ_SELECT选择“010: 10 Mbps (select for non-synchronous back channel compatibility)”

偶然成功了一次，其他不能获取到link状态，按代码逻辑无法进行后续的寄存器配置。

CLK_OUT:27MHZ

