

![image-20230315165022110](E:\typora_files\外置sensor驱动理解.assets\image-20230315165022110.png)

![image-20230315165101653](E:\typora_files\外置sensor驱动理解.assets\image-20230315165101653.png)

![image-20230315184859140](E:\typora_files\外置sensor驱动理解.assets\image-20230315184859140.png)

![image-20230315190120177](E:\typora_files\外置sensor驱动理解.assets\image-20230315190120177.png)

struct regval {
	u16 addr;
	u8 val;
	u32 delay_time;
};



	for (i = 0; i < ARRAY_SIZE(des_954_regs); i++) {
		buf[0] = des_954_regs[i].addr;
		buf[1] = des_954_regs[i].val;
		ret = i2c_master_send(sc130gs_953_954->client_954, buf, 2);
		if (2 != ret) {
			dev_err(&sc130gs_953_954->client_954->dev, "Send 954 register failed, addr=%#x val=%#x ret = %d.\n", buf[0], buf[1], ret);
			return -1;
		}
		msleep(5);
	}

static int sc130gs_953_954_read_reg(struct i2c_client *client,
	u16 reg, unsigned int len, u32 *val)
{
	struct i2c_msg msgs[2];
	u8 *data_be_p;
	__be32 data_be = 0;
	__be16 reg_addr_be = cpu_to_be16(reg);
	int ret;

		struct i2c_msg msgs[2];
		u8 *data_be_p;
		__be32 data_be = 0;
		__be16 reg_addr_be = cpu_to_be16(reg);
		int ret;
	
		if (len > 4 || !len)
			return -EINVAL;
	
		data_be_p = (u8 *)&data_be;
		/* Write register address */
		msgs[0].addr = client->addr;
		msgs[0].flags = 0;
		msgs[0].len = 2;
		msgs[0].buf = (u8 *)&reg_addr_be;
	
		/* Read data from register */
		msgs[1].addr = client->addr;
		msgs[1].flags = I2C_M_RD;
		msgs[1].len = len;
		msgs[1].buf = &data_be_p[4 - len];
	
		ret = i2c_transfer(client->adapter, msgs, ARRAY_SIZE(msgs));
		if (ret != ARRAY_SIZE(msgs)) {
			return -EIO;
		}
	
		*val = be32_to_cpu(data_be);
	
		return 0;
}





###### 2023_04_13

struct sc130gs_953_954_info {
	struct i2c_client	*client;
	struct i2c_client	*client_953;
	struct i2c_client	*client_954;
	struct gpio_desc	*pwdn_gpio;
	struct gpio_desc	*led_gpio;
	struct regulator_bulk_data supplies[SC130GS_953_954_NUM_SUPPLIES];

	struct v4l2_subdev	subdev;
	struct media_pad	pad;
	struct v4l2_fwnode_endpoint bus_cfg;
	
	struct v4l2_ctrl_handler ctrl_handler;
	struct v4l2_ctrl	*exposure;
	struct v4l2_ctrl	*anal_gain;
	
	struct pwm_device   *trigger_pwm;
	struct pwm_state	 trigger_pwm_state;
	struct v4l2_fract 	 cur_fps;
	const struct sc130gs_953_954_mode *support_modes;
	const struct sc130gs_953_954_mode *cur_mode;
	
	bool			streaming;
	bool			power_on;
	u32				cfg_num;
	u32				module_index;
	const char		*module_facing;
	const char		*module_name;
	const char		*len_name;
	
	struct mutex		mutex;
};



sensor驱动比953和954的驱动多一组pm函数指针。