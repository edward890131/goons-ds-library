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

## 2026-07-14　元件微調（selector / tag / 邊框）

- **checkbox / radio**：修等寬等高 bug——`.cbx.sz-m` 等尺寸 class 撞到通用按鈕 `.sz-m`（帶 `padding:0 16px`），border-box 下被撐成長方形；base 補 `padding:0;box-sizing:border-box;flex:0 0 auto`。移除 focus 外陰影（只留框色）。size 軸收成只剩 `m`（矩陣、轉盤、Live 分段、公式同步）。
- **tag**：新增 `corner` 軸（圓角 999 / 方角 5px），矩陣欄＝intent×corner、Live 加 Corner 分段、轉盤多一軸。字級 padding 縮一級（font 12→11、pad 11→8、height 26→22）與按鈕拉開。移除 `selected` 狀態。
- **outline 邊框 0.75px**：button / field(input+dropdown) / textarea / file / tag 的 base border 一律 `0.75px`（filled/solid 邊框透明或同色不受影響）。

## 2026-07-14　側邊欄 menu list 大改版

- 類別標題與頁籤標題同字級/字重/色（sans 13.5 / 600 / `--muted`）；計數＋chevron 靠標題右側、標題靠左、無底色。
- 展開內層往內縮＋導引細線；類別間 1px `--line` 分隔線。
- 當前頁淺藍底（`.nav-item[aria-current]`）；「匯出需求」pin 套同一套高亮邏輯。
- 建立狀態改成右側色圈（綠已定案／琥珀部分／灰待建），移除文字 tag。hover 底色調淺（`--panel-2`→`--canvas`）。類別標題前加 emoji（🧱/🧩/📊）。

## 2026-07-14　分類藍圖重組（8 組 → 3 組、37 項）

- **Basic（18）**：併入 Feedback（alert/tooltip/notification）、Loading（skeleton/loader）、Media（image）。
- **Complex（15）**：收 step、announcement、section-head。
- **Table & Chart（4）**：table、chart 各自子部件收成一頁；併入原 Special（tree-view / schedule-chart）。
- selector / tag 狀態改「已定案」。詳見 memory `project_ds_component_taxonomy`。

## 2026-07-14　品牌 / 主題 / scrollbar

- 站名改 **Design System Library**；topbar 換 **GOONS logo**（單一 path + `currentColor`，色綁 `--ink` → 深淺自動替換）。
- 主題切換鈕改 **toggle switch**（軌道＋滑動 knob，深色右滑、太陽/月亮）。
- **scrollbar**：最終改**純標準屬性**（`scrollbar-width:thin` + `scrollbar-color`，會被繼承，側邊欄與整頁自動一致）——不再混用 `::-webkit-scrollbar`（與標準並存會讓 Chrome 對 root/元素捲軸走不同渲染、一粗一細）。無底色、深淺分色（淺 `#C4C9D3` / 深 `#3D4553`）。

## 2026-07-14　匯出需求頁（原「複雜度確認」）大改

- 頁名改 **匯出需求**（pin、eyebrow、h1）；設為**預設首頁**、**預設展開第一張**卡片。
- 元件牌卡可**展開／收合**；展開顯示該元件在自己頁面上的**實際操作 Live**——抽出共用 `LIVE` registry（button/input/dropdown/checkbox/radio/tag/tab），**元件頁與此頁共用同一份**、不 drift。
- 牌卡前加**選取 checkbox**（決定要不要產出，`SUMSEL`）；右下角「**我要調整**」→ 跳該元件頁（checkbox/radio → selector）。
- **「AI 建議產出」toggle**：開→套用建議（已定案 6 顆）並存快照；關→還原點擊前選擇；手動改勾選即脫離 AI 態。
- 「複製並匯出JSON」primary 按鈕移到**頁面右上角**（只匯出已選元件）。
- 每頁底部固定 footer：`© 2026 GOONS DESIGN, all rights reserved. Powered by 最厲害的 Yuu`。

## 2026-07-14　大頁面框架改 app-shell（RWD 基礎修正）

- 從「document 捲動 + sticky」換成 **app-shell**：`.shell` 固定 `100vh/100dvh` flex 直列、`body{overflow:hidden}`；topbar `flex:0 0 58px`；`.layout flex:1;min-height:0`；`.sidebar` 與 `.main` 各自 `overflow:auto` 內捲（`min-height/min-width:0`）。
- 側邊欄寬 `max(200px,20vw)`；`.container max-width:none`（內容填滿寬）；main 高度填滿、內容過寬可左右捲。
- 解掉舊的「sticky 側欄比內容矮 → 左欄下方露白」bug（不再需要漸層補底）。`renderView` 改捲 `.main`。

## 2026-07-14　Basic 排序重整 + Tab 家族頁

- **Basic 18 項改依「心智模型」排序**（原本前 6 依建置狀態、之後無邏輯）：動作(button) → 表單輸入(input/dropdown/selector) → 導覽(tab/menu/action-menu/breadcrumb/pagination) → 標示內容(tag/icon/price/image) → 回饋(alert/notification/tooltip) → 載入(skeleton/loader)。順序講「使用者主動操作 → 系統被動回饋」的故事。tab 從第 6 移入導覽群、tag 從第 5 移入標示群。
- **Tab 擴成家族頁**（tab + tab-item + toggle），狀態改 `ready`：
  - 基本 tab：三 variant 併成 **State × Variant 對照表**（列＝狀態、欄＝底線／容器／藥丸並排）+ 轉盤 + Live（內容開關 icon／count，`LIVE.tab` 共用到匯出需求頁）。
  - **size 軸 m/s**：加入 `.tabi.sz-s`（h30／padding12／font12.5，count/icon 同步縮小）；DIAL_SPECS.tab 加 size 軸，公式 `3 × 4 × 2`；轉盤勾 size → 每 size 各出一張對照表（標準 m／小 s），Live 加 size seg。
  - **contained 圓角改 999**：`.v-contained .tabi` border-radius 8 → 999（帶邊框的藥丸，仍與 pill 的無邊框灰底容器區隔）。
  - **tab-item 原子**：`tabItemEl()` 支援 leading icon（TAB_ICONS 池：house/list/gear）、count badge（`.tcount`，選中態轉 accent 底）、size m/s；四種內容組合示意。（可關閉 × 已依需求移除）。
  - **toggle 開關**：新增 `.switch` 樣式 + `switchEl()`；關／開／停用(關)／停用(開) 四態 + 可點擊 Live。

## 2026-07-14　回饋+載入 5 頁（平行 workflow 首批）

用平行 workflow 一次產 5 顆元件頁（每顆一個 agent 產 self-contained `buildXxx` + namespace CSS），主控端序列整合。狀態設 `partial`（已建、待 Yuu 定案軸）。

- **alert**（`.al-`）：intent(success/warning/error/info) × layout(banner/stacked/compact)=12；白底 inline-card + intent 圖示 + accent line（banner/compact 左直條、stacked 頂橫條）+ brand CTA + dismiss ×。feature：icon/title/action/dismissible。
- **tooltip**（`.tt-`）：placement(top/bottom/left/right) × size(s/m)=8；solid 深底白字（`--ink`/`--canvas` 反相、免 feedback hex）+ rotate 方塊箭頭；Live 真 hover 浮出。feature：with-arrow / placement-auto（行為旗標僅文案示意）。
- **notification**（`.nt-`）：variant(solid/outline/soft) × intent 4=12 toast 卡 + Position 三停靠小螢幕示意；auto-dismiss 進度條 CSS 動畫、hover 暫停。feature：close/icon/action/auto-dismiss。
- **skeleton**（`.sk-`）：shape(text/rect/circle) × size(s/m/l)=9；純中性 `--panel-3` 佔位；shimmer/pulse/none 動畫對照 + 組合範例（頭像卡）；`prefers-reduced-motion` 停動畫。
- **loader**（`.ld-`）：variant(circular/dots/bar) × size=9 + intent(neutral/primary) 對照；keyframes `ld-spin/ld-bounce/ld-bar`；feature：label/inline/full-screen/progress。

