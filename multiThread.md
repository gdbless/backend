# python 多线程详解

来源：<a href="https://blog.csdn.net/m0_67900727/article/details/123399522">https://blog.csdn.net/m0_67900727/article/details/123399522</a>

什么是线程？

线程也叫轻量级进程，是操作系统能够进行计算调度的最小单位，它被包含在进程之中，是进程中的实际运作单位。

线程自己不拥有系统资源，只拥有一点儿在运行中必不可少的资源，但它可与同属一个进程的其他线程共享进程所拥有的全部资源。一个线程可以创建和撤销另一个线程，同一个进程中的多个线程之间可以并发执行。

为什么要使用多线程？

线程在程序中是独立的、并发的执行流。与分割的进程相比，进程中线程之间的隔离程度要小，它们共享内存、文件句柄和其他进程应有的状态。

因为线程的划分尺度小于进程，使得多线程程序的并发性高。进程在执行过程之中拥有独立的内存单元，而多个线程共享内存，从而极大的提升了程序的运行效率。

线程比进程具有更高的性能，这是由于同一个进程中的线程都有共性，线程之间很容易实现通信。

操作系统再创今晋城市，必须为该进程分配独立的内存空间，并分配大量的相关资源，但创建线程则简单得多。因此，使用多线程来实现并发比使用多进程的性能要高的多。

总结起来，使用多线程编程具有以下几个优点：
- 进程之间不能共享内存，但线程之间共享内存非常容易。
- 操作系统在创建进程时，需要为该进程重新分配系统资源，但创建线程的代价则小得多。因此使用多线程来实现多任务并发执行比使用多进程的效率要高。
- python 语言内置了多线程功能支持，而不是单纯地作为底层操作系统的调度方式，从而简化了 python 的多线程编程。

**普通创建方式**
```python
import threading


def run(n):
    print('task', n)
    time.sleep(1)
    print('2s')
    time.sleep(1)
    print('1s')
    time.sleep(1)
    print('0s')
    time.sleep(1)


if __name__ == '__main__':
    t1 = threading.Thread(target=run, args=('t1',))
    t2 = threading.Thread(target=run, args=('t2',))
    
    t1.start()
    t2.start()

```

**自定义线程**

继承 threading.Thread 来定义线程类，其本制是重构 Thread 类中的 run 方法

```python
class MyThread(threading.Thread):
    def __init__(self, n):
        super(MyThread, self).__init__()  # 重构 run 函数必须写
        self.n = n

    def run(self):
        print('task', self.n)
        time.sleep(1)
        print('2s')
        time.sleep(1)
        print('1s')
        time.sleep(1)
        print('0s')
        time.sleep(1)


if __name__ == '__main__':
    t1 = MyThread('t1')
    t2 = MyThread('t2')
    t1.start()
    t2.start()

```

**守护线程**

下面这个例子，这里使用 setDaemon(True) 把所有的子线程都变成了主线程的守护线程，因此当主线程结束后，子线程也会随之结束，所以当主线程结束后，整个程序就退出了。

所谓“线程守护”，就是主线程不管该线程的执行情况，只要其他子线程结束且主线程执行完毕，主线程就会关闭。也就是说：主线程不等待该守护线程的执行完再去关闭。

```python
def run(n):
    print('task', n)
    time.sleep(1)
    print('3s')
    time.sleep(1)
    print('2s')
    time.sleep(1)
    print('1s')


if __name__ == '__main__':
    t = threading.Thread(target=run, arg=('t1',))
    t.setDaemon(True)
    t.start()
    print('end')

```
通过执行结果可以看出，设置守护线程之后，当主线程结束时，子线程也将立即结束，不再执行。

**主线程等待子线程结束**

为了让守护线程执行结束之后，主线程再结束，我们可以使用 join 方法，让主线程等待子线程执行完。

```python
def run(n):
    print('task', n)
    time.sleep(2)
    print('5s')
    time.sleep(2)
    print('3s')
    time.sleep(2)
    print('1s')


if __name__ == '__main__':
    t = threading.Thread(target=run, args=('t1',))
    t.setDaemon(True)  # 把线程设置为守护线程，必须再 start() 之前设置
    t.start()
    t.join()  # 设置主线程等待子线程结束
    print('end')

```

**多线程共享全局变量**

线程是进程的执行单元，进程是系统分配资源的最小执行单位，所以在同一个进程中的多线程是共享资源的。

```python
g_num = 100

def work1():
    global g_num
    for i in range(3):
        g_num += 1
    print('in work1 g_num is: {:d}'.format(g_num))

def work2():
    global g_num
    print('in work2 g_num is: {:d}'.format(g_num))


if __name__ == '__main__':
    t1 = threading.Thread(target=work1)
    t1.start()
    time.sleep(1)
    t2.threading.Thread(target=work2)
    t2.start()

```

由于线程之间是进行随机调度，并且每个线程可能只执行 n 条之后，当多个线程同时修改同一条数据时可能会出现脏数据，所以出现了线程锁，即同一时刻允许一个线程执行操作。线程锁用于锁定资源，可以定义多个锁，像下面的代码，当需要独占某一个资源时，任何一个锁都可以锁定这个资源，就好比你用不同的锁都可以把这个相同的门锁住一样。

由于线程之间是进行随即调度的，如果有多个线程同时操作一个对象，如果没有很好地保护该对象，会造成程序结果的不可预期，称之为 “线程不安全”。为了防止这种情况的发生，就出现了互斥锁(Lock)。

