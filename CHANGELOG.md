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

## 2026-07-14　側邊欄改用自訂分類藍圖 + 永遠顯示

- **側邊欄結構重寫**：從舊的「Basic 6 顆 + Feedback/Complex 少數」換成 Yuu 自訂的分類藍圖 —— 8 大類、**43 個陳列項目**（把框架 60 顆 L3 併組而成，詳見 memory `project_ds_component_taxonomy`）。
  - Basic 27→14：button（含 icon-button）、form、selector、tag、step、tab（+toggle）、menu、action-menu、breadcrumb、icon、pagination、price、section-head、announcement-item。
  - Complex 16→12：calendar（併 date/time/range picker + calendar-day）、collapse、list、card、modal、drawer、header、footer、floating-line、product-card、category-tile、article-card。
  - 其餘 6 類（table/chart/feedback/loading/special/media）照框架原樣列出。
- **狀態三態**：已定案（綠）／部分（琥珀，代表子元件已建、家族其餘待補）／待建（灰）。
- **新增 `selector` 家族頁**：一頁內用「家族小標」分隔，同時攤開 Checkbox、Radio 兩個既有矩陣 + Multi-select 待建佔位（把舊的獨立 checkbox/radio 收進來，不損失既有成果）。form→接 input、tag→接 tag、tab→接 tab 為代表子元件。
- **側邊欄永遠顯示**：移除 header 漢堡鈕與 scrim 遮罩、拿掉 `nav-hidden` 收合邏輯；行動版改成靜態堆疊（不再是覆蓋抽屜）。

---

## 2026-07-14　側邊欄分類收合 + 轉盤改勾選清單

- **側邊欄分類可收合**：類別標題（`.gh`）改成按鈕，左側 caret、右側顯示該類項目數；點一下收合／展開該類的項目（`.nav-group.collapsed .ng-items{display:none}`）。預設全展開。
- **檔案可直接雙擊開**：開頭補 `<meta charset="utf-8">`，不再依賴 http server（原本臨時 server 跑在沙箱內、空檔會被凍結導致「拒絕連線」）。
- **複雜度轉盤：tab-button chips → checkbox list**。每個軸值一列（前置 `<input type=checkbox>`），勾／取消即時過濾矩陣與變體計數；每軸至少保留一個（取消最後一個會被擋回）。button 頁 `renderAxes` 與其餘元件共用的 `mountDial` 兩處都改。
- **預設值**：初始強制淺色（`set('light')`，不跟隨系統）；側邊欄只展開第一類 Basic、其餘 7 類預設收合（`renderNav` 對 `gi>0` 加 `collapsed`）。

---

## 2026-07-14　Input 依 Figma spec 調整（Web Guideline input 元件）

比對來源 Figma：Web Guideline 檔 `InputFill`（node `17405:44467`）。token 差異這次不處理（框架每次建立會重設），只調行為／樣式與內容軸：

- **保留** outline（線框）＋ filled（底色）兩個 variant。
- **focus 去外陰影**：`.field.st-focus` 與 `input.field:focus` 移除 `box-shadow` ring，只留邊框變色。（error 狀態的紅框 ring 仍保留。）
- **移除 success 狀態**：states 從 7 → 6（default/hover/focus/filled-value/disabled/error），formula 改 `2 × 6`。錯誤文案維持用 boolean 控制（`error + hint`），不併進矩陣。
- **Content boolean 新增兩項**：`label 必填 (required)`（label 帶紅色 `*`，樣式 `.fieldwrap .fl .req`）、`後綴 eye (密碼顯示切換)`（欄位右側 Phosphor eye icon，新增 `ICON_EYE`）。

### 追加（同日）
- **filled hover 改加深底色**：`.field.v-filled.st-hover` 改成 `background:var(--panel-3)`、`border-color:transparent`（原本會冒出邊框）。outline hover 維持換邊框色。
- **error 去外陰影**：`.field.st-error` 移除 `box-shadow` ring（與 focus 一致，只描紅框）。
- **補上真實 hover 互動**：
  - live 欄位加 `input.field:hover`（outline 換框色）、`input.field.v-filled:hover`（加深底色）；Live 區改放 outline + filled 兩個真欄位可互動試。
  - 其餘 live demo 一併補真 `:hover`（scoped 不影響 matrix 靜態格）：checkbox／radio 用 `.optrow:hover`、tag 用 `.row-demo .tg:hover`、tab 用 `.row-demo .tabi:hover`。

---

## 2026-07-14　每個元件都補上「Live 控制面板」

原本只有 button 頁有 segmented 控制 + boolean 勾選 + 即時預覽的互動面板；抽成通用 `mountLive(host,cfg)`，套到所有已建元件。

