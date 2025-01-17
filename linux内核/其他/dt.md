```c
struct of_device_id {
  char name[32];
  char type[32];
  char compatible[32];
  void *data;
};

const struct of_device_id *of_match_node(const struct of_device_id *matches, 
                                         const struct device_node *node);
```

