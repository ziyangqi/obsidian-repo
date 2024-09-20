
### 1 怎么删除仓库

Seeting - > 拉到最下面 Delete this repository


### 2 branches have diverged


### 3 error: src refspec main does not match any error: failed to push some refs to 'https://github.com/ziyangqi/zyq-api.git'


git remote add origin https://github.com/ziyangqi/yangdada.git

``` bash
# 切换到主分支（可以是 main 或 master，取决于您的习惯或远程仓库的设置）

# 添加内容的东西
git remote add origin https://github.com/ziyangqi/yangdada.git

git checkout -b main

# 添加文件（如果有需要添加到仓库的文件）
git add .

# 进行首次提交
git commit -m "Initial commit"

# 推送到远程仓库
git push -u origin main

```