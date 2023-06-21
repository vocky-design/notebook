目标：快速出货使用，用于楼下外置sensor实车测试监控。

目前能考虑到要做的工作：

1. 筛选出需要监控的寄存器

   ![image-20230529113146071](Y:\github_local\notebook\项目评估\.FPD-LINK的寄存器监控工作评估.assets\image-20230529113146071.png)

   

   5c: CSI-2 Error Count

   5d: CSI-2 Error Status

   5e: CSI-2 Errors Data Lanes 0 and 1

   64: CSI_ECC

   

   ![image-20230529113210835](Y:\github_local\notebook\项目评估\.FPD-LINK的寄存器监控工作评估.assets\image-20230529113210835.png)

​	4d: RX_PORT_STS1

​	4e: RX_PORT_STS2

​	55: RX_PAR_ERR_HI

​	56: RX_PAR_ERR_LO

​	7a: CSI_RX_STS

​	7b: CSI_ERR_COUNTER

1. 监控机制：

   1. “变频”
   2. 日志记录
      - 全体寄存器记录
      - 变化寄存器说明
2. 读写寄存器函数实现

   1. 仿照R349的寄存器读写
   2. 在v4l_get_frame中判断是否丢帧，丢帧即去读一次寄存器，然后记录在一份新的日志中。（可以用dsp日志吗？）
3. 寄存器读写总用时监控







------

## sensor_ctrl_i2c模块

```c
#define MAX_I2C_BUS_NUM (12)
struct i2c_bus_attr
{
	uint32_t i2c_bus_num;       /**< i2c总线 */
	int32_t i2c_fd;             /**< i2c文件描述符 */
	int32_t i2c_inited;         /**< i2c是否被初始化,1:初始化 0:未初始化 */
	pthread_mutex_t i2c_lock;   /**< i2c总线锁 */
};
static struct i2c_bus_attr i2c_bus[MAX_I2C_BUS_NUM] = {0};      /**< i2c总线的属性信息 */
```



```c
//用户iic操作单位
struct sns_attr
{
	uint32_t i2c_bus_num; /**< i2c总线*/
	uint32_t i2c_addr; /**< sensor地址*/
	uint32_t reg_width; /**< 寄存器地址宽度*/
	uint32_t data_width; /**< 数据宽度*/
	int32_t sensor_fd;
};
struct sensor_reg_attr
{
	int16_t reg_addr; /**< 寄存器地址*/
	int16_t value; /**< 寄存器的值*/
	int32_t delay_time; /**< 写入值后需要延时时间*/
};
```

## 全部读取寄存器报错问题

v4l_get_frame调用一次DQBUF

```c
buf.type = p_cap->buf_type;
	buf.memory = V4L2_MEMORY_MMAP;
	memset(&plane, 0, sizeof(plane));
	if (V4L2_BUF_TYPE_VIDEO_CAPTURE_MPLANE == p_cap->buf_type)
	{
        buf.m.planes = &plane;
        buf.length = 1;
    }
	ret = xioctl(p_cap->fd, VIDIOC_DQBUF, &buf);
```



### v4l应用程序的逻辑

一个fd对应一个cap

```c
struct v4l_caps_manage
{
	uint32_t active_dev_cnt;
	struct v4l_cap_info cap_dev[V4L_CAP_DEV_NUM_MAX];
};
static struct v4l_cap_info *get_cap_info_from_fd(int32_t fd)
{
	int32_t i = 0, cnt = 0;

	cnt = ARRAY_SIZE(caps_manage.cap_dev);
	for (i = 0; i < cnt; i++)
	{
		if ((caps_manage.cap_dev[i].cap_status & V4L_DEV_STAT_OPEN) 
								&& (fd == caps_manage.cap_dev[i].fd))
		{
			return (&caps_manage.cap_dev[i]);
		}
	}

	return NULL;
}
```

一个cap中有多个buf

```c
struct v4l_cap_info
{
	uint32_t cap_status;
	int32_t fd;
	char dev_node[32];
	struct v4l_cap_param cap_param;
	struct v4l2_capability capability;
	struct v4l2_format cur_fmt;
	int32_t frm_timeout_cnt; //获取图像出现连续超时的次数?
	uint32_t n_buffers;
	enum v4l2_buf_type buf_type;
	struct v4l_cap_buf_info buf[V4L_CAP_BUF_NUM_MAX];
};
static struct v4l_cap_buf_info *get_cap_buf_from_frm_ptr(struct v4l_cap_info *p_cap, void *frm_ptr)
{
	int32_t i = 0;

	for (i = 0; i < p_cap->n_buffers; i++)
	{
		if (frm_ptr == p_cap->buf[i].frm_ptr) //指向图像数据区域的指针相同。
		{
			return (&p_cap->buf[i]);
		}
	}

	return NULL;
}
```



- 寄存器读取的时间点是？
  - 要监控的问题：ISP判断图像信息错误然后丢弃->（上层表现）丢帧/长时间取不到流，（目前是帧间隔的3倍30ms）
  - 

- 寄存器值要记录在哪里？
  - （测试阶段）全部寄存器存储在dsp日志中

- 日志记录

- 日志自动对比
- 4ms延迟带来的影响
- 寄存器读取优化