- **`mountLive` cfg**：`segs`（分段控制，如 variant/intent/size）、`bools`（勾選內容軸）、`render(state)`（回傳預覽節點）、選用 `free`/`note`（快速對照列）。控制項一動 → 重繪 stage。
- **各元件配置**：
  - **input**：Variant(outline/filled) × 內容 label／必填／hint／leading icon／後綴 eye；預覽是真的可打字欄位（`.field.live-field` + 裸 `.bare` input，hover/focus-within 為真，eye 可切換 password 顯示）＋ outline/filled 快速對照列。
  - **checkbox**：Size(s/m/l) × indeterminate／disabled；預覽可真的點擊勾選。
  - **radio**：Size × disabled；預覽群組真的單選。
  - **tag**：Variant(solid/outline/soft) × Intent(brand/neutral) × icon／removable／selectable。
  - **tab**：Variant(line/contained/pill)；預覽分頁列可真的切換。
  - selector 家族頁因含 checkbox+radio，兩個 Live 面板都在。
- button 頁維持原本自己的 `renderLive`（外觀相同，未動以免回歸）。

---

## 2026-07-14　Input 補清除 × + form 拆成 input／dropdown

- **清除 × icon**：
  - live 可輸入欄位：打字後右側自動出現清除 ×（`.fieldclear`），點了清空並重新聚焦、隱藏（`onmousedown preventDefault` 避免點擊時失焦）。
  - 矩陣 `filled-value` 狀態也補上清除 ×（`inputEl` 對齊 Figma：有值 → clear）。後綴 eye demo 變成 clear ×＋eye 並存。
  - **清除 icon 改用圓底版**：新增 `ICON_XCIRCLE`（Phosphor `x-circle` fill，灰底圓 + 鏤空 ×），input 清除鍵改用它；配色 `.field .fieldclear svg` 預設 `--faint`、hover `--muted`。tag 的 removable × 維持細線 `ICON_CLOSE`。
- **後綴 eye 切換 icon**：新增 `ICON_EYE_SLASH`（Phosphor `eye-slash` regular，curl 官方 raw SVG）。live 密碼欄位點眼睛時，除了切 input type，icon 也在 eye ↔ eye-slash 間切換（遮罩→一般眼、顯示明文→斜線眼，示意再點會隱藏）。
- **Live 預覽 stage 背景調淡**：`.live-stage` 背景 `--panel-2` → `--panel`。原本 stage 與 filled 欄位同為 `--panel-2`，filled 預覽會融進背景看不到；改白底後淺灰 filled 欄位可浮現（虛線框仍界定預覽區）。
- **分類拆分**：原 `form` 家族拆成兩個獨立 Basic 項目 —— **input**（input／password／file／search／textarea，接 `buildInput`）與 **dropdown**（dropdown／cascader，待建 placeholder）。NAV id `form`→`input`、新增 `dropdown`；`BUILD.form`→`BUILD.input`。Basic 由 14 → 15 項、總數 43 → 44。詳見 memory `project_ds_component_taxonomy`。

---

## 2026-07-14　Input 擴成家族頁（補齊 search／password／textarea／file）

input 頁改成家族頁：基本 input 之後用家族小標接續四個子元件，各有「狀態列 + Live 控制面板」。狀態升為「已定案」。

- **共用**：新增 `famBlock(host,cfg)`（小標 + 說明 + 狀態列 + Live）；`mountLive` 支援無控制項（空 ctl-row 不顯示）；新增 `ICON_UPLOAD`（Phosphor upload-simple）。
- **Search 搜尋框**：前置放大鏡 + 有值清除 ×；狀態 default/hover/focus/filled-value/disabled；Live 可 outline/filled + 真搜尋。
- **Password 密碼輸入**：遮罩 •••• + 後綴 eye（eye↔eye-slash）+ 清除；狀態含 error；Live 真的可輸入切明碼。
- **Textarea 多行輸入**：新 `.tarea` 樣式（多行、可垂直 resize）；6 狀態；Live outline/filled 真 textarea。
- **File 檔案上傳**：新 `.filefield`／`.filebtn` 樣式（選檔鈕 + 檔名 + 已選檔清除）；狀態含未選／已選檔／error；Live 點鈕切換檔名。
  - 修：file live 補真實 hover（`.live-stage .filefield:hover` 邊框轉 accent、按鈕 bg 加深，scoped 不影響狀態列）＋ 選檔後出現圓底刪除 ×（點了清掉檔案、重置未選）。

---

## 2026-07-14　新增「複雜度確認」總覽頁 + 中央複雜度 store

- **中央複雜度 store**：新增 `DIAL_SPECS`（button/input/checkbox/radio/tag/tab 六個轉盤元件的 axes/zh/label）＋ `CHOICE`（各元件選擇集，載入時預設全展）。六個 build 的 `active` 改接 `CHOICE[id].active`、axes/zh 改讀 `DIAL_SPECS[id]`，選擇**跨切頁持久**（原本切頁重置）。
- **複雜度確認總覽頁**（側邊欄置頂 📋）：`renderSummary` 彙整每個轉盤元件最後選定的 variant × state；每軸顯示選中（accent 藥丸）vs 排除（刪除線），附各軸 n/total、單元件變體數、全頁總計（例 154/189）。
- **複製篩選結果 (JSON)**：一鍵把各元件選擇 + 變體數複製成 JSON，供 DS 建立流程使用。
- 用途：每次建新 DS 時，這頁即為預覽 + 篩選結果交付，讓 Yuu 確認會在設計稿中建立哪些元件與狀態。

