# Vapi integration

1. In the Vapi dashboard, open any assistant and set **Model → Provider** to **Custom LLM**.
2. Paste your masker URL into the **Custom LLM URL** field. Format:
   `https://try.masker.dev/agents/vapi-public-demo/s/<your-session>/v1`
3. Save. Place a test call. Watch the redaction firewall in your masker dashboard.

No API key, no headers — your session token is pinned in the URL path.
