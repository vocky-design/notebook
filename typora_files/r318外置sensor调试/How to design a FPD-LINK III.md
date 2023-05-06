## 3.2 BIST

### 3.2.1 BIST Configuration and Status

串行器时钟的选择：

- external PCLK
- internal oscillator clock (OSC)

### 3.2.2 BIST Procedure

![image-20230414150821725](E:\typora_files\r318外置sensor调试\How to design a FPD-LINK III.assets\image-20230414150821725.png)

#### 1.Use the reset register to reset the entire digital block and set the serializer (SER) and deserializer (DES) to a known state.

![image-20230414152717284](E:\typora_files\r318外置sensor调试\How to design a FPD-LINK III.assets\image-20230414152717284.png)

- Register 0x01 is the RESET_CTL register on both the 953 and 954.
- 通常，最好选择不清楚寄存器版本的reset操作来节省初步故障排除的时间。

#### 2.Confirm that SER and DES are locked by accessing the respective devices IDs and DES device status.

当LOCK高时，DES的PLL被锁定，使数据可用，以及从输入信号中复原时钟。

DEVICE_STS(0x04)应该是0xCF

#### 3. Enable the write permission for RX Port0.

请记住，配置与RX0端口相关的参数需要一个写命令，如果不给写RX0端口的权限，那么即使执行写命令，寄存器中的值也不会改变。

FPD3_PORT_SEL：0x4C on 954

#### 4.On the SER, clear any previous errors in the system before enabling BIST by clearing the CRC errors and BIST CRC errors in addition to reading the BCC status and SER general status.

BC_CTRL(0x49) on the 953，建议值：0x28。selects the self-clearing bits to clear the BIST CRC and CRC errors。

BCC_STATUS（0x79）on the 953。

GENERAL_STS(0x52) on the 953，建议值：0x45。showing that RX Lock Detect, HS PLL Lock, and Link Detect flags are high。

#### 5.Read the BIST error counter before BIST.

BIST_ERR_CNT（0x54）on the 953， 在BIST结束后的期待值是0x01。

#### 6.On the DES, read the BIST control register, enable the BIST, force the singular, multiple, or no errors, and read the RX port status after the BIST starts and before the BIST ends.

BIST_CTL、0xB3 on the  954

在启用BIST之前和之后读取它有助于显示已启用BIST。通过使用sleep命令，BIST可以运行用户想要的时间。

RX_PORT_STS1：0x4D on 954

RX端口状态(general status and BCC status) 包含需要错误标志。包括：

- BCC CRC error
- lock status change
- BCC sequence error
- parity error
- receiver pass indication
- lock status
- locked to recovered clock status

LOCK_STS_CHG：在开始之后和结束之前监控它，在BIST启动后读取它很重要，因为使能和禁用BIST会迫使设备**relock**。

PORT_DEBUG：0xD0 on 954

可以用来测试错误检测机制是否正常。

#### 7.Disable the BIST and write permissions of RX Port0

FPD3_PORT_SEL：0x4C on 954

disable the write permission是一个很好的实践。

#### 8.Check for errors on the SER general status, DES device status, and BIST error count