---

## 2026-07-14　新增 Dropdown 下拉元件

- **Dropdown**（側邊欄升「已定案」）：對齊 spec 但沿用 input 決策 —— variant outline/filled × state default/hover/focus/**open**/disabled/error（拿掉 spec 的 size 與 success，已在頁面註明可補）。
- 觸發器用 `.field.dd` + `.ddcaret`（open 時 caret 朝上 + accent 描邊）；矩陣 State × Variant。
- **Live**：真的可點開/選項/關閉的下拉（`.ddmenu`/`.ddopt`），`searchable` 開啟時出現 `.ddsearch` 過濾。
- 已納入中央 `DIAL_SPECS`/`CHOICE` → 複雜度總覽自動多一張 Dropdown 卡（共 7 張）。

### 追加：label／hint boolean + 單選／多選
- **Content boolean**：比照 input 加 `with-label`／`label 必填 (required)`／`with-hint` demo（＋Live 的 label／必填 *／hint 勾選，label 開時勾必填出現紅星 `.req`）。
- **單選／多選兩種**：Live 加 Type 分段（單選／多選）。`ddField(variant,{searchable,multi})` —— 單選點選項即關閉；多選選項帶勾選框（`.ddcheck`）、勾選不關、選中在 trigger 內成可移除 chip（`.ddchip`，`.field.dd.multi` 允許換行）。
- **補已選狀態**：state 軸加 `selected`（已選）—— 矩陣單選已選顯示選定值。

### 藍圖決策：single/multi 改成 dropdown 的變體軸（併入 multi-select）
- 原本 single/multi 是 Live 的功能開關；改成**跟 outline/filled 同層級的 `type` 變體軸**。dropdown 軸 = `type(單選/多選) × variant(outline/filled) × state` = **2×2×7 = 28**。
- **multi-select 併入 dropdown**（作為多選 type），從 selector 家族移除（`buildSelector` 拿掉 multi-select 佔位、改註記）。
- 矩陣改成「每個 type 一塊、欄=variant、列=state」；`ddEl(variant,state,multi)` 的 selected 列：單選顯示值、多選顯示 chips。`ddField` Live 不變（Type 分段仍在）。
- 中央 `DIAL_SPECS.dropdown` 加 type 軸 → 複雜度總覽 Dropdown 卡自動變 type×variant×state。詳見 memory `project_ds_component_taxonomy`。

---

## 2026-07-14　RWD：平板／手機改漢堡抽屜

原本窄螢幕把側欄靜態堆疊，整頁被側欄佔滿、內容被推到下面。改為：

- **桌機（>900px）**：側欄常駐 sticky 雙欄，無漢堡（維持原樣）。
- **平板／手機（≤900px）**：加回漢堡鈕（`.hamb`，只在窄螢幕顯示）；側欄改 `position:fixed` 抽屜、預設 `translateX(-100%)` 收起、內容全寬可見；點漢堡 `shell.nav-open` 滑出 + 遮罩（`.scrim`）、點遮罩或選完項目自動收起（`renderView` 在 `innerWidth<=900` 時移除 `nav-open`）。抽屜**不加陰影**，改 1px `border-right` 界定邊緣。

---

## 2026-07-14　Input 家族矩陣調整（variant 並排 + search/file variant）

- **Input base 矩陣**：改成單一表格、外框／實填**左右並排**（variant 當欄，原本是兩塊上下堆疊）。
- **famBlock 擴充**：支援 `variants` 矩陣模式（state 列 × variant 欄，`el(variant,state)`）。
- **Search 搜尋框**：補 variant 矩陣（外框／實填），`searchEl(variant,state)`。
- **File 檔案上傳**：加自己的 variant（外框／實色）；`fileEl(variant,state)` + `.filefield.v-solid` 樣式；矩陣外框／實色並排；Live 也加 variant 分段（outline/solid）。
  - 修：file 外框／底色**對齊 input** —— 邊框由虛線改 1px 實線（`--i-sec-edge`）、實色底 `--panel-2`，computed 值與 `.field` 一致；差別只在 content（按鈕＋檔名）。

- **已完成**：Basic 六顆（button / input / checkbox / radio / tag / tab），皆經 Playwright 驗證。
- **待建**：Feedback（tooltip / alert）＋ Complex（card / modal / dropdown）。
- **之後可能**：正式版控（建 repo / commit）、接真 token、生成 React/Vue 元件庫。

## 已知踩坑（避免重犯）
- 本機用 Playwright 驗證含中文的 HTML：`python -m http.server` 要送 `charset=utf-8`，否則瀏覽器誤解碼中文、噴假的 JS 語法錯（`node --check` 過就是 server 的鍋）。`file://` 會被 Playwright 擋。
- SVG 放在 `inline-flex` 容器內、寬度用 `%` 會塌成 0px → icon 尺寸一律用固定 px。
