```python
import pandas as pd
import xlsxwriter

df = pd.DataFrame({'Data': [10, 20, 30, 20, 15, 30, 45]})
writer = pd.ExcelWriter('pandas_simple.xlsx', engine='xlsxwriter') # Установка названия

df.to_excel(writer, sheet_name='Sheet1')  # Происходит запись данных в файл

workbook = writer.book
worksheet = writer.sheets['Sheet1']

border_fmt = workbook.add_format({'bottom':1, 'top':1, 'left':1, 'right':1}) # Задается форматирование в документе
```