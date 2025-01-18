```c
static LIST_HEAD(subdev_list); //挂载struct v4l2_subdev
static LIST_HEAD(notifier_list); //挂载struct v4l2_async_notifier
static DEFINE_MUTEX(list_lock);

struct v4l2_subdev {
  struct list_head asc_list; //挂载struct v4l2_async_connection
  struct list_head async_list; //挂载到subdev_list
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

static struct v4l2_async_notifier *
v4l2_async_find_subdev_notifier(struct v4l2_subdev *sd);
1. 遍历notifier_list，寻找属于sd的notifier。

/*Return true if all child sub-device notifiers are complete, false otherwise*/
static bool
v4l2_async_nf_can_complete(struct v4l2_async_notifier *notifier);
1. 如果notifier->waiting_list不为空，返回false；
2. 遍历notifier->done_list:找到相关子设备的notifier，递归调用v4l2_async_nf_can_complete。

static bool
v4l2_async_nf_has_async_match(struct v4l2_async_notifier *notifier,
			                        struct v4l2_async_match_desc *match);
1. 遍历notifer->waiting_list和notifier->done_list，如果match被添加两次，返回true。
2. 检查notifier_list上挂载的notifier的waiting_list和done_list，如果存在相同match，返回true。

static int 
v4l2_async_nf_match_valid(struct v4l2_async_notifier *notifier,
				                  struct v4l2_async_match_desc *match);
1. v4l2_async_nf_has_async_match(notifier, match);

static int
v4l2_async_nf_try_complete(struct v4l2_async_notifier *notifier);
1. 如果notifier->waiting_list不为空，返回0；
2. 

static int 
__v4l2_async_nf_register(struct v4l2_async_notifier *notifier);
1. 遍历notifier->waiting_list:

/*找到notifier->waiting_list挂载的属于subdev的connection*/
static struct v4l2_async_connection *
v4l2_async_find_match(struct v4l2_async_notifier *notifier,
		                  struct v4l2_subdev *sd);
		                  
static int
v4l2_async_nf_try_all_subdevs(struct v4l2_async_notifier *notifier);
1. 遍历subdev_list上挂载的subdev：找到notifier->waiting_list挂载的属于subdev的connection。
2. 


static int v4l2_async_match_notify(struct v4l2_async_notifier *notifier,
                        				   struct v4l2_device *v4l2_dev,
                        				   struct v4l2_subdev *sd,
                        				   struct v4l2_async_connection *asc)；
1. v4l2_async_nf_call_bound(notifier, sd, asc);
2. list_add(&asc->asc_subdev_entry, &sd->asc_list); asc->sd = sd;
3. Move from the waiting list to notifier's done.

4. subdev_notifier->parent = notifier;
8. v4l2_async_nf_try_all_subdevs(subdev_notifier);

static struct v4l2_async_notifier *
v4l2_async_find_subdev_notifier(struct v4l2_subdev *sd);
1. 遍历notifier_list上挂载的struct v4l2_async_notifier实例
  返回n->sd == sd的那个notifier实例

```