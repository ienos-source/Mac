# Python


### 安装路径

Mac 系统自带的 Python 路径，在其目录下的 `Current/bin` 可查看 Python 有多少个版本

```
/System/Library/Frameworks/Python.framework/Version
```


HomeBrew 安装的 Python 路径

```
/usr/local/Cellar/python
```

### 执行文件路径

查看当前 python 路径 - `which python`

- Mac Python 在 `/usr/bin/` 目录下
- HomeBrew Python 在 `/usr/local/bin/` 目录下

### /usr/bin & /usr/local/bin

系统命令默认在 `/usr/bin`，用户命令默认放在 `/usr/local/bin`


### **环境变量**

在终端输入下面的指令可以编辑环境变量，最好是增加变量，不要修改原有的

```
sudo vi /etc/paths
```

使用 `echo $PATH` 可以直接打印环境变量，从左到右依次排序。如果系统命令和用户命令冲突，则会根据 `/etc/paths` 中的环境变量顺序从上到下依次查找；在里面我们可以看到 `/usr/local/bin` 是优先于 `/usr/bin`；也就是只要确保 `/usr/local/bin` 中的 python 软连接到 HomeBrew 安装的 python 即可，在终端执行命令如下:

```
sudo ln -f /usr/local/bin/python3.7 /usr/local/bin/python
```

### 其他 Python 指令

卸载 Homebrew 下的 python

```
brew uninstall python@3.7
```
使用 Homebrew 更新 python
```
brew upgrade python
```
