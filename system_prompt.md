# System Prompt: 極簡倒數計時器

## 專案概述

建立一個美觀、功能完整的倒數計時器網頁應用程式，使用純 HTML、CSS、JavaScript 實現，無需任何外部依賴。重點功能包括 URL 狀態共享、主題切換、視覺化進度顯示。

## 核心需求

### 1. 基礎功能
建立一個倒數計時器，具備以下功能：
- 時、分、秒獨立調整（使用 +/- 按鈕）
- 快速預設按鈕：5、10、15、20、30 分鐘
- 開始/暫停/重設功能
- 可編輯的計時器標題
- 計時結束時播放鬧鈴音效（5 次）

### 2. 視覺設計

#### 設計風格
- 使用 **Glassmorphism**（毛玻璃效果）設計風格
- 漸層背景，營造現代感
- 圓形進度環顯示倒數進度
- 平滑的動畫過渡效果
- **行動裝置優化**：標題使用 Grid 佈局確保絕對居中，主題按鈕固定寬度防止擠壓

#### 配色方案

**白天主題（預設）：**
- 背景：紫色漸層 (`#667eea` → `#764ba2` → `#f093fb`)
- 卡片：半透明白色毛玻璃效果
- 文字：白色
- 進度環：鮮豔紫色 (`#a855f7`)
- 按鈕：半透明白色，hover 時加深

**黑夜主題：**
- 背景：深色漸層 (`#0f172a` → `#1e293b` → `#334155`)
- 卡片：半透明深色毛玻璃效果
- 文字：淺灰白色
- 進度環：藍色 (`#60a5fa`)
- 按鈕：半透明灰色，hover 時加深

#### 視覺元素
- 圓形 SVG 進度環，使用 `stroke-dashoffset` 動畫
- 倒數時間顯示在進度環中央，大字體、等寬數字
- 時、分、秒調整器顯示當前數值
- 按鈕使用圓角設計（14-16px border-radius）
- Hover 效果：輕微上移 + 陰影加深
- 主題切換按鈕：圓形，帶旋轉動畫

### 3. URL 狀態共享（重要功能）

實現完整的 URL 狀態共享系統：

#### URL 參數設計
- `t` - 初始時間（秒數），保持不變
- `s` - 狀態（`running` 或 `paused`）
- `start` - Unix timestamp（秒），記錄開始時間點
- `title` - 計時器標題（URL 編碼）

#### 實現邏輯

**開始計時時：**
```javascript
// 計算開始時間戳記（考慮已經過的時間）
const elapsed = initialSeconds - totalSeconds;
startTimestamp = Math.floor(Date.now() / 1000) - elapsed;

// 更新 URL
url.searchParams.set('t', initialSeconds);
url.searchParams.set('s', 'running');
url.searchParams.set('start', startTimestamp);
url.searchParams.set('title', encodeURIComponent(title));
```

**暫停時：**
```javascript
// 保持相同的 start timestamp，狀態改為 paused
url.searchParams.set('s', 'paused');
```

**載入 URL 時：**
```javascript
// 從 URL 讀取參數
const initialSeconds = parseInt(urlParams.get('t'));
const state = urlParams.get('s');
const startTimestamp = parseInt(urlParams.get('start'));

// 計算當前剩餘時間
const now = Math.floor(Date.now() / 1000);
const elapsed = now - startTimestamp;
const currentSeconds = Math.max(0, initialSeconds - elapsed);

// 如果狀態是 running，自動開始倒數
if (state === 'running' && currentSeconds > 0) {
    // 自動啟動計時器
}
```

#### 關鍵要點
- 時間參數 `t` 始終是初始設定時間，不隨倒數改變
- 使用 Unix timestamp 確保跨裝置時間一致
- `running` 狀態的 URL 開啟時自動繼續倒數
- `paused` 狀態的 URL 開啟時顯示剩餘時間但不自動開始

### 4. 音效系統

使用 **Web Audio API** 生成音效，無需外部音檔：

