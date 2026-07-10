---
name: longbridge-lbti
description: 長橋 LBTI 投資風格測試。當用戶說「投資風格測試 / LBTI / 測投資人格 / 測下我係邊種投資者 / 投資人格測試」等，依次問 5 道題，根據答案判定 8 種投資人格之一（深潛獵人／方舟艦長／財報狙擊手／板塊輪動客／賽道信徒／順風航海家／閃電交易者／情緒遊俠），返回對應的成品結果卡圖片 + 二維碼；若客戶端無法發送圖片，則返回純文字結果 + 落地頁連結。全程繁體、在對話內完成。
---

# 長橋 LBTI 投資風格測試

用戶答 5 道題，判定其投資人格（8 選 1），返回專屬結果卡。**全程繁體，在對話框內完成。**

## 資源

- `questions.json`：5 道題（用 `hk` 欄位）、每個選項的 `letter`、計分規則、`meta.codeToPersona`（code→人格）映射。
- `personas.json`：8 種人格文案（`hk` 名、`en` 英文名、`quote_hk` 金句、`desc_hk` 描述、`image` 圖片名）。
- `share.json`：8 種人格的分享話術。
- `assets/{persona}.png`：8 張成品結果卡。
- `assets/qr/{persona}.png`：8 張二維碼（**掃碼直接打開該人格結果卡大圖**，公網 CDN、免登錄，手機長按存圖/分享）。二維碼編碼的就是下面 `CDN_BASE` 的卡圖直鏈（帶 release tag，穩定抗緩存）。
- **`CDN_BASE`**（結果卡與二維碼的公網基址，用於 widget 內嵌顯示）：
  ```
  https://cdn.jsdelivr.net/gh/longbridge/extras-skills@main/skills/longbridge-lbti/assets/
  ```
  卡片 = `{CDN_BASE}{persona}.png`，二維碼 = `{CDN_BASE}qr/{persona}.png`。此域名在 widget 沙箱 CSP 白名單內，可正常載入。
  > 註：`@main` 跟隨分支最新提交，jsDelivr 對分支 URL 有 ~12 小時快取，換圖後不即時生效。上線建議打 tag（如 `@v1`）並改用 tag URL，快取穩定且可版本回滾。

## 流程

1. **開場**：出一句 —— 「🧭 長橋投資風格測試 · LBTI，5 條題約 30 秒，憑直覺揀，冇標準答案。」
2. **逐題問**：按 `questions.json` 順序，用 `hk` 文案逐題問；每題 2 個選項標 **A / B**。等用戶回覆再問下一題；也接受用戶一次過回覆 5 個字母（如 `ABABA`）。
3. **計分**（規則見 `questions.json.scoring`）：
   - 取第 1、2、3 題所選選項的 `letter`，拼成三字母 `code`（例：`F`+`S`+`C` = `FSC`）。
   - `persona = questions.json.meta.codeToPersona[code]`（如 `FSC` → `earnings-sniper`）。
   - 第 4 題 → 後綴：選 A = 「溫和版」，選 B = 「激進版」。
   - 第 5 題 → 後綴：選 A = 「紀律型」，選 B = 「情緒型」。
4. **返回結果**：分「正常」與「兜底」兩種，見下。

## 返回結果

### 正常情況（能內嵌顯示圖片）

**目標：圖直接渲染喺對話流裡,唔係淨返回下載文件卡片。** 用 `show_widget`（HTML）內嵌 `<img>`,`src` 直接指向 `CDN_BASE` 的公網 URL(原圖零壓縮、幾乎唔耗 token)。把下面 `{persona}` 換成結果人格:

```html
<div style="display:flex; gap:20px; align-items:flex-start; flex-wrap:wrap; padding:1rem 0;">
  <img src="{CDN_BASE}{persona}.png" alt="{hk} 投資人格卡"
       style="width:280px; max-width:100%; border-radius:12px; border:0.5px solid var(--border); display:block;" />
  <div style="flex:1 1 180px; min-width:160px;">
    <p style="font-size:13px; color:var(--text-secondary); margin:0 0 6px;">掃碼睇高清大圖 · 可存相簿分享</p>
    <img src="{CDN_BASE}qr/{persona}.png" alt="二維碼"
         style="width:120px; height:120px; border-radius:8px; border:0.5px solid var(--border); background:var(--surface-2); padding:6px; display:block;" />
  </div>
</div>
```

跟住出一段文字(用戶睇得到,寫喺 widget 外):

```
🎴 你嘅投資人格係：【{hk}】{en}　{後綴：溫和/激進版 · 紀律/情緒型}
「{quote_hk}」
{desc_hk}
📲 掃二維碼 / 開連結,就可以存到手機同分享。
```
（可再附 `share.json` 對應話術,方便用戶轉發。）

### 無 `show_widget`（Claude Code CLI / Cowork / IDE 等）

好多環境（Claude Code CLI、Claude 桌面 Cowork、IDE 內嵌 agent）**沒有 `show_widget` 工具**，通常要靠「讀取圖片檔案」嘅工具先可以喺對話流裡渲染圖。但實測有兩個坑，唔避開就會「圖唔見咗」：

1. **唔好一次過批量讀多張圖**：同一條回覆入面連續讀 2 張或以上圖片（例如一次過 `Read` 幾個檔 / 顯示為「Read N files」），客戶端好多時**只會渲染其中一張，甚至一張都唔顯示**。
2. 因此**唔好嘗試喺同一條回覆裡同時內嵌「結果卡 + 二維碼」兩張圖**——實測會掉。

