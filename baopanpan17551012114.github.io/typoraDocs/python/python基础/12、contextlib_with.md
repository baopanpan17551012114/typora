# contextlib_with

```python
import contextlib


# 使得方法也能够使用with
@contextlib.contextmanager
def open_file(file_name):
    print('open: {}'.format(file_name))
    # 被contextmanager装饰的函数必须是一个生成器函数
    yield {'name': '顾安', 'age': 18}
    print('close: {}'.format(file_name))


with open_file('顾安.txt') as f_open:
    print('程序启动...')
    print(f_open)
```