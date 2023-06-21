### 修改点1

954的MODE选择的是“CSI-2 non synchronous Back Channel” RX MODE

但BCC_CONFIG (Address 0x58)的 BC_FREQ_SELECT 选择的不对应

应将0x5e修改为：0x5a

![image-20230603115558077](Y:\github_local\notebook\项目评估\.fpd_link寄存器纠错.assets\image-20230603115558077.png)

### 修改点2

BC_GPIO_CTL1 (Address 0x6F)  目前配置的是0a，应修改为8a

![image-20230603140120242](Y:\github_local\notebook\项目评估\.fpd_link寄存器纠错.assets\image-20230603140120242.png)

### 修改点3

FS_CTL (Address 0x18)  目前配置是0xa1，应修改为a0

![image-20230603141257782](Y:\github_local\notebook\项目评估\.fpd_link寄存器纠错.assets\image-20230603141257782.png)