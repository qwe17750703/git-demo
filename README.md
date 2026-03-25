可參考我在Heptabase上的[筆記](https://app.heptabase.com/p/whiteboard/24984eb9-f299-4866-832e-8b9bf301ef27)

[Udemy課程證書](https://www.udemy.com/certificate/UC-f419ba06-0a61-4f8f-bdfc-05cab8038a14/) 

## 筆記內容整理

這是一個用來紀錄與演練 Git 版本控制與 GitHub 協作的 Demo 專案，整理了從基礎指令、分支操作、遠端協作到底層運作機制的實用筆記。

## 1. 基礎設定與初始化

在使用 Git 之前，我們需要先設定使用者資訊，這些設定會跟著你的 Commit 紀錄：

### 全域設定 (Global) - 適用於所有專案
```
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

#### 檢視目前的設定
```
git config --list
```

設定的優先權為：Local (當下專案) > Global (全域) > System (系統)。

要讓一個資料夾開始被 Git 控管，需要進行初始化：

```
git init
```

這會在專案目錄下建立一個隱藏的 .git 資料夾，用來儲存所有的版本資訊與追蹤紀錄。

## 2. 日常工作流程 (Workflow)

- Git 的檔案狀態主要分為：工作區 (Working Directory)、暫存區 (Staging Area / Index) 與 儲存庫 (Repository)。

- 查看狀態：git status 可以確認目前檔案是處於 untracked（未追蹤）、modified（已修改）還是已加入暫存區。

- 加入暫存區：將檔案交給 Git 追蹤。

```
git add <file_name>
git add --all  # 將所有變更加入暫存區
```

註：每次修改檔案後，都需要重新 git add 才能將最新狀態放入暫存區。

提交紀錄：將暫存區的檔案永久存入儲存庫。

git commit -m "簡單、清楚的提交訊息 (例如：#34 bug fixed)"

分段 Commit 的好處是讓程式碼審查（Code Review）時能看到更完整且有條理的內容。

#### 查看歷史紀錄：
```
git log --oneline --graph  # 精簡顯示並帶有分支圖
git log -2                 # 只顯示最近兩次
```

## 3. 分支 (Branch) 與合併 (Merge)

分支是指向 Commit 的指標，而 HEAD 是一個特殊的指標，永遠指向當前所在的分支。

### 分支合併 (Merge)：

- 3-Way Merge：當兩個分支分叉後各自有新的 Commit，Git 會找尋共同祖先進行三方合併，並產生一個全新的 Merge Commit。如果修改了同一個檔案的同一個部分，就會產生衝突（Conflict），需要人工解決。

- Fast-Forward：如果當前分支沒有新的修改，單純落後於目標分支，合併時只會將指標「快進」過去。

### 分支重寫 (Rebase)：
- git rebase <branch> 可以將分支的歷史紀錄改寫成一直線，避免產生多餘的 Merge Commit。但絕對不要在多人共用的 master 分支上使用 rebase！

### 清理合併過的分支：

#### 刪除本地已經合併到 master 的分支

```
git branch --merged master | grep -v "master" | xargs git branch -d
```

## 4. 遠端操作與協作 (Remote)

### 連接遠端倉庫：

```
git remote add origin <遠端網址>
git remote -v  # 檢查連動的遠端倉庫清單
```

### 同步與推送：

- git fetch：去遠端看看有哪些更新，並抓回電腦，但先不合併到當前進度。

- git pull：相當於 git fetch + git merge，拉取遠端進度並直接合併。

- git push -u origin <branch>：初次推送時使用 -u (即 --set-upstream) 來建立本地分支與遠端分支的關聯，之後只需打 git push 即可。

協作機制：在 GitHub 上稱為 Pull Request (PR)，在 GitLab 稱為 Merge Request (MR)。這是你將修改好的程式碼請求併入主分支的審查過程。如果是別人的開源專案，你需要先 Fork 到自己的帳號下才能修改並發起 PR。

## 5. SSH 連線設定

- 使用 SSH 金鑰可以免去每次推拉程式碼都要輸入帳號密碼的麻煩。目前最推薦的加密演算法是 ed25519。

- 產生 SSH Key (建議加上 email 註解以利辨識)
ssh-keygen -t ed25519 -C "your.email@example.com"

- 測試連線
ssh -T git@github.com

產生公鑰 (.pub) 後，將其內容貼到 GitHub 的 SSH Keys 設定中，並將本地 Repo 的 URL 改為 SSH 格式即可。

## 6. 標籤 (Tags) 與版本號

Tag 就像是 Git 歷史紀錄裡的「書籤」，用來標記重大里程碑（如版本發佈）。相較於會移動的 Branch，Tag 是靜態不變的。

- 輕量標籤 (Lightweight)：git tag v1.0 (僅標記)

- 附註標籤 (Annotated)：git tag -a v1.0 -m "穩定版" (包含作者、時間等詳細資訊，正式發佈強烈推薦使用此種)。

- 語義化版本 (SemVer) 規範：主版本號.次版本號.修訂號 (例如：v1.2.3)。

註：在 GitHub 上，Release 是包裝在 Tag 之上的產品發佈說明。

## 7. 進階概念與底層探究

- Git 物件儲存機制：Git 透過 SHA-1 演算法計算哈希值來儲存物件。如果檔案內容一模一樣，算出來的 ID 就會相同，極度節省空間。

- blob：單純的檔案內容。

- tree：目錄（資料夾），記錄檔名與對應的 blob。

- commit：提交紀錄，包含作者與指向 tree 的指標。

- Git Hooks：Git 的「自動化觸發器」（例如 Pre-commit Hook），可以在 commit 或 push 前後自動執行腳本（如程式碼檢查）。

- 垃圾回收 (Garbage Collection)：git gc 用來幫專案進行大掃除，清理不再被需要的孤兒物件並進行壓縮。

- ORIG_HEAD：執行破壞性指令（如 reset 或 rebase）前的自動存檔點，搞砸時可以作為「後悔藥」快速跳回。