**互斥锁**

```python
def work():
    global n
    lock.acquire()
    temp = n
    time.sleep(0.1)
    n = temp - 1
    lock.release()


if __name__ == '__main__':
    lock = Lock()
    n = 100
    l = []
    for i in range(100):
        p = threading.Thread(target=work)
        l.append(p)
        p.start()
    
    for p in l:
        p.join()

```

**递归锁**

RLock 类的用法和Lock类一模一样，但它支持嵌套，在多个锁没有释放的时候一般会使用 RLock 类。

```python
def func(work):
    global gl_num
    lock.acquire()
    gl_num += 1
    time.sleep(1)
    print(gl_num)
    lock.release()


if __name__ == '__main__':
    gl_num = 0
    lock = threading.RLock()
    for i in range(10):
        t = threading.Thread(target=func, args=(lock,))
        t.start()

```

**信号量（BoundedSemaphore 类）**

互斥锁同时只允许一个线程更改数据，而 Semaphore 是同时允许一定数量的线程更改数据。比如厕所有3个坑，那最多只允许3个人上厕所，后面的任只能等里面有人出来了才能进去。

```python
def run(n, semaphore):
    semaphore.acquire()
    time.sleep(3)
    print('run the thread:{}\n'.format(n))
    semaphore.release()


if __name__ == '__main__':
    num = 0
    semaphore = threading.BoundedSemaphore(5)  # 最多允许 5 个线程同时运行
    for i in range(22):
        t = threading.Thread(target=run, args=('t-{}'.format(i), semaphore))
        t.start()
    
    while threading.active_count() != 1:
        pass
    else:
        print('-------all threads done--------')

```

python 线程的事件用于主线程控制其他线程的执行，事件是一个简单的线程同步对象，其主要提供以下的几个方法：

- clear 将 flag 设置为 False
- set 将 flag 设置为 True
- is_set 判断是否设置了 flag
- wait 会一直监听 flag, 如果没有检测到 flag 就一直处于阻塞状态

事件处理的机制：全局定义了一个 Flag，当 Flag 的值为 False，那么 event.wait() 就会阻塞，当 flag 的值为 True，那么 event.wait() 便不再阻塞。

```python
event = threading.Event()
def lighter():
    count = 0
    event.set()  # 初始为绿灯
    while True:
        if 5 < count <= 10:
            event.clear()  # 红灯，清除标志位
            print('Imgreen light is on...')
        elif count > 10:
            event.set()  # 绿灯，设置标志位
            count = 0
        else:
            print('Imgreen light is on...')
        
        time.sleep(1)
        count += 1

def car(name):
    while True:
        if event.is_set():  # 判断是否设置了标志位
            print('{} is running'.format(name))
            time.sleep(1)
        else:
            print('{} sees red light, waiting...'.format(name))
            event.wait()
            print('{} green light is on, start going...'.format(name))

startTime = time.time()
light = threading.Thread(target=lighter,)
light.start()

car = threading.Thread(target=car, args=('MINT',))

car.start()
endTime = time.time()

print('用时 {}', endTime-startTime)

```

## 补充：各种锁
来源：<a href="https://blog.csdn.net/ON_THE_WAY2/article/details/125284444?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-4-125284444-null-null.pc_agg_new_rank&utm_term=python%E4%B8%AD%E5%A4%9A%E7%BA%BF%E7%A8%8B%E5%92%8C%E5%A4%9A%E8%BF%9B%E7%A8%8B%E5%92%8C%E5%8D%8F%E7%A8%8B&spm=1000.2123.3001.4430">csdn</a>

```python
# 锁的作用：在多线程中使用lock可以让多个线程在共享资源的时候遵循一定的规则。
# 常见锁类型：
# Lock()/RLock：普通锁（互斥锁）
    # 解决资源争用，数据读取不一致等
# Semaphore ：信号量
    # 最多允许同时N个线程执行内容
# Event: 事件锁
    # 根据状态位，决定是否通过事件
# Condition: 条件锁

```

**互斥锁**

```python
# 互斥锁 LOCK/RLOCK

# 互斥锁就是解决资源征用，数据读取不一致等问题
# 当出现公共资源争用时，使用互斥锁来解决资源征用问题

# 互斥锁原理：锁只有一把，每次只能一个线程获得锁，然后进入执行区，最后释放锁给下一个线程实现排队效果，解决资源抢占问题

# 实例：
import time
import threading
num = 0  # 公共资源


def sum_num(i):
    print(f"lock start ... {i}")  # 提示锁开始的位置
    # lock.acquire()  # 获得锁
    with lock:  # 获得锁，并自动释放锁
        global num
        time.sleep(0.5)
        num += i
        print(num)
    # lock.release()  # 释放锁
    print(f"lock end ... {i}")  # 提示锁结束的位置


# lock = threading.Lock()  # 创建互斥锁对象
lock = threading.RLock()  # 创建互斥锁对象
for i in range(5):
    t = threading.Thread(target=sum_num, args=(i,))
    t.start()
# 结果：
"""
lock start ... 0
lock start ... 1
lock start ... 2
lock start ... 3
lock start ... 4
0
lock end ... 0
1
lock end ... 1
3
lock end ... 2
6
lock end ... 3
10
lock end ... 4
"""

```

**死锁**

