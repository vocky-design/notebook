#### iic总线拓扑图

![image-20230619115519961](Y:\github_local\notebook\基本知识\.i2c.assets\image-20230619115519961.png)



I2C总线数据传输速率在标准模式下可达100kbit/s，快速模式下可达400kbit/s，高速模式下可达3.4Mbit/s。

一般通过I2C总线接口可编程时钟来实现传输速率的调整，同时也跟所接的上拉电阻的阻值有关。

#### 起始和结束信号

![image-20230619115840240](Y:\github_local\notebook\基本知识\.i2c.assets\image-20230619115840240.png)



#### 传输

![image-20230619120009206](Y:\github_local\notebook\基本知识\.i2c.assets\image-20230619120009206.png)

![image-20230619120110662](Y:\github_local\notebook\基本知识\.i2c.assets\image-20230619120110662.png)(Y:\github_local\notebook\基本知识\.i2c.assets\image-20230619120207440.png)

#### 应答信号

主机在时钟周期9间释放数据线

从机在第9个时钟周期间将数据线拉低并保持，被认为说ACK