**整合方式**：CSS 插 `</style>` 前、`buildXxx` 插 `const BUILD=` 前、擴充 BUILD map；撞名稽核（僅註解內 token 路徑誤判、零實際撞名）；整檔 `node --check` + 5 頁 Playwright render 驗證。
**待辦**：success/warning/info 回饋色框架尚無 token，暫用固定 hex（CSS 標 TODO）；這 5 頁尚未接進「匯出需求」中央轉盤（各自 local 軸），待定案後再決定是否納入。

## 2026-07-14　狀態定案 + notification 接入 summary + 刪除 alert

- **notification/tooltip/skeleton/loader 狀態 partial→ready**（NAV 綠點）。
- **notification 接入匯出需求頁**：`NT_IC_*`/`NT_ICON`/`NT_TITLE`/`NT_MSG`/`dismiss`（改名 `ntDismiss`）/`ntEl` 提升頂層、註冊 `LIVE.notification`；buildNotification 改接 `DIAL_SPECS.notification`＋`CHOICE`；加 DIAL_SPECS/DIAL_PAGE 條目。summary 卡片依側邊欄序出現在 tag 之後（4×4=16）。ntRectEl + rect 區塊保留在頁面（不進 summary Live）。
- **刪除 alert 元件**（Yuu：其他元件已能涵蓋其功能）：移除 NAV 項、`.al-*` CSS、`buildAlert` 函式、BUILD map 的 `alert:buildAlert`。Basic 由 18→17；alert 本就不在 DIAL_SPECS，summary 無殘留。

## 2026-07-14　notification 調整（說明文字 flag + auto-dismiss 簡化）

- **說明文字改 feature flag**：`ntEl` 的 `nt-msg` 從固定顯示改成 `opts.desc!==false` 條件顯示（預設仍顯示，矩陣外觀不變）；Live 加 `說明文字` bool（with-icon 之後）。關掉只留標題。features 說明同步補上。
- **auto-dismiss 拿掉倒數進度條**：移除 `.nt-bar`／`@keyframes nt-count`／`restartBar` 與 hover 暫停；改成 `setTimeout` 3 秒後直接 `dismiss` 淡出（示意用復原後重新計時）。注意坑：`arm()` 在 `ntEl` 建構時呼叫，此時 toast 尚未 append，`isConnected` 為 false → isConnected 檢查要移到 timer **觸發時**、不能在設定時。
- **info icon 改實心**：`NT_IC_INFO` 從 Phosphor outline（空心環）換成 fill 實心版，與 success／warning／error 三個 fill icon 一致。
- **加第 4 個 variant `dark`（半透明黑）**：`.nt-v-dark` frosted 半透明黑底（`rgba(22,24,30,.82)` + backdrop blur）、白字、icon 保留 intent 語意色、白 ×。矩陣 3×4→**4×4=16**；軸/zh/VN/Live seg/lede/formula 同步更新。
- **加 Rect Toast 方形（獨立區塊）**：對齊 Yuu Figma guideline 的 `toast/rect_success`（置中方形：圓 icon badge 在上、文字在下、白底圓角 8、toast 陰影 w124）。icon badge 32px（內 svg 18px）、文字 14px 一般內文。卡片固定 124×124 方形；互動改用一致的 `.chk` checkbox「自動輪播」，勾選後每 3 秒切換一種 intent（`setInterval`，離開頁面自動停），取消清空。`ntRectEl(intent)` + `.nt-rect`；intent 配色沿用本頁 DS 色（非 Figma 橘）。靜態四意圖預覽 + 互動（選 intent → 顯示 toast → `requestAnimationFrame` 彈入、3 秒 `setTimeout` 自動移除）。獨立於上方 banner 矩陣，屬另一個 layout 格式。

## 2026-07-14　tooltip/skeleton/loader 接入匯出需求頁

- **接中央 store**：把 tooltip/skeleton/loader 三顆從「自包含 local 軸」改成接 `DIAL_SPECS`＋`CHOICE`（與既有 8 顆一致），summary 才能顯示軸 chips＋計數。alert/notification 因 Yuu 還要調整，**維持自包含、不進 summary**。
- **builder 提升頂層**：tooltip 的 `TT_INFO/ttEl/ttTrigger`、loader 的 `loaderEl` 原本 nested 在 buildFn 內 → 提升到頂層，並註冊 `LIVE.tooltip/skeleton/loader`（skeleton 的 `skelEl` 等本就在頂層）；summary 卡展開即用 `LIVE[id]` 掛實際操作。頁面 `mountLive` 改呼叫 `LIVE.xxx(sl)`（單一來源、免漂移）。
- **summary 卡片改依側邊欄順序**：`renderSummary` 不再照 `Object.keys(DIAL_SPECS)`，改用 `_navOrder`（NAV flatMap）＋ `DIAL_PAGE` 反查排序（checkbox/radio 同映射 selector、穩定排序保留原序）。實測序＝button→input→dropdown→checkbox→radio→tab→tag→tooltip→skeleton→loader。
- 轉換用 tokenizer-aware brace matcher（跳過字串/樣板/註解）精準替換三個函式，`node --check` + Playwright 驗證 10 張卡 Live 全掛、alert/notification 未進。

## 2026-07-15　上線 GitHub + Vercel／導覽 4 顆（平行 workflow 第二批）+ review 修正

**上線基礎建設**：本資料夾接 GitHub **public** repo `edward890131/goons-ds-library`（push 全歷史）＋ **Vercel** 部署（綁 repo、push 自動重新部署）：**https://goons-ds-library.vercel.app** 。`vercel.json` 用 rewrite 把根網址 `/` 導到 `ds-component-gallery.html`；README 補專案說明＋線上連結。

**平行建 4 顆導覽元件**（做法同首批：平行產碼＋序列整合；4 個 agent 各讀同檔 helper 合約＋golden 範例 buildTooltip、依 L0 軸線設計，回傳 namespaced CSS＋`buildXxx`＋`DIAL_SPECS/DIAL_PAGE/BUILD/LIVE` 字串，主控端序列插入）：
- **Menu 選單**（`mn-`）：常駐導航容器。variant(horizontal/vertical) × state(default/hover/active/disabled/focus) × size(s/m/l)＝**2×5×3=30**。State×Size 矩陣＋Orientation（navbar 橫／sidebar 直，active 指示）＋Live 點項切 active。
- **Action Menu 動作選單**（`am-`）：trigger 觸發浮出的動作清單（≠ dropdown 表單選值、≠ menu 常駐導航）。variant(default/with-icon/with-divider) × state(closed/open/hover/focused/disabled) × size＝**3×5×3=45**。Variant×Size 矩陣（展開面板）＋Item States 排＋Live 可開合。
- **Breadcrumb 麵包屑**（`bc-`）：階層路徑。L0 原生 variant/size 皆空、state=default/hover/current。**gallery 端補一個 separator 軸**（chevron/slash/arrow）讓它能進總覽——已在 lede 明確註記此為陳列頁擴充、非 L0 定義（呼應「未來以 library 為主體」的方向）。States 排＋分隔符×(標準/省略) 矩陣＋Live 切分隔符/省略/層數。
- **Pagination 分頁器**（`pg-`）：variant(numbered/prev-next) × state(default/hover/active/disabled) × size＝**2×4×3=24**。單顆頁碼鈕 State×Size 矩陣＋numbered/prev-next 並排＋Live 點頁碼真的移動 current。
- 4 顆 NAV 狀態設 **ready**；全數接中央 store、進「複雜度確認」總覽頁，序＝…tab／menu／action-menu／breadcrumb／pagination／tag…（依側邊欄由上至下）。

