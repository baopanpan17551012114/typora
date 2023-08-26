# with语句

```python
# # try语句的使用
# try:
#     print('程序运行...')
# except KeyError:
#     print('key error 错误...')
# else:
#     print('程序未产生异常时则执行当前代码...')
# finally:
#     print('程序无论是否出现异常都执行此语句...')


# 在函数中使用try语句
# def func_except():
#     try:
#         print('程序运行...')
#         return 1
#         raise KeyError
#     except KeyError:
#         print('key error 错误...')
#         return 2
#     else:
#         print('程序未产生异常时则执行当前代码...')
#         return 3
#     finally:
#         print('程序无论是否出现异常都执行此语句...')
#         # 如果finally中出现return 则优先返回finally中的返回值
#         return 4
#
# res = func_except()
# print(res)


# 上下文管理协议 - 魔术方法
class Sample:
    def __enter__(self):
        try:
            self.file_obj = open('顾安.txt')
        except FileNotFoundError:
            self.file_obj = None
        print('我被执行了: __enter__')
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        print('我被执行了: __exit__')
        if self.file_obj is None:
            print('当前文件不存在...')

    def run(self):
        print('程序启动')

with Sample() as sample:
    sample.run()
```