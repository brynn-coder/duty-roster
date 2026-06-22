# 值日生排班表

## GitHub Pages + Supabase 部署

1. 在 Supabase 建立新 project。
2. 到 Supabase SQL Editor，執行 `supabase-schema.sql`。
3. 到 Supabase Project Settings > API，複製：
   - Project URL
   - anon public key
4. 編輯 `config.js`：

```js
window.DUTY_ROSTER_CONFIG = {
  supabaseUrl: 'https://YOUR_PROJECT_ID.supabase.co',
  supabaseAnonKey: 'YOUR_SUPABASE_ANON_KEY',
  tableName: 'duty_roster_state',
  horoscopeTable: 'daily_horoscopes',
  recordId: 'main'
};
```

5. 把 `duty-roster` 資料夾內容推到 GitHub repo；`data/` 是本機測試資料，不需要上傳。
6. 到 GitHub repo Settings > Pages，選擇部署來源。

部署後，所有人打開同一個 GitHub Pages 網址，會透過 Supabase 同步同一份排班資料。

注意：`config.js` 一定要填入 Supabase URL 和 anon public key，GitHub Pages 才會同步。現在的 RLS 規則適合小團隊內部工具：知道網頁網址的人都可以讀取與修改排班資料。

## 每日 12 星座運勢

前端會讀取 Supabase 的 `daily_horoscopes` 資料表，依序輪播 12 星座。Edge Function 每天從科技紫微網今日運勢抓取 12 星座內容，整理成短句後寫入 Supabase。若資料表還沒有今天的內容，會先顯示本機預覽文案，不影響排班功能。

### Supabase Edge Function

函式位置：

```text
supabase/functions/update-horoscopes/index.ts
```

目前不需要 OpenAI API，也不需要付費模型。Function 會直接解析免費網頁來源，並只儲存短句摘要與來源連結。

部署：

```bash
supabase functions deploy update-horoscopes
```

手動測試可呼叫 Function URL：

```bash
curl -X POST "https://YOUR_PROJECT_ID.supabase.co/functions/v1/update-horoscopes" \
  -H "Authorization: Bearer YOUR_ANON_KEY" \
  -H "apikey: YOUR_ANON_KEY"
```

每天自動更新可用 Supabase Dashboard 的 Scheduled Functions，排程例如每天台北時間早上 8 點執行一次。

## 本機測試

沒有 Supabase 設定時，仍可用本機 Node server 測試：

```bash
node server.js
```

再打開：

```text
http://127.0.0.1:8765/
```