**Yuu review 修正（4 項，皆以 computed style 屬性查驗通過）**：
- **Menu**：focus 外框 `2px→1px`；垂直 sidebar 的 active 左側指示線段移除（`.mn-bar--vertical .mn-item--active .mn-ind{display:none}`，保留 `--i-primary-tint` 底當指示）。
- **Pagination**：非 active 頁碼鈕去外框（base `border` 改 transparent、hover 只留底色不加框）；current 仍 `--i-primary` 實心。達成「直接呈現數字」。
- **Breadcrumb**：hover 去底線、只變色（移除 `text-decoration:underline`）。
- **Action Menu**：修正浮出面板在矩陣格子內被壓成單列高的 bug（見踩坑）。後續延伸修 Live 區同坑（根因統一到 base `.am-panel{min-height:max-content}`、Live 面板改順流排 trigger 下方）。
- **Action Menu（trigger 統一）**：Live 的「動作」trigger 原本是自訂 `.am-trigger`（solid 底卻配 outline 文字色的混搭），改成直接沿用 Button 元件 class `.btn v-solid i-secondary sz-m live`（次要實心），computed 樣式與按鈕頁 100% 一致（bg/color/height/radius/border/font/padding/gap 全對）；僅保留 `.am-trig .am-tcaret` 處理 caret 展開翻轉。
- **Action Menu（Live 互動）**：Live 展開後不再預設第一項 focused、全部 default；新增 scoped `.am-live-wrap .am-item:hover`（含 danger 項走 danger-tint）讓**真滑鼠 hover** 能看到 hover 效果（實測 hover 背景＝`--panel-2`）。矩陣仍用 class 版 `.st-hover` 靜態展示、不受影響。
- **匯出需求頁**：移除「第一張牌卡預設展開」的設定，改成**全部預設收合**（`sumcard` 一律加 `collapsed`、移除 i===0 立即掛 Live 的分支）；Live 仍由展開時 lazy 掛載。

## 2026-07-15　媒體/標示 3 顆（icon / price / image，平行 workflow 第三批）

**平行建**（做法同前批：3 個 agent 依 L0 軸線各產 self-contained `buildXxx`＋namespaced CSS，主控端序列整合、接中央 store、進總覽頁）：
- **Icon 圖示**（`ic-`）：採 **Phosphor**（Yuu 指定的開源 set，單一 weight、fill-based、viewBox 256）。L0 的 variant（line/solid/duocolor/duotone，源自 Untitled UI）**不採用**、改以 Phosphor 現實陳列並在 lede 註記落差。軸＝`size × tone`＝**4×4=16**（size s/m/l/xl＝16/20/24/32 px 為 L0 原生；**tone 為陳列頁擴充軸**，對應 `semantic.icon.*`）。含 Size×Tone 矩陣＋Icon Library grid（20 顆：既有 `ICON_*` 15＋自訂 heart/star/bell/trash/user）＋Usage（decorative/semantic、行內對齊、in-button）＋Live。
- **Price 價格**（`pr-`）：variant(normal/discount/sale/free) × size(sm/md/lg)＝**4×3=12**。discount 用真 `<s>` 包原價（`line-through`＋`aria-label` 讓報讀「原價 X、優惠價 Y」）、sale 帶 SALE badge、free 標「免費」。矩陣＋情境示意＋Live。
- **Image 圖片**（`im-`）：variant(rectangular/rounded/circular 形狀) × state(default/loading/error) × size(s/m/l/xl)。**素材統一用 Unsplash**（`images.unsplash.com` CDN，10 個 ID 事前 curl 驗證 200；circular 用人像圖）。default=真圖 object-fit cover、loading=shimmer 骨架、error=破圖 icon＋「載入失敗」。Shape×State 矩陣（實測 8 張圖全載入 400×400）＋Size 列＋Live。**註**：image 是唯一允許外部資源（Unsplash CDN）的元件，非嚴格自包含，但 CDN 圖在 Vercel/本機皆正常。
- 3 顆 NAV 設 **ready**；序＝…pagination／**icon／price／image**／notification…（依側邊欄）。

## 2026-07-15　icon 換官方 Phosphor regular 全集 + image 換美圖／加長寬比

**Icon**：Yuu 反映 gear/heart 有立體感、跟線框不搭（實際是 house/x-circle/sparkle 為 fill 實心、整組 weight 混雜）。改用**官方 Phosphor regular（線框）**：curl `phosphor-icons/core` 的 `assets/regular/*.svg`、事前驗證 200，抓 **38 顆常見 dashboard icon** 重建 Icon Library（house/dashboard/settings/user/users/bell/search/calendar/clock/chart-bar/trending/pie/folder/file/mail/download/upload/trash/edit/plus/check/close/caret-down/arrow-right/heart/star/eye/tag/filter/sliders/card/money/cart/bookmark/chat/gift/location/logout）。全部單一 regular weight、風格一致。矩陣示意 icon／LIVE glyph／Usage 都改用新 regular（gear→settings/dashboard）。**與其他元件共用的舊 `ICON_*`（menu/input/summary 在用）不動**，library 改用獨立 `IC_*` regular 集、解耦（menu 側邊欄仍用舊 fill icon，之後要統一再議）。

**Image**：① 換掉醜圖 → 8 張 curl 驗證的**漂亮自然風景** Unsplash（山湖／森林／瀑布／綠丘）；circular 改用構圖置中的湖光山色（不再用人像）。② `imUrl` 拿掉 `&h`（不鎖正方），裁切交給盒子 `aspect-ratio` + `object-fit:cover`，圖片 w 400→600。③ **新增長寬比軸 ratio**（`1:1 / 4:3 / 16:9 / 2:1`）：進 `DIAL_SPECS`（image 變 4 軸、summary 顯示 **3×3×4×4=144**）、CSS 加 `.im-r-*` aspect-ratio、`imEl` 加 ratio class（circular 恆正方不套）、新增「Ratio 長寬比」section、LIVE 加 ratio 控制。

**（同日再調）size 改由「高度」驅動**：Yuu 指定 s/m/l/xl 高度＝**40／64／120／240 px**，寬度交給 `aspect-ratio` 依比例算（`.im-sz-*` 從設 width 改設 height、`.im-box` 保留 `aspect-ratio:1` 預設方形）。Size section 標註改高度值；Ratio section 改「固定高度（l＝120）、寬度依比例伸縮」（1:1 120×120、4:3 160×120、16:9 213×120、2:1 240×120）；circular 恆正方。

## 2026-07-15　Tag 加 danger intent + price SALE 統一用 DS Tag

Yuu 發現 price 的 SALE 是價格 agent 自製的 `.pr-badge`（沒用 DS Tag 元件）。因 **alert 已刪**（當初 Tag 不做 feedback 色的理由消失），決定**幫 Tag 元件補 `danger` intent**、SALE 維持紅：
- **Tag**：CSS 加 `.tg.i-danger`（鏡射 i-brand、綁 `--i-danger` token）；`DIAL_SPECS.tag` intent 軸 `brand/neutral → +danger`（矩陣/轉盤/summary 自動長出 danger，18 顆紅 tag；tag 全展 3×2×2→**3×2×3=18**）；`buildTag` 的 colLab 從硬寫 `brand?品牌:中性` 改用 `zh.intent[]` map（否則 danger 誤標中性）；LIVE.tag intent seg 加 danger；lede/formula 更新。
- **Price**：`prEl` 的 sale 從 `<span class="pr-badge">` 改成 DS Tag `<span class="tg v-solid c-round i-danger st-default"><span>SALE</span></span>`；移除 `.pr-badge` CSS、加 `.pr-price .tg{align-self:center}`（在 baseline flex 內置中）。實測 SALE 背景＝`--i-danger`、與 Tag 元件同源。

## 2026-07-15　「複雜度轉盤」改名變數篩選器 + Complex 群組重排

- **改名**：各元件頁的可收合「**複雜度轉盤**」→「**變數篩選器**」（6 處：mountDial 標題、button 頁硬寫標題、summary lede 引用、3 處註解）。**匯出總覽頁「複雜度確認」不動**（不同東西）。
- **Complex 群組重排**：原 15 顆順序無規律，改依**心智模型 5 群**（維持扁平側邊欄、相鄰即群、與 Basic 一致，NAV 內加群註解）：① 版面結構 header／footer／section-head ② 內容容器 card／list／collapse ③ 內容卡片 product-card／category-tile／article-card／announcement ④ 覆蓋／浮動 modal／drawer／floating-line ⑤ 流程／時間 step／calendar。敘事：頁面骨架 → 裝內容的容器 → 特定內容卡 → 浮在上層的覆蓋層 → 流程與時間。summary 卡片依 NAV 順序、自動跟進。

## 2026-07-15　新增「Card 卡片」群 + calendar 移入 Table & Chart

