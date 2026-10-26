## changing from open ai connection to gemini's connection

```
Migrate this project’s existing OpenAI API connection to Gemini using Google’s OpenAI-compatible endpoint. Keep the application’s existing behavior and input/output handling intact.

First inspect the current integration and explain the smallest implementation plan, including the files you will change. Then implement it using existing project patterns. Avoid unrelated refactoring, new abstractions, or unnecessary dependencies.

Configure the following:

1. Connection
- Continue using the OpenAI Python SDK.
- Set base_url to:
  https://generativelanguage.googleapis.com/v1beta/openai/
- Read credentials from environment variables or a project-root .env file.
- Existing environment variables must take precedence over .env.
- Find .env relative to the project location, independent of the working directory.
- Fail clearly before sending requests when credentials or the model identifier are blank.
- Disable SDK retries with max_retries=0 so the shared request wrapper controls every send.
- Use a 60-second network timeout.

2. Environment settings
Use these defaults:
GEMINI_API_KEY=
GEMINI_MODEL=gemini-3.1-flash-lite
GEMINI_REQUESTS_PER_MINUTE=7
GEMINI_REQUESTS_PER_DAY=1200
GEMINI_MIN_REQUEST_INTERVAL=8.571429
GEMINI_MAX_REQUEST_ATTEMPTS=3
GEMINI_MAX_REQUESTS_PER_RUN=1200
GEMINI_MAX_OUTPUT_TOKENS=512

Support optional GEMINI_API_KEY_LIST=["key1","key2"].
A nonempty list takes precedence over GEMINI_API_KEY.
Remove blank and duplicate keys while preserving their order.
Never print or commit credentials.
Keep .env ignored by Git and provide placeholders in .env.example.
Validate numeric settings as positive values, rejecting nonfinite numbers.

3. Shared request handling
Route every outgoing request through one shared guarded function.
Preserve existing request parameters unless Gemini requires a compatibility change; explain any such change.
Apply GEMINI_MAX_OUTPUT_TOKENS to the outgoing request.
Warn when the provider reports output truncation.
Do not silently change models, enable billing, or use another provider as fallback.

4. Pacing and budgets
- Serialize requests within the process and across processes using the same checkout.
- Enforce a minimum interval of:
  max(60 / GEMINI_REQUESTS_PER_MINUTE,
      GEMINI_MIN_REQUEST_INTERVAL)
- Enforce GEMINI_MAX_REQUESTS_PER_RUN per process.
- Enforce GEMINI_REQUESTS_PER_DAY over a rolling 24-hour window, shared across all configured keys and processes using this checkout.
- Count every physical send, including failed attempts, retries, and requests repeated after empty or invalid results.
- Record each attempt before sending.
- Persist the rolling budget and cooldown in an ignored .runtime directory using a ledger and filesystem lock.
- Write ledger updates atomically.
- Restarting must not clear the rolling budget or cooldown.
- Invalid, unreadable, unsafe, or abandoned in-flight state must block requests with a clear diagnostic; never automatically delete or reset the ledger.
- Explain that these local controls do not coordinate separate checkouts or machines and do not increase Google’s actual quotas.
- Preserve any existing stricter limits. Do not introduce paths that bypass rate limiting.

5. Retry policy
- Retry connection failures, timeouts, ordinary HTTP 429 responses, and HTTP 5xx errors.
- Allow up to GEMINI_MAX_REQUEST_ATTEMPTS for temporary failures.
- Use exponential backoff starting at 30 seconds, then 60 seconds.
- Honor longer server delays from Retry-After or Google retry metadata.
- Fail immediately on nonretryable errors.
- After exhausted temporary retries, block further requests in the process and persist a cooldown of at least 60 seconds, extended by any longer server delay.
- Outer application retries must not restart an exhausted retry cycle.

6. Optional key failover
- Switch to the next configured key only when a structured HTTP 429 error explicitly indicates exhausted daily quota or zero quota.
- Do not rotate keys for ordinary rate limits, timeouts, authentication failures, or server errors.
- Keep using the selected key for the remainder of the process.
- Each failover send must still satisfy pacing and consume both shared budgets.
- Key failover must not reset budgets or cooldowns.
- Keep key failover separate from the temporary-failure retry counter.
- When all configured keys report unavailable quota, block the process and persist a cooldown of at least 24 hours, or longer if requested by the server.
- Explain that keys belonging to the same Google project may share provider quota.

7. Observability
- Log request attempt number, model identifier, wait duration, and status without credentials.
- Distinguish logical application calls from physical network attempts.
- Preserve existing input/output logging behavior. Do not introduce full payload logging without identifying the privacy impact and obtaining approval.

8. Verification and documentation
- Use mocked tests to check configuration precedence, pacing, budgets, retries, failover, persistent cooldowns, and credential redaction.
- Do not make live API requests or incur costs without explicit authorization.
- Document setup, environment variables, persistent state, and recovery diagnostics.
- Report changed files, verification results, assumptions, and limitations.
- Briefly explain commands before running them.

Treat the model identifier and numeric values above as requested configuration defaults, not proof of current model availability, free access, or provider quota. Verify compatibility against official Google documentation before implementation, and flag any required configuration change.
```