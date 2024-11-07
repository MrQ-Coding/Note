
# macOS如何部署Odoo的Demo项目

在macOS上部署Odoo的Demo项目需要搭建Odoo的运行环境。以下步骤将指导你如何在macOS上从零开始部署一个Odoo的Demo项目，主要包括环境安装、数据库配置、Odoo源码下载及运行配置。

## 环境准备

在macOS上运行Odoo需要满足几个基本条件，包括安装Python（Odoo 15版本使用Python 3.7+）、PostgreSQL数据库以及一些Odoo的依赖包。

### 1. 安装Homebrew

Homebrew是macOS下常用的包管理工具。打开终端，输入以下命令安装Homebrew：

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

完成后，执行以下命令更新Homebrew：

```bash
brew update
```

### 2. 安装Python和Pip

通过Homebrew安装Python 3.x（Odoo 14+ 支持 Python 3.7及以上版本）：

```bash
brew install python
```

安装完成后检查版本：

>  这里建议用3.12的版本，目前试了3.9～3.13，就3.12能正常安装依赖

```bash
python3 --version
pip3 --version
```

### 3. 安装PostgreSQL

Odoo依赖PostgreSQL数据库进行数据存储。可以通过Homebrew安装：

```bash
brew install postgresql
```

安装完成后启动PostgreSQL服务：

```bash
brew services start postgresql
```

## 配置PostgreSQL

1. 创建一个用于Odoo的数据库用户，并赋予它数据库访问权限：

   ```bash
   psql postgres
   ```

2. 在PostgreSQL控制台中，创建一个新用户并设置密码：

   ```sql
   CREATE USER odoo WITH PASSWORD 'odoo';
   ALTER USER odoo CREATEDB;
   ```

3. 退出PostgreSQL控制台：

   ```sql
   \q
   ```

## 安装Odoo

接下来，通过Git下载Odoo的源码，并安装依赖项。

### 1. 下载Odoo源码

Odoo的源代码托管在GitHub上。可以直接下载指定的版本代码，例如Odoo 15：

```bash
git clone https://www.github.com/odoo/odoo --depth 1 --branch 15.0 --single-branch
cd odoo
```

### 2. 安装依赖包

- 给odoo单独配置一个虚拟环境，然后再安装依赖包

```bash
# 在odoo的目录下创建虚拟环境
python -m venv myenv
# 启用虚拟环境(如果结束运行，直接输入deactivate退出虚拟环境)
source myenv/bin/activate
```

- Odoo需要一些Python的依赖包，可以使用pip3安装：

```bash
pip3 install -r requirements.txt
```

**注意**：可能会有一些依赖包的编译问题，确保安装了`gcc`、`libxml2`、`libxslt`等编译工具。可以通过以下命令安装这些依赖：

```bash
brew install libxml2 libxslt
brew link libxml2 --force
brew link libxslt --force
```

## 配置Odoo

Odoo的配置文件`odoo.conf`可以自定义一些必要的参数，比如数据库信息、访问端口等。

### 1. 创建配置文件

在`odoo`文件夹中创建一个名为`odoo.conf`的文件：

```bash
touch odoo.conf
```

### 2. 编辑配置文件内容

将以下内容粘贴到`odoo.conf`中，并根据需要更改设置：

```ini
[options]
; Odoo服务监听的端口
xmlrpc_port = 8069

; 数据库配置
db_host = False
db_port = False
db_user = odoo
db_password = odoo

; addons路径
addons_path = addons,custom_addons

; 启用调试模式
; dev_mode = True
```

## 启动Odoo

确保PostgreSQL已经启动，然后在终端中执行以下命令启动Odoo服务：

```bash
python3 odoo-bin -c odoo.conf
```

如果一切顺利，可以通过浏览器访问`http://localhost:8069`查看Odoo界面。在首次访问时，Odoo会要求创建数据库。使用前面在PostgreSQL中创建的用户名和密码登录。

## 安装Demo数据

在Odoo的初始配置界面中，可以选择加载“演示数据”（Demo Data）选项，这样在数据库创建完成后，Odoo会自动生成Demo环境供测试使用。

## 常见问题

1. **依赖安装失败**：确保所有依赖都已安装并链接到系统路径，特别是`libxml2`、`libxslt`、`psycopg2`等。

2. **PostgreSQL连接错误**：确保PostgreSQL服务正常运行，并且用户和数据库配置正确。

3. **MacOS pip安装依赖报错：error: externally-managed-environment**

	- 建议根据brew的提示使用虚拟环境来安装

		```bash
		#If you wish to install a Python library that isn't in Homebrew,
		#use a virtual environment:
		
		#python3 -m venv path/to/venv
		#source path/to/venv/bin/activate
		#python3 -m pip install xyz
		
		# 创建虚拟环境（在Odoo项目目录中）
		python3 -m venv odoo-venv
		
		# 激活虚拟环境
		source odoo-venv/bin/activate
		
		# 在虚拟环境中安装依赖
		pip install -r requirements.txt
		```

## 总结

通过以上步骤，你就可以在macOS上成功部署并运行Odoo的Demo项目了。
