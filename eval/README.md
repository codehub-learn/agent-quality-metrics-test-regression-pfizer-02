# Evaluation set

- Version: `v1`
- Cases: `dev` 9; `heldout` 3
- SHA-256 prefix of `data/transcripts.sha256` (first 12 characters): `ED3A0A4A0764`
- Command used: `Get-FileHash .\data\transcripts.sha256 -Algorithm SHA256 | ForEach-Object { $_.Hash.Substring(0,12) }`

Held-out cases are not used for tuning.