```python
# LOCK 原始锁 获取锁之前不做判断 直到获取锁为止
# RLOCK 重入锁 获取锁之前先判断 如果自己有了锁，那就立即放回锁

# 第一种 死锁
# 死锁--Lock 获取锁之前不做判断，直到获取锁为止
import threading  # 当如threading模块
lock = threading.Lock()  # 生成一把锁
lock.acquire()  # 获得锁
print("lock acquire...1")
lock.acquire()  # 再次获得锁，当时由于是原始锁，导致无法获得锁时，只会干耗时间，导致死锁
print("lock acquire...2")
lock.release()  # 释放锁
print("lock release")
lock.release()  # 释放锁
print("lock release")

# RLock 获取锁之前判断当前主线程是否有锁，自己有锁则把锁返回

import threading

lock = threading.RLock()  # 生成一把锁
lock.acquire()  # 获得锁
print("lock acquire...1")
lock.acquire()  # 再次获得锁，此时是重入锁，则会事前判断当前线程是否有锁，若有则返回锁再获取锁，若无则直接获取锁，不会死锁
print("lock acquire...2")
lock.release()
print("lock release")
lock.release()
print("lock release")

# # 结果：
# # lock acquire...1
# # lock acquire...2
# # lock release
# # lock release


# 第二种 死锁
from threading import Thread, Lock  # 从threading模块中导入Thread和Lock类
from time import sleep


class Account:  # 定义账户类
    # 每个账户自带一把锁
    # 当balance发生变动就上锁
    # _id是账户名字、balance是账户金额、lock是锁
    def __init__(self, _id, balance, lock):  # 定义初始化方法，其中包含_id balance lock属性
        self._id = _id
        self.balance = balance
        self.lock = lock

    # get money
    def withdraw(self, amount):
        self.balance -= amount

    # save money
    def deposite(self, amount):
        self.balance += amount

    # check money
    def get_balance(self):
        return self.balance

# 生成两个账户
# 第一个账户hepang，金额为10000，并且创建一个锁对象
# 第二个账户luoziyao，金额为5000，并且创建一个锁对象
hepang = Account("hepang", 10000, Lock()) # lock是一个类，Lock()创建一个锁对象
luoziyao = Account("luoziyao", 5000, Lock())

# 转账函数，转账基本原则：谁的账户要动，需要先上锁
# from_参数为转出账户，to为转入账户，amount为金额
def transfer(from_,to,amount):
    # from_账户上锁
    if from_.lock.acquire():
        from_.withdraw(amount)  # from_账户金额减少
        sleep(100)
        print('wait...')
        if to.lock.acquire():
            to.deposite(amount)  # to加钱
            to.lock.release()  # 价钱完毕，解锁
        from_.lock.release()  # from_账户也转账完毕，解锁
    print(f'{from_._id}给{to._id}转了{amount}元')

# 生成两个线程
t1 = Thread(target=transfer, args=(hepang, luoziyao, 4000))  # hepang给luoziyao转4000
t2 = Thread(target=transfer, args=(luoziyao, hepang, 4000))  # hepang给luoziyao转4000
# 两个账户同时转账，线程t1先给hepang上锁，线程t2给luoziyao上锁
# 两个线程往下运行，准备给转入账户上锁，发现对方已经锁上了
# hepang需要获取luoziyao的锁
# luoziyao需要hepang的锁
t1.start()
t2.start()

# 避免死锁
# 尽量避免同一个线程对多个lock进行锁定
# 多个线程需要对多个lock进行锁定的时候，尽量保证它们以相同的顺序上锁

```


**信号锁**

```python
# 信号锁-->本质是一个计数器，主要解决进程信息同步
# 进程通信的几种方式：
# 管道，消息队列，信号，信号量，共享内存
# 共享内存：映射部分内核空间到用户空间；信号量则是控制几个程序进行读写

import time
import threading
num = 0


def sum_num(i):
    with lock:
        time.sleep(2)
        print(f"lock start ... {i}")
    # lock.acquire()  # 获得锁
    with lock2:
        global num
        time.sleep(0.5)
        num += i
        print(num)
    # lock.release()  # 释放锁
    # print(f"lock end ... {i}")


# lock = threading.Lock()  # 创建互斥锁对象
lock = threading.BoundedSemaphore(2)  # 创建信号锁，同时定义2把锁，可以有2个线程去执行
lock2 = threading.Lock()  # 创建互斥锁对象
for i in range(5):
    t = threading.Thread(target=sum_num, args=(i,))
    t.start()

```

**事件锁**

```python
# 事件锁：用于主线程控制其他线程的执行
# 事件机制：全局定义了一个“Flag”，如果“Flag”的值为False，那么当程序执行wait方法时就会阻塞
# 如果“Flag”值为True，那么执行wait方法时便不再阻塞。
# 这种锁，类似交通红绿灯（默认是红灯），它属于在红灯的时候一次性阻挡所有线程，在绿灯的时候，一次性放行所有的排队中的线程。
# Event是线程间通信的机制之一：一个线程发送一个event信号，其他的线程则等待这个信号

# Event: 事件锁
# 实例方法：
# e.wait([timeout])：堵塞线程，直到Event对象内部标识位被设为True或超时（如果提供了参数timeout）
# e.set() ：将标识位设为Ture
# e.clear() ：将标识伴设为False
# e.isSet() ：判断标识位是否为Ture

```

**条件锁**

