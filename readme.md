# 極簡倒數計時器

一個美觀、功能完整的倒數計時器網頁應用程式，支援 URL 狀態共享、主題切換、鍵盤快捷鍵等進階功能。

## ✨ 主要功能

### 🎯 核心功能
- **彈性時間設定**：支援時、分、秒獨立調整
- **快速預設**：一鍵設定 5/10/15/20/30 分鐘
- **視覺化進度**：圓形進度環即時顯示倒數進度
- **音效提示**：操作回饋音效 + 計時結束鬧鈴
- **自訂標題**：可編輯計時器名稱

### 🔗 URL 狀態共享
- **跨裝置同步**：複製 URL 即可在任何裝置繼續倒數
- **即時狀態追蹤**：URL 自動包含計時狀態（進行中/暫停）
- **精確時間計算**：基於時間戳記，確保跨裝置時間一致
- **標題同步**：自訂標題也會包含在分享連結中

**URL 參數說明：**
- `t` - 初始時間（秒數）
- `s` - 狀態（`running` 或 `paused`）
- `start` - 開始時間戳記
- `title` - 計時器標題

**範例：**
```
?t=1800&s=running&start=1736564921&title=%E5%B7%A5%E4%BD%9C%E8%A8%88%E6%99%82
```

### 🎨 主題系統
- **白天主題**：紫色漸層背景 + 紫色進度環
- **黑夜主題**：深色系背景 + 藍色進度環
- **平滑切換**：主題切換動畫流暢自然
- **狀態保存**：主題偏好自動儲存

### ⌨️ 鍵盤快捷鍵
- `Space` - 開始/暫停計時
- `↑` - 增加 1 分鐘
- `↓` - 減少 1 分鐘
- `R` - 重設計時器

### 💾 本地儲存
- 自動儲存最後設定的時間
- 記憶主題偏好
- 保存自訂標題

## 📖 操作說明

### 基本使用

1. **設定時間**
   - 使用 `+` / `-` 按鈕調整時、分、秒
   - 或點擊預設按鈕快速設定（5/10/15/20/30 分鐘）

2. **開始倒數**
   - 點擊「開始」按鈕或按 `Space` 鍵
   - 計時器開始倒數，進度環同步更新
   - 按鈕變為「暫停」

3. **暫停/繼續**
   - 點擊「暫停」或再次按 `Space` 鍵暫停
   - 再次點擊「開始」繼續倒數

4. **重設計時器**
   - 點擊「重設」按鈕或按 `R` 鍵
   - 計時器回到初始設定時間

5. **自訂標題**
   - 點擊頂部標題文字即可編輯
   - 標題會自動儲存

6. **切換主題**
   - 點擊右上角月亮/太陽圖示
   - 在白天/黑夜主題間切換

### 進階使用：URL 分享

1. **分享進行中的計時器**
   - 設定並開始計時器
   - 複製瀏覽器網址列的 URL
   - 分享給他人或在其他裝置開啟
   - 計時器會自動從正確的剩餘時間繼續倒數

2. **分享暫停狀態**
   - 暫停計時器時複製 URL
   - 他人開啟時會看到相同的剩餘時間
   - 需手動點擊「開始」繼續倒數

3. **使用情境範例**
   - 工作計時：在電腦開始 25 分鐘專注時間，複製 URL 到手機繼續追蹤
   - 團隊協作：分享倒數連結給團隊成員，確保所有人看到相同的剩餘時間
   - 跨裝置：在不同裝置間無縫切換，不中斷計時

## 🛠️ 技術說明

### 技術架構
- **純前端實現**：單一 HTML 檔案，無需後端伺服器
- **零依賴**：不使用任何外部 JavaScript 框架或函式庫
- **現代 Web API**：使用 Web Audio API、LocalStorage、URL API

### 核心技術

#### 1. 時間管理
```javascript
// 使用 Unix timestamp 確保跨裝置時間一致
startTimestamp = Math.floor(Date.now() / 1000) - elapsed;
const now = Math.floor(Date.now() / 1000);
const elapsed = now - startTimestamp;
totalSeconds = Math.max(0, initialSeconds - elapsed);
```

#### 2. URL 狀態同步
- 使用 `URLSearchParams` 管理 URL 參數
- `window.history.replaceState()` 更新 URL 不重新載入頁面
- 頁面載入時解析 URL 參數並恢復狀態

#### 3. 視覺效果
- **Glassmorphism**：毛玻璃效果卡片設計
- **SVG 進度環**：使用 `stroke-dashoffset` 動畫
- **CSS 變數**：主題系統使用 CSS Custom Properties
- **平滑動畫**：`cubic-bezier` 緩動函數

#### 4. 音效系統
```javascript
// Web Audio API 生成音效，無需外部音檔
const audioCtx = new AudioContext();
const osc = audioCtx.createOscillator();
osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
```

#### 5. 響應式設計
- 使用 `clamp()` 函數實現流體字體大小
- Flexbox 佈局適應不同螢幕尺寸
- 移動裝置優化：觸控友善的按鈕尺寸

### 瀏覽器兼容性
- Chrome/Edge 88+
- Firefox 85+
- Safari 14+
- 支援所有現代瀏覽器

### 效能優化
- 單檔案架構，載入速度快
- 最小化 DOM 操作
- 使用 CSS 動畫而非 JavaScript 動畫
- LocalStorage 減少不必要的狀態計算

## 📦 安裝說明

### 方法一：直接使用（推薦）

1. 下載 `index.html` 檔案
2. 用瀏覽器開啟即可使用
3. 無需安裝任何依賴或執行建置步驟

### 方法二：本地伺服器

如果需要測試 URL 分享功能，建議使用本地伺服器：

**使用 Python：**
```bash
# Python 3
python -m http.server 8000

# Python 2
python -m SimpleHTTPServer 8000
```

**使用 Node.js：**
```bash
# 安裝 http-server
npm install -g http-server

# 啟動伺服器
http-server -p 8000
```

**使用 VS Code：**
- 安裝 "Live Server" 擴充功能
- 右鍵點擊 `index.html` → "Open with Live Server"

然後在瀏覽器開啟 `http://localhost:8000`

### 方法三：部署到網頁伺服器

**GitHub Pages：**
1. 建立 GitHub repository
2. 上傳 `index.html`
3. 在 Settings → Pages 啟用 GitHub Pages
4. 訪問 `https://yourusername.github.io/repository-name`

**Netlify/Vercel：**
1. 拖放 `index.html` 到 Netlify Drop 或 Vercel
2. 自動部署完成

**傳統主機：**
- 直接上傳 `index.html` 到網頁伺服器根目錄
- 通過網域訪問

## 🎯 使用場景

- **番茄工作法**：25 分鐘專注 + 5 分鐘休息
- **會議計時**：控制會議時間，提高效率
- **運動訓練**：間歇訓練計時
- **烹飪計時**：精確控制烹飪時間
- **考試模擬**：模擬考試時間限制
- **團隊協作**：分享倒數連結，團隊同步

## 📝 授權

MIT License - 自由使用、修改、分發

## 🔄 更新日誌

### v1.0.0 (2026-01-11)
- ✨ 初始版本發布
- 🎨 白天/黑夜雙主題
- 🔗 URL 狀態共享功能
- ⌨️ 鍵盤快捷鍵支援
- 🎵 Web Audio API 音效系統
- 💾 LocalStorage 狀態保存
- 📱 響應式設計
