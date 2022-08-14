## Resty服务器配置

[OpenResty-CentOS 平台安装](https://moonbingbing.gitbooks.io/openresty-best-practices/content/openresty/install_on_centos.html)

```shell
# 命令
sudo yum-config-manager --add-repo https://openresty.org/yum/cn/centos/OpenResty.repo
sudo yum install openresty
```

### 遇到的问题 

#### 安装遇到404错误

具体错误信息如下

```
Errors during downloading metadata for repository 'openresty':
  - Status code: 404 for https://openresty.org/package/centos/9/x86_64/repodata/repomd.xml (IP: 182.92.4.22)
Error: Failed to download metadata for repo 'openresty': Cannot download repomd.xml: Cannot download repodata/repomd.xml
```

**产生问题的原因和解决方案：**

上面的xml文件请求的配置在 /etc/yum.repos.d/openresty.repo中
baseurl=https://openresty.org/package/centos/$releasever/$basearch

通过rpm -qi centos-release或者rpm -q --qf %{version} centos-release;echo来查看$releasever的值，如果提示未安装软件包 centos-release，可以通过cat /etc/redhat-release来查看Linux发行版本号。

通过arch查看$basearch的值。确定上面两个变量的值之后，就可以访问 https://openresty.org/package/centos/ 查看目前支持的所有发行版本，然后相应的修改 openresty.repo 即可。

**解决步骤**

```shell
$ vim /etc/yum.repos.d/openresty.repo

# 将 https://openresty.org/package/centos/$releasever/$basearch 
# 替换为 https://openresty.org/package/centos/8/$basearch
```