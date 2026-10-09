# Scopebond chain anchors

An append-only public record of the evidence chains kept by Scopebond Cloud.

Every computer connected to Scopebond Cloud receives a signed **chain head** with each delivery: the newest sequence number Cloud admitted for its environment and the digest of the newest evidence segment. Once a day, the workflow in this repository copies the previous UTC day's signed list of heads from Cloud's public endpoint

```
https://cloud.scopebond.com/v1/anchors/YYYY-MM-DD
```

into `anchors/YYYY/MM/DD.json`, byte for byte. A published day is never rewritten, and the default branch rejects force pushes and deletions, so this repository's history shows whether Cloud ever changed what it had said.

## What a file contains

- `heads`: one entry per evidence chain. Each is named only by `anchor_id`, a salted SHA-256, and holds its newest sequence number, newest segment digest and issue time. There are no workspace names, ids or personal data.
- `signed`, `key` and `signature`: an Ed25519 signature over the list, and the public key that made it.

## Checking your own records

A computer keeps the heads it was given (`chain-heads.json` beside its receipts). With a Scopebond hook or agent release that includes `verify --anchor`:

```
scopebond verify --anchor https://raw.githubusercontent.com/avouro-com/scopebond-anchors/main/anchors/YYYY/MM/DD.json
```

The check fails when a chain went backwards, when one position names two different segments, or when a head the computer was given is missing from the chain.
