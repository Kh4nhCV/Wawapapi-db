# wawapapi-db

The prebuilt vulnerability database used by the **wawapapi** command-line scanner and
browser extension. It is published here, in a public repository, so clients can download
it without credentials:

```
releases/download/db/latest.json
releases/download/db/wawapapi-db.json.gz
```

`latest.json` names the build time, the SHA-256 of the database and (when signing is
configured) an Ed25519 signature; clients verify both before using the data, and refuse a
manifest older than the copy they already hold.

## How it is built

[`.github/workflows/build.yml`](.github/workflows/build.yml) runs every day at 06:17 UTC
(and on demand from the Actions tab). It checks out the source repository, runs
`go run ./cmd/wawapapi-db`, and uploads both files to the rolling `db` release,
overwriting the previous build.

The data comes from public sources: NVD, CISA KEV and Vulnrichment, FIRST EPSS,
WPVulnerability.net, endoflife.date, Retire.js, and the Nuclei and Metasploit exploit
indexes. Each source's own terms apply to its data.

## Setup

Repository secrets (Settings → Secrets and variables → Actions):

| Secret | Required | What it is |
|---|---|---|
| `SOURCE_REPOSITORY` | yes | `owner/name` of the private source repository (a secret, so it is masked in public logs) |
| `WAWAPAPI` | yes | Fine-grained personal access token with **read-only Contents** access to the private source repository only |
| `WAWAPAPI_DB_SIGNING_KEY` | no | Base64 Ed25519 seed from `go run ./cmd/wawapapi-db -keygen`; its public key goes into the clients |

Nothing else is stored here; there is no code in this repository besides the workflow.
