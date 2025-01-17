```c
static LIST_HEAD(subdev_list);
static LIST_HEAD(notifier_list); //挂载struct v4l2_async_notifier实例
static DEFINE_MUTEX(list_lock);

struct v4l2_subdev {
  struct list_head asc_list;
  struct list_head async_list; //挂载到subdev_list
};

struct v4l2_async_notifier {
  const struct v4l2_async_notifier_operations *ops;
  struct v4l2_device *v4l2_dev;
  struct v4l2_subdev *sd;
  struct v4l2_async_notifier *parent;
  struct list_head waiting_list; //挂载的是struct v4l2_async_connection(通过asc_entry)
  struct list_head done_list;
  struct list_head notifier_entry;
};

struct v4l2_async_connection {
  struct v4l2_async_match_desc match;
  struct v4l2_async_notifier *notifier;
  struct list_head asc_entry;
  struct list_head asc_subdev_entry;
  struct v4l2_subdev *sd;
};

struct v4l2_async_match_desc {
  enum v4l2_async_match_type type;
  union {
    struct fwnode_handle *fwnode;
    struct {
      int adapter_id;
      unsigned short address;
    } i2c;
  };
};

static struct v4l2_async_connection *
v4l2_async_find_match(struct v4l2_async_notifier *notifier,
		                  struct v4l2_subdev *sd);
1. 遍历notifier->waiting_list上挂载的struct v4l2_async_connection实例
  如果asc->match.type是IIC类型，检查asc->match与sd的iic描述是否一致，如果一致返回asc；
  如果asc->match.type是FWNODE类型
  
static int
v4l2_async_nf_try_all_subdevs(struct v4l2_async_notifier *notifier);
1. 遍历subdev_list上挂载的subdev


static struct v4l2_async_notifier *
v4l2_async_find_subdev_notifier(struct v4l2_subdev *sd);
1. 遍历notifier_list上挂载的struct v4l2_async_notifier实例
  返回n->sd == sd的那个notifier实例

static int v4l2_async_match_notify(struct v4l2_async_notifier *notifier,
                        				   struct v4l2_device *v4l2_dev,
                        				   struct v4l2_subdev *sd,
                        				   struct v4l2_async_connection *asc)；
1. 如果sd->asc_list空，__v4l2_device_register_subdev(v4l2_dev, sd, sd->owner);
2. v4l2_async_nf_call_bound(notifier, sd, asc);
3. v4l2_async_create_ancillary_links
4. list_add(&asc->asc_subdev_entry, &sd->asc_list);
5. list_move(&asc->asc_entry, &notifier->done_list);
6. subdev_notifier = v4l2_async_find_subdev_notifier(sd);
7. subdev_notifier->parent = notifier;
8. v4l2_async_nf_try_all_subdevs(subdev_notifier);
```

