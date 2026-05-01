# Detector coverage matrix

| Identifier | Detector | Format examples |
|---|---|---|
| SSN | regex + spoken-form | `123-45-6789`, "one two three forty-five sixty-seven eighty-nine" |
| US phone | regex + spoken-form | `(510) 555-1212`, "five ten five five five…" |
| Email | RFC 5322 subset | `name@example.com` |
| URL | URL parser | `https://example.com/path` |
| IPv4 / IPv6 | regex | `192.0.2.1`, `2001:db8::1` |
| ZIP | regex | `94110`, `94110-1234` |
| Dates | dateparser | `Jan 5, 1980`, "January fifth, nineteen eighty" |
| VIN | regex + checksum | `1HGCM82633A004352` |
| Credit card | Luhn | `4111 1111 1111 1111` |
| Names | NER | proper-noun spans |
| Addresses | geo + regex | street numbers, unit, city/state |

Coverage is continuously expanded. File an issue if you have a PHI shape that's missed.
