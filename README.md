# Goons DS Library

果思設計（Goons Design）的**設計系統元件陳列頁** — 一份自包含（self-contained）的視覺化網站，攤開每顆元件的**狀態 / 樣式 / 尺寸**矩陣，並提供「複雜度轉盤」與「匯出需求」頁，用來在進入 Figma 建檔前對元件範圍做**篩選與控管**。

## 檔案

| 檔案 | 用途 |
|---|---|
| `ds-component-gallery.html` | 主體，單一自包含 HTML（所有樣式內嵌，可直接雙擊開啟） |
| `CHANGELOG.md` | 逐日變更紀錄 |
| `vercel.json` | 讓根網址 `/` 直接顯示元件陳列頁 |

## 本機預覽

因含中文，需以 UTF-8 提供，避免瀏覽器誤判編碼：

```bash
python3 -m http.server 8791 --bind 127.0.0.1
# 開 http://127.0.0.1:8791/ds-component-gallery.html
```

或直接雙擊 `ds-component-gallery.html`（已含 `<meta charset="utf-8">`）。

## 分類

元件分三大組陳列：**Basic 基本 / Complex 複雜 / Table & Chart 表格圖表**。狀態以彩色圓點標示：已定案（綠）／部分（琥珀）／待建（灰）。

---

線上版本部署於 Vercel。
