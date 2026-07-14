# DS 元件陳列頁 — 變更紀錄

> 檔案：`ds-component-gallery.html`（self-contained HTML，用來跟設計/團隊溝通「元件該多複雜」）
> 來源 spec：`../ds-framework/L0-scope-manifest.yaml`（唯讀，勿寫入）
> 註：本專案尚未建 git repo，此檔手動記錄變更；之後若正式版控可移除。

---

## 2026-07-13 ~ 07-14　初版建置（Basic 六顆）

分階段做出來，依序如下：

### 1. Button 陳列頁（起點）
- 依 L3 spec 攤開 button 完整複雜度：variant × intent × state × size（3 × 3 × 4 × 3 = 108）。
- State × Intent 矩陣（每 variant 一塊）、Size 尺寸列、Content 內容軸（boolean：leading/trailing icon、icon-only）。
- 「複雜度轉盤」：勾掉任一軸值 → 矩陣與變體計數即時縮減。
- 色值為 placeholder token（inline 寫死、self-contained），對應 `semantic.action.*`。
- 深淺色兩套 token 都調過。

### 2. 導覽 + Live 互動
- 左側元件導覽（依 L0 category 分組：Basic / Feedback / Complex），header 有 ☰ 開關、手機版變抽屜。
- 右上深淺色切換（sun/moon + 標籤）。
- 新增「實際互動 Live」區：真的可 hover / 按下的按鈕 + 可調 variant/intent/size/icon/disabled 的控制器。

### 3~4. 複雜度轉盤樣式
- 轉盤 chip 改成 tab button 樣式：active 實色填滿、inactive 灰階。
- chip 圓角改為 999（全圓角膠囊）。

### 5. 補齊其餘 Basic 元件（input / checkbox / radio / tag / tab）
- 每顆依各自 L3 spec 的實際軸建矩陣（不是自編）：
  - **Input**：variant(outline/filled) × 7 狀態（含 focus/error/success）；label/hint/icon 走 boolean；Live 欄位可 focus。
  - **Checkbox**：6 狀態 × 3 size（含 indeterminate）；無 variant；Live 可勾選。
  - **Radio**：5 狀態 × 3 size；Live 群組單選。
  - **Tag**：variant(solid/outline/soft) × intent(brand/neutral) × 狀態；Live 有 removable / selectable。
  - **Tab**：variant(line/contained/pill) × 4 狀態；Live 三條可切換的分頁列。

### 6. 複雜度轉盤 → 可收合具名牌卡
- 轉盤改成獨立牌卡，標題列（標題 + 即時計數 + caret）可點擊收合。
- 六顆元件**都**配上各自的可收合轉盤，且真的能過濾各自矩陣（不是裝飾）。

### 7. 全頁 icon 改用 Phosphor（開源）
- 來源：`phosphor-icons/core` regular（viewBox 256、fill-based）。
- 對應：check / minus / caret-down / pencil-simple / arrow-right / x / magnifying-glass / list / sun / moon-stars。
- 修好兩個原本沒顯示的 icon：
  1. 收合 caret（原本用文字字元 `▾`，部分字型不顯示）＋ button 那顆漏改的 chevron。
  2. **Checkbox 勾看不到（真 bug）**：`.cbx svg` 用 `width:70%`，SVG 在 inline-flex 容器裡百分比寬度會塌成 0px；改固定 px（11/14/16）修正。

---

## 現況與待辦

- **已完成**：Basic 六顆（button / input / checkbox / radio / tag / tab），皆經 Playwright 驗證。
- **待建**：Feedback（tooltip / alert）＋ Complex（card / modal / dropdown）。
- **之後可能**：正式版控（建 repo / commit）、接真 token、生成 React/Vue 元件庫。

## 已知踩坑（避免重犯）
- 本機用 Playwright 驗證含中文的 HTML：`python -m http.server` 要送 `charset=utf-8`，否則瀏覽器誤解碼中文、噴假的 JS 語法錯（`node --check` 過就是 server 的鍋）。`file://` 會被 Playwright 擋。
- SVG 放在 `inline-flex` 容器內、寬度用 `%` 會塌成 0px → icon 尺寸一律用固定 px。
