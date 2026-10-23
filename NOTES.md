# QuickstartX 交接說明 / Handoff notes

最後更新：2026-10-23　作者：布滷斯

## 1. 這是什麼
單一 HTML 檔的量測路徑規劃器，產生 Blum Quickstart 的 Fanuc G65 P9700 / P9703 量測 NC 程式，並可載入機台 DPRNT 回傳結果，輸出 CSV 與 PDF 報表。目前這個倉庫的 `index.html` 就是**觸控版**（iPad + 桌機）。

## 2. 三個版本（不要混在一起）
| 版本 | 說明 | 預設是否修改 |
|---|---|---|
| 觸控版 | iPad + 桌機，品牌 QuickstartX，紫色配色 | **是（沒特別指定就只改它）** |
| 進階版 | 觸控版 + 「XY 移動改用 G0 快速移動」選項（參數設置頁，勾選前需確認警語）。檔案 `advanced.html` | 只有使用者說「進階版」才改（共通功能兩版都要改） |
| Studio 版 | 非觸控重新設計版，舊品牌名稱 | 只有使用者說「Studio 版」才改 |
| 原版 | 最早的中英版 | 只有使用者說「原版」才改 |

Claude 上發布的頁面（舊帳號私有，需用 Share 分享或重新發布）：
- 觸控版：https://claude.ai/artifact/HHU72cq8jzMaJpDMW2n7tb
- 進階版：https://claude.ai/artifact/HLzEES8n94CPsV2UTA3hNg
- Studio 版：https://claude.ai/artifact/7vVrB4XFN98CtvKFMQ9eSp
- 原版：https://claude.ai/artifact/HX8B7rYGVxztvXFQDQYRnW
- 使用手冊（文件）：https://claude.ai/artifact/8fZhTuDvi6RGDdBF69RP9L

## 3. 程式結構（index.html）
四個 script 區塊：
1. i18n：`EX` 完整比對表、`SUB` 子字串表（長的優先）、`window.__tr` / `__lang`、canvas `fillText` 補丁。新增中文字串後，要在 `SUB["自動"]="Auto";` 之後補英文。
2. 核心：`DEF` 預設值、`plan()`、`gen()`（NC 行）、DXF。
3. `core3d`：3D 模型（M3）。
4. UI（IIFE）：工具列、清單、屬性、報表、模擬、3D 檢視。

重要機制：
- 報表：`parseDprnt` → `st.run`；`buildRep`、`repCsv`、`repPages`（canvas）、`mkPdf`（JPEG 組 PDF）、`previewRep`。DPRNT 格式 `MP 序號 類型 數值…`。
- 公差：`tolKind` none / size / pos / both；勾選「檢查」會在 G65 P9700 加 T；距離與角度只有報表公差。
- 儲存：`hsnap` 含 run/rep（含 LOGO）；瀏覽器 localStorage + IndexedDB 自動儲存；「儲存專案」存成檔案。
- 碰撞模擬：`G.col`；球徑、測針長度（預設 50 mm）、探頭本體直徑（預設 40 mm，為假設值）。
- 孔壁完整性：`sec3`（依曲面）、`secChain`（沿切面輪廓追蹤圓弧）、`pick3Angles`；孔壁不完整會自動改 3 點量測。
- 新手引導：`showTour`，`localStorage` 鍵 `qx-tour`。
- 觸控：`TABLET()`（601–1100px 抽屜）、`COARSE`（指標粗）、指標式拖曳排序、兩指 3D 平移。

## 4. 使用者的固定偏好
- 語言：繁體中文（台灣）。
- 沒指定版本就改觸控版。
- 公差提示文字：「超出公差時機台報警」；選項名稱「檢查」；報表開關名稱「建立量測報表」，沒勾選時隱藏報表資訊。
- 介面顏色參考使用者的 QuickstartX 標誌（紫、粉）。
- PDF 報表頁尾只寫「QuickstartX」，不要出現 Fanuc G65 字樣。
- 作者「布滷斯」，免責聲明在左下角。

## 5. 測試方式
用 Playwright（headless Chromium）：桌機約 1180×820、iPad 直式 820×1180；外部網路請求用 route 擋掉，three.js 與 occt-import-js 從本機供應。測試模型用小型 STEP / OBJ（長槽板可驗證 3 點量測判斷）。

## 6. 尚未驗證 / 已知限制
- 真實 Fanuc 機台的 DPRNT（間接變數 `#[#111+6]`）輸出格式尚未實機驗證。
- 尚未在 iPad 實機測試手勢；手機（≤600px）版面未優化。
- 點到長槽直邊壁時，仍可能算出很大的孔徑（原有行為）。
- 純前端無法真正防抄；若要保護核心演算法需移到後端。
- 倉庫目前是公開的；GitHub Pages 網頁一定公開。

## 7. 發布流程
1. 修改 `index.html`（觸控版）。
2. Playwright 測試。
3. 用 Artifact 工具以同一網址更新觸控版頁面。
4. 推送到本倉庫。
