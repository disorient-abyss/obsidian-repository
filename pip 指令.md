[conda指令](conda指令.md)
[vscode指令](vscode指令.md)
### 1. 安装
```pip
pip install <软件包名>
```

### 2. 卸载
```pip
pip unstall <软件包名>
```

### 3. 导出所有包名列表
```pip
# 先激活目标环境
conda activate 你的环境名

# 导出所有 pip 安装的依赖
pip freeze > requirements.txt

# 仅导出不依赖其他本地包的“顶层”包
pip list --not-required --format=freeze > requirements.txt

# 导出包含 pip 包的 Conda `environment.yml`
conda env export > environment.yml

创建存放安装包的目录，并执行下载：
mkdir -p ./packages
pip download -r requirements.txt -d ./packages
```

### 4. 安装离线软件包，需要拷贝二进制文件和包名列表
```pip
pip install --no-index --find-links=./packages -r requirements.txt
```