側邊欄從 3 大組 → **4 大組**（共 36 項不變）：
- **新增「Card 卡片」群**（🗂️，放 Basic 之後、Complex 之前）：把 `card` 與內容卡 `product-card／category-tile／article-card／announcement-item` 從 Complex 抽出獨立成群（5 項）。
- **calendar 移入 Table & Chart**（放末尾、跟 schedule-chart 同屬時間類）。
- **Complex 瘦身為 9 項**：header／footer／section-head（版面結構）、list／collapse（容器）、modal／drawer／floating-line（覆蓋/浮動）、step（流程）。
- 群順序＝Basic → Card → Complex → Table & Chart（積木 → 內容卡 → 結構/覆蓋 → 資料）。summary 卡片依 NAV 順序自動跟進（Card/Complex/T&C 尚未建、暫不進總覽）。

## 已知踩坑（避免重犯）
- summary 卡片要「接進匯出需求頁」＝該元件必須進 `DIAL_SPECS`（給軸/計數）＋ 有頂層 `LIVE[id]`（給展開的實際操作）。自包含頁若 Live builder nested 在 buildFn 內，summary 首次載入時 `LIVE[id]` 還沒定義 → 必須把 builder 提升頂層再註冊。
- 平行建「同一個 self-contained 單檔」的多個頁面：不要讓多 agent 同時寫檔（會衝突）。正解＝平行「設計+產碼」（各回傳 namespace 過的 `buildXxx`+CSS 字串）、主控端序列整合。CSS class 一律加元件前綴防撞。
- 本機用 Playwright 驗證含中文的 HTML：`python -m http.server` 要送 `charset=utf-8`，否則瀏覽器誤解碼中文、噴假的 JS 語法錯（`node --check` 過就是 server 的鍋）。`file://` 會被 Playwright 擋。
- SVG 放在 `inline-flex` 容器內、寬度用 `%` 會塌成 0px → icon 尺寸一律用固定 px。
- `var()` 在 `::-webkit-scrollbar-*` 偽元素解析不穩，會掉回瀏覽器預設；捲軸樣式用明確 hex 或純標準 `scrollbar-*` 屬性。
- 尺寸類 class（sz-s/m/l）跨元件共用會撞（按鈕 padding 洩漏到 checkbox）→ 命名或在 base 重置 padding/box-sizing。
- **頂層 `const` 陣列/物件引用「定義在更後面」的 `const` 會 TDZ 崩整份 script**：icon 的 `const IC_LIBRARY=[...ICON_SPARK...]` 在載入時求值，但 `ICON_SPARK`（給 summary 用、定義在檔案較後）當下還在 TDZ → `ReferenceError: Cannot access 'ICON_SPARK' before initialization`，整個 `<script>` 中斷。平行產碼的元件被「插在 BUILD 前」，很容易跨過某些後定義的常數。修法：把這種 top-level 資料改成**延遲函式**（`function icLibrary(){return [...]}`，render 時才求值），或確保被引用的常數定義在前。整合後**務必開 console 看有沒有 error**（node --check 抓不到 TDZ，只有執行時才爆）。
- **`flex-direction:column` 的高瘦卡片巢在別的 flex/grid 容器內，auto min-height 會被算成單列高、內容溢出**（實測 action-menu 面板 `offsetHeight=32/40` 但 `scrollHeight=151`）。`align-self`／`flex:none`／改父層 `align-items`／改順流 都無效，**只有撐 `min-height:max-content` 有效**。此坑**會同時出現在矩陣（`.mcell` 內）與 Live（`.am-live-wrap` flex 容器內）兩處** —— 一開始只 scoped `.mcell` 修矩陣，Live 仍壞；正解是把 `min-height:max-content` 加在 **base `.am-panel`**（一次涵蓋所有掛載位置）。凡「容器裡放 flex-column 卡片」的元件都要在該卡片 base 補這行。（另：Live 的浮出面板也從 `position:absolute` 改順流排在 trigger 下方，框才會跟著長高、開合時 stage 130↔295 伸縮。）

## 2026-07-15　Complex 群序上移 + Complex 9 顆全數建置（平行 agent）

- **群序調整**：把 **Complex 移到 Card 之上** → 側邊欄群序＝**Basic → Complex → Card → Table & Chart**（積木 → 結構/覆蓋 → 內容卡 → 資料）。
- **Complex 9 顆一次建完**（開 9 個並行 agent、各回傳 namespace 過的 `buildXxx`＋CSS＋store 片段，主控端序列整合）：
  - **header**（`hdr-`）：layout(logo-nav/logo-search/centered) × density(comfortable/compact)＝3×2。矩陣每格一整條頁首 bar（logo＋導覽/搜尋＋登入 CTA＋鈴鐺＋頭像）、行動版收合列、Live。自訂 icon `HDR_IC_BELL/LIST`。
  - **footer**（`ftr-`）：variant(rich/simple/minimal)，勾選幾個就渲染幾段完整頁尾。rich＝品牌＋4 欄連結＋電子報訂閱＋社群＋語言 pill。自訂 6 社群/品牌 icon。Live。
  - **section-head**（`sch-`）：align(left/center) × size(m/l)。矩陣＋5 種內容組合（eyebrow/計數 badge/分隔線/filter 群/麵包屑 eyebrow）＋Live。重用 Button/Tag/ICON_ARROW，無自訂 icon。
  - **list**（`lst-`）：variant(simple/detailed/avatar) × state(default/hover/selected/disabled)。矩陣＋整組列表（可多選＋分隔線＋動作鈕）＋Live。重用 `.cbx`/Button/ICON_*。自訂 `LST_IC_CHEVRON`。
  - **collapse**（`cps-`）：variant(bordered/filled/plain) × state(collapsed/expanded/hover/disabled)。矩陣＋真互動手風琴（單開）＋Live（multiple 切多開）。caret 用 ICON_CARET rotate 180。自訂 `CPS_ICON_QUESTION`。
  - **modal**（`mdl-`）：variant(dialog/confirm/form) × size(s/m/l)。矩陣（縮小靜態卡）＋浮層結構示意＋Live（**overlay `position:absolute` 限縮在 stage 內、不遮全頁**，× / 取消 / 點遮罩可關）。重用 Button（含 i-danger 刪除）/field。自訂 `MDL_ICON_WARNING`。
  - **drawer**（`drw-`）：side(left/right) × size(s/m/l)。矩陣（裝置框內滑入抽屜＋backdrop）＋內容結構＋Live（stage 內滑入動畫、close/backdrop 可關）。backdrop 用 `color-mix` 從 token 混。無自訂 icon。
  - **floating-line**（`flo-`）：variant(selection/toolbar/fab) × position(bottom-center/bottom-right)。矩陣（假內容區浮動列）＋型態細節＋Live。selection pill 用 `--ink`底/`--canvas`字（自動反相）。自訂 `FLO_ICON_PLUS/TRASH`。
  - **step**（`stp-`）：orientation(horizontal/vertical) × variant(numbered/dotted/icon)。矩陣（4 步混合狀態 stepper）＋單一 step 狀態列（done/current/todo/error/disabled）＋Live（上一步/下一步互動）。自訂 `STP_ICON_WARN`。
- **驗證**：整合後 `node --check` 過；Playwright 逐頁 `renderView` 9 頁全渲染、**0 console error**；匯出總覽頁 27 卡（18 舊＋9 新）正常；modal Live overlay 實測 `position:absolute` 限縮 stage、側邊欄仍可見。
- **NAV 狀態**：9 顆全設 `st:'ready'`。**Complex 群 9/9 建置完成**。
- **整合手法沿用**：9 agent 各寫 7 個分離純文字檔（css/icons.js/build.js/dialspec/dialpage/buildmap/live）避開 JSON escape；主控端用 `integrate.js` 依 8 個錨點序列插入（CSS 進 `</style>` 前、buildFn 進 `const BUILD=` 前、dialspec 進 DIAL_SPECS、dialpage/buildmap/NAV ready flag）。事前掃描跨檔命名衝突＋未定義 token（皆 0）。

## 2026-07-15　Header 整修：原子重用 + 分類邏輯重構