```python
# 条件锁
# Condition: 条件锁
# 该机制会使得线程等待，只有满足某条件时，才释放n个线程。
# 实例方法：
# wait_for(func): 等待函数返回结果，如果结果为True-放行一个线程
# wait、lock.notify(N): 一次性放行N个wait
# acquire、release: 以上的wait和wait_for需要在锁中间使用

```



**GIL 全局解释器**

在非 python 环境中，单核情况下，同时只能有一个任务执行。多核时可以支持多个线程同时执行。但是在 python 中，无论多少个核，同时只能执行一个线程。究其原因，这就是由于 GIL 的存在导致的。

GIL 的全称是全局解释器，来源是 python 设计指出的考虑， 为了数据安全所做的决定。某个线程想要执行，必须先拿到 GIL, 我们可以把 GIL 看作是 “通行证”，并且在一个 python 进程之中， GIL只有一个。拿不到线程的通行证，就不允许进入 CPU 执行。GIL 只有在 cpython 中才有，因为 cpython 调用的是 C 语言的原生线程，所以它不能直接操作 CPU, 而只能利用 GIL 保证同一时间只能有一个线程拿到数据。而在 pypy 和 jpython 中是没有 GIL 的。

python 针对不同类型的代码执行效率也是不同的。

1. CPU 密集型代码（各种循环处理、计算等），在这种情况下，由于计算工作多， ticks 技术很快就会达到阈值，然后触发 GIL 的释放与再竞争（多个线程来回切换当然是需要消耗资源的），所以 python 下的多线程对 CPU 密集型代码并不友好。

2. IO 密集型代码（文件处理、网络爬虫等涉及文件读写操作），多线程能够有效提升效率（单线程下有 IO 操作会进行 IO 等待，造成不必要的时间浪费，而开启多线程能在线程 A 等待时，自动切换到线程 B，可以不浪费 CPU 的资源，从而能提升程序的执行效率）。所以 python 的多线程对 IO 密集型代码比较友好。

结论： I/O 密集型任务，建议采用多线程，还可以采用多进程+协程的方式（例如：爬虫多采用多线程处理爬取的数据）；对于计算密集型任务，python此时就不适用了。

---

# 另一版本介绍 threading 和 concurrent.futures
来源：<a href="https://blog.csdn.net/Qingyuyuehua/article/details/114296245">https://blog.csdn.net/Qingyuyuehua/article/details/114296245</a>

## 一. 多线程支持模块

- _thread（不推荐使用）
- threading
- quene

## 二. threading 模块的对象

<table><thead><tr><th>对象</th><th>描述</th></tr></thead><tbody><tr><td>Thread</td><td>表示一个执行线程的对象</td></tr><tr><td>Lock</td><td>锁原语对象（互斥锁）</td></tr><tr><td>RLock</td><td>可重入锁对象，单一线程可以获得已持有的锁（递归锁）</td></tr><tr><td>Condition</td><td>条件变量对象，使得一个线程等待另一个线程满足特定的“条件”</td></tr><tr><td>Event</td><td>任意数量线程等待某个事件的发生，该事件发生后所有等待该事件的线程将激活</td></tr><tr><td>Semaphore</td><td>为线程间共享的优先资源提供一个“计数器”，如果没有可用资源时会被阻塞</td></tr><tr><td>BoundedSemaphore</td><td>和Semaphore相似，不过不允许超过初始值</td></tr><tr><td>Timer</td><td>和Thread相似，不过运行前会等待一段时间</td></tr><tr><td>Barrier</td><td>创建一个障碍，必须达到指定数量线程才开始运行</td></tr></tbody></table>

1. Thread 类：

Thread 对象的属性和方法：

<table><thead><tr><th>属性</th><th>描述</th></tr></thead><tbody><tr><td><strong>对象数据属性</strong></td><td></td></tr><tr><td>name</td><td>线程名</td></tr><tr><td>ident</td><td>线程的标识符</td></tr><tr><td>daemon</td><td>布尔标志，表示这个线程是否是守护线程</td></tr><tr><td><strong>Thread对象方法</strong></td><td></td></tr><tr><td><strong>init</strong>(group=None, tatget=None, name=None, args=(),kwargs={}, verbose=None, daemon=None)</td><td>实例化一个线程对象，需要有一个可调用的target，以及其参数args或kwargs。还可以传递name或group参数，不过后者还没实现。此外，verbose标志也是可接受的，而daemon的值将会设定thread.daemon标志</td></tr><tr><td>start()</td><td>开始执行该线程</td></tr><tr><td>run()</td><td>定义线程功能的方法（通常在子类中被应用开发者重写）</td></tr><tr><td>join(timeout=None)</td><td>直到启动的线程终止之前一直挂起；除非给出timeout（秒）否则会一直阻塞</td></tr><tr><td>isAlivel()</td><td>布尔标志，表示这个线程是否还存活</td></tr></tbody></table>

创建线程的方法：

1. 创建 Thread 的实例，传给它一个函数

范例：

```python
import threading
from time import sleep, ctime


loops = [4, 2]

def loop(nloop, nsec):
    print("start loop" + str(nloop) + " at:" + str(ctime()))
    sleep(nsec)
    print("start loop" + str(nloop) + " at:" + str(ctime()))


if __name__ == '__main__':
    print("starting at: " + str(ctime()))
    threads = []
    nloops = range(len(loops))

    for i in nloops:
        t = threading.Thread(target=loop, args=(i, loops(i)))
        threads.append(t)

    for i in nloops:
        threads[i].start()
    
    for i in nloops:
        threads[i].join()
    
    print("All done at: " + str(ctime()))

```

