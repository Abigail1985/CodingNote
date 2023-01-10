
### pytest.mark.usefixtures(*args)

*args – The names of **the fixture to use**, as strings.

看到装饰器的参数就能晓得，usefixtures和fixture要配合使用的。

下面使用例子演示一下：先在conftest.py配置fixture。再定义使用fixture的程序。

```text
# conftest.py
@pytest.fixture()
def login():
    print('输入用户名，密码。完成登录')
    yield
    print('退出')
```

test_usefixture_001.py代码如下：

```text
#!/usr/bin/env python
# -*- encoding: utf-8 -*-
'''
@File    :   test_usefixture_001.py
@Time    :   2021/06/13 16:13:30
@Author  :   软件质量保障
wechat   :   ISTE1024
@Email   :   byteflow@163.com
@Blog    :   https://www.zhihu.com/people/iloverain1024
@Copyright : 侵权必究
'''

import pytest

@pytest.mark.usefixtures('login')
class Test_01:

    def test_01(self):
        print('---用例01---')

    def test_02(self):
        print('---用例02---')

    def test_03(self):
        print('---用例03---')
```

  

![](https://pic1.zhimg.com/80/v2-7c3aab4a22d4709e156b2e755438021c_1440w.webp)

  

通过执行结果可以发现，通过使用usefixture方法后，测试类包含的每个用例都会执行前置和后置内容。

  

### usefixtures也可以同时配置多个fixture。

```text
# conftest.py
@pytest.fixture()
def login():
    print('输入用户名，密码。完成登录')
    yield
    print('退出')

@pytest.fixture()
def data():
    print('---我是data函数-----')

# test_usefixture_002.py
import pytest

@pytest.mark.usefixtures('login', 'data')
class Test_01:

    def test_01(self):
        print('---用例01---')

    def test_02(self):
        print('---用例02---')

    def test_03(self):
        print('---用例03---')
```

  

![](https://pic3.zhimg.com/80/v2-fec830eff75780d8e8da9c3c40e557ea_1440w.webp)

  

usefixtures参数的顺序就是执行顺序，那个fixture名称在前面，就先执行那个方法。

[Pytest系列（10）-pytest.mark.usefixtures详解 - 知乎](https://zhuanlan.zhihu.com/p/382158304)

#python 
#pytest