依 Yuu 6 點回饋改寫 Header：
- **陳列改「上下堆疊」**：原 layout×density 2D 矩陣把整條頁首 bar 擠進窄欄 → 改成 `.hdr-stack` 垂直堆疊，每條 bar 全寬、上方帶 caption，不再擠壓。
- **logo 換 Goons 官方字標**：`hdrLogo` 從「accent 方塊＋文字 Goons」改成**複用檔內 topbar 既有的 Goons wordmark svg**（`apply_header.js` 自動從 `.brand-logo` 抽出、去 class、注入 `HDR_LOGO` 常數，避免手抄長 path）。
- **登入鈕移到 avatar 右側**：`hdrActions` 順序 登入→鈴鐺→頭像　改成　**鈴鐺→頭像→登入**（登入最右）。
- **搜尋與導覽並存（分類邏輯重構）**：原 `layout` 軸 logo-nav／logo-search／centered **互斥** → 拿掉。改成 **導覽（nav）與搜尋（search）都是可並存的內容開關**，bar 版位＝`logo · nav · spacer · search · actions`，兩者可同時出現。
- **行動版/電腦版升格為軸**：新增 `device` 軸（desktop／mobile），取代原本獨立的「行動版收合」section；篩選器與 Live 都可切換。行動版導覽收進漢堡鈕。DIAL_SPECS.header 改 `device × density`（2×2=4）。
- **原子建構原則**：header 內部零件全部改用 library 既有元件——**導覽＝Menu 元件 `mnBar('horizontal')`**（在 header 內用 `.hdr-nav-slot .mn-bar{border:0;background:transparent;padding:0}` de-chrome）、搜尋＝Search／Input `.field v-outline`、漢堡/鈴鐺＝Button icon-only、登入＝`.btn v-solid i-primary`。specStrip 加 `atoms` 欄標註 Button·Icon Button·Menu·Search。
- **驗證**：`node --check` 過；Playwright renderView header **0 console error**、4 條上下堆疊 bar、nav 用 `.mn-item`（Menu 元件）、logo svg、nav+search 並存、actions 順序 bell→avatar→login、mobile 有漢堡，全數確認。
- **新增 feedback memory**：`feedback_ds_atomic_reuse` — 複合元件內部一律取用 library 原子、禁 inline 重畫（Yuu 要求「確保不再犯」）。

## 2026-07-15　Header 微調：移除緊湊密度 + icon btn 補 .live 吃 hover

- **移除「緊湊 compact」**：Header 拿掉 density 軸，DIAL_SPECS.header 改單軸 `device`（desktop／mobile），陳列從 4 條 → 2 條堆疊 bar；Live 移除密度 seg；清掉所有 `.hdr-compact` CSS（0 殘留）。
- **修 icon btn 沒 hover**：Button 元件把真實 `:hover`/`:active` 用 `.live` class 把關（矩陣格子用 `.st-hover`/`.st-pressed` 假狀態、不吃真 hover）。header 的鈴鐺／漢堡／登入鈕原本漏了 `.live` → 全補上（`btn v-text i-secondary sz-s icon-only live` 等），實測 hover 後背景轉 `--i-sec-tint`（`rgba(90,100,116,.1)`）。頭像加 `:hover` inset ring。**釐清**：無底色無外框的純 icon btn 樣式本來就有（`v-text`＋`icon-only`），只是要 `.live` 才有真實 hover。
- **memory**：`feedback_ds_atomic_reuse` 補記「重用 .btn 的互動實例必須加 .live 才吃 hover」。
- 驗證：`node --check` 過、Playwright 0 console error、2 條堆疊 bar、7 顆按鈕全帶 live、`.v-text.live:hover` 規則存在、實測 hover 背景變色。

## 2026-07-15　Button 補完：新增 soft 淺色變體 + Live 加 icon-only 開關

Yuu 檢查 Button 發現兩缺口：
- **Live 沒有 icon-only 選項**：Button 元件本來就支援 icon-only（`mkBtn` 有 `iconOnly`、下方「Content 內容軸」靜態區也展示 `show-label=false (icon-only)`），只是**實際互動 Live 的 Content 勾選漏了**。已補：button 頁 renderLive 的 checks 加 `['iconOnly','icon only']`、stage mkBtn 傳 `iconOnly`、`live` 物件加 `iconOnly:false`；registry `LIVE.button` 的 bools/render 同步加（summary 展開頁也吃到）。
- **缺 soft 淺色變體**：Button 只有 solid/outline/text，沒有 avatar 那種「淡底填色＋彩色字」的 soft 樣式。已新增 **`v-soft`**：CSS `.v-soft{background:var(--tint);color:var(--fg);border-color:transparent}` + hover/pressed/live 用 `--tint2`；`AXES.variant`＝`['solid','soft','outline','text']`（矩陣/變數篩選器/summary 自動長出 soft 欄，button 全展 3→**4 variant**，FULL 108→**144**、公式 `4×3×4×3`）；`ZH.variant` 加 `soft:'柔色'`、`VDESC` 加 soft 說明；LIVE.button 與 renderLive 的 free 對照列加 soft、note 更新。
- **驗證**：`node --check` 過、Playwright 0 console error；矩陣 4 variant panel（實心/柔色/外框/文字）、Live Content 含 icon only（勾選後 stage 按鈕帶 `icon-only`）、soft primary 實測 bg=`rgba(51,88,212,.1)`/color=`rgb(51,88,212)`、公式顯示 144。

## 2026-07-15　統一：header avatar 改走 Button v-soft 原子

延續 soft 變體，把 header 頭像從自製淺色圓形元素改成**複用 Button `v-soft` 原子**：
- `hdrAvatar` 從 `<div class="hdr-avatar">` 改成 `<button class="btn v-soft i-primary sz-s icon-only live hdr-avatar">楊</button>`。
- CSS 移除自製的 `.hdr-avatar{background/color/...}` 與 `:hover` ring，只留 `.hdr-avatar.btn{border-radius:50%}` 把 32px 方鈕覆蓋成圓形；底色/文字色/尺寸/hover 全部繼承 Button v-soft（`--tint`/`--fg`，hover→`--tint2`）。
- 掃描確認：其餘 tint 背景（menu active、list selected、tab/toggle on、action-menu focus 等）都是**元件內部狀態色**、非獨立 soft 按鈕，維持原樣不動；複合元件內沒有其他自製 soft 鈕。
- 驗證：`node --check` 過、Playwright 0 console error；avatar tag=BUTTON、class 含 `btn v-soft`、圓形 radius 50%、bg=`rgba(51,88,212,.1)`、hover→`rgba(51,88,212,.2)`、文字「楊」，外觀與改前一致但多了真實 hover。

## 2026-07-15　統一 gear icon：舊 ICON_GEAR → 新版 settings（IC_SETTINGS）

Yuu 發現 tab／menu 等元件仍用「已移除的舊 gear」。查出檔內有兩顆 gear：舊 `ICON_GEAR`（line 1983，被 6 顆元件共用）與 icon library 重建後的新版 `IC_SETTINGS`（Phosphor regular，兩者 path 微異，`107.6`→`107.21`）。
- **做法**：把 `ICON_GEAR` 的 svg 內容替換成 `IC_SETTINGS` 的 glyph（保留 const 名，單一 literal 替換）。**不直接 `const ICON_GEAR=IC_SETTINGS`**，因 `TAB_ICONS`／`MN_ICONS` 等 top-level const 在 IC_SETTINGS 定義之前求值、會 TDZ。改一處即讓所有共用處同步。
- **波及並全部更新**：tab（TAB_ICONS）、menu（MN_ICONS）、list（LST_LEAD_ICONS＋動作鈕）、drawer（DRW_NAV「系統設定」）、floating-line（FLO_TOOLS）、step（STP_ICONS＋「完成設定」）共 6 顆。
- **驗證**：`node --check` 過；`ICON_GEAR===IC_SETTINGS` glyph；Playwright renderView 6 顆全渲染新版 gear（尾段特徵 `199.87` 有、舊 `199.9` 無）、0 console error。
- **新規則 memory**：`feedback_ds_crosscomponent_confirm` — 改動會牽動其他元件/頁面時，先回報波及清單並問 Yuu 要不要一併統一調整。

## 2026-07-15　Icon Library 擴充到 60 顆、依產品功能分 6 類

