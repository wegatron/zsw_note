## git remote
远程处理
```bash
# 查看远程信息
git remote -v

# 删除远程
git remote remove [name]

# 添加远程
git remote add [name] [url]
```

## 创建本地远程仓库
```bash
git init --bare e:/tmp2/remote.git
git clone e:/tmp2/remote.git local
```

## ssh
①. 生成key
```bash
# 自动保存到~/.ssh目录下
ssh-keygen -t rsa -C "YOUR EMAIL"
```
②. 在gitlab/github中添加key, 将id_rsa.pub中的内容复制到其中即可.

③. 在ubuntu上还需要添加ssh key到ssh-agent
```bash
# 权限设置, 不允许其他用户访问
chmod 400 ~/.ssh/id_rsa

# 添加
ssh-add
```

④. github测试
```bash
ssh -T git@github.com
```

## git submodule
1. 添加submodule
```bash
git submodule add [URL to Git repo]
git submodule init
```
2. 移除submodule
```bash
# 删除相应项目的cache
git rm --cached [name]
# 删除相应项目的文件
rm -rf pod-library
# 删除 .git/modules 中的相应文件
rm -rf .git/modules/[name] 
# 修改文件 .gitmodules 删除相应项
vim .gitmocules
# 修改 .git/config 文件, 删除相应项
vim .git/config
```

3. Cloning a repository that contains submodules
```bash
git clone --recursive [URL to Git repo]
git submodule update --init
# if there are nested submodules, -j means parallel download
git submodule update --init --recursive -j 8
```

4. submodule 更新到最新版本
```bash
git pull
git submodule update
```

reference: https://git-scm.com/docs/git-submodule
