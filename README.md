# Figma Copy to Sheets (self-hosted)

  Extract text from selected Figma containers (Frame/Group/Section/Component/Instance), de-duplicate it, add Figma node links, upload to your Google Sheet, and auto-translate zh-TW → en / zh-CN using
  LanguageApp.translate. This project does not provide a backend; set up your own Apps Script Web App and Sheet.

  ## Features

  - Extracts all visible text under the selected container(s); hidden or opacity=0 content is skipped.
  - Per-run de-duplication (Total / Unique / Duplicates removed).
  - Writes to Google Sheets: inserts under the header (top of sheet) while keeping row formatting and avoiding header formatting.
  - Adds Figma node links for traceability.
  - Translates zh-TW → en / zh-CN via LanguageApp.translate.
  - Error logging: WebhookErrors sheet captures errorId and details.

  ## Self-hosted backend (5 steps)

  1. Copy Code.gs into your Apps Script project.
  2. Add Script Properties (Project Settings → Script properties):
      - TOKEN: your custom long token
      - SHEET_ID: your target Google Sheet ID (/d/<ID>/edit)
      - DEFAULT_SHEET_NAME: 
  3. Deploy Web App: Deploy → New deployment → Web app
      - Execute as: Me
      - Who has access: Anyone with the link (protected by TOKEN)
  4. Prepare your Sheet
      - Create the tab with header:
        property | character type | zh-TW | eng | zh-cn | Glossary | note | reviewer | ticket | Figma
      - (Optional) create WebhookErrors for error logging
  5. In the plugin: paste your Web App URL + TOKEN + Sheet tab name. Settings are stored per user (clientStorage).

  ## Plugin usage

  1. Select one or more containers in Figma (Frame/Group/Section/Component/Instance).
  2. Open the plugin, enter Web App URL / TOKEN / Sheet tab.
  3. Click Extract & Upload.
  4. You’ll see: Total / Unique / Duplicates removed / Added.

  ## Troubleshooting

  - Plugin shows Error ID: ...: open your Sheet’s WebhookErrors tab and search that errorId for details.
  - Unauthorized: TOKEN mismatch.
  - Sheet tab not found: tab name typo or not created (watch for leading/trailing spaces).
  - Translate failed: LanguageApp translation failed (check quotas/error message).
  - Server error: write/insert/format copy failed; use errorId in WebhookErrors to see the exception.

  ## Security tips

  - Do not hardcode Web App URL or TOKEN in code or public repos.
  - Rotate TOKEN regularly; use a long, random value.
  - Web App should be Execute as Me + Anyone with link, protected by TOKEN. If you have Workspace, consider domain-only access.

  ## If you want your own translation pipeline

  - Replace the LanguageApp.translate block in doPost with your API/logic; wrap in try/catch and return errorId on failure.
  - Don’t hardcode API keys; use Script Properties or pass from the plugin UI.

  ## Test your Web App (optional)

  curl -L "https://script.google.com/macros/s/<YOUR_DEPLOYMENT_ID>/exec" \
    --data '{"token":"<YOUR_TOKEN>","sheetName":"{{Your sheet name}}","items":[{"text":"ping","figmaUrl":"test"}]}'

  Expect {"ok":true,...} on success, or {ok:false,error:"...",errorId:"..."} on failure.