2. 创建 Thread 的实例，传给它一个可调用的类实例

范例：

```python
import threading
from time import sleep, ctime


loops = [4, 2]

class ThreadFunc(object):
    def __init__(self, func, args, name=""):
        self.name = name
        self.func = func
        self.args = args
    
    def __call__(self):
        self.func(*self.args)


def loop(nloop, nsec):
    print("start loop " + str(nloop) + " at: " + str(ctime()))
    sleep(nsec)
    print("start loop " + str(nloop) + " at: " + str(ctime()))


if __name__ == '__main__':
    print("starting at:" + str(ctime()))

    threads = []
    nloops = range(len(loops))

    for i in nloops:
        t = threading.Thread(target=ThreadFunc(loop, (i, loop[i]), loop.__name__))
        threads.append(t)
    
    for i inn nloops:
        threads[i].start()
    
    for i in nloops:
        threads[i].join()

    print("All done at: " + str(ctime()))

```

3. 派生 Thread 的子类，并创建子类的实例

范例：

```python
import threading
from time import sleep, ctime

loops = [4, 2]

class ThreadFunc(object):
    def __init__(self, func, args, name=""):
        threading.Thread.__init__(self)
        self.name = name
        self.func = func
        self.args =args
    
    def run(self):
        self.func(*self.args)


def loop(nloop, nsec):
    print("Start loop " + str(nloop) + " at:",ctime())
    sleep(nsec)
    print("loop " + str(nloop) + " done at:",ctime())

if __name__ == '__main__':
    print("starting at:", ctime())
    threads = []
    nloops = range(len(loops))

    for i in nloops:
        t = MyThread(loop, (i, loops[i]), loop.__name__)
        threads.append(t)

    for i in nloops:
        threads[i].start()

    for i in nloops:
        threads[i].join()

    print("all DONE at:", ctime())

``` 

2. threading 模块的其他函数

<table><thead><tr><th>函数对象</th><th>描述</th></tr></thead><tbody><tr><td>activeCount()</td><td>返回当前活动的Thread对象个数</td></tr><tr><td>currentThread()</td><td>返回当前的Thread对象</td></tr><tr><td>enumerate()</td><td>返回当前活动的Thread对象列表</td></tr><tr><td>settrace(func)</td><td>为所有线程设置一个trace函数</td></tr><tr><td>setprofile(func)</td><td>为所有线程设置一个profile函数</td></tr><tr><td>stack_size(size=0)</td><td>返回新创建的线程的栈大小；或为后续创建线程设定栈的大小为size</td></tr></tbody></table>

## 三. 同步

1. 锁

锁的两种状态：锁定和未锁定

锁对象：Lock（或RLock）

获得锁：acquire()

释放锁：release()

多线程争夺锁时，允许第一个获得锁的线程进入临界区，并执行代码。之后所有到达的线程将被阻塞，直到第一个线程执行结束，退出临界区，并释放锁。此时其他等待的线程中随机一个（可根据Python实现的不同而有所区别）可以获得锁并进入临界区。

范例：

```python
from atexit import register
from random import randrange
from threading import Thread, Lock, currentThread
from time import sleep, ctime

class CleanOutputSet(set):
    def __str__(self):
        return ", ".join(x for x in self)

lock = Lock()
loops = (randrange(2,5) for x in range(randrange(3,7)))
remaining = CleanOutputSet()

def loop(nsec):
    myname = currentThread().name
    lock.acquire()
    remaining.add(myname)
    print("[%s] Started %s" % (ctime(), myname))
    lock.release()
    sleep(nsec)
    lock.acquire()
    remaining.remove(myname)
    print("[%s] Completed %s (%d secs)" % (ctime(), myname, nsec))
    print("    (remaining: %s)" % (remaining or "NONE"))
    lock.release()

if __name__ == "__main__":
    for pause in loops:
        Thread(target=loop, args=(pause,)).start()

@register
def _atexit():
    print("all DONE at: ",ctime())

```

上下文管理：

使用with语句，此时每个上下文管理器负责在进入该语句块前调用acquire()并在执行之后调用release()

范例：

```python
from atexit import register
from random import randrange
from threading import Thread, Lock, currentThread
from time import sleep, ctime

class CleanOutputSet(set):
    def __str__(self):
        return ", ".join(x for x in self)

lock = Lock()
loops = (randrange(2,5) for x in range(randrange(3,7)))
remaining = CleanOutputSet()

def loop(nsec):
    myname = currentThread().name
    with lock:
        remaining.add(myname)
        print("[%s] Started %s" % (ctime(), myname))

    sleep(nsec)
    with lock:
        remaining.remove(myname)
        print("[%s] Completed %s (%d secs)" % (ctime(), myname, nsec))
        print("    (remaining: %s)" % (remaining or "NONE"))

if __name__ == "__main__":
    for pause in loops:
        Thread(target=loop, args=(pause,)).start()

@register
def _atexit():
    print("all DONE at: ",ctime())

```

2. 信号量

信号量是一个计数器，当资源消耗时递减，资源释放时递增，如果没有可用资源时消耗资源会被阻塞。

信号量对象：Semaphore（或BoundedSemaphore）

资源释放：release()

资源消耗：acquire()

范例：

