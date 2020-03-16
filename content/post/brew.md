---
title: "解决Homebrew下载极其“快速”问题"
date: 2020-03-12T17:11:52+08:00
draft: false
tags: ["homebrew", "brew", "镜像源"]
---

### 解决Homebrew下载极其“快速”问题

[Homebrew是什么？](https://zh.wikipedia.org/wiki/Homebrew)
> The Missing Package Manager for macOS (or Linux)

当你运行 `brew update`，你会发现你可以看着屏幕一整天。

```
# 运行过程。。。
brew update --verbose
Checking if we need to fetch /usr/local/Homebrew...
Checking if we need to fetch /usr/local/Homebrew/Library/Taps/caskroom/homebrew-fonts...
Fetching /usr/local/Homebrew...
Checking if we need to fetch /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask...
Checking if we need to fetch /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core...
Fetching /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask...
Fetching /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core...
remote: Enumerating objects: 337, done.
remote: Counting objects: 100% (337/337), done.
remote: Compressing objects: 100% (88/88), done.
remote: Total 298 (delta 221), reused 287 (delta 210), pack-reused 0
Receiving objects: 100% (298/298), 50.91 KiB | 39.00 KiB/s, done.
Resolving deltas: 100% (221/221), completed with 39 local objects.
From https://github.com/Homebrew/homebrew-core
   65a45a9..583b7f1  master     -> origin/master
remote: Enumerating objects: 179429, done.
remote: Counting objects: 100% (179429/179429), done.
remote: Compressing objects: 100% (56607/56607), done.
Receiving objects:   4% (7628/177189), 1.48 MiB | 8.00 KiB/s
```

从上面运行过程发现，罪魁祸首竟然是`github`（科学），update是从github仓库获取文件的过程。

### 解决方法

> 替换brew的镜像源

```
# 1、替换brew.git
cd $(brew --repo) #进入brew仓库目录
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git # 我所使用的是中科大的镜像

# 2、替换brew-core.git
cd $(brew --repo)/Library/Taps/homebrew/homebrew-core
git remote set-url origin https://mirrors.aliyun.com/homebrew/homebrew-core.git

# 3、替换brew-cask.git -》已经编译好了的应用包 （.dmg/.pkg），仅仅是下载解压，放在统一的目录中
cd $(brew --repo)/Library/Taps/homebrew/homebrew-cask
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git

# 4、替换homebrew-bottles
export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles # 临时终端
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc # 永久
```

再次运行`brew update`

```
Checking if we need to fetch /usr/local/Homebrew...
Checking if we need to fetch /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask...
Fetching /usr/local/Homebrew...
Checking if we need to fetch /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core...
Fetching /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask...
Fetching /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core...
remote: Counting objects: 148, done.
remote: Compressing objects: 100% (63/63), done.
remote: Total 148 (delta 110), reused 122 (delta 84)
Receiving objects: 100% (148/148), 20.58 KiB | 554.00 KiB/s, done.
Resolving deltas: 100% (110/110), completed with 22 local objects.
From https://mirrors.ustc.edu.cn/homebrew-core
   bb6a3216a..9dfa98810  master     -> origin/master
remote: Counting objects: 25, done.
remote: Compressing objects: 100% (15/15), done.
remote: Total 25 (delta 10), reused 22 (delta 10)
Unpacking objects: 100% (25/25), done.
From https://mirrors.ustc.edu.cn/brew
   ee69c5aec..f5ba503aa  master     -> origin/master
remote: Counting objects: 181, done.
remote: Compressing objects: 100% (73/73), done.
remote: Total 181 (delta 134), reused 155 (delta 108)
Receiving objects: 100% (181/181), 38.02 KiB | 905.00 KiB/s, done.
Resolving deltas: 100% (134/134), completed with 43 local objects.
From https://mirrors.ustc.edu.cn/homebrew-cask
   658b0704f8..d9ce11afe2  master     -> origin/master
Updating /usr/local/Homebrew...
Branch 'master' set up to track remote branch 'master' from 'origin'.
Reset branch 'master'
Your branch is up to date with 'origin/master'.
Switched to and reset branch 'stable'
Current branch stable is up to date.
```

### 使用代理
