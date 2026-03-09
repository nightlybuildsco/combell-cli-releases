# combell-cli

A command-line interface for managing Combell services via the [Combell API](https://api.combell.com/v2/documentation).

## Installation

### Homebrew

```bash
brew install nightlybuildsco/tap/combell-cli
```

### Download binary

Download the latest release from the [releases page](https://github.com/nightlybuildsco/combell-cli/releases) for your platform (Linux, macOS, Windows — amd64 and arm64).

### Build from source

Requires [Go](https://go.dev/) 1.25 or later.

```bash
git clone https://github.com/nightlybuildsco/combell-cli.git
cd combell-cli
go build -o combell-cli .
```

## Configuration

Before using the CLI, configure your Combell API credentials:

```bash
# Interactive
combell-cli configure

# With flags
combell-cli configure --api-key=YOUR_KEY --api-secret=YOUR_SECRET

# Custom API endpoint
combell-cli configure --base-uri=https://api.combell.nl
```

Credentials are stored in `~/.combell-cli` as JSON.

You can also set credentials via environment variables (these take precedence over the config file):

```bash
export COMBELL_API_KEY=your-key
export COMBELL_API_SECRET=your-secret
```

## Usage

```bash
combell-cli <command> [subcommand] [arguments] [flags]
```

### Global flags

| Flag | Description |
|---|---|
| `--format <type>` | Output format: `table` (default), `json`, `csv` |
| `--force` | Skip confirmation prompts for destructive actions |
| `--verbose` | Enable verbose output |

### Commands

#### accounts

```bash
combell-cli accounts list [--skip N] [--take N]
combell-cli accounts get <accountId>
combell-cli accounts create --identifier <id> --servicepack-id <id> --ftp-password <pw>
```

#### domains

```bash
combell-cli domains list [--skip N] [--take N]
combell-cli domains get <domainName>
combell-cli domains register <domainName>
combell-cli domains transfer <domainName> --auth-code <code>
combell-cli domains set-nameservers <domainName> --nameservers ns1.example.com,ns2.example.com
combell-cli domains set-renewal <domainName> --will-renew
```

#### dns

```bash
combell-cli dns list <domainName> [--skip N] [--take N] [--type A]
combell-cli dns get <domainName> <recordId>
combell-cli dns create <domainName> --type A --name www --content 1.2.3.4 [--ttl 3600] [--priority 10]
combell-cli dns update <domainName> <recordId> [--type A] [--name www] [--content 1.2.3.4] [--ttl 3600]
combell-cli dns delete <domainName> <recordId>
```

#### linuxhostings

```bash
combell-cli linuxhostings list [--skip N] [--take N]
combell-cli linuxhostings get <domainName>
combell-cli linuxhostings php-versions <domainName>
combell-cli linuxhostings set-php-version <domainName> --version 8.3
combell-cli linuxhostings set-gzip <domainName> --enabled
combell-cli linuxhostings set-http2 <domainName> <siteName> --enabled
combell-cli linuxhostings set-letsencrypt <domainName> <hostname> --enabled
combell-cli linuxhostings set-ssl-redirect <domainName> <hostname> --enabled
combell-cli linuxhostings create-subsite <domainName> --subdomain sub --path /sub
combell-cli linuxhostings delete-subsite <domainName> <siteName>
combell-cli linuxhostings add-hostheader <domainName> <siteName> --domain extra.example.com
combell-cli linuxhostings set-ftp <domainName> --enabled
combell-cli linuxhostings set-ssh <domainName> --enabled
combell-cli linuxhostings set-php-memory <domainName> --limit 256
combell-cli linuxhostings set-php-apcu <domainName> --enabled [--size 64]
```

Scheduled tasks:

```bash
combell-cli linuxhostings tasks list <domainName>
combell-cli linuxhostings tasks get <domainName> <taskId>
combell-cli linuxhostings tasks create <domainName> --cron "0 * * * *" --script /path/to/script.sh
combell-cli linuxhostings tasks update <domainName> <taskId> [--enabled] [--cron "..."] [--script ...]
combell-cli linuxhostings tasks delete <domainName> <taskId>
```

SSH keys:

```bash
combell-cli linuxhostings ssh-keys list <domainName>
combell-cli linuxhostings ssh-keys add <domainName> --public-key "ssh-ed25519 ..."
combell-cli linuxhostings ssh-keys delete <domainName> <fingerprint>
```

#### windowshostings

```bash
combell-cli windowshostings list [--skip N] [--take N]
combell-cli windowshostings get <domainName>
```

#### mailboxes

```bash
combell-cli mailboxes list [--skip N] [--take N] [--domain example.com]
combell-cli mailboxes get <mailboxName>
combell-cli mailboxes create --email user@example.com --account-id <id> --password <pw>
combell-cli mailboxes delete <mailboxName>
combell-cli mailboxes set-password <mailboxName> --password <pw>
combell-cli mailboxes set-autoreply <mailboxName> --enabled --subject "Away" --message "I'm away"
combell-cli mailboxes set-autoforward <mailboxName> --enabled --emails fwd@example.com --copy-to-myself
```

#### mailzones

```bash
combell-cli mailzones get <domainName>
combell-cli mailzones set-antispam <domainName> --type <type>
combell-cli mailzones create-catchall <domainName> --email catch@example.com
combell-cli mailzones delete-catchall <domainName> <emailAddress>
combell-cli mailzones create-alias <domainName> --email alias@example.com --destinations dest1@example.com,dest2@example.com
combell-cli mailzones update-alias <domainName> <emailAddress> --destinations dest@example.com
combell-cli mailzones delete-alias <domainName> <emailAddress>
combell-cli mailzones add-smtp-domain <domainName> --hostname smtp.example.com
combell-cli mailzones update-smtp-domain <domainName> <hostname> --enabled
combell-cli mailzones delete-smtp-domain <domainName> <hostname>
```

#### mysql

```bash
combell-cli mysql list [--skip N] [--take N]
combell-cli mysql get <databaseName>
combell-cli mysql create --name <name> --account-id <id> --password <pw>
combell-cli mysql delete <databaseName>
```

Database users:

```bash
combell-cli mysql users list <databaseName>
combell-cli mysql users create <databaseName> --name <user> --password <pw>
combell-cli mysql users set-status <databaseName> <userName> --enabled
combell-cli mysql users set-password <databaseName> <userName> --password <pw>
combell-cli mysql users delete <databaseName> <userName>
```

#### ssl

```bash
combell-cli ssl list [--skip N] [--take N]
combell-cli ssl get <fingerprint>
combell-cli ssl download <fingerprint>
```

SSL certificate requests:

```bash
combell-cli ssl-requests list [--skip N] [--take N]
combell-cli ssl-requests get <id>
combell-cli ssl-requests create --csr <csr> --type <type> --validation-level <level>
combell-cli ssl-requests verify <id>
```

#### servicepacks

```bash
combell-cli servicepacks list
```

#### jobs

```bash
combell-cli jobs get <jobId>
```

#### version

```bash
combell-cli version
```

## Development

### Running tests

```bash
go test ./... -v -race
```

### Running the linter

```bash
golangci-lint run
```

### Project structure

```
combell-cli/
├── cmd/              # CLI command definitions (one file per command group)
├── internal/
│   ├── api/          # API client, authentication, HTTP helpers
│   ├── config/       # Configuration loading/saving
│   └── output/       # Output formatting (table, JSON, CSV)
├── main.go           # Entrypoint
├── go.mod
└── go.sum
```

## License

MIT