**穩陣做法（下面兩樣都要做）：**

- **① 必給文字連結（最可靠，任何環境都掂）**：喺結果文字裡直接俾出兩條公網連結，用戶撳／掃即用：
  - 結果卡：`{CDN_BASE}{persona}.png`
  - 二維碼：`{CDN_BASE}qr/{persona}.png`
- **② 可選內嵌一張**：想圖直接渲染嘅話，**一條回覆最多內嵌一張**（優先結果卡），用讀圖工具**單獨讀嗰一個檔**（唔好連二維碼一齊批量讀）。二維碼就用上面①條連結交付，或提示「想睇二維碼覆一聲，我單獨出畀你」。

跟住照樣附上「正常情況」嗰段用戶可見文字（人格名／金句／描述／`share.json` 話術）。

> 關鍵：只要①嘅兩條連結一定俾齊，就算內嵌渲染失敗，用戶都實攞到卡片同二維碼——唔會再出現截圖嗰種「兩張都唔見」或「漏咗二維碼」。`show_widget` 唔存在 **≠** 圖出唔到；只有連文字連結都俾唔到先至走下面純文字兜底。

### 兜底情況（連圖片都顯示唔到）

若客戶端**連圖片都渲染唔到**（既冇 `show_widget`，用讀圖工具都貼唔出嚟，極少數情況）,**唔好發任何圖**(二維碼都係圖,一樣顯示唔到)→ 改用**純文字 + 落地頁文字連結**。不要報錯、不要空著。格式：

```
🎴 你嘅投資人格係
【{hk}】{en}　{後綴}
「{quote_hk}」
{desc_hk}

⚠️ 你而家用嘅助手暫時顯示唔到圖片——你張專屬人格卡喺下面條連結度，可以睇同儲存 👇
📲 {CDN_BASE}{persona}.png
（手機打開 → 長按卡圖存相簿 / 分享；此鏈公網免登錄，任何人可開）
```

> 呢條就係二維碼掃出嚟嘅同一條公網卡圖直鏈，任何裝置／未登錄都開到，故兜底一律用佢，唔好再用 `claude.ai/...` 之類要登錄先開嘅內部鏈。

## 落地目標（二維碼掃出嚟去邊）

**當前預設：二維碼 = 公網卡圖直鏈（免登錄，任何人可開）。**

- 8 張 `assets/qr/{persona}.png` 編碼的就係對應人格嘅結果卡直鏈：
  ```
  https://cdn.jsdelivr.net/gh/longbridge/extras-skills@longbridge-lbti-v1.0.0/skills/longbridge-lbti/assets/{persona}.png
  ```
  用 release tag（`@longbridge-lbti-v1.0.0`）而唔係 `@main`，係為咗打印/分發出去嘅二維碼**穩定、抗 jsDelivr 分支緩存、可版本回滾**。卡面設計已定稿唔會變，tag 鏈長期有效。
- 掃碼體驗：手機瀏覽器直接開卡圖 → 長按存相簿 / 分享。無需登錄、無需任何 App。
- ⚠️ **切勿再用 `https://claude.ai/code/artifact/...` 之類鏈接**——嗰啲要登錄／權限先開得到，絕大多數用戶掃咗係白屏。舊版二維碼曾誤指向呢個地址，已修正。

> **可選升級（未做）**：若日後想要帶品牌樣式、分享按鈕、活動 CTA 嘅正式落地頁，由運營／開發整一個公網頁（如短鏈 `https://lb.com/lbti`，8 型用 `#{persona}` 直達），再重新生成 8 張二維碼指向佢即可。整之前唔好改二維碼，免得指向未上線嘅頁。

## 重要約束

- **語言用繁體。**
- **結果圖只「取」不「生成」**：從 `CDN_BASE`(即倉庫 `assets/`)取對應檔案**原樣返回**,絕不重新生成、繪製或改動卡面文字,確保每個用戶拿到的同設計稿一致。
- **內嵌顯示優先，但要睇環境**：有 `show_widget`（claude.ai / 桌面普通對話）一律用 widget 引 `CDN_BASE` URL 令圖喺對話流裡直接渲染，唔好淨用 `present_files` 出下載卡片。**冇 `show_widget` 嘅環境（CLI / Cowork / IDE）見上面「無 `show_widget`」節**：唔好因為冇 widget 就退去純文字，但亦唔好一條回覆批量讀多圖（會漏）。
- **結果卡同二維碼係一套，二維碼唔准漏**：無論行邊條路徑，結果卡同二維碼都要一齊交付——**內嵌 或 CDN 文字連結皆可**（冇 widget 時，二維碼用連結交付即可，唔一定要內嵌）。一條回覆內嵌圖最多一張。只有連文字連結都俾唔到先至走純文字兜底。
- **計分與映射一律以 `questions.json` 為準**，唔好自行改判。
- 本版**暫不做結尾 CTA**（安裝/活動引導）。
- 8 種人格 code 對照：FLC 深潛獵人 / FLD 方舟艦長 / FSC 財報狙擊手 / FSD 板塊輪動客 / MLC 賽道信徒 / MLD 順風航海家 / MSC 閃電交易者 / MSD 情緒遊俠。
