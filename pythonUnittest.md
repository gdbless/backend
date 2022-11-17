# python 单元测试

来源：<a href="https://blog.csdn.net/weixin_40611700/article/details/120506451">https://blog.csdn.net/weixin_40611700/article/details/120506451</a>

不管是测试的函数，类，还是接口，都把其放到一个函数里，作为被测试内容。

在 python 中进行测试，判断结果用 assert 而不是 if。

## assert 介绍

断言：判断测试用例的结果是否成功

- assertEqual() 断言是否相等
- assertTrue() 断言是否为真
- assertIn() 断言是否包含

## 测试框架

- unittest python 内置
- pytest 第三方库，更强大

下以 unittest 介绍

## unitttest 的几个概念

test case: 测试用例
test suite: 测试套件/测试集
test loader：测试加载（加载测试用例）
test runner: 运行器/执行器
fixture：夹具（前置准备和后置处理）


## 收集测试用例

项目下新建一个 tests 包，来管理所有的测试用例，新建一个 run.py 来收集用例和运行用例

! 注意：

1. 模块必须以 test 开头，否则收集不到

2. 因为 tests 是包，包内必须有 __init__.py 模块

3. run.py 位置与 tests 是平级的

```python
import unittest


def login(username=None, password=None):
    if username is None or password is None:
        return {"code":"400", "msg":"用户名或密码为空"}
    if username == 'yuz' and password == '123':
        return {"code":"200", "msg":"登录成功"}
    return {"code":"300", "msg":"用户名或密码错误"}


# 类遵守规则：必须集成 unittest.TestCase
class TestLogin(unittest.TestCase):
    # 测试用例
    # 成功用例
    def test_login_1(self):
        username = 'li'
        password = '123'
        expected = {"code":"300", "msg":"用户名或密码错误"}
        actual = login(username, password)
        self.assertEqual(expected, actual)

    # 失败用例
    def test_login_2(self):
        username = 'yuz'
        password = '123'
        expected = {"code":"300", "msg":"用户名或密码错误"}
        actual = login(username, password)
        self.assertEqual(expected, actual)

```

## 收集测试用例

run.py 模块下代码如下：

```python
import unittest

# tests：上一步指定的包名
suite = unittest.defaultTestLoader.discover('tests')
# 运行用例
runner = unittest.TextTestRunner()
runner.run(suite)

```

## 夹具（fixture）

夹具的执行过程：

每个用例之前，自动执行 setUp;

每个用例之后，自动执行 tearDown.


```python
import unittest


class TestAdd(unittest.TestCase):
    # 用例的前置条件
    def setUp(self) -> None:
        print('连接数据库...')
    
    # 用例的后置清理
    def tearDown(self) -> None:
        print('断开数据库连接...')
    
    def test_db(self):
        # 测试一个数据库
        print('正在执行测试')
        self.assertTrue(1 + 1 == 2)

    def test_db2(self):
        print('正在执行测试')
        self.assertEqual(1, 4)

```

### 类的夹具

```python
import unittest


class TestAdd(unittest.TestCase):
    @classmethod
    def setUpClass(cls) -> None:
        print('每个测试类执行一次')
    
    @classmethod
    def tearDownClass(cls) -> None:
        print('每个测试类之后执行一次')
    
    # 用例的前置条件
    def setUp(self) -> None:
        print('连接数据库...')

    # 用例的后置体哦阿健
    def tearDown(self) -> None:
        print('断开数据库...')

    def test_db1(self):
        print('正在执行测试 1')
        self.assertTrue(1 + 1 == 2)
    
    def test_db2(self):
        pritn('正在执行测试 2')
        self.assertTrue(1, 4)

```

## 测试报告

生成测试报告有两种方法：

1. unittestreport

2. beautifulreport 

### unittestreport

修改 run.py 模块下的代码如下：

```python
import unittest
import unittestreport


# 收集用例
suite = unittest.defaultTestLoader.discover('tests')

# 生成报告
runner = unittestreport.TestRunner(suite, title='xx测试报告', test='haha', templates=2)
runner.run()

```

### beautifulreport

```python
import unittest
from BeautifulReport import BeautifulReport


suite = unittest.defaultTestLoader.discover('tests')

br = BeautifulReport(suite)
br.report(description='xx测试报告')

```