# SourceTree 操作步驟文件

---

## 前置準備

1. 開啟 **SourceTree**。
2. 點選上方工具列 **「New」→「Create Local Repository」**。
3. 選擇目的地路徑（例如 `C:\Java_Framework\ai_skills\sourcetree-demo`），輸入名稱後按 **「Create」**。
4. SourceTree 開啟該 Repository 的主畫面。

---

## 加入 README.md 檔案

1. 在 `sourcetree-demo` 資料夾中手動新增 `README.md` 檔案（使用文字編輯器或檔案總管）。
2. 回到 SourceTree，左側點選 **「File Status」**（或上方 **「Uncommitted changes」**）。
3. 在 **「Unstaged files」** 區域看到 `README.md`，勾選左側核取方塊將其移至 **「Staged files」**。
4. 在下方 **「Commit message」** 欄輸入：`加入 README.md`。
5. 按右下角 **「Commit」** 按鈕完成提交。

---

## 加入 Branch yahoo-v1

1. 在上方工具列點選 **「Branch」** 按鈕。
2. 在 **「New Branch」** 對話框輸入分支名稱：`yahoo-v1`。
3. 確認 **「Checkout New Branch」** 核取方塊已勾選。
4. 按 **「Create Branch」**，SourceTree 會自動切換到 `yahoo-v1` 分支。

### Branch yahoo-v1 加入檔案 A1.txt

1. 在 `sourcetree-demo` 資料夾中手動新增 `A1.txt` 檔案。
2. 回到 SourceTree，**「File Status」** 頁籤顯示 `A1.txt` 在 Unstaged 區域。
3. 勾選 `A1.txt` 移至 **「Staged files」**。
4. 輸入 Commit message：`加入 A1.txt`。
5. 按 **「Commit」**。

---

## 加入 Branch seednet-v1

1. 先切換回 `master`：在左側 **「BRANCHES」** 清單中，雙擊 **`master`** 切換過去。
2. 在上方工具列點選 **「Branch」** 按鈕。
3. 輸入分支名稱：`seednet-v1`。
4. 確認 **「Checkout New Branch」** 已勾選，按 **「Create Branch」**。

### Branch seednet-v1 加入檔案 A2.txt

1. 在 `sourcetree-demo` 資料夾中手動新增 `A2.txt` 檔案。
2. 回到 SourceTree，**「File Status」** 顯示 `A2.txt` 在 Unstaged 區域。
3. 勾選 `A2.txt` 移至 **「Staged files」**。
4. 輸入 Commit message：`加入 A2.txt`。
5. 按 **「Commit」**。

### Branch seednet-v1 加入檔案 A3.txt

1. 在 `sourcetree-demo` 資料夾中手動新增 `A3.txt` 檔案。
2. 回到 SourceTree，**「File Status」** 顯示 `A3.txt` 在 Unstaged 區域。
3. 勾選 `A3.txt` 移至 **「Staged files」**。
4. 輸入 Commit message：`加入 A3.txt`。
5. 按 **「Commit」**。

---

## Rebase Branch seednet-v1 到 yahoo-v1

> 目的：將 `seednet-v1` 的 commit（A2.txt、A3.txt）接到 `yahoo-v1` 的最新 commit 之後。

1. 確認目前位於 **`seednet-v1`** 分支（左側 BRANCHES 清單中該分支名稱為粗體）。
   - 若不是，雙擊 `seednet-v1` 切換過去。
2. 在上方工具列點選 **「Repository」→「Rebase...」**（部分版本在 **「Actions」** 選單）。
3. 在 Rebase 對話框的 **「Rebase current branch onto:」** 下拉選單中選擇 **`yahoo-v1`**。
4. 按 **「OK」** 執行 Rebase。
5. Rebase 完成後，左側 **「History」** 圖形視圖會顯示線性提交記錄：

```
●  加入 A3.txt       ← seednet-v1 (HEAD)
●  加入 A2.txt
●  加入 A1.txt       ← yahoo-v1
●  加入 README.md    ← master
```

---

## 最終 Branch 結構說明

| 分支 | 包含檔案 |
|------|---------|
| `master` | README.md |
| `yahoo-v1` | README.md、A1.txt |
| `seednet-v1`（rebase 後） | README.md、A1.txt、A2.txt、A3.txt |



# 根據文件目前的分支結構，**不行直接用 Rebase 只讓 A2.txt 進入 `yahoo-v1`**，原因與正確做法如下：

---

## 為什麼 Rebase 不適合這個目的

Rebase 的方向是：**把「目前分支」上 unique 的 commit，移植到「目標分支」的最新 commit 之後**。

目前完成文件操作後的歷史圖：

```
C0(README) → C1(A1.txt)             ← yahoo-v1
                    ↓
             C2'(A2.txt) → C3'(A3.txt)   ← seednet-v1
```

若你切換到 `yahoo-v1` 再 rebase onto `seednet-v1`，結果是：
- `yahoo-v1` 的 commit（C1）已經在 `seednet-v1` 的歷史裡了（因為之前就是 rebase 在 yahoo-v1 之上）
- Git 判斷「沒有需要移植的 commit」→ 直接 fast-forward，`yahoo-v1` 會跳到 `seednet-v1` 最頂端
- 結果 `yahoo-v1` = README + A1 + A2 + A3，**A3.txt 也一起進來了**，無法只取 A2.txt

---

## 正確做法對應需求

