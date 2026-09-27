# Ares CLI

Command-line interface for Ares ASM.

## Install

```bash
pip install requests
# then either:
python ares_cli.py <command>
# or install globally:
pip install -e .
# → ares <command>
```

## Quick start

```bash
# 1. Configure server and API key (generated from Ares web UI → Settings → API Keys)
ares configure

# 2. List assets for a project
ares assets --org ACME --project ACME-ASSESS-26-1

# 3. Upload a scan file
ares load nuclei_output.jsonl --org ACME --project ACME-ASSESS-26-1

# 4. Interactive shell
ares cli
```

## Interactive shell

```
ares cli
Ares CLI — type 'help' for commands, 'exit' to quit.
ares > org ACME
Organization set: ACME Corp
ares [ACME] > proj ACME-ASSESS-26-1
Project set: ACME Assessment 2026 Q1 (ACME-ASSESS-26-1)
ares [ACME/ACME-ASSESS-26-1] > assets --type host
Code                  Type                Identifier
--------------------  ------------------  --------------------------------------------------
TEST-HOST-1           host                myserver.acme.com
TEST-HOST-2           host                host-192.168.1.5
…

ares [ACME/ACME-ASSESS-26-1] > load /path/to/scan.jsonl
Uploading scan.jsonl as 'nuclei' to project ACME-ASSESS-26-1…
✓ Import completed: 14 assets, 37 new detections, 3 updated

ares [ACME/ACME-ASSESS-26-1] > exit
Goodbye.
```

## Commands

| Command | Description |
|---|---|
| `ares configure` | Set server URL and API key |
| `ares orgs` | List all organizations |
| `ares projects --org SLUG` | List projects in an organization |
| `ares cli` | Interactive shell |
| `ares assets [OPTIONS]` | List assets |
| `ares load FILE [OPTIONS]` | Upload scan file |
| `ares exploits [OPTIONS]` | Search exploits/PoCs (by CVE, text, source) |
| `ares exploits pull ID\|CVE... [OPTIONS]` | Download exploit(s) as zip |
| `ares tag {add,rm,ls} ASSET ...` | Manage tags on an asset |

### `ares assets`
```
--org SLUG       Organization slug
--project CODE   Project code
--type TYPE      Filter by type: host, ip, service, domain, web_application, …
--size N         Page size (default 50)
--page N         Page number (default 0)
--format         table (default), json, or list
```

### `ares load`
```
FILE             Path to scan output file
--org SLUG       Organization slug
--project CODE   Project code (required)
--tool ID        Tool override: nuclei, nmap, burp, wpscan, testssl, pingcastle
                 (auto-detected from filename if not specified)
```

### `ares exploits`
```
--cve ID         Filter by CVE ID (e.g. CVE-2021-44228)
-q, --query TEXT Free-text search (title/description)
--source SRC     Filter by source (repeatable): manual_git, manual_upload,
                 exploitdb, vulncheck_xdb
--size N         Page size (default 50)
--page N         Page number (default 0)
--format         table (default), json, or list (one exploit ID per line)
```

### `ares exploits pull`
```
TARGETS...       Exploit ID(s) and/or CVE ID(s) — a CVE ID pulls every
                 exploit matching it, so search-by-CVE-and-download is one command
--out DIR        Output directory (default: current directory)
```
```bash
# Download every known PoC for a CVE in one go:
ares exploits pull CVE-2021-44228 --out ./poc
```

### `ares tag`
```
ares tag add ASSET NAME [--org SLUG] [--project CODE] [--color HEX]
                 Tag an asset — creates the tag in the org if it doesn't exist yet
ares tag rm  ASSET NAME [--org SLUG] [--project CODE]
                 Remove a tag from an asset
ares tag ls  ASSET [--org SLUG] [--project CODE]
                 List tags currently on an asset
```
`ASSET` is the asset code (needs `--org`/`--project` to resolve, or the current
interactive-shell context) or a raw numeric asset ID, which skips resolution
entirely. Tags are organization-scoped.
```bash
ares tag add ACME-HOST-1 internet-facing --org ACME --project ACME-ASSESS-26-1
```

## API Key

Generate an API key in the Ares web UI under **Settings → API Keys**.
The key is shown only once — save it immediately.

The CLI stores config in `~/.ares/config.json`.
