# Genesis Outpost

The Genesis Outpost is one small program you run inside your own network — on a laptop to
try it, or on a VM, in Docker or in Kubernetes (AWS, Azure, GCP or on-prem) — so that
[Genesis](https://www.genesiscomputing.ai) can reach databases and services there: Postgres
and other databases, Jira and Confluence Data Center, GitLab, internal APIs.

- **Outbound only.** The Outpost dials out to your Genesis over HTTPS (443). You open no
  inbound ports, and nothing passes through infrastructure Genesis runs.
- **You set the ceiling.** The Outpost connects only to the `host:port` destinations you
  allow in `ALLOWED_DESTINATIONS`, checked every time a connection opens. Cloud metadata and
  link-local addresses are always refused.
- **Credentials can stay home.** A Genesis connection can hold a reference instead of a
  password; the Outpost reads the value from your own secret store when Genesis connects.
- **No command execution, no general proxy.**

This repository holds **release downloads and install notes only**.

## Set it up from Genesis

In Genesis, open **Config → Outposts → Add Outpost**. The wizard asks where the Outpost will
run and gives you the exact commands for that, with your Genesis endpoint and settings
filled in, then waits for the Outpost to connect and lets you test a route.

Full documentation: [Genesis Outposts](https://docs.genesiscomputing.com/docs/Setup/Outposts-Overview).

## Downloads

Each [release](../../releases) has one static binary per platform, a `SHA256SUMS` file and
`THIRD_PARTY_NOTICES`:

| Platform | File |
|---|---|
| macOS, Apple silicon | `genesis-outpost-darwin-arm64` |
| macOS, Intel | `genesis-outpost-darwin-amd64` |
| Linux, x86-64 | `genesis-outpost-linux-amd64` |
| Linux, ARM64 (e.g. Graviton) | `genesis-outpost-linux-arm64` |
| Windows, x86-64 | `genesis-outpost-windows-amd64.exe` |
| Windows, ARM64 | `genesis-outpost-windows-arm64.exe` |

Verify a download before running it:

```bash
VERSION=0.3.2
FILE=genesis-outpost-linux-amd64          # pick yours from the table
curl -fLO https://github.com/genesis-computing-ai/genesis-outpost/releases/download/outpost-v$VERSION/$FILE
curl -fLO https://github.com/genesis-computing-ai/genesis-outpost/releases/download/outpost-v$VERSION/SHA256SUMS
grep "$FILE" SHA256SUMS | sha256sum -c        # macOS: shasum -a 256 -c
```

On macOS, a downloaded binary is quarantined until you allow it:
`xattr -d com.apple.quarantine genesis-outpost-darwin-arm64`. On Windows, SmartScreen may
ask you to confirm. The binaries are not code-signed yet.

## Try it on a laptop

```bash
chmod +x genesis-outpost-darwin-arm64
./genesis-outpost-darwin-arm64 init    # asks for the values the Genesis wizard shows you
./genesis-outpost-darwin-arm64 run
```

Evaluation only: it stops when the laptop sleeps or you close the terminal. For anything
people rely on, run it on a VM, in Docker or in Kubernetes as the Genesis wizard describes.

## Docker and Kubernetes

The container image is published for linux/amd64 and linux/arm64:

```bash
docker pull ghcr.io/genesis-computing-ai/genesis-outpost:0.3.2
```

It is distroless (the binary, CA certificates, the LICENSE and `THIRD_PARTY_NOTICES` under
`/usr/share/doc/genesis-outpost/`), runs as uid 10001, and keeps its state in the volume
`/var/lib/genesis-outpost`. The Genesis wizard gives you the complete `docker run` command,
or a ready-to-apply Kubernetes manifest with a Secret, for your Outpost. The settings are
environment variables, for example:

```bash
docker run -d --name genesis-outpost --restart unless-stopped \
  -e GENESIS_ENDPOINT='<your Genesis tunnel endpoint>' \
  -e OUTPOST_SLOT='<from the wizard>' \
  -e ALLOWED_DESTINATIONS='db.corp.internal:5432,jira.corp.internal:443' \
  -v genesis-outpost-state:/var/lib/genesis-outpost \
  ghcr.io/genesis-computing-ai/genesis-outpost:0.3.2
```

plus the sign-in settings the wizard shows for your deployment (a Snowflake key pair, a
Databricks service principal, or a tunnel password).

## License

The binaries are proprietary; see [LICENSE](LICENSE). Open-source components included in
them are listed, with their licenses and notices, in `THIRD_PARTY_NOTICES` in each release.