Yuu 要求針對旅遊／生活／工作／購物／社群等常見產品功能，把 icon 補到 60 顆。
- **+22 顆新 Phosphor regular icon**（官方 `phosphor-icons/core` curl 驗證 200、去 xmlns 正規化成 `<svg viewBox="0 0 256 256" fill=currentColor>`）：airplane／suitcase／map／globe／bed／camera／coffee／sun／music／food(fork-knife)／briefcase／note／paperclip／send(paper-plane-tilt)／bag(shopping-bag)／wallet／receipt／store(storefront)／truck／percent／share／thumbs-up。新 `IC_*` const 插在 `IC_LOGOUT` 後（在 `icLibrary()` 之前定義、無 TDZ）。
- **icLibrary() 從扁平 38 顆改成分組 60 顆**：通用介面 12／工作 10／資料操作 8／購物 11／社群溝通 9／旅遊生活 10。回傳結構從 `[[name,svg]]` 改成 `[[群名,[[name,svg]…]]]`。
- **buildIcon Library 區改分組陳列**：每類一個 `.ic-lib-group`（標題 `.ic-lib-cap` 顯示群名＋數量）+ 一個 `.ic-lib` grid；section 副標更新為「共 60 顆、6 類、涵蓋旅遊/生活/工作/購物/社群」。
- **範圍**：只動 Icon 頁自己的 `IC_*` 與 `icLibrary()`／`buildIcon`，**不碰其他元件共用的 `ICON_*`**，自包含。
- **驗證**：`node --check` 過；Playwright renderView icon → 6 群、60 cell、0 空格（全有 svg path）、新 icon 全在、0 console error。

## 2026-07-15　Footer 大改：全寬版面 + 原子重用 + 聯絡資訊 + 社群進 Icon 頁

依 Yuu 8 點回饋改寫 Footer：
- **版面全寬對齊**：`.ftr-main`/`.ftr-simplerow`/`.ftr-minirow` 都 `width:100%`；連結欄群 `.ftr-cols` 改 `justify-content:flex-end`（靠右）；simple 用 `justify-content:space-between`（logo 左、連結右）。
- **分隔線撐滿寬度**：`.ftr-bottom` 用負邊距 breakout（`margin:28px -32px 0;padding:20px 32px 0`）+ 容器 `overflow:hidden`，border-top 從卡片左緣延伸到右緣。
- **版權與按鈕左右撐滿**：底列 = 版權（左）+ `.ftr-botspacer`（flex:1）+ `.ftr-actions`（社群／語言／回到頂部，右）。
- **移除電子報功能組**：刪 `ftrNews()`、`.ftr-news*` CSS、Live 的 newsletter bool。
- **新增「回到頂部」按鈕篩選項**：`ftrBackTop()` = `.btn v-outline i-secondary sz-s live` + 上箭頭（`ICON_CARET` rotate 180）；Live 加 `backTop` bool、rich/simple/minimal 底列都可帶。
- **社群 icon 修正（2 項）**：① `ftrSocial()` 從自製 `.ftr-soc` 錨點改成 **library Button**（`.btn v-text i-secondary sz-s icon-only live`）；② 4 顆品牌社群 icon（Twitter/GitHub/LinkedIn/Instagram）**收進 Icon Library** 新增「品牌社群 Brand」分類，Icon 頁從 60 → **65 顆**（另把 phone 加進「社群溝通」群、供聯絡資訊用）。
- **logo 與 header 同一 svg**：`ftrLogo()` 從自製 cube mark + 文字改成複用 header 的 `HDR_LOGO`（Goons 官方字標）。
- **多欄版加聯絡資訊**：`ftrContact()` = 地址／信箱／電話三列，各帶 library icon（`IC_LOCATION`／`IC_MAIL`／新增 `IC_PHONE`）。
- **驗證**：`node --check` 過、Playwright 0 console error；logo=字標（viewBox 209 64）、社群/回到頂部皆 `.btn`、聯絡 3 列文字正確、電子報 0 殘留、分隔線 `margin-left:-32px`、Icon 頁 7 群 65 cell 含品牌社群。三變體截圖確認版面。

## 2026-07-15　Footer 二修：修 display 撐滿 bug + 移除資源欄/簡介 + 語言改 library Button

- **底列分隔線與按鈕群沒撐滿的根因**：全域 `footer{display:flex;flex-wrap:wrap;…}`（第 506 行，給 button 頁的來源 footer 用）覆蓋了 `.ftr-foot`（也是 `<footer>`），使 `.ftr-bottom`（無 width:100%）在 flex 容器內被 shrink-wrap 成 713px、`.ftr-botspacer` 塌成 0。修法：`.ftr-foot` 明確 `display:block;margin-top:0`。改後底列寬 713→**1041**（撐滿卡片）、spacer 0→**322px**（把社群/語言/回到頂部推到最右）、分隔線 breakout 到卡片邊緣。
- **移除「資源 Resources」連結欄**：FTR_COLS 從 4 欄 → 3 欄（產品／公司／法務）。
- **移除 logo 下方簡介**：`ftrBrand` 拿掉 `.ftr-desc`，只留 logo + 聯絡資訊。
- **語言切換改用 library Button**：`ftrLang` 從自製 `.ftr-lang` pill 改成 `.btn v-outline i-secondary sz-s live`（globe＋繁體中文＋caret），與回到頂部同款；移除 `.ftr-lang*` CSS。至此底列 social／language／back-to-top 全部為 library Button 實例。
- **驗證**：`node --check` 過、Playwright 0 console error；`.ftr-foot` display=block、bottom 撐滿 1041、spacer 322、連結欄 3 欄、無 desc、語言與回到頂部 class 皆 `btn v-outline i-secondary sz-s live`。截圖確認。

## 2026-07-15　Footer 加 device（電腦／行動）軸，補手機版

依 Yuu「和 header 一樣補上控制選項」——footer 新增 `device` 軸（desktop／mobile），與 header 一致：
- **DIAL_SPECS.footer** order `['variant','device']`、axes 加 `device:['desktop','mobile']`；變數篩選器與 Live 都能切電腦／行動。
- **手機版設計**：`ftrFooter` 加 `device` 參數，mobile 時 foot 掛 `.ftr-mobile` 並包進 `.ftr-phone`（限寬 390px 手機框）。CSS `.ftr-mobile`（container-based、不靠 viewport media query）把 `.ftr-main`／`.ftr-cols`／`.ftr-simplerow`／`.ftr-bottom`／`.ftr-minirow` 全改單欄堆疊、隱藏 spacer、actions 換行；分隔線 breakout 改 `-20px` 對齊手機 padding。
- **陳列**：renderM 改 variant × device（全勾 3×2＝6 段，標題標「電腦版／行動版」）；Live 加 Device seg（電腦版／行動版）。
- **驗證**：`node --check` 過、Playwright 0 console error；dial 兩軸（Variant／Device）、6 段、`.ftr-phone` 寬 390、mobile main/cols/bottom flex-direction=column、Live segs＝Variant/Device/Content。截圖確認手機版單欄堆疊（logo→聯絡→連結欄縱堆→分隔線→版權→按鈕換行）。

## 2026-07-15　header/footer 行動版統一以 375px 手機框示意

- **footer**：`.ftr-phone` max-width 390 → **375px**。
- **header**：行動版 bar 原本全寬呈現，改包進 `.hdr-phone`（max-width **375px**）手機框，與 footer 一致、和全寬電腦版對比清楚。`hdrBar` mobile 分支改回傳 frame。
- 驗證：`node --check` 過、Playwright 0 console error；header/footer 行動版框實測寬 **375px**。

## 2026-07-15　Complex 五元件平行升級（fable agent）

以 fable 模型開 5 個平行 agent（各產出 anchor-based 套用腳本、自我驗證），主流程依序整合、統一 Playwright 驗收。

