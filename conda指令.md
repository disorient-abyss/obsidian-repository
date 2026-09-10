[pip 指令](pip%20指令.md)
[vscode指令](vscode指令.md)
### 1. 取消自动进入base环境
```conda
conda config --set auto_activate_base false
```

### 2. 启动虚拟环境
```conda
conda activate <虚拟环境名称>
``` 

### 3. 创建虚拟环境
```conda
conda create -n <虚拟环境名> python=<python的版本号>
```

### 4.  查看所有的虚拟环境
```conda
conda env list
```

### 5. 复制虚拟环境
```conda
conda create --name <虚拟环境名> --clone <需要复制的环境名>
```

### 6. 退出当前环境
```conda
conda deactivate
```

### 7. 删除旧环境
```conda
conda remove --name <虚拟环境名> --all
```

### 8. windows环境下使用PowerShell并初始化
```conda
conda init powershell
```

### 9. 下载安装包
```conda
- 一般的安装方式：
	conda install <包名>
- 如果是给有名字的环境安装：
	conda install -n <环境名> <包名>
- 从指定的“国内镜像源”或特定仓库下载：
	conda install <包名> -c <特定仓库名>
```

### 10. 版本回滚机制
```conda
- 列出每一次下载安装的历史
	conda list --revisions
- 回滚到指定的安装历史
	conda compare --revision <指定版本号>
```

### 11. 更新mini-forge
```conda
1. conda activate base
2. mamba update -n base -c conda-forge --all
```