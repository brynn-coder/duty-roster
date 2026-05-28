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
  recordId: 'main'
};
```

5. 把 `duty-roster` 資料夾內容推到 GitHub repo；`data/` 是本機測試資料，不需要上傳。
6. 到 GitHub repo Settings > Pages，選擇部署來源。

部署後，所有人打開同一個 GitHub Pages 網址，會透過 Supabase 同步同一份排班資料。

注意：`config.js` 一定要填入 Supabase URL 和 anon public key，GitHub Pages 才會同步。現在的 RLS 規則適合小團隊內部工具：知道網頁網址的人都可以讀取與修改排班資料。

## 本機測試

沒有 Supabase 設定時，仍可用本機 Node server 測試：

```bash
node server.js
```

再打開：

```text
http://127.0.0.1:8765/
```
