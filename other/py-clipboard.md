# 写入剪切板中可直接 ctrl+v粘贴

```python
import win32clipboard as w
import win32con
def setText(message):
    w.OpenClipboard()
    w.EmptyClipboard()
    w.SetClipboardData(win32con.CF_UNICODETEXT,message)
    w.CloseClipboard()
input = u"xxxxx"
setText(input)
```