```python
# 使用锁和信号量来模拟一个糖果机
from atexit import register
from random import randrange
from threading import BoundedSemaphore, Lock, Thread
from time import sleep, ctime


lock = Lock()
MAX = 5
candytray = BoundedSemaphore(MAX)

def refill():
    with lock:
        print("Refilling candy...")
        try:
            candytray.release()
        except ValueError:
            print("full, skipping")
        else:
            print("OK")

def buy():
    with lock:
        print("Buying candy...")
        if candytray.acquire(False):
            print("OK")
        else:
            print("Empty, skipping")

def produce(loops):
    for i in range(loops):
        refill()
        sleep(randrange(3))

def consumer(loops):
    for i in range(loops):
        buy()
        sleep(randrange(3))


if __name__ == '__main__':
    print("Starting at: ", ctime())
    nloops = randrange(2, 6)
    print("THE CANDY MACHINE (full with %d bars)!" % MAX)
    Thread(target=consumer, args=(randrange(nloops, nloops+MAX+2),)).start()
    Thread(target=produce, args=(nloops,)).start()

@register
def _atexit():
    print("All done at: ", ctime())

```

## 四. quene 模块

1. quene 模块

常用属性：

<table><thead><tr><th>属性</th><th>描述</th></tr></thead><tbody><tr><td><strong>类</strong></td><td></td></tr><tr><td>Queue(maxsize=0)</td><td>创建一个先入先出的队列。如果给定最大值，则在队列没有空间时阻塞；否则为无限队列</td></tr><tr><td>LifoQueue(maxsize=0)</td><td>创建一个后入先出的队列。如果给定最大值，则在队列没有空间时阻塞；否则为无限队列</td></tr><tr><td>PriorityQueue(maxsize=0)</td><td>创建一个优先级队列。如果给定最大值，则在队列没有空间时阻塞；否则为无限队列</td></tr><tr><td><strong>异常</strong></td><td></td></tr><tr><td>Empty</td><td>当对空队列调用get*()方法时抛出异常</td></tr><tr><td>Full</td><td>当对已满的队列调用put*()方法时抛出异常</td></tr><tr><td><strong>queue对象方法</strong></td><td></td></tr><tr><td>qsize()</td><td>返回队列大小（由于返回时队列大小可能被其他线程修改，所以该值为近似值）</td></tr><tr><td>empty()</td><td>如果队列为空，则返回True；否则，返回False</td></tr><tr><td>full()</td><td>如果队列为满，则返回True；否则，返回False</td></tr><tr><td>put(item, block=True,timeout=None)</td><td>将item放入队列。如果block为True（默认）且timeout为 None，则在有可用空间之前阻塞；如果timeout为正值，则最多阻塞timeout秒；如果block为False，则抛出Empty异常</td></tr><tr><td>put_nowait(item)</td><td>和put(item, False)相同</td></tr><tr><td>get(block=True, timeout=None)</td><td>从对列中取得元素。如果给定了block(非0)，则一直阻塞到有可用的元素为止</td></tr><tr><td>get_nowait()</td><td>和get(False)相同</td></tr><tr><td>task_done()</td><td>用于表示对列中某个元素已执行完成，该方法会被下面的join()使用</td></tr><tr><td>join()</td><td>在队列中所有元素执行完毕并调用上面的task_done()信号之前，保持阻塞</td></tr></tbody></table>

2. 队列范例及生产者-消费者问题

```python
import threading
from random import randint
from time import sleep, ctime
frome quene import Quene


class MyThread(threading.Thread):
    def __init__(self, func, args, name=""):
        threading.Thread.__init__(self)
        self.name=name
        self.func = func
        self.args = args
    
    def run(self):
        print("starting", self.name, "at:", ctime())
        self.res = self.func(*self.args)
        print(self.name, "finished at:", ctime())

    def getResult(self):
        return self.res


def writeQ(quene):
    print("producing object for Q...")
    quene.put("xxx", 1)
    print("size now", quene.qsize())

def readQ(quene):
    val = quene.get(1)
    print("consumed object from Q... size now", quene.qsize())

def writer(quene, loops):
    for i in range(loops):
        writeQ(quene)
        sleep(randint(1, 3))

def reader(quene, loops):
    for i in range(loops):
        readQ(quene)
        sleep(randint(2, 5))

funcs = [writer, reader]
nfuncs = range(len(funcs))


if __name__ == "__main__":
    nloops = randint(2, 5)
    q = Quene(32)
    threads = []

    for i in nfuncs:
        t = MyThread(func[i], (q, nloops), funcs[i].__name__)
        threads.append(t)

    for i in nfuncs:
        threads[i].start()
    
    for i in nfuncs:
        threads[i].join()
    
    print("All done")

```

## 五. 高级线程模块 concurrent.futures

concurrent.futures 主要用于管理并发任务池

常用属性：

