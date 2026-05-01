# masker

**HIPAA-grade compliance firewall for voice AI.** masker is a wedge that sits between your voice agent and your upstream LLM. On every turn it detects PHI in the transcript and replaces it with stable placeholders (`[SSN_01]`, `[PHONE_02]`, …) before forwarding to OpenAI / Anthropic / your model of choice — then rehydrates placeholders on the way back so your agent reads natural answers to the caller.

Real PHI never crosses the firewall. Public LLMs only ever see placeholders.

## Live demo

→ **[demo.masker.dev](https://demo.masker.dev)** — paste one URL into your Vapi / ElevenLabs / Bolna assistant and watch redaction happen in real time.

## What it does

- **Detects** HIPAA Safe Harbor identifiers: SSN (formatted & spoken-form), US phone, fax, email, URLs, IPs, ZIP, dates, VIN, credit cards, names, addresses.
- **Redacts** in-flight: every span replaced with a stable placeholder so the LLM sees structure without identity.
- **Rehydrates** on the way back: placeholders are restored before the response reaches the voice provider's TTS.
- **Audits** every detection: signed event chain per session, exportable as a compliance report.
- **Stays out of the way**: a single Custom-LLM URL, OpenAI-compatible. No SDK to install.

## Supported voice vendors

| Vendor | Integration | Status |
|---|---|---|
| Vapi | Custom LLM URL | ✅ |
| ElevenLabs | Custom LLM URL | ✅ |
| Bolna | Custom LLM URL | ✅ |
| Custom (any OpenAI-compatible) | Custom LLM URL | ✅ |

## Architecture

```
Patient ──voice──> [Voice Vendor] ──text──> [masker proxy] ──redacted──> [Your LLM]
                                                  │
                                                  └──> Audit chain (signed)
```

masker is a Rust service deployed in your VPC (Docker/Kubernetes/Fly). Source is under [maskerdev/masker-core](https://github.com/maskerdev/masker-core) (private until GA).

## Compliance posture

- **HIPAA Safe Harbor** covered by detector matrix (see [`docs/coverage.md`](docs/coverage.md)).
- **PCI-DSS** scope reduction: cardholder data redacted before egress.
- **No persistence** of originals on the public demo. Self-hosted deployments retain only the encrypted audit chain.
- **BAA-gated** TTS rehydration slug for production deployments.

## Get involved

- **Pilots**: we're onboarding a small group of healthcare voice teams. [Reach out →](mailto:hello@masker.dev)
- **Docs**: see [`docs/`](docs/) for the integration guides per vendor.
- **Issues**: file under this repo for public-facing bugs / feature requests.

## License

Public docs are MIT. Core engine is proprietary (closed beta).
