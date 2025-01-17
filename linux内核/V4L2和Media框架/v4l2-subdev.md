```c
/* v4l2-subdev.h */
struct v4l2_subdev_route {
  _u32 sink_pad;
  _u32 sink_stream;
  _u32 source_pad;
  _u32 source_stream;
  _u32 flags;
  _u32 reserved[5];
};
struct v4l2_subdev_stream_config {
  u32 pad;
  u32 stream;
  bool enabled;
  struct v4l2_mbus_framefmt fmt;
  struct v4l2_rect crop;
  struct v4l2_rect compose;
  struct v4l2_fract interval;
};
struct v4l2_subdev_stream_configs {
  u32 num_configs;
  struct v4l2_subdev_stream_config *configs;
};
struct v4l2_subdev_state {
  struct mutex _lock;
  struct mutex *lock;
  struct v4l2_subdev *sd;
  struct v4l2_subdev_pad_config *pads;
  struct v4l2_subdev_krouting routing;
  struct v4l2_subdev_stream_configs stream_configs;
};

static void v4l2_subdev_collect_streams(struct v4l2_subdev *sd,
                                        struct v4l2_subdev_state *state,
                                        u32 pad,
                                        u64 stream_mask,
                                        u64 *found_streams,
                                        u64 enabled_streams)
遍历state->stream_configs.configs[]，
如果记录的信息与pad和stream_mask匹配，found_streams置位；
如果记录的信息中表明已经enabled，enable_streams置位。
```



````
flowchart TD
    Start --> Stop
````

```c
struct v4l2_subdev {
#if defined(CONFIG_MEDIA_CONTROLLER)
	struct media_entity entity;
#endif
  struct list_head async_list;
};
```



