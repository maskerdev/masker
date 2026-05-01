# masker

**HIPAA-grade compliance firewall for voice AI.** masker is a wedge that sits between your voice agent and your upstream LLM. On every turn it detects PHI in the transcript and replaces it with stable placeholders (`[SSN_01]`, `[PHONE_02]`, …) before forwarding to OpenAI / Anthropic / your model of choice — then rehydrates placeholders on the way back so your agent reads natural answers to the caller.

Real PHI never crosses the firewall. Public LLMs only ever see placeholders.

## Live demo

→ **[try.masker.dev](https://try.masker.dev)** — paste one URL into your Vapi / ElevenLabs / Bolna assistant and watch redaction happen in real time.

## What you get

Three artifacts per session, all derived from the same event stream:

### 1. Live transcript firewall
A side-by-side view of the call, split by a vertical "compliance firewall":

- **Left of the firewall (regulated):** Patient ↔ voice vendor. Real SSNs, phones, names, addresses live here and ONLY here.
- **Right of the firewall (public):** masker ↔ your LLM provider. Every PHI span is replaced with a stable placeholder like `[SSN_01]`.
- **Across the firewall:** animated chips that visualise each redaction (going out) or rehydration (coming back).

This is what you show an auditor when they ask "prove no PHI left the regulated boundary."

### 2. Audit chain (real-time)
Every detection becomes a tamper-evident event in a hash-chained journal:

```jsonl
{"seq":0,"kind":"detection","detector":"ssn_v1","placeholder":"[SSN_01]","prev_hash":"0000…","curr_hash":"a3f2…","ts":"2026-05-01T18:33:01Z"}
{"seq":1,"kind":"detection","detector":"usphone_v2","placeholder":"[USPHONE_01]","prev_hash":"a3f2…","curr_hash":"7c9e…","ts":"2026-05-01T18:33:02Z"}
{"seq":2,"kind":"redaction_applied","span":[12,23],"placeholder":"[SSN_01]","prev_hash":"7c9e…","curr_hash":"e1b4…","ts":"2026-05-01T18:33:02Z"}
```

Every event carries:
- a SHA-256 `prev_hash` linking it to the previous event,
- a SHA-256 `curr_hash` covering its own contents + the prev_hash.

A single byte mutated anywhere in the chain breaks every downstream hash. The dashboard's audit view shows `seq`, `kind`, detector, placeholder, and 8-char hash prefixes per row, plus a session `merkle_root_hex` you can verify offline.

`POST /audit/verify` re-runs the chain check and returns `{ok, event_count, message}` — the literal string `"chain ok"` is what you screenshot for an auditor.

### 3. Session compliance report (signed)
At call-end, masker mints a **HIPAA Safe Harbor compliance report** as two consistent artifacts derived from the same event chain:

- A **Masker Audit Schema v1 JSON** record (machine-checkable, [schema](https://github.com/maskerdev/masker-core/blob/main/schemas/masker-audit.v1.schema.json)).
- An **auditor-ready HIPAA PDF** (human-readable).

Both must agree on every count, status, and risk statement — that's enforced by the reporting engine's contract. The report includes:

- **HIPAA Safe Harbor coverage** — which of the 18 categories were detected this session, which detectors fired, and any partials.
- **PCI-DSS scope** — was cardholder data redacted before egress? Luhn checks logged.
- **Leak detection** — any placeholder that didn't round-trip cleanly is flagged with the offending turn.
- **Retention attestation** — originals are not persisted on the public demo; self-hosted deployments retain only the encrypted chain.
- **BAA chain** — which BAA-gated paths were used (e.g. production TTS rehydration slug).

Download is one click from the Reports tab; both formats share the same `merkle_root_hex` so you can prove the PDF and JSON describe the same chain.

## What it detects

HIPAA Safe Harbor identifiers — see [`docs/coverage.md`](docs/coverage.md) for the full matrix.

| Identifier | Detector | Examples |
|---|---|---|
| SSN | regex + spoken-form | `123-45-6789`, "one two three forty-five sixty-seven eighty-nine" |
| US phone / fax | regex + spoken-form | `(510) 555-1212` |
| Email | RFC 5322 subset | `name@example.com` |
| URL / IPv4 / IPv6 | URL parser + regex | |
| ZIP | regex | `94110-1234` |
| Dates | dateparser | "January fifth, nineteen eighty" |
| VIN | regex + checksum | `1HGCM82633A004352` |
| Credit card | Luhn | `4111 1111 1111 1111` |
| Names / addresses | NER + geo | proper-noun spans, street numbers |

Coverage is continuously expanded. File an issue if you have a PHI shape that's missed.

## Supported voice vendors

| Vendor | Integration | Status |
|---|---|---|
| [Vapi](docs/integration-vapi.md) | Custom LLM URL | ✅ |
| [ElevenLabs](docs/integration-elevenlabs.md) | Custom LLM URL | ✅ |
| [Bolna](docs/integration-bolna.md) | Custom LLM URL | ✅ |
| [Custom](docs/integration-custom.md) (any OpenAI-compatible) | Custom LLM URL | ✅ |

One paste, no SDK. The session token is pinned in the URL path so events scope to the right dashboard tab.

## Architecture

```
Patient ──voice──► [Voice Vendor] ──text──► [masker proxy] ──redacted──► [Your LLM]
                                                  │
                                                  ▼
                                          Audit chain (hash-linked, signed)
                                                  │
                                                  ▼
                                       Session report (JSON + PDF)
```

masker is a Rust service deployable in your VPC (Docker / Kubernetes / Fly). The OpenAI-compatible `/v1/chat/completions` endpoint means any voice vendor that supports a Custom LLM URL plugs in with zero code.

Source is under [maskerdev/masker-core](https://github.com/maskerdev/masker-core) (private until GA).

## Compliance posture

- **HIPAA Safe Harbor** — 9 of 18 categories fully covered today (D phone, E fax, F email, G SSN, N URL, O IP, …); 3 partial (B ZIP, C dates, L VIN). Surfaced live in the proxy compliance report.
- **PCI-DSS scope reduction** — cardholder data Luhn-checked and redacted before egress.
- **Fail-closed audit** — if the durable journal append fails, processing returns `AuditUnavailable`. No quiet drops.
- **No persistence** of originals on the public [try.masker.dev](https://try.masker.dev) demo. Self-hosted deployments retain only the encrypted audit chain.
- **BAA-gated** TTS rehydration slug for production deployments.

## Get involved

- **Pilots** — we're onboarding a small group of healthcare voice teams. [hello@masker.dev](mailto:hello@masker.dev)
- **Docs** — see [`docs/`](docs/) for per-vendor integration guides and the coverage matrix.
- **Issues** — file under this repo for public-facing bugs / feature requests.

## License

Public docs are MIT. Core engine is proprietary (closed beta).
