```c
static LIST_HEAD(subdev_list); //挂载struct v4l2_subdev
static LIST_HEAD(notifier_list); //挂载struct v4l2_async_notifier
static DEFINE_MUTEX(list_lock);

struct v4l2_subdev {
  ......
  struct list_head asc_list; //挂载struct v4l2_async_connection
  struct list_head async_list; //挂载到subdev_list
  ......
};

struct v4l2_async_notifier {
  const struct v4l2_async_notifier_operations *ops;
  struct v4l2_device *v4l2_dev;
  struct v4l2_subdev *sd;
  struct v4l2_async_notifier *parent;

  struct list_head waiting_list; //挂载struct v4l2_async_connection
  struct list_head done_list;    //挂载struct v4l2_async_connection
  struct list_head notifier_entry; //挂载到notifier_list
};

struct v4l2_async_connection {
  struct v4l2_async_match_desc match;
  struct v4l2_async_notifier *notifier;
  struct list_head asc_entry; //挂载到waiting_list或done_list
  struct list_head asc_subdev_entry; //挂载到struct v4l2_subdev
  struct v4l2_subdev *sd; //相关的子设备
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

/* Find the sub-device notifier registered by a sub-device driver. */
static struct v4l2_async_notifier *
v4l2_async_find_subdev_notifier(struct v4l2_subdev *sd);
1. 遍历notifier_list，寻找对应sd的notifier。

static struct v4l2_async_connection *
v4l2_async_find_match(struct v4l2_async_notifier *notifier,
		                  struct v4l2_subdev *sd);
1. 遍历notifier->waiting_list, 

void 
v4l2_async_nf_init(struct v4l2_async_notifier *notifier,
			             struct v4l2_device *v4l2_dev);
1. 初始化notifier->waiting_list、notifier->done_list、notifier->notifier_entry.
2. notifier->v4l2_dev = v4l2_dev.

/* Get v4l2_device related to the notifier if one can be found. */
static struct v4l2_device *
v4l2_async_nf_find_v4l2_dev(struct v4l2_async_notifier *notifier);
1. 根据notifier->parent机制找到最上层，返回top_notifier->v4l2_dev;

/*Return true if all child sub-device notifiers are complete, false otherwise*/
static bool
v4l2_async_nf_can_complete(struct v4l2_async_notifier *notifier);
1. 如果notifier->waiting_list不为空，返回false；
2. 遍历notifier->done_list:找到相关子设备的notifier，递归调用v4l2_async_nf_can_complete。

/*
 * Complete the master notifier if possible. This is done when all async
 * sub-devices have been bound; v4l2_device is also available then.
 */
static int
v4l2_async_nf_try_complete(struct v4l2_async_notifier *notifier);
1. 如果notifier->waiting_list不为空，返回0；
2. 找到top_notifier;
3. 如果top_notifier->v4l2_dev为空，返回0；
4. 如果v4l2_async_nf_can_complete(top_notifier)返回false，返回0；
5. v4l2_async_nf_call_complete(top_notifier);

void 
v4l2_async_subdev_nf_init(struct v4l2_async_notifier *notifier,
			                    struct v4l2_subdev *sd)
1. 初始化notifier->waiting_list、notifier->done_list、notifier->notifier_entry.
2. notifier->sd = sd.

/*
 * Find out whether an async sub-device was set up already or whether it exists
 * in a given notifier.
 */
static bool
v4l2_async_nf_has_async_match(struct v4l2_async_notifier *notifier,
			                        struct v4l2_async_match_desc *match);
1. 遍历notifer->waiting_list和notifier->done_list，如果match被添加两次，返回true。
2. 遍历notifier_list，检查traveral_notifer>waiting_list和traveral_notifer->done_list
是否有匹配match的asc，如果有返回true。

static int 
v4l2_async_nf_match_valid(struct v4l2_async_notifier *notifier,
				                  struct v4l2_async_match_desc *match);
1. v4l2_async_nf_has_async_match(notifier, match);

static int 
__v4l2_async_nf_register(struct v4l2_async_notifier *notifier);
1. 遍历notifier->waiting_list，如果v4l2_async_nf_match_valid(notifier, &asc->match)为真，
退出返回错误值。
2. 

/*找到notifier->waiting_list挂载的属于subdev的connection*/
static struct v4l2_async_connection *
v4l2_async_find_match(struct v4l2_async_notifier *notifier,
		                  struct v4l2_subdev *sd);
		                  
static int
v4l2_async_nf_try_all_subdevs(struct v4l2_async_notifier *notifier);
1. 遍历subdev_list上挂载的subdev：找到notifier->waiting_list挂载的属于subdev的connection。
2. v4l2_async_match_notify(notifier, v4l2_dev, sd, asc);

static int 
v4l2_async_match_notify(struct v4l2_async_notifier *notifier,
				                struct v4l2_device *v4l2_dev,
				                struct v4l2_subdev *sd,
				                struct v4l2_async_connection *asc);
1. 如果sd->asc_list为空，
    调用__v4l2_device_register_subdev(v4l2_dev, sd, sd->owner);
    registered = true;
2. v4l2_async_nf_call_bound(notifier, sd, asc);
3. if(registered)
    v4l2_async_create_ancillary_links(notifier, sd);
4. list_add(&asc->asc_subdev_entry, &sd->asc_list);
5. list_move(&asc->asc_entry, &notifier->done_list);
6. 调用v4l2_async_find_subdev_notifier(sd), 
    See if the sub-device has a notifier. If not or subdev_notifier->parent exist, return 0.
7. subdev_notifier->parent = notifier;
8. return v4l2_async_nf_try_all_subdevs(subdev_notifier);

static struct v4l2_async_notifier *
v4l2_async_find_subdev_notifier(struct v4l2_subdev *sd);
1. 遍历notifier_list上挂载的struct v4l2_async_notifier实例
  返回n->sd == sd的那个notifier实例

```