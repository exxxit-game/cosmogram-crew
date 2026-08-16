# Morning report via cosmogram-sync

The morning report uses the existing Supabase Edge Function:

`POST https://cwpijvgdrrvnvldhnmbj.supabase.co/functions/v1/cosmogram-sync`

Payload:

```json
{"action":"morning_report","source":"github-actions","version":"1.400.1"}
```

Required `config` rows in Supabase:

- `morning_report_token`: random long secret used by the scheduler;
- `morning_report_chat_id`: Telegram numeric chat/user ID that receives the report;
- `bot_token`: existing Telegram bot token already used by `cosmogram-sync`.

The request must include:

```text
Authorization: Bearer <morning_report_token>
```

The workflow in the game repository calls this same endpoint at `08:00 UTC` using GitHub secrets:

- `MORNING_REPORT_URL=https://cwpijvgdrrvnvldhnmbj.supabase.co/functions/v1/cosmogram-sync`
- `MORNING_REPORT_TOKEN=<same value as config.morning_report_token>`

The Edge Function returns `401` for a missing/wrong scheduler token and `503` when the chat ID or bot token is not configured. It sends one Telegram message through the existing `notifyBot` helper and reports aggregate counts from `players`, `scores`, `player_days`, and `daily_runs`.