| 需求 | 方法 |
|------|------|
| 只讓 A2.txt 進入 `yahoo-v1` | **Cherry-pick** |
| 讓 A2.txt + A3.txt 全部進入 `yahoo-v1` | **Merge** 或 Rebase（fast-forward） |

---

### 方法一：Cherry-pick（只取 A2.txt）

在 SourceTree 操作：

1. 切換到 **`yahoo-v1`** 分支（左側雙擊）
2. 點選上方 **「History」** 頁籤，找到 `seednet-v1` 的「加入 A2.txt」那筆 commit
3. **右鍵點選該 commit** → 選擇 **「Cherry Pick...」**
4. 確認後，只有 A2.txt 的變更會被複製到 `yahoo-v1`

結果：
```
C0 → C1(A1.txt) → C2''(A2.txt)   ← yahoo-v1（只有 A2.txt 進來）
C0 → C1 → C2'(A2.txt) → C3'(A3.txt)   ← seednet-v1（不受影響）
```

---

### 方法二：Merge（A2 + A3 全部進入）

1. 切換到 **`yahoo-v1`**
2. 上方工具列點選 **「Merge」**
3. 選擇 **`seednet-v1`** → 按 **「OK」**

結果 `yahoo-v1` 就包含 README、A1、A2、A3 全部檔案。

---

## Pull Request：將 seednet-v1 合併進 yahoo-v1

> **前置條件**：本機 Repository 必須已推送到 GitHub（或 Bitbucket）遠端，Pull Request 是遠端平台的功能，無法在純本機 Repository 發起。

---

### 步驟一：連結遠端 Repository（GitHub）

若尚未建立遠端連結，先在 GitHub 建立同名空白 Repository，再於 SourceTree 加入 Remote：

1. 上方工具列點選 **「Repository」→「Repository Settings...」**。
2. 切換到 **「Remotes」** 頁籤，按 **「Add」**。
3. **Remote name** 填入 `origin`，**URL** 貼上 GitHub Repository 的 HTTPS 或 SSH 網址。
4. 按 **「OK」** 儲存。

---

### 步驟二：推送所有分支到遠端

1. 上方工具列點選 **「Push」** 按鈕。
2. 在推送對話框勾選所有要推送的分支：
   - ☑ `master`
   - ☑ `yahoo-v1`
   - ☑ `seednet-v1`
3. 按 **「Push」**，將三條分支全部推送到 GitHub。
4. 推送完成後，左側 **「BRANCHES」** 清單的分支前方會出現遠端追蹤標籤（`origin/...`）。

---

### 步驟三：在 SourceTree 發起 Pull Request

> SourceTree 需登入 GitHub / Bitbucket 帳號才可直接發起 PR。

1. 在左側 **「BRANCHES」** 清單中，**右鍵點選 `seednet-v1`**。
2. 選擇 **「Create Pull Request...」**（部分版本顯示為 **「Pull Request」**）。
3. SourceTree 會開啟網頁瀏覽器，直接跳到 GitHub 的 Pull Request 建立頁面，並預填：
   - **base**（目標分支）：`yahoo-v1`
   - **compare**（來源分支）：`seednet-v1`

   > 若預填的 base 不是 `yahoo-v1`，手動在下拉選單中修改。

4. 填寫 PR 資訊：
   - **Title**：例如 `feat: 加入 A2.txt 與 A3.txt`
   - **Description**（說明欄）：描述這次異動的目的，例如：
     ```
     ## 變更內容
     - 新增 A2.txt
     - 新增 A3.txt

     ## 測試方式
     切換到 yahoo-v1 合併後確認兩個檔案存在
     ```
5. 按 **「Create Pull Request」** 完成建立。

---

### 步驟四：審查並合併 Pull Request（Merge PR）

Pull Request 建立後，由有權限的成員（或自己）在 GitHub 上進行審查：

1. 進入 GitHub Repository → 點選上方 **「Pull requests」** 頁籤。
2. 點選剛才建立的 PR 標題進入詳細頁面。
3. 確認 **「Files changed」** 頁籤顯示的差異內容正確（A2.txt、A3.txt）。
4. 確認無衝突（頁面底部顯示 **「This branch has no conflicts with the base branch」**）。
5. 按下 **「Merge pull request」→「Confirm merge」**，`seednet-v1` 的內容即合併進 `yahoo-v1`。

合併後 GitHub 上的 `yahoo-v1` 分支將包含：

| 檔案 | 來源 |
|------|------|
| `README.md` | master 初始 commit |
| `A1.txt` | yahoo-v1 原有 |
| `A2.txt` | 由 PR 從 seednet-v1 合併 |
| `A3.txt` | 由 PR 從 seednet-v1 合併 |

---

### 步驟五：同步遠端變更回本機

PR 合併後，遠端的 `yahoo-v1` 已更新，需要拉回本機：

1. 在 SourceTree 左側雙擊 **`yahoo-v1`** 切換到該分支。
2. 上方工具列點選 **「Pull」** 按鈕。
3. 確認 **Remote branch to pull** 為 `origin/yahoo-v1`，按 **「OK」**。
4. 本機 `yahoo-v1` 即更新為合併後的最新狀態。

---

### Pull Request 流程總覽

```
本機 seednet-v1
      │
      │  Push
      ▼
GitHub seednet-v1 ──► 發起 PR（base: yahoo-v1）
                            │
                       Code Review
                            │
                       Confirm Merge
                            │
                            ▼
                    GitHub yahoo-v1（包含 A2、A3）
                            │
                            │  Pull
                            ▼
                    本機 yahoo-v1（同步完成）
```
