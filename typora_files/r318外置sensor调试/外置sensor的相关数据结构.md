```c
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
```



```c
struct sc130gs_953_954_mode {
	u32 bus_fmt;
	u32 width;
	u32 height;
	struct v4l2_fract max_fps;
	u32 exp_def; /* 默认曝光 */
	u32 bpp; /*Bits Per Pixel*/
	u32 mipi_freq_idx;
	unsigned long mclk; /*svclk_freq*/
	enum SC130GS_953_954_SENSOR_MODE sensor_mode; /*主或从*/
	const struct regval *reg_list;
};
static const struct sc130gs_953_954_mode supported_modes[] = {
	{
		.bus_fmt = MEDIA_BUS_FMT_SBGGR8_1X8,
		.width = 960,
		.height = 720,
		.max_fps = {
			.numerator = 10000,
			.denominator = 1200000,
		},
		.exp_def = 1050, //这里默认曝光1050us
		.bpp = 8,
		.mipi_freq_idx = 0,
		.mclk = SC130GS_953_954_XVCLK_FREQ,
		.sensor_mode = SC130GS_953_954_MASTER_TRIGGER,
		.reg_list = sc130gs_953_954_master_960x720_regs,
	},
};
```



```c
struct v4l2_subdev {
#if defined(CONFIG_MEDIA_CONTROLLER)
	struct media_entity entity;
#endif
	struct list_head list;
	struct module *owner;
	bool owner_v4l2_dev;
	u32 flags;
	struct v4l2_device *v4l2_dev;
	const struct v4l2_subdev_ops *ops;
	const struct v4l2_subdev_internal_ops *internal_ops;
	struct v4l2_ctrl_handler *ctrl_handler;
	char name[V4L2_SUBDEV_NAME_SIZE];
	u32 grp_id;
	void *dev_priv;
	void *host_priv;
	struct video_device *devnode;
	struct device *dev;
	struct fwnode_handle *fwnode;
	struct list_head async_list;
	struct v4l2_async_subdev *asd;
	struct v4l2_async_notifier *notifier;
	struct v4l2_async_notifier *subdev_notifier;
	struct v4l2_subdev_platform_data *pdata;
};

struct v4l2_subdev_ops {
	const struct v4l2_subdev_core_ops	*core;
	const struct v4l2_subdev_tuner_ops	*tuner;
	const struct v4l2_subdev_audio_ops	*audio;
	const struct v4l2_subdev_video_ops	*video;
	const struct v4l2_subdev_vbi_ops	*vbi;
	const struct v4l2_subdev_ir_ops		*ir;
	const struct v4l2_subdev_sensor_ops	*sensor;
	const struct v4l2_subdev_pad_ops	*pad;
};
```

