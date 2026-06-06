# tg-singer-actions

Use `tg-signer` in GitHub Actions for scheduled Telegram sign-in tasks.

This workflow installs a `tg-signer` fork that supports native AI vision actions from `TG-SignPulse`, including image OCR replies.

## Repository variables

Set these in **Settings -> Secrets and variables -> Actions -> Variables**:

- `TG_SIGNER_REPO`: your maintained fork URL, for example `https://github.com/Jessire/TG-SignPulse.git`.
  If omitted, the workflow installs `https://github.com/Jessire/TG-SignPulse.git`.
- `TG_SIGNER_REF`: branch or tag to install, usually `main`.
- `TG_SIGNER_NUM_OF_DIALOGS`: optional, defaults to `500`.

## Repository secrets

Set these in **Settings -> Secrets and variables -> Actions -> Secrets**:

- `SESSION_STRING`: Telegram session string.
- `TASK_MAP`: task configuration JSON. See `TASK_MAP_DEMO.json`.
- `OPENAI_API_KEY`: required only when using AI actions.
- `OPENAI_BASE_URL`: optional OpenAI-compatible API base URL.
- `OPENAI_MODEL`: optional model name. Use a vision-capable model when using image actions.

## AI actions

OpenAI secrets only provide credentials. AI is enabled by actions in `TASK_MAP`:

- `action: 4`: image plus buttons, choose and click the correct option.
- `action: 5`: text calculation/question, send the answer.
- `action: 6`: image OCR/recognition, send the recognized answer as text.
- `action: 7`: text calculation/question, click the button matching the answer.

For image captchas that require replying with text, use `action: 6`:

```json
{
  "okemby": {
    "chats": [
      {
        "chat_id": 8321810754,
        "name": "okemby_bot",
        "actions": [
          { "action": 1, "text": "/checkin" },
          {
            "action": 6,
            "ai_prompt": "Read the image captcha or sign-in answer from the image. Return only the exact text that should be sent to the bot. Do not explain. Do not use Markdown."
          }
        ],
        "action_interval": 1.0
      }
    ],
    "sign_at": "10 0 * * *",
    "random_seconds": 300,
    "sign_interval": 1
  }
}
```

## Notes

- Numeric `chat_id` must be known by the Telegram session. Keep `TG_SIGNER_NUM_OF_DIALOGS` high enough, or use a username when available.
- The upstream `amchii/tg-signer` package does not currently include `action: 6`, so this workflow installs a fork instead of patching the package at runtime.
