# Days-06

## 使用正则表达式

这一块涉及的知识点比较多，具体详细内容可参考以下地址

[《正则表达式30分钟入门教程》](https://deerchao.cn/tutorials/regex/regex.htm)

### Python对正则表达式的支持

Python提供了re模块来支持正则表达式相关操作，下面是re模块中的核心函数

| 函数 |   说明 |
| :----: | :----: |
| compile(pattern, flags=0)  | 编译正则表达式返回正则表达式对象 |
| match(pattern, string, flags=0) | 用正则表达式匹配字符串 成功返回匹配对象 否则返回None |
| search(pattern, string, flags=0) | 搜索字符串中第一次出现正则表达式的模式 成功返回匹配对象 否则返回None |
| split(pattern, string, maxsplit=0, flags=0) | 用正则表达式指定的模式分隔符拆分字符串 返回列表 |
| sub(pattern, repl, string, count=0, flags=0) | 用指定的字符串替换原字符串中与正则表达式匹配的模式 可以用count指定替换的次数 |
| fullmatch(pattern, string, flags=0) | match函数的完全匹配（从字符串开头到结尾）版本 |
| findall(pattern, string, flags=0) | 查找字符串所有与正则表达式匹配的模式 返回字符串的列表 |
| finditer(pattern, string, flags=0) | 查找字符串所有与正则表达式匹配的模式 返回一个迭代器 |
| purge() | 清除隐式编译的正则表达式的缓存|
| re.I / re.IGNORECASE | 忽略大小写匹配标记 |
| re.M / re.MULTILINE | 多行匹配标记 |

#### 如何正确使用正则表达式

例子：验证输入用户名和QQ号是否有效并给出对应的提示信息
```
import re

def main():
    username = input('请输入用户名：')
    qq = input('请输入QQ号：')
    m1 = re.match(r'^[0-9a-zA-Z_]{6,20}$', username) #检测用户名是否只包括数字和字母，特殊符号_,长度在6-20之间
    if not m1:
        print('请输入有效的用户名.')
    m2 = re.match(r'^[1-9]\d{4,11}$', qq) #检测QQ是否只包括数字，长度在4-11之间
    if not m2:
        print('请输入有效的QQ号.')
    if m1 and m2:
        print('你输入的信息是有效的!')

if __name__ == '__main__':
    main()
```
##### 在爬虫项目中，关于正则表达式的编写比较麻烦，后面会考虑用到Beautiful Soup或Lxml来进行匹配和信息的提取

## 进程和线程

使用Python实现并发编程主要有3种方式：多进程、多线程、多进程+多线程

这里可以举出一个例子来表示该代码使用多进程的方式将两个下载任务放到不同的进程中

```
from multiprocessing import Process #通过Process类创建进程对象
from os import  getpid
from random import randint
from time import time, sleep

def download_task(filename):
    print('启动下载进程，进程号[%d].' % getpid())
    print('开始下载%s...' % filename)
    time_to_download = randint(5, 10)
    sleep(time_to_download)
    print('%s下载完成！耗费了%d秒' % (filename, time_to_download))
    
def main():
    start = time()
    p1 = Process(target=download_task, args=('Python入门到入土',))
    p1.start() #start方法用来启动进程
    p2 = Process(target=download_task, args=('我的天才女友',))
    p2.start()
    p1.join() #join方法用来等待进程结束
    p2.join()
    end = time()
    print('总共耗费了%.2f秒.' % (end - start))
    #以上两个进程同时启动，程序执行时间大大缩短
    
if __name__ == '__main__':
    main()
```

考虑到无论是多进程还是多线程，只要数量一多，效率肯定上不去，在切换进程/线程时通常需要：保存原有的CPU寄存器状态、内存页等->恢复上次的寄存器状态、切换内存页等，这是第一个要考虑的；第二个考虑的是任务的类型，可以把任务分为计算密集型和I/O密集型。

计算密集型任务的特点是要进行大量的计算，消耗CPU资源，这类任务用Python这种脚本语言去执行效率通常很低，最优的办法是采用Python中嵌入C/C++代码的机制。

I/O密集型任务的特点是CPU消耗很少，任务的大部分时间都在等待I/O操作完成，如果启动多任务，就可以减少I/O等待时间从而让CPU高效率运转。