```javascript
const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

function playSound(freq, duration, type = 'sine') {
    if (audioCtx.state === 'suspended') audioCtx.resume();
    const osc = audioCtx.createOscillator();
    const gain = audioCtx.createGain();
    osc.type = type;
    osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
    gain.gain.setValueAtTime(0.08, audioCtx.currentTime);
    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + duration);
    osc.connect(gain);
    gain.connect(audioCtx.destination);
    osc.start();
    osc.stop(audioCtx.currentTime + duration);
}
```

**音效使用：**
- 按鈕點擊：400-600Hz，短促音效（0.05-0.1 秒）
- 計時結束：880Hz，方波，持續 0.5 秒，重複 5 次

### 5. 本地儲存

使用 `localStorage` 儲存：
- 最後設定的時間（`initialSeconds`）
- 主題偏好（`dark` 或 `light`）
- 自訂標題

```javascript
localStorage.setItem('timer_settings', JSON.stringify({
    title: appTitleInput.value,
    lastTime: initialSeconds,
    theme: theme
}));
```

### 6. 鍵盤快捷鍵

實現以下快捷鍵：
- `Space` - 開始/暫停計時
- `↑` - 增加 1 分鐘
- `↓` - 減少 1 分鐘
- `R` - 重設計時器

注意：計時進行中時，只允許 Space 鍵操作。

### 7. 響應式設計

**桌面版（>640px）：**
- 最大寬度 540px，置中顯示
- 按鈕橫向排列
- 進度環 280px

**移動版（≤640px）：**
- 全寬顯示，保持邊距
- 按鈕改為縱向堆疊
- 字體大小使用 `clamp()` 自適應

### 8. 動畫與互動

**進度環動畫：**
```css
.progress-ring-progress {
    transition: stroke-dashoffset 1s linear;
}
```

**脈衝動畫（計時進行中）：**
```css
@keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.6; }
}
```

**按鈕 Hover 效果：**
- `transform: translateY(-2px)` - 輕微上移
- `box-shadow` 加深
- 過渡時間 0.2-0.3 秒，使用 `cubic-bezier(0.4, 0, 0.2, 1)`

**主題切換動畫：**
- 按鈕旋轉 180 度
- 背景、文字顏色平滑過渡（0.4 秒）

## 技術規格

### HTML 結構
```html
<div class="container">
    <div class="glass-card">
        <!-- Header: 標題 + 主題切換 -->
        <div class="header">
            <input type="text" class="title-input" value="倒數計時器">
            <button class="theme-toggle">🌙</button>
        </div>
        
        <!-- Timer Display: SVG 進度環 + 時間顯示 -->
        <div class="timer-container">
            <svg class="progress-ring">
                <circle class="progress-ring-circle"></circle>
                <circle class="progress-ring-progress"></circle>
            </svg>
            <div class="timer-display">00:00:00</div>
        </div>
        
        <!-- Controls: 時間調整器 + 預設按鈕 -->
        <div class="controls">
            <!-- 時、分、秒調整器 -->
            <!-- 預設按鈕 -->
        </div>
        
        <!-- Actions: 開始/暫停、重設 -->
        <div class="actions">
            <button class="action-btn btn-start">開始</button>
            <button class="action-btn btn-reset">重設</button>
        </div>
    </div>
</div>
```

### CSS 架構

**使用 CSS 變數管理主題：**
```css
:root {
    --bg-gradient-1: #667eea;
    --text-primary: #ffffff;
    --progress-fill: #a855f7;
    /* ... 其他變數 */
}

body.dark {
    --bg-gradient-1: #0f172a;
    --text-primary: #f1f5f9;
    --progress-fill: #60a5fa;
    /* ... 覆寫變數 */
}
```

**關鍵樣式：**
- `backdrop-filter: blur(20px)` - 毛玻璃效果
- `font-variant-numeric: tabular-nums` - 等寬數字
- `clamp(2.5rem, 10vw, 4rem)` - 流體字體大小

### JavaScript 架構

