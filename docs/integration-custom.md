# Custom voice vendor integration

masker exposes an OpenAI-compatible `/v1/chat/completions` endpoint. Any voice vendor that supports a "Custom LLM" or "OpenAI-compatible" base URL will work.

Base URL format:
`https://try.masker.dev/agents/custom/s/<your-session>/v1`

The vendor will append `/chat/completions` automatically. The `/s/<token>/` path segment pins the call to the dashboard tab that minted the token.
