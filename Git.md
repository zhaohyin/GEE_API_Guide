# Git Notes
Edit by zhyin in Jan 5,2021

Main content: 1) Git basic operation; 2) Git Bug && DeBug
![image](https://upload-images.jianshu.io/upload_images/4991519-8aa5cd170adb37f2.png?imageMogr2/auto-orient/strip|imageView2/2/w/900/format/webp)

## 0. Connect local repositories and Github repositories
    error: ssh keys have been used by other people.

```zsh
$ ssh -T -ai ~/.ssh/id_rsa git@github.com
# Connect to GitHub using a specific ssh key
> Hi username! You have successfully authenticated, but GitHub does not
> provide shell access.
```
```zsh
Solution:
    # remove id_rsa and id_rsa.pub
    > cd ~/.ssh
    > rm id_rsa
    > rm id_rsa.pub
    # create new ssh keys
    > cd ~
    > ssh-keygen
```

    Copy the content in id_rsa.pub.
    Sign in Github.com 
    Paste in  settings -> SSH and GPG keys -> New SSH key

## 1. Local --> Github.com
``` zsh
(base) Star@zhyin ~ % cd xxx/xxx/Programming_Notes/
(base) Star@zhyin Programming_Notes % git init      ## Creat .gitignore 
(base) Star@zhyin Programming_Notes % git add .     ## Add all Files to staging area
(base) Star@zhyin Programming_Notes % git commit -m 'init commit'   # commit to git directory
```
![image](https://upload-images.jianshu.io/upload_images/3807682-c011a8ccb5122d4f.png?imageMogr2/auto-orient/strip|imageView2/2/w/574/format/webp)
```zsh
(base) Star@zhyin Programming_Notes % git push
```
## 2. Github.com --> Local
    git pull
## 3. Git Bug and Debug
### 3.1 Commit your changes or stash them before you can merge.
    error: Your local changes to the following files would be overwritten by merge:xxx/xxx/xxx.md
    Please, commit your changes or stash them before you can merge.
    Aborting
 - [x] Solution1: check the difference between the lastest version and this version.
```zsh
git stash
git pull
git stash pop
```
 - [x] Solution2: overwritten
```zsh
git reset --hard
git pull
```

---
    fatal: refusing to merge unrelated histories
    问题出现时间情景：
    1）本地创建仓库，本地存在git且已commit并未提交；
    2）Github上新建仓库，存在git-history；
    3）从Github上面 git pull时存在该问题

- [x] Solution:在你操作命令后面加--allow-unrelated-histories\
  
    eg:git merge master --allow-unrelated-histories
---
``` zsh
    (base) Star@zhyin Les-Trois-Mousquetaires % git push origin main
        > error: src refspec main does not match any
        > error: failed to push some refs to 'https://github.com/zhaohyin/Les-Trois-Mousquetaires.git'
    (base) Star@zhyin Les-Trois-Mousquetaires % git remote add origin https://github.com/zhaohyin/Les-Trois-Mousquetaires.git
        > fatal: remote origin already exists.
    (base) Star@zhyin Les-Trois-Mousquetaires % git push -u origin main  
        > error: src refspec main does not match any
        > error: failed to push some refs to 'https://github.com/zhaohyin/Les-Trois-Mousquetaires.git'
    (base) Star@zhyin Les-Trois-Mousquetaires % git push origin main
        > error: src refspec main does not match any
        > error: failed to push some refs to 'https://github.com/zhaohyin/Les-Trois-Mousquetaires.git'
```
- [x] Solution:
```zsh
# 添加远程链接为github网络
(base) Star@zhyin Les-Trois-Mousquetaires % git remote add origin https://github.com/zhaohyin/Les-Trois-Mousquetaires.git
# 新建分支为 master
(base) Star@zhyin Les-Trois-Mousquetaires % git push --set-upstream origin master 
# 温馨提示：最好新建的时候不要用main，而是用master，不然就会很尴尬...[经验之谈]
```