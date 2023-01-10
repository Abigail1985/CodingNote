
pytest里面有些文件是非test文件

-   pytest.ini pytest的主配置文件，可以改变pytest的默认行为
-   conftest.py 测试用例的一些fixture配置
-   __init__.py 识别该文件夹为python的package包
-   tox.ini 与pytest.ini类似，用tox工具时候才有用
-   setup.cfg 也是ini格式文件，影响setup.py的行为

ini文件基本格式

```ini
# 保存为pytest.ini文件

[pytest]

addopts = -rsxX
xfail_strict = true

```
#python 
#pytest 
[[pytest.usefixtures用法]]
