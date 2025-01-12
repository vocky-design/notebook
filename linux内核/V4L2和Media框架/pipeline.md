### hello

```c
/* I/O entity functions */
#define MEDIA_ENT_F_IO_V4L			(MEDIA_ENT_F_OLD_BASE + 1)
/* Sensor functions */
#define MEDIA_ENT_F_CAM_SENSOR			(MEDIA_ENT_F_OLD_SUBDEV_BASE + 1)

int v4l2_mc_create_media_graph(struct media_device *medv);
1. 校验：遍历mdev的所有entity，如果entity->function有CAM_SENSOR作用的同时没有IO_V4L2作用的，返回-EINVAL。
2. media_create_pad_link(entity, 0, io_v4l, 0, MEDIA_LNK_FL_ENABLED):将sensor entity的pad0与v4l2 entity的pad0建立link。
3. media_create_pad_link(decoder, pad_source, io_v4l, 0, MEDIA_LNK_FL_ENABLED):将decoder entity传输video信号的pad与v4l2 entity的pad0建立link。

int v4l_enable_media_source(struct video_device *vdev);
调用vdev->entity.graph_obj.mdev->enable_source(&vdev->entity, &vdev->pipe);
void v4l_disable_media_source(struct video_device *vdev)
调用vdev->entity.graph_obj.mdev->disable_source(&vdev->entity);
```



```c
struct v4l2_subdev {
#if defined(CONFIG_MEDIA_CONTROLLER)
  struct media_entity entity;
#endif
};
```