- **Price 價格**：新增「數值 Metric」大類，型態分 **價格 / 數值** 兩種。metric 四子型態 plain（純數字）/ delta（趨勢，升綠 `--ok`／降紅 `--i-danger`、箭頭重用 `IC_TRENDING`）/ unit（帶單位）/ label（小標＋大數字）；獨立「數值矩陣」（4 子型態 × sm/md/lg）＋ Live 兩段化。未動 Tag（無 success intent，依「跨元件先問」原則改用 icon＋token 上色）。
- **List 列表**（參考 Figma）：矩陣重構為 **basic 基本列 / two-column 雙欄 / selection 選項卡** × 狀態。新增 selection card（checkbox 版＋radio 版、default/hover/selected/error/disabled 五態＋卡下錯誤訊息＋展開帶 inline 下拉）；整組列表補巢狀可展開子列與明細總計列（總計重用 `.pr-price`）；trailing 標準化。全數重用 `.cbx/.rdo/.tg/.field/.pr-price/.btn` 原子。
- **Collapse 摺疊**：① filled 改整張填色（`--panel-2` 從 head 移到 panel 本體）② 移除互動 hover（刪 `.cps-head-btn:hover` 底色）③ 收合寬度＝展開寬度（panel/acc/live-box 補 `width:100%`）④ 往下展開淡出（`.cps-body-wrap` grid-rows 0fr↔1fr＋opacity，toggle 改切 class）⑤ 指示可切 chevron／加減 icon（新增 `CPS_ICON_PLUS/MINUS`、Live 加 Indicator seg）。
- **Modal 對話框**（參考 Figma）：軸改 `kind(popup/modal) × variant`。popup＝置中小卡（警示圖／記住帳號 checkbox／桌機並排・行動堆疊 footer）；modal＝寬版（header＋可捲動 content max-height 80vh/min 30vh＋右對齊 footer，regular 800／larger 1224px）。**Live 預設即顯示開啟的對話框**，關閉後浮出「重新開啟」鈕。舊 `.mdl-sz-*` class 全清。
- **Drawer 抽屜**：① 修高度（面板 `top/bottom:24px` 在 `min-height` 容器內不自動撐開，補 `height:calc(100% - 48px)` → 272px）② 面板浮動、四邊內縮 24px、圓角 14px（backdrop 仍鋪滿舞台）③ **Live 預設即顯示開啟的抽屜**，保留關閉／重開互動。
- **驗證**：整合後 `node --check` SYNTAX OK、Playwright 0 JS console error（僅 favicon 404）；逐一屬性查驗（drawer inset 24/24/24・高 272、collapse filled=panel-2 底・hover 規則已移除、price 升綠降紅、modal Live 預設開啟・舊 class 0、list 選項卡五態＋原子重用）＋ list/modal/drawer 截圖目視符合 Figma。
- **已知小殘留**：modal 匯出/篩選頁乘積顯示 `2×4=8`（實際組合 4，variant 依 kind 各取兩種），文案已註明；未擅改共用計數邏輯（跨元件改動）。

## 2026-07-15　List 拆分重構：抽出 List Item、雙欄改上下巢狀

依 Yuu 五點回饋修正 list：
- **抽出「List Item 資訊列」獨立元件頁**（Complex 群、List 隔壁）：專門呈現「左 leading（無／圖示／勾選框）＋中 title（可加副描述）＋右 trailing（tag／info／meta／動作鈕／caret／checkbox 可複選）」的單列，每個 slot 都能獨立開關。矩陣 `leading × state`（3×4），Live 每個 slot 一個開關、全部對應樣式。原 list 的「基本列 trailing 組合」整段移來成「Slot 組合」。
- **雙欄改成上下巢狀**：原本誤做成左右兩欄，修正為「母列＋內縮子列」的上下兩層巢狀結構，與原「巢狀可展開列」合併成同一 `nested` variant；左右式 value 列本質是 List Item 的 trailing，不再重複放。DIAL_SPECS.list variant 改 `nested / selection`。
- **Live 每個選項都對應樣式**：mountLive 控制列只渲染一次、不支援依 variant 動態顯示，故把 list 的 Live 拆成兩段 scoped block（雙欄／巢狀、選項卡各一），只放對該類有效的開關，消除空轉勾選。
- **狀態表格已選出現 checkbox**：矩陣 selected 欄，List Item（basic）與 list 的巢狀母列都出現「已勾選 checkbox」當選取指示；非 checkbox-leading 的已選列於右側補已勾選框（`lstRowBasic` 新增 `selCheck`）。
- **註冊**：NAV／DIAL_SPECS／BUILD／DIAL_PAGE 都加 `list-item`；CHOICE 與匯出頁自動涵蓋（皆從 DIAL_SPECS 生成）。Complex 群 9 → 10。
- **驗證**：`node --check` SYNTAX OK、Playwright 0 console error；矩陣已選 checkbox（List Item 3 格、巢狀 1 格）、兩段 scoped Live、逐一互動測試（filled→v-filled、關展開→子列隱藏、關 divider→無分隔線、expand→出現下拉）全部即時反應；截圖確認 List Item 矩陣＋Slot 組合、list 上下巢狀＋兩段 Live。

## 2026-07-15　List Item／List 細修 ＋ Collapse／Modal／Drawer 第二輪（fable 平行 agent）

List Item／List 由主流程直接改；Collapse／Modal／Drawer 開 3 個 fable 平行 agent 產腳本、序列整合。

**List Item 資訊列**
- 矩陣「已選」狀態只有 checkbox leading 適用，圖示／無 leading 的已選改「—（需勾選框）」不適用。
- Slot 動作鈕從 icon-only 改**文字按鈕**（`btn v-text i-primary sz-s`＋文字）。
- Live 拿掉 State seg，改成比照其他頁面的 **disabled 勾選**＋真實 hover；新增 **amount 金額** trailing 開關（重用 `.pr-price`）。
- 已選狀態**移除左側 accent 色條**（`.lst-item.is-selected` 去掉 `box-shadow`），只留淡藍底。

**List 列表**
- 雙欄／巢狀不再分「白底／填色灰底」兩版本，改為**內層子列用灰底圓角區塊**（`.lst-subwrap` 綁 `--panel-2`＋radius）；移除 filled 版 demo 與 Live 的 filled 開關。
- 選項卡 Live 操作區**移除 expand 展開下拉**開關（展開版保留在上方靜態示意）。
- 明細金額能力已同步進 List Item（amount 開關）。

**Collapse 摺疊**（agent）
- 修收合殘留 padding：根因＝`.cps-body` 的 `padding-bottom:16px` 撐出 grid track 最小高度；改 `padding:0 16px`＋`::after{height:16px}`（收合時被 overflow 夾掉），收合面板高度＝header 高、垂直置中。
- **補回實際互動區 hover**（上一輪誤移除）：`.cps-head-btn:hover` 底色回歸，filled 深一階；矩陣靜態 state 不動。

**Modal 對話框**（agent）
- popup 按鈕排列與 device 脫鉤：改 segmented「按鈕排列（並排／垂直堆疊）」＋「文字按鈕」boolean（並排/垂直互斥故用 segment，非三個獨立 boolean——待 Yuu 確認是否改回）。
- 一般版也有 **icon 開關**，警示 vs 一般差異只在**顏色**（一般＝primary、警示＝danger）。
- 大 modal 行動版改 **bottom sheet**（貼底、全寬、上緣圓角、由下滑入、max-height 95%）；device 軸真正造成差異。
- Live 拆 **popup 段／modal 段兩個 scoped block**，各只顯示對該 kind 有效的控制項，消除「一般版勾長內容卷軸卻無差異」的空轉。

**Drawer 抽屜**（agent）
- 矩陣（Side × Size）改用**浮動內縮樣式**（縮小版：內縮 9px＋1px border＝10px、圓角 9px、正確高度、無錯位超框）。
- Live 導覽改 **9 項長清單**，內容溢出可捲（scrollable 236px）。

**驗證**：整合後 `node --check` SYNTAX OK、Playwright 0 console error；逐一屬性＋幾何查驗（collapse 收合高=header、hover 規則在；drawer 矩陣四邊內縮 10px、Live 9 項可捲；modal 兩段 scoped、icon 顏色語意、bottom sheet 貼底全寬圓角；list-item NA 2 格、無左色條、文字按鈕、amount；list 子列灰底、無 filled、選項卡無 expand）＋ drawer 矩陣／modal Live 截圖確認。

## 2026-07-15　Modal popup 按鈕排列改四選一

依 Yuu：popup footer 按鈕排列從「並排／垂直」二選一，擴為 **並排／垂直／單顆主按鈕／單顆次按鈕** 四選一（`mdlPopFoot` 的 `lay.arrange`：row／stack／primary／secondary；單顆走 `.stack` 全寬）。矩陣 popup 欄向、Live「按鈕排列」seg、mdlCard 參數（`stack`→`arrange`）同步。文字按鈕仍為獨立 boolean。驗證：`node --check` OK、Playwright 逐一切換確認 並排=次+主、垂直=主/次堆疊、單顆主=solid、單顆次=outline。

## 2026-07-15　Modal 標準/大版統一內容（只差寬度）