<table><thead><tr><th>属性</th><th>描述</th></tr></thead><tbody><tr><td><strong>类</strong></td><td></td></tr><tr><td>Executor</td><td>抽象类。提供异步执行调用方法。要通过它的子类调用，而不是直接调用。</td></tr><tr><td>ThreadPoolExecutor(max_workers=None, thread_name_prefix=“”, initializer=None, initargs=())</td><td>是 Executor 的子类，使用线程池来异步执行调用。使用最多 max_workers 个线程的线程池来异步执行调用。initializer 是在每个工作者线程开始处调用的一个可选可调用对象。initargs 是传递给初始化器的元组参数。任何向池提交更多工作的尝试， initializer 都将引发一个异常，当前所有等待的工作都会引发一个 BrokenThreadPool</td></tr><tr><td>Future</td><td>将可调用对象封装为异步执行。Future 实例由 Executor.submit() 创建</td></tr><tr><td><strong>异常</strong></td><td></td></tr><tr><td>CancelledError</td><td>future 对象被取消时会触发</td></tr><tr><td>TimeoutError</td><td>future 对象执行超出给定的超时数值时引发</td></tr><tr><td>BrokenExecutor</td><td>派生于 RuntimeError 的异常类，当执行器被某些原因中断而且不能用来提交或执行新任务时就会被引发。</td></tr><tr><td>BrokenThreadPool</td><td>当 ThreadPoolExecutor 中的其中一个工作者初始化失败时会引发派生于 BrokenExecutor 的异常类</td></tr><tr><td><strong>Executor对象方法</strong></td><td></td></tr><tr><td>submit(fn, *args, **kwargs)</td><td>调度可调用对象 fn，以 fn(*args **kwargs) 方式执行并返回 Future 对像代表可调用对象的执行</td></tr><tr><td>map(func, *iterables, timeout=None, chunksize=1)</td><td>将 iterable 参数传入的可迭代对象func传递给不同的线程来处理，返回所有结果收集后的可迭代对象。如果从原始调用到 Executor.map() 经过 timeout 秒后， <strong>next</strong>() 已被调用且返回的结果还不可用，那么已返回的迭代器将触发 concurrent.futures.TimeoutError 。 timeout 可以是整数或浮点数。如果 timeout 没有指定或为 None ，则没有超时限制。如果 *func调用引发一个异常，当从迭代器中取回它的值时这个异常将被引发</td></tr><tr><td>shutdown(wait=True)</td><td>当待执行的 future 对象完成执行后向执行者发送信号，它就会释放正在使用的任何资源。 在关闭后调用 Executor.submit() 和 Executor.map() 将会引发 RuntimeError。如果 wait 为 True 则此方法只有在所有待执行的 future 对象完成执行且释放已分配的资源后才会返回。 如果 wait 为 False，方法立即返回，所有待执行的 future 对象完成执行后会释放已分配的资源。 不管 wait 的值是什么，整个 Python 程序将等到所有待执行的 future 对象完成执行后才退出。</td></tr><tr><td><strong>Future 对象方法</strong></td><td></td></tr><tr><td>result(timeout=None)</td><td>返回调用返回的值。如果调用还没完成那么这个方法将等待 timeout 秒。如果在 timeout 秒内没有执行完成，concurrent.futures.TimeoutError 将会被触发。timeout 可以是整数或浮点数。如果 timeout 没有指定或为 None，那么等待时间就没有限制。如果 futrue 在完成前被取消则 CancelledError 将被触发。如果调用引发了一个异常，这个方法也会引发同样的异常。</td></tr><tr><td>done()</td><td>如果调用已被取消或正常结束那么返回 True</td></tr><tr><td>add_done_callback(fn)</td><td>附加可调用 fn 到 future 对象。当 future 对象被取消或完成运行时，将会调用 fn，而这个 future 对象将作为它唯一的参数。加入的可调用对象总被属于添加它们的进程中的线程按加入的顺序调用。如果可调用对象引发一个 Exception 子类，它会被记录下来并被忽略掉。如果可调用对象引发一个 BaseException 子类，这个行为没有定义。如果 future 对象已经完成或已取消，fn 会被立即调用</td></tr><tr><td><strong>模块函数</strong></td><td></td></tr><tr><td>wait(fs, timeout=None, return_when=ALL_COMPLETED)</td><td>等待 fs 指定的 Future 实例（可能由不同的 Executor 实例创建）完成。 返回一个由集合构成的具名 2 元组。 第一个集合名称为 done，包含在等待完成之前已完成的期程（包括正常结束或被取消的 future 对象）。 第二个集合名称为 not_done，包含未完成的 future 对象（包括挂起的或正在运行的 future 对象）。<br>timeout 可以用来控制返回前最大的等待秒数。 timeout 可以为 int 或 float 类型。 如果 timeout 未指定或为 None ，则不限制等待时间。<br>return_when 指定此函数应在何时返回。它必须为以下常数之一:FIRST_COMPLETED（函数将在任意可等待对象结束或取消时返回）、FIRST_EXCEPTION（函数将在任意可等待对象因引发异常而结束时返回。当没有引发任何异常时它就相当于 ALL_COMPLETED）、ALL_COMPLETED（函数将在所有可等待对象结束或取消时返回）</td></tr><tr><td>as_completed(fs, timeout=None)</td><td>返回一个包含 fs 所指定的 Future 实例（可能由不同的 Executor 实例创建）的迭代器，这些实例会在完成时生成 future 对象（包括正常结束或被取消的 future 对象）。 任何由 fs 所指定的重复 future 对象将只被返回一次。 任何在 as_completed() 被调用之前完成的 future 对象将优先被生成。 如果 next() 被调用并且在对 as_completed() 的原始调用 timeout 秒之后结果仍不可用，则返回的迭代器将引发 concurrent.futures.TimeoutError。 timeout 可以为整数或浮点数。 如果 timeout 未指定或为 None，则不限制等待时间。</td></tr></tbody></table>

范例：

