备份与恢复环境

```shell
conda env export > environment.yaml

conda env create -f environment.yaml
```

查看conda环境

```python
conda info --envs	查看所有已创建的环境

conda info --env	查看当前激活环境的
```

