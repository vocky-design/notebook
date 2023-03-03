###### Range

选择单个单元格

```python
import xlwings as xw 

#打开工作簿并选择Sheet1 

wb = xw.Book('example.xlsx') 

sht = wb.sheets['Sheet1']

cell = sht.range('A1')
cell = sht.range(1,1)
```

选择一个区域

```python
rng = sht.range('A1:B10')
```

选择整行或整列

```python
row = sht.range('1:1')
col = sht.range('A:A')
```

