## 制作自己的python包并安装到环境

当我们要跨项目共享python代码时创建本地python包是个不错的选择，本文介绍如何制作pip包并安装到虚拟环境。

### 制作包

包的目录结构需要满足以下条件：
1. 所有文件名均要使用英文小写
2. 不包含下划线，空格等特殊字符
3. 包名在pypi网站上未被使用过

我们创建一个名为quanterpanpy的包，目录应该如下：

```
quanterpanpy/
    quanterpanpy/
        __init__.py
    setup.py
```

下面是setup.py的配置示例

```python
from setuptools import setup
setup(
    name="quanterpanpy",
    version="0.0.1",
    author="zhangpan",
    author_email="yxh.****.com",
    description="项目共用库",
    packages = ['quanterpanpy'],

    # 主页
    url="https://blog.page3app.com/"
)
```

### 安装包

打开终端，切换到setup.py所在目录，激活待安装虚拟环境，使用如下命令以可编辑模式（开发者模式）安装包

```shell
pip3 install -e .
```




