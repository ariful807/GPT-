# Ariful Assistant — GPT Actions deployment checklist

## Current repository files
- `Code.gs`: version 4.3.0 source. GPT Actions API actions: health, search, faq, categories, get_by_id, login_admin, logout_admin, add_knowledge, update_knowledge, add_faq.
- `openapi.yaml`: draft OpenAPI schema for GPT Builder.

## Important status
GitHub source changes do not automatically update the Google Apps Script deployment. The project owner must copy/deploy the new source in Apps Script and test it. Do not consider the live API updated until a new deployment has been tested.

## Deploy the backend
1. Open the Apps Script project attached to the existing web app.
2. Replace the existing `Code.gs` with the repository's latest `Code.gs` content.
3. Save.
4. In Project Settings > Script Properties, verify `ADMIN_USERNAME`, `ADMIN_PASSWORD_SALT`, and `ADMIN_PASSWORD_HASH` already exist. If the admin has not been initialized yet, set `ADMIN_INITIAL_USERNAME` and a unique password of at least 12 characters, then run the existing `initializeAdminCredentials()` initializer once from the editor. Do not put the password in GitHub or GPT instructions.
5. Confirm the project has the required spreadsheet access and the database sheets exist. If necessary, run `setupDatabase_()` from the editor.
6. Select Deploy > Manage deployments > Edit (pencil) > New version > Deploy. Keep the existing web app URL if updating the same deployment.
7. Test the deployed URL with a GET request; it should return health JSON with version 4.3.0. A GET health response alone does not prove POST actions work.

## Configure GPT Actions
1. In GPT Builder, open the GPT > Configure > Actions > Create new action.
2. Copy the contents of `openapi.yaml` into the schema editor.
3. Keep authentication set to None for this schema because the current implementation does not use a GPT Actions auth header. Do not put admin credentials in the schema.
4. Save, then use Preview to test `health`, `search`, and `faq` first.
5. Only after backend POST tests pass, test `login_admin` with the admin username/password, confirm the response contains `data.token`, then use that token as `session_token` for one test write action. Test `logout_admin` and confirm that the old token is rejected.
6. For a public GPT, provide a valid privacy policy URL in the GPT publishing settings if required by the builder.

## Security notes
- Public read actions are open to anyone who can reach the web app. Consider adding rate limits and abuse monitoring before broad public release.
- Chat-based password entry may be retained in the conversation history. A separate login page / proper OAuth flow is safer for sensitive production use.
- Session tokens are bearer credentials. Do not publish or share them; log out after use.
- Never commit passwords, session tokens, API keys, or Script Properties to GitHub.
- The old `ARIFUL_ASSISTANT_API_KEY` and `ARIFUL_ASSISTANT_ADMIN_KEY` helper functions may remain in source but are no longer used by the GPT Actions path described above.
