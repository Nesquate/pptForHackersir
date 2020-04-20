---
title: Git 自學心得
tags: 黑客社, 簡報
---
[TOC]


# Git
###### 學到目前為止比較熟悉的 Git 自學想法

---

# Git 是什麼 ?

----

* ~~英式英文：不愉快的人~~
* 檔案版本控制軟體 （O）
* 他來幫你紀錄有什麼樣的變更
* 你不用自己複製一堆檔案ㄌ

----

Git 最大好處 - 多人專案
* 分散式系統
* 不用怕不知道哪部分是誰做的
* 不用擔心萬一做壞了沒有先前的紀錄
* 遵守 Git Flow 的開發流程

----

開始使用 : 
```shell=
git init
```

---

## Git 分區結構

----

* 工作目錄
* 暫存區
* 版本庫

----

![圖例1](https://i.imgur.com/aU6aA0Y.png)

----

![圖例2](https://i.imgur.com/D4tkxjW.png)

----

![圖例3](https://i.imgur.com/9UHHREu.png)

----

![圖例4](https://i.imgur.com/JvyOv0P.png)

---

## 重要標頭

----

* HEAD
    * 標示目前所在 Commit 
* ORIG_HEAD
    * 執行重大變更前 (git rebase、git reset、git merge...) 的最後一個 Commit

---

# 常用 Git 指令

----

## git add

----

* 新增檔案/變更到暫存區裡面 (還沒進入版本庫)
* `.` 打下去就是工作目錄的所有檔案都會被加入
    * `.gitignore` 內的檔案例外
* 其他檔案操作相關 : `git mv` (重新命名) 、 `git rm` (移除 Track 狀態)

----

## git status

----

顯示目前工作目錄的狀態

----

![modified](https://i.imgur.com/MtYFvKI.png)

----

## git commit

----

* 把暫存區的變更存進版本庫內
* 輸入後會進入文字編輯器內，可輸入多行訊息
    * 如果要單行就 `git commit -m "訊息"` 就好了

----

![多行 commit](https://i.imgur.com/uLKoojj.png)

----

## git log

----

顯示所有的 Commit 紀錄
也可以用來搜尋特定 Commit

----

![gitLog](https://i.imgur.com/DsgQOnM.png)

----

## git clone

----

把遠端的 Git 版本庫抓過來
ex : 
```shell=
git clone https://github.com/Nesquate/me.git
```

---

# 分支、遠端指令

----

## 分支是什麼 ?

----

你可以把它當作是一種分身的概念
「橫的移植」 - by 現代詩社

----

不過實際上可以當作是
一個指向某個 Commit 的"貼紙"

----

![branches](https://i.imgur.com/ZtDE4BE.png)

----

## git branch

----

* 什麼都沒加 -> 顯示本機所有分支
* 後面打不存在的分支名稱 會幫你建立分支 
    * 以目前的 commit 開始
* `-a` : 顯示所有分支 (包含遠端目標)
* `-m` : 重新命名分支
* `-d` : 刪除分支

----

## git checkout

----

* ~~Magic~~~
* 功用有點多
    * 切換分支
    * 回復檔案
    * 切到某個 Commit (detached HEAD)
    * 建立分支 (`git checkout -b`)

----

## git merge

----

- 合併分支
- 用法 : `git merge haha` 
    - -> 拿 haha 分支的內容合併到目前的分支
- 詳細參數 : 
    - `--no-ff` : 不要用快轉模式 (Fast Forward)

----

## git pull

----

* 其實是 `git fetch` + `git merge` (Fast Forward)
* `git fetch` 才是抓回來的主要指令
    * 抓回來的部分會被放在 `FETCH_HEAD` 

----

## git push

----

* 把東西推回遠端主機上
* 範例 : `git push orign master`

---

# Pull Request 示範
###### Step-by-step

----

* 多人專案必學操作
* 也算是使用 Git 的社群之獨特的互動方式

----

大致上的流程
* Fork 一份到你的 Github/GitLab
* Clone 你 Fork 的那份下來編輯
* Push 修改回你 Fork 的那份
* 向對方提出 Pull Resquest (PR)

----

假設你今天看到專案有 Bug 你很想修....

![PR_01](https://i.imgur.com/u9gaNeo.png)

----

你再看看上面...

![PR_02](https://i.imgur.com/t4EKvKj.png)

----

![PR_03](https://i.imgur.com/KBefko3.png)

----

登愣
![PR_04](https://i.imgur.com/WWSrcYc.png)

----

抓它下來
![PR_05](https://i.imgur.com/HyhGugt.png)

----

新增一個分支~~來保留證據~~來避免出錯
![PR_06](https://i.imgur.com/3tEKBry.png)

----

開始~~爆~~改

----

改完之後要記得生出 Commit
![PR_07](https://i.imgur.com/aNlpRMf.png)

----

Push 到我 Fork 的那份上面
![PR_08](https://i.imgur.com/CjZYg10.png)

----

去 Github 上點 「New Pull Resquest」
![PR_09](https://i.imgur.com/WQYwe7s.png)

----

![PR_10](https://i.imgur.com/WqRApzs.png)

----

請有禮貌的敘述你的 PR 後送出
![PR_11](https://i.imgur.com/KcFprwn.png)

----

Done!
![PR_12](https://i.imgur.com/T8PsgXp.png)

---

# Git 學習資源與工具

----

學習資源
* man git
    * 最佳學習手冊，還是官方的ㄟ
* [高見龍 - 為你自己學 Git](https://gitbook.tw/) 
    * `https://gitbook.tw/`
* [貝格樂 - 連猴子都能懂的Git入門指南](https://backlog.com/git-tutorial/tw/)
    * `https://backlog.com/git-tutorial/tw/`

----

工具
* GitKraken
    * 推薦！該有的都有，介面也很漂亮
    * (與SourceTree相比)
* Visual Studio Code
    * 內附有 Git 工具可以用
    * 強烈建議語系切成英文來學習
    * 你肯定看不懂中文到底在寫啥...