**狀態管理：**
```javascript
let totalSeconds = 0;        // 當前剩餘秒數
let initialSeconds = 0;      // 初始設定秒數
let timerInterval = null;    // 計時器 interval ID
let isRunning = false;       // 是否正在運行
let startTimestamp = 0;      // 開始時間戳記
```

**核心函數：**
- `updateDisplay()` - 更新時間顯示
- `updateProgress()` - 更新進度環
- `updateURL()` - 更新 URL 參數
- `toggleTimer()` - 開始/暫停
- `resetTimer()` - 重設
- `adjust(unit, val)` - 調整時間
- `setPreset(mins)` - 設定預設時間
- `saveState()` - 儲存到 localStorage
- `toggleTheme()` - 切換主題

## 字體

使用 Google Fonts：
- **Inter** - 英文、數字（主要）
- **Noto Sans TC** - 中文

```html
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&family=Noto+Sans+TC:wght@400;500;600;700&display=swap" rel="stylesheet">
```

## 實現細節

### 進度環計算
```javascript
const radius = 90;
const circumference = 2 * Math.PI * radius;
const progress = totalSeconds / initialSeconds;
const offset = circumference - (progress * circumference);
progressCircle.style.strokeDashoffset = offset;
```

### 時間格式化
```javascript
const h = Math.floor(totalSeconds / 3600);
const m = Math.floor((totalSeconds % 3600) / 60);
const s = totalSeconds % 60;
const formatted = `${String(h).padStart(2, '0')}:${String(m).padStart(2, '0')}:${String(s).padStart(2, '0')}`;
```

### URL 更新策略
- 調整時間時：更新 URL（清除狀態參數）
- 開始計時時：添加 `s=running` 和 `start` 參數
- 暫停時：改為 `s=paused`，保持 `start` 參數
- 重設時：清除狀態參數，保留時間參數
- 計時結束時：清除狀態參數

## 品質要求

### 效能
- 單檔案，總大小 < 25KB
- 首次渲染 < 100ms
- 動畫流暢，60fps

### 可用性
- 所有互動元素有明確的視覺回饋
- 按鈕尺寸適合觸控操作（最小 36x36px）
- 色彩對比符合 WCAG AA 標準
- 鍵盤完全可操作

### 兼容性
- 支援所有現代瀏覽器（Chrome、Firefox、Safari、Edge）
- 移動裝置完全支援
- 無需 polyfill

### 程式碼品質
- 使用 ES6+ 語法
- 函數單一職責
- 適當的註解說明
- 一致的命名規範

## 測試場景

### 基本功能測試
1. 設定時間並開始倒數
2. 暫停後繼續
3. 重設計時器
4. 計時結束鬧鈴
5. 主題切換

### URL 分享測試
1. 開始計時器，複製 URL，在新分頁開啟 → 應自動繼續倒數
2. 暫停計時器，複製 URL，在新分頁開啟 → 應顯示相同剩餘時間
3. 等待一段時間後開啟 URL → 時間應正確計算經過時間
4. 修改標題後分享 → 標題應正確顯示

### 邊界情況測試
1. 時間為 0 時點擊開始 → 不應啟動
2. 計時進行中調整時間 → 應被禁用
3. 快速連續點擊按鈕 → 不應出現錯誤
4. 長時間運行（>1 小時）→ 應正常運作

## 完整實現提示

這是一個**單檔案應用程式**，所有 HTML、CSS、JavaScript 都在 `index.html` 中。

關鍵實現順序：
1. HTML 結構 + 基礎 CSS
2. 主題系統（CSS 變數）
3. 時間調整功能
4. 計時器核心邏輯
5. 進度環動畫
6. 音效系統
7. URL 狀態共享（最複雜）
8. 鍵盤快捷鍵
9. LocalStorage 整合
10. 響應式優化

重點難點：
- **URL 狀態共享**：需要精確計算時間戳記和經過時間
- **進度環動畫**：SVG stroke-dashoffset 計算
- **主題切換**：CSS 變數的正確使用
- **音效系統**：Web Audio API 的正確初始化
