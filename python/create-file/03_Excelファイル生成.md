# Excel ファイルを生成する

作成日 2019/11/28

## 01. openpyxl とは

ドキュメント => [https://openpyxl.readthedocs.io/en/stable/](https://openpyxl.readthedocs.io/en/stable/)

インストール => `pip install openpyxl`

最新バージョン => 3.0.1 (2019/11/14)

## 02. openpyxl のサンプルコード

`test1.py`

```python
import openpyxl as excel

wb = excel.Workbook()
ws = wb.active

ws['A1'] = 'こんにちは'
wb.save(TEMP_FILE)
```

`test2.py`

```python
import openpyxl as excel

wb = excel.Workbook()
ws = wb.active
for i in range(1, 10):
    for j in range(1, 10):
        v = i * j
        ws.cell(column=j, row=i, value=v)
wb.save(TEMP_FILE)
```

`test3.py`

```python
import openpyxl as excel
from openpyxl.styles.alignment import Alignment

wb = excel.Workbook()
ws = wb.active

# 列幅を設定する
ws.column_dimensions['A'].width = 5
ws.column_dimensions['B'].width = 15
ws.column_dimensions['C'].width = 60
ws.column_dimensions['D'].width = 10

# cellを使って値とスタイルを入力していく
# タイトル行
ws.cell(row=1, column=1).value = '#'
ws.cell(row=1, column=1).alignment = Alignment(horizontal='center')
ws.cell(row=1, column=2).value = 'ASIN'
ws.cell(row=1, column=3).value = 'タイトル'
ws.cell(row=1, column=4).value = '実価格'
ws.cell(row=1, column=4).alignment = Alignment(horizontal='right')
# データ行
ws.cell(row=2, column=1).value = 1
ws.cell(row=2, column=1).alignment = Alignment(horizontal='center')
ws.cell(row=2, column=2).value = 'ABCDEFGH'
ws.cell(row=2, column=3).value = 'タイトルタイトル'
ws.cell(row=2, column=4).value = 5678
ws.cell(row=2, column=4).number_format = '#,##0'

wb.save(TEMP_FILE)
```

## 03. Cell モジュール

[openpyxl\.cell\.cell module — openpyxl 3\.0\.1 documentation](https://openpyxl.readthedocs.io/en/stable/api/openpyxl.cell.cell.html)

- cell.row ... セルの行番号を返す
- cell.column ... セルの列番号を返す
- cell.value ... セルに値を設定する、もしくは値を得る
- cell.hyperlink ... セルにハイパーリンクを設定する、もしくは値を得る

## 04. styles パッケージ

[openpyxl\.styles package — openpyxl 3\.0\.1 documentation](https://openpyxl.readthedocs.io/en/stable/api/openpyxl.styles.html)

以下の Cell モジュールのプロパティには、特定の syltes パッケージを設定する

- cell.alignment
- cell.border
- cell.fill
- cell.font
- cell.number_format
- cell.style

```python
from openpyxl.styles import Font, PatternFill
from openpyxl.styles.alignment import Alignment

ws.cell(row=2, column=4).value = 5678
ws.cell(row=2, column=4).number_format = '#,##0'
ws.cell(row=2, column=4).alignment = \
    Alignment(horizontal='center')
ws.cell(row=2, column=4).font = \
    Font(underline='single', color='FF0563c1')
ws.cell(row=2, column=4).fill = \
    PatternFill(patternType='solid', fgColor='d3d3d3')

# リンクを入れるときは、自分で青色と下線を追加する必要ある
ws.cell(row=j, column=10).value = rankingTitle
ws.cell(row=j, column=10).hyperlink = rankingUrl
ws.cell(row=j, column=10).font = \
    Font(underline='single', color='FF0563c1')
```

## 05. 計算式を入力する

cell の指定は番号を使うが、計算式の中でのセルの指定は、A1 形式を使う

```python
ws.cell(row=current, column=15).value = \
    f'=IF(M{current} < F{current}, TRUE, FALSE)'
```
