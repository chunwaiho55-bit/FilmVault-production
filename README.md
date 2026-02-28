# 雪櫃菲林 · Film Fridge Inventory PWA

底片攝影全生命週期管理工具

## 📁 檔案結構

```
film-fridge/
├── index.html      ← 主應用 (React + 所有邏輯)
├── manifest.json   ← PWA 設定
├── sw.js           ← Service Worker (離線緩存)
├── icon.svg        ← App 圖示
└── README.md       ← 本文件
```

## 🚀 本地運行

需要一個 HTTP 伺服器（不能直接雙擊 HTML，PWA 功能需要 HTTP）：

### 方法 1：Python（最簡單）
```bash
cd film-fridge
python3 -m http.server 8080
# 打開瀏覽器：http://localhost:8080
```

### 方法 2：Node.js
```bash
npx serve .
# 或
npx http-server . -p 8080
```

### 方法 3：VS Code Live Server
安裝 Live Server 插件，右鍵 index.html → Open with Live Server

## 📱 安裝到手機

1. 用 **Chrome（Android）** 或 **Safari（iOS）** 開啟
2. Android：點擊瀏覽器「安裝 App」提示 / 選單 → 「加到主畫面」
3. iOS：分享按鈕 → 「加到主畫面」
4. 安裝後以全螢幕獨立模式運行，離線也可用

## 🌐 公開部署（可讓手機訪問）

### Netlify Drop（最快）
1. 去 https://app.netlify.com/drop
2. 把整個 film-fridge 資料夾拖進去
3. 即時獲得 HTTPS URL

### GitHub Pages
```bash
git init && git add . && git commit -m "init"
# 在 GitHub 建 repo，push，開啟 Pages
```

## 📊 資料模型

```typescript
// 菲林類型
interface Film {
  id: string;
  name: string;      // "Kodak Portra 400"
  iso: string;
  frames: number;    // 36 | 24 | 12
  expiry: string;    // "2027-06"
  barcodes: string[];
}

// 庫存（雪櫃）
type Stock = Record<string, number>; // { filmId: qty }

// 相機
interface Camera {
  id: string;
  name: string;  // "Leica M6"
}

// 活躍菲林卷（貫穿整個生命週期）
interface ActiveRoll {
  id: string;
  filmId: string;
  filmName: string;
  cameraId: string;
  cameraName: string;
  status: 'loaded' | 'unloaded' | 'developing' | 'archived';
  loadedAt: string;      // ISO timestamp
  unloadedAt?: string;
  sentAt?: string;
  archivedAt?: string;
  notes: string;         // "檳城街拍"
  lab: string;           // "Lab88"
  downloadUrl: string;
  folderName: string;    // "202604_LeicaM6_Portra400_PenangTrip"
  posted: boolean;
}

// 操作紀錄
interface LogEvent {
  id: string;
  type: 'stock' | 'load' | 'note' | 'unload' | 'lab' | 'archive' | 'post';
  date: string;        // "2026-04-10"
  timestamp: string;   // ISO
  title: string;
  detail: string;
}
```

## ✨ 功能清單

### Tab 1：📦 雪櫃
- [x] 庫存清單（依菲林名稱排序）
- [x] 過期菲林紅色標示
- [x] LOW 庫存警示（≤2卷）
- [x] 入貨按鈕 + 表單
- [x] Barcode 掃描（Quagga.js）
- [x] 本地學習型條碼庫（掃描 → 綁定名稱 → 下次自動識別）
- [x] 庫存 +/- 手動調整

### Tab 2：📷 相機
- [x] 服役中列表（Loaded）
- [x] 等待送沖列表（Unloaded）
- [x] 入機（庫存自動 -1）
- [x] 拍攝筆記（隨時更新）
- [x] 退片 Unload
- [x] 送沖曬（輸入沖曬店名稱）

### Tab 3：📁 歸檔
- [x] 沖曬中列表
- [x] 一鍵歸檔 + 自動 Folder 命名
- [x] 格式：YYYYMM_Camera_Film_Notes
- [x] 一鍵複製命名
- [x] Download URL 記錄
- [x] Posted Toggle（IG 發佈管理）

### Tab 4：🗓 紀錄
- [x] 全自動 Timeline（無需手動輸入）
- [x] 依日期倒序分組
- [x] 7 種事件類型色碼分類

## 🔮 未來可加功能
- [ ] 雲端同步（Supabase / Firebase）
- [ ] 匯出 CSV / PDF 報告
- [ ] 相機統計（用了幾卷、最常用菲林）
- [ ] 照片縮圖（歸檔時上傳代表作）
- [ ] 多語言（英文 / 日文）
