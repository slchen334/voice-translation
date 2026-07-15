# SL翻譯神器 · 即時翻譯

雙向即時語音翻譯 App（單人與面對面模式），支援 Gemini 與 OpenAI 相容供應商。以 Flask + Socket.IO 打造，可安裝為手機 PWA。

---

## 🚀 一鍵部署到你自己的帳號

點下面的按鈕，用**你自己的 GitHub / Render / Gemini 金鑰**部署一份專屬的網站：

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/slchen334/voice-translation)

部署後你會得到一個屬於你自己的網址（例如 `https://xxxx.onrender.com`），金鑰、帳單都算你自己的，不會用到別人的額度。

---

## 你需要準備 3 樣東西

| 項目 | 說明 | 連結 |
|------|------|------|
| GitHub 帳號 | 免費 | <https://github.com> |
| Render 帳號 | 用 GitHub 登入；需綁一張卡做身分驗證（Free 方案仍 **$0**，不扣款） | <https://render.com> |
| Gemini API Key | 免費申請，貼到 Render 環境變數 | <https://aistudio.google.com/apikey> |

> Tavily API Key 為選填（旅遊助手上網查資料用）：<https://tavily.com>

---

## 部署步驟

### 方式 A：一鍵按鈕（最簡單）

1. 點上方 **Deploy to Render** 按鈕
2. 用你自己的 GitHub 登入 Render
3. Render 會自動讀取本專案的 `render.yaml`，跳出要你填環境變數：
   - `GEMINI_API_KEY` ← 填你自己的 Gemini 金鑰（必填）
   - `TAVILY_API_KEY` ← 選填
4. 按 **Apply**，等 2～5 分鐘，狀態變綠色 `Live` 即上線

### 方式 B：先 Fork 再部署（想完全獨立、可自己改程式）

1. 按本 repo 右上角 **Fork**，複製一份到你自己的 GitHub
2. 到 Render → **New +** → **Blueprint** → 選你 Fork 的 repo → **Connect**
3. 填入你自己的 `GEMINI_API_KEY` 等環境變數 → 部署
4. 之後你可以自由修改程式、改密碼，`git push` 後重新部署

---

## 環境變數

| Key | 必填 | 說明 |
|-----|------|------|
| `GEMINI_API_KEY` | ✅ | 你的 Gemini 金鑰 |
| `GOOGLE_API_KEY` | 選填 | 同 Gemini 金鑰的別名，建議一併填相同值 |
| `TAVILY_API_KEY` | 選填 | 旅遊助手上網查資料 |
| `OPENAI_API_KEY` | 選填 | 改用 OpenAI／Groq／DeepSeek 等相容服務時填 |
| `FLASK_DEBUG` | 自動 | `render.yaml` 已設為 `0` |
| `PYTHON_VERSION` | 自動 | `render.yaml` 已設為 `3.12.10` |

> 🔐 金鑰只填在 Render 儀表板，**不要**寫進程式碼或提交到 GitHub。本專案的 `.gitignore` 已排除 `.env`。

---

## 進入密碼

本 App 前端有一道密碼閘門（僅供阻擋一般瀏覽，**非安全機制**）。預設密碼寫死在 `templates/index.html` 的 `BUILTIN_HASHES`（以 SHA-256 儲存）。

要改成你自己的密碼：

1. 算出新密碼的 SHA-256（PowerShell 範例）：
   ```powershell
   $s="你的新密碼"; -join ([System.Security.Cryptography.SHA256]::Create().ComputeHash([Text.Encoding]::UTF8.GetBytes($s)) | % { $_.ToString("x2") })
   ```
2. 把 `templates/index.html` 裡 `BUILTIN_HASHES`（進入密碼）與 `ADMIN_HASH`（管理員密碼）的雜湊換成新值
3. 把 `static/sw.js` 的 `CACHE` 版本號 +1（讓 PWA 快取更新）
4. `git push` → 重新部署

---

## 本機開發

```bash
pip install -r requirements.txt
cp .env.example .env   # 填入你的金鑰
python app.py          # 預設 http://localhost:5001
```

---

## 技術架構

- **後端**：Flask + Flask-SocketIO（即時語音串流）
- **翻譯引擎**：Google Gemini Live／OpenAI 相容供應商
- **前端**：原生 HTML／JS，PWA（可加入手機主畫面、離線快取）
- **部署**：Render（Python 3.12，`render.yaml` Blueprint）
