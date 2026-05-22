# Papis 本地启动、使用和停止指南

这份文档说明如何在当前项目目录 `/Users/ben/Desktop/papis` 中启动、使用和停止 Papis。当前项目已经下载自 `https://github.com/papis/papis`，并已创建本地虚拟环境 `.venv`。

## 1. 当前本地环境

项目路径：

```bash
/Users/ben/Desktop/papis
```

本地虚拟环境：

```bash
/Users/ben/Desktop/papis/.venv
```

默认文献库目录：

```bash
/Users/ben/Desktop/papis/library
```

Papis 配置文件：

```bash
/Users/ben/Library/Application Support/papis/config
```

当前配置内容等价于：

```ini
[settings]
default-library = library

[library]
dir = /Users/ben/Desktop/papis/library
```

## 2. 启动命令行环境

每次打开新终端后，先进入项目目录：

```bash
cd /Users/ben/Desktop/papis
```

激活虚拟环境：

```bash
source .venv/bin/activate
```

确认 Papis 可用：

```bash
papis --version
```

如果看到类似输出，说明启动成功：

```text
papis, version 0.15.0
```

也可以不激活虚拟环境，直接使用完整路径运行：

```bash
/Users/ben/Desktop/papis/.venv/bin/papis --version
```

## 3. 常用命令

查看总帮助：

```bash
papis -h
```

查看某个子命令帮助：

```bash
papis add -h
papis list -h
papis serve -h
```

检查当前文献库：

```bash
papis doctor
```

列出所有文献：

```bash
papis list --all
```

当前文献库刚创建时是空的，出现 `No documents retrieved` 是正常的。

## 4. 添加文献

添加一个 PDF，并手动指定标题：

```bash
papis add /path/to/paper.pdf --set title "Paper title" --confirm
```

添加 PDF，并写入多个字段：

```bash
papis add /path/to/paper.pdf \
  --set author "Author Name" \
  --set title "Paper title" \
  --set year 2026 \
  --set tags research \
  --confirm
```

通过 DOI 获取元数据并添加文件：

```bash
papis add --from doi 10.1090/noti963 /path/to/paper.pdf
```

添加后再次查看：

```bash
papis list --all
```

## 5. 查找、打开和编辑文献

按关键词查找：

```bash
papis list keyword
```

打开匹配到的文献文件：

```bash
papis open keyword
```

编辑匹配到的文献信息：

```bash
papis edit keyword
```

查看某条文献的 BibTeX 导出：

```bash
papis export keyword --format bibtex
```

导出全部文献为 BibTeX：

```bash
papis export --all --format bibtex > mylib.bib
```

从 BibTeX 文件导入：

```bash
papis bibtex read mylib.bib import --all
```

## 6. 启动网页界面

Papis 也可以启动一个本地网页服务。

进入项目并激活环境：

```bash
cd /Users/ben/Desktop/papis
source .venv/bin/activate
```

启动默认网页服务：

```bash
papis serve
```

通常可以在浏览器打开：

```text
http://localhost:8888
```

如果要指定端口：

```bash
papis serve --port 8890
```

然后打开：

```text
http://localhost:8890
```

如果要指定监听地址和端口：

```bash
papis serve --address 127.0.0.1 --port 8890
```

网页服务运行期间，终端会一直停在服务进程上，这是正常的。

## 7. 停止

### 停止普通命令

`papis list`、`papis add`、`papis export` 这类命令执行完会自动结束，不需要手动停止。

### 停止网页服务

如果是用下面命令启动的：

```bash
papis serve
```

在同一个终端里按：

```text
Ctrl+C
```

即可停止服务。

如果终端找不到了，但端口还被占用，可以查找并停止监听 `8888` 的进程：

```bash
lsof -ti :8888
```

确认输出的是要停止的进程后，再执行：

```bash
lsof -ti :8888 | xargs kill
```

如果你使用的是其他端口，把 `8888` 换成对应端口。

### 退出虚拟环境

使用完后可以退出虚拟环境：

```bash
deactivate
```

退出后，终端提示符前面的 `(.venv)` 会消失。

## 8. 常见问题

### 提示 Python 版本太低

Papis 当前要求 Python `>=3.10`。macOS 自带的 `/usr/bin/python3` 可能是 `3.9`，不要用它直接安装当前项目。

本项目已经使用内置 Python 创建好了 `.venv`，平时只需要：

```bash
cd /Users/ben/Desktop/papis
source .venv/bin/activate
papis --version
```

### 提示没有配置文件

如果看到类似：

```text
No configuration file exists
```

说明 Papis 没有读到配置。确认配置文件存在：

```bash
ls "/Users/ben/Library/Application Support/papis/config"
```

如果文件不存在，可以重新创建：

```bash
mkdir -p "/Users/ben/Library/Application Support/papis"
```

然后写入：

```ini
[settings]
default-library = library

[library]
dir = /Users/ben/Desktop/papis/library
```

### 提示没有文献

如果看到：

```text
No documents retrieved
```

通常不是错误，只表示默认文献库 `/Users/ben/Desktop/papis/library` 还是空的。添加至少一篇文献后再运行 `papis list --all` 即可看到内容。

## 9. 推荐日常流程

每次使用时：

```bash
cd /Users/ben/Desktop/papis
source .venv/bin/activate
papis list --all
```

需要网页界面时：

```bash
papis serve
```

用完后：

```text
Ctrl+C
```

然后：

```bash
deactivate
```