```python
from concurrent.futures import ThreadPoolExecutor
import time

# 参数 times 用来 test 的时间
def test(times):
    time.sleep(times)
    print("test {}s finished".format(times))
    return times

executor = ThreadPoolExecutor(max_worker=2)

# 通过 submit 函数提交执行的函数到线程池中，submit 函数立即返回，不阻塞
task1 = executor.submit(test, (3))
task2 = executor.submit(test, (2))

# done 方法用于判定某个任务是否完成
print(task1.done())
time.sleep(4)
print(task1.done())

# result 方法可以获取 task 的执行结果
print(task1.result())

```

- ThreadPoolExecutor 构造实例的时候，传入 max_workers 参数来设置线程池中最多能同时运行的线程数目。

- 使用 submit 函数来提交线程需要执行的任务（函数名和参数）到线程池中，并返回该任务的句柄（future对象），submit() 不阻塞立即返回

- 通过 submit 函数返回的 future 对象，能够使用 done() 方法判断该任务是否结束。上面的例子可以看出，由于任务有2s的延时，在 task1 提交后立刻判断， task1 还未完成，而在延时4s之后判断， task1 就完成了

- 使用 result() 方法可以获取任务的返回值。查看内部代码，发现这个方法是阻塞的

范例2：

```python
from concurrent.futures import ThreadPoolExecutor
import time


# 参数 times 用来 test 的时间
def test(times):
    time.sleep(times)
    print("test {}s finished".format(times))
    return times

executor = ThreadPoolExecutor(max_worker=2)
ns = [3, 2, 4]

for data in executor.map(test, ns):
    print("test {}s success".format(data))

```

- map 方法与 python 标准库中的map含义相同，都是将序列中的每个元素都执行同一个函数。上面的代码就是对 ns 的每个元素都执行 test 函数，并分配线程。可以看到输出顺序和 urls 列表的顺序相同。

范例3：

```python
from concurrent.futures import ThreadPoolExecutor, as_completed
import time

# 参数times用来test的时间
def test(times):
    time.sleep(times)
    print("test {}s finished".format(times))
    return times

executor = ThreadPoolExecutor(max_workers=2)
ns = [3, 2, 4]
all_task = [executor.submit(test, (n)) for n in ns]

for future in as_completed(all_task):
    data = future.result()
    print("in main: test {}s success".format(data))

```

- as_completed() 方法是一个生成器，在没有任务完成的时候，会阻塞，在有某个任务完成的时候，会 yield 这个任务，就能执行 for 循环下面的语句，然后继续阻塞住，循环到所有的任务结束。从结果也可以看出，先完成的任务会先通知主线程。

范例 4：

```python

from concurrent.futures import ThreadPoolExecutor, wait, ALL_COMPLETED, FIRST_COMPLETED
import time


# 参数 times 用来 test 的时间
def test(times):
    time.sleep(times)
    print("get page {}s finished".format(times))
    return times

executor = ThreadPoolExecutor(max_worker=2)
ns = [3, 2, 4]
all_task = [executor.submit(test, (n)) for n in ns]
wait(all_task, return_when=ALL_COMPLETED)
print("main")

```

- wait方法接收3个参数，等待的任务序列、超时时间以及等待条件。等待条件 return_when 默认为 ALL_COMPLETED，表明要等待所有的任务都结束。可以看到运行结果中，确实是所有任务都完成了，主线程才打印出main。

concurrent.futures 部分 4 个范例参考自<a href="https://www.jianshu.com/p/b9b3d66aa0be">https://www.jianshu.com/p/b9b3d66aa0be</a>

案例 5：

```python
import concurrent.futures
import urllib.request


URLS = ['http://www.foxnew.com/'
        'http://www.cnn.com/',
        'http://europe.wsj.com/',
        'http://www.bbc.co.uk/',
        'http://some-made-up-domain.com/']

# Retrieve a single page and report the URL and contents
def load_url(url, timeout):
    with urllib.request.urlopen(url, timeout=timeout) as conn:
        return conn.read()

# We can use a with statemetn to ensure thread are cleaned up promptly
with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
    # Start the load operations and mark each future with its URL
    future_to_url = {executor.submit(load_url, url, 60):url for url in URLS}
    for future in concurrent.futures.as_completed(future_to_url):
        url = future_to_url(future)
        try:
            data = future.result()
        except Exception as exc:
            print("%r generated an exxeption: %s" %(url, exc))

```
-----
以下来源：<a href="https://zhuanlan.zhihu.com/p/438627177">https://zhuanlan.zhihu.com/p/438627177</a>

**那么已经有threading、multiprocess了，为什么还要整出一个concurrent.futures呢？**

concurrent.futures是3.2引入的新库，在python的多线程threading、多进程multiprocesssing上进一步封装，实现了进程池和线程池。之前介绍多进程时就讲到了multiprocessing.Pool（进程池），不同的是，concurrent.futures实现的都是异步操作。

concurrent.futures主要实现了进程池和线程池，适合做派生一堆任务，异步执行完成后，再收集这些任务，且保持相同的api，池的引入带来了一定好处：

- 程序猿开发更快，代码简洁，调调函数
- 进程线程复用，减去了大量开辟，删除进程线程时的开销
- 有效避免了因为创建进程线程过多，而导致负荷过大的问题
- concurrent.futures是重要的异步编程库。内部实现机制非常复杂，简单来说就是开辟一个固定大小为n的进程池/线程池。进程池中最多执行n个进程/线程，当任务完成后，从任务队列中取新任务。若池满，则排队等待。
