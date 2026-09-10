# 1  add


# 2  commit
- 查看提交记录
```zsh
git ls-tree HEAD
```

# 3  status
- 查看暂存区有哪些文件
```zsh
git status
```

# 4  branch
- 删除分支
```zsh
# 1. 安全删除
git branch -d <分支名>
# 2. 强制删除
git branch -D <分支名>
```

# 5  remote
-  添加新仓库
```zsh
git remote add <远程仓库别名> <新的仓库地址>
```

- 删除旧仓库
```zsh
git remote remove <旧的仓库名>
```

- 更改远程推送名称
```zsh
git remote rename <原远程仓库别名> <新的远程仓库别名>
```

- 更改远程仓库链接
```zsh
git remote set-url <远程仓库别名> <新的仓库地址>
```

- 查看本地记录的远程仓库详细信息
```zsh
git remote -v
```

