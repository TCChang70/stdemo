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