依 Yuu：regular 與 larger 應只差寬度。移除 larger 專屬的兩個示範輸入框（收件人姓名／配送備註），並把 `MDL_CONTENT.modal.larger` 內容改成與 regular 完全一致（標題／副標題／footer 皆同）。兩版現在只差 max-width（regular 800px、larger ≤1224px）。`.mdl-fields` CSS 保留但已無引用。驗證：node --check OK、Playwright 兩版標題／段落數／輸入框數一致、寬度 regular<larger。

## 2026-07-15　List 對齊/寬度 ＋ Drawer/Floating-line 修 ＋ 新增 Quantity/Progress

List/List Item、Quantity、Progress 由主流程直接做；Drawer、Floating-line 開 fable agent 修。

**List／List Item**
- 實際互動區寬度統一 **300px**（list 巢狀群組、list-item 單列）。
- 巢狀子列 padding-left 18→**30px**，補足「母列 checkbox(16)＋gap(12)」寬度，讓子標題與母標題**左緣對齊**（實測差 0）。

**Drawer**（agent）
- header 分隔線內縮根因＝面板 `sz-s/m/l` class **撞到 Button 裸 `.sz-*` padding 規則**（padding 漏進面板）。修法：`.drw-live-panel`／`.drw-panel` 補 `padding:0`。Live headW 298／panelW 300、矩陣 6 格同修，分隔線填滿寬度。
- ⚠️ agent 提醒：`.am-panel.sz-*` 等其他掛 `sz-*` 的非按鈕元素可能有同樣 padding 漏染——待 Yuu 決定是否統一處理。

**Floating-line**（agent）
- 矩陣爆版根因＝`.flo-stage` 在 flex 置中格內 intrinsic 寬度趨近 0、`.flo-toolbar`/`.flo-select` 缺 `display:flex` → 直立堆疊。修法：`.flo-stage` 加 `width:100%`、toolbar/select 補 inline-flex、mincol 320→360。
- selection 爆版：圓角 999px→**10px**、padding 調整、按鈕統一 `.btn sz-s` 等高、細節欄改整列寬。FAB 展開加 `.flo-tall` 防裁切。

**新增 Quantity 商品計數**（Complex）
- 電商數量選擇器 −／值／＋，variant 外框／膠囊／極簡 × s/m/l；狀態 default／min(−停用)／max(＋停用)／disabled；＋／− 重用 Button 原子（icon-only）。Live 範圍 1–10 邊界自動停用。

**新增 Progress 進度條**（Complex）
- 線性進度 track＋fill，variant 基本／帶百分比／不確定(animated) × 細/標準/粗；語意色 primary(--accent)／success(--ok)／warning(--warn)／danger(--i-danger)。Live 滑桿控 0–100%。

**註冊**：NAV／DIAL_SPECS／BUILD／DIAL_PAGE 加 quantity、progress；Complex 群 10→**12**。
**驗證**：node --check OK、Playwright 0 console error；list 寬 300／巢狀對齊差 0、drawer 分隔線 298/300、floating-line selection 10px flex、quantity 用 .btn 原子、progress success 綠(46,158,107)＋四色語意；quantity／progress／floating-line 截圖確認。

---

## 2026-07-15　RWD 修正 ＋ Floating-line/Progress/List 再修

手機開網址顯示電腦版縮放、floating 子項灰底不明顯、progress 缺漸層、list 巢狀仍未內縮 → 一次修。全部由主流程直接做（無 agent）。

**RWD（全域）**
- 根因：`<head>` 缺 `<meta name="viewport">`，手機 Safari 以 ~980px 桌面寬渲染 → `@media(max-width:900px)` 從不觸發，看到的是桌面版縮放。
- 修法：補 `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">`。既有響應式殼（漢堡＋抽屜側欄＋scrim＋窄螢幕自動收）本就完整，加一行即生效。
- 驗證：390px 下 `docScrollW==viewportW`（無橫向捲）、漢堡 display:flex、側欄 translateX(-300) 收起；progress／list／匯出頁橫向溢出皆 0。

**Floating-line — FAB 展開子項**
- 「勾選坐進按鈕點擊」＋「白底 library 樣式」：原本「灰底文字 pill ＋ 圓形 mini 鈕」兩件拆開 → 合成**單顆白底膠囊按鈕**（icon＋文字整顆可點，`.flo-fab-child-btn`）。白底＋1px 外框＋陰影，明顯浮於灰底。移除 `.flo-fab-lb`／`.flo-fab-mini`。

**Progress**
- **移除 indeterminate**（不確定循環動畫）variant 與相關 CSS/keyframes。
- **新增 gradient 漸層** variant：每語意色各自 `base→淺色` 專屬漸層（`color-mix`），矩陣一列、Live 一個勾選項；prgEl 重構成 label／gradient 可用旗標獨立疊加。
- **粗(l) 尺寸**標題與百分比字級一併放大（cap 13→15、pct 12→14）。
- **Live 進度條寬度固定為互動區 50%**（實測 452/903＝0.50）；**滑桿移到勾選項右側**同一列（改自訂 Live 手刻控制列，非 mountLive）。

**List／List Item**
- 巢狀**再修內縮**：改由**整塊灰底 `.lst-subwrap` margin-left:28px**（＝母列 checkbox.sz-s 16＋gap 12）內縮，子列還原預設 padding；子標題左緣＝28+14=42＝母標題（pad14＋cbx16＋gap12），**實測對齊差 0**、灰底又明顯內縮。（先前假設 checkbox 20px 算成 32→差 4px，改 28 修正。）
- 實際互動區寬度 300→**600px**（list 巢狀群組、list-item 單列、selection Live 一併）。

**驗證**：node --check OK、Playwright 0 console error；RWD 390px 無橫向溢出、progress 漸層四色 render＋l 字級 15/14＋bar 50%＋滑桿在勾選右、floating 子項白底(255,255,255) pill、list 巢狀對齊差 0／寬 600。截圖：mobile 首頁、floating 展開、progress 矩陣、list 巢狀。

---

## 2026-07-15　Price→Number 改名＋metric 可組合 ＋ progress 字級 ＋ FAB 點擊展開 ＋ 檢視記憶

**Price → Number 數值**
- 頁面／nav／DIAL label 顯示改「Number 數值」（內部 id 仍 `price`、class 仍 `pr-*` 不動，避免連鎖破壞）；eyebrow → `L3 Component · Number`。umbrella 概念：price 與 metric 都是「數值」。
- **儀表數值 metric 改可組合式**：`prMetricEl(size,o)`（原 `(mvariant,size,opts)` 四選一），基底大數字＋`o.title/o.unit/o.trend` 三者正交、可任意疊加。Live 控制改：Size seg＋Trend seg（無／上升／下降，「無」＝隱藏）＋顯示欄位 checkbox（標題／單位），三者皆可開關；矩陣改「單一欄位示意」列（純數字／＋趨勢／＋單位／＋標題）。

**Progress — m／l 百分比數字放大**
- `.prg-sz-m .prg-pct` 12→**15px**、`.prg-sz-l .prg-pct` 14→**19px**（s 維持 12）。

**Floating-line — FAB Live 可點擊展開**
- `floFab` 加 `o.interactive`：子項預建、預設隱藏，**點主鈕即時展開／收合**（＋ 旋轉 45° 成 ×，`.flo-fab.is-open svg`）。Live fab 一律 interactive，「展開子項」勾選改為設定初始狀態。

**檢視記憶（重整不跳回匯出頁）＋ Basic 不預設展開**
- 加 URL hash 路由：`renderView` 寫 `location.hash`、init 用 `hashId()` 還原（無效／無 hash → summary）、`hashchange` 監聽支援深連結／上一頁。
- 側邊欄群組改「只展開當前檢視所在群組」（原本 `gi>0` 收合＝Basic 永遠展開）；summary 或其他群組時 Basic 收合。

**驗證**：node --check OK、Playwright 0 console error。Number title/nav=「Number 數值」、metric 三欄位可同時出現（本月營收／8,542 筆／↗+12.5%）、Trend「無」→ 趨勢消失；progress pct s/m/l=12/15/19；FAB 點主鈕 0→3 子項＋is-open、再點→0；重整 #floating-line 停留原頁＋僅 Complex 展開、fresh 載入→summary＋四群全收合。截圖：Number metric 組合。
