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
