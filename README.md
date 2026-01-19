# Windows Encrypted DNS Configurator

A PowerShell script that configures **DNS over HTTPS (DoH)** and **DNS over TLS (DoT)** on Windows with Cloudflare as primary and Google as secondary DNS providers.

## Features

- Configures encrypted DNS with a single command
- Auto-detects active network adapter (works on any machine)
- Sets up both **DoH** and **DoT** for redundancy
- Supports **IPv4** and **IPv6**
- Properly configures Windows Settings UI dropdowns
- No manual registry editing required

## DNS Configuration

| Role | Provider | IPv4 | IPv6 |
|------|----------|------|------|
| **Primary** | Cloudflare | `1.1.1.1` | `2606:4700:4700::1111` |
| **Secondary** | Google | `8.8.8.8` | `2001:4860:4860::8888` |

## Requirements

- Windows 11 or Windows 10 (Build 19628+)
- Administrator privileges
- PowerShell 5.1+

## Usage

### Option 1: Direct Download & Run

```powershell
# Run as Administrator
powershell -ExecutionPolicy Bypass -File configure-dns-doh-dot.ps1
```

### Option 2: One-liner (download and execute)

```powershell
irm https://raw.githubusercontent.com/YOUR_USERNAME/YOUR_REPO/main/configure-dns-doh-dot.ps1 | iex
```

## What It Does

1. **Registers DoH templates** for Cloudflare and Google DNS servers
2. **Detects your active network adapter** automatically
3. **Sets DNS servers** (IPv4 and IPv6) via `netsh`
4. **Configures DoH** in the registry with correct flags (`0x11` QWORD)
5. **Configures DoT** with proper hostnames for TLS validation
6. **Restarts DNS cache** to apply changes immediately

## Verification

After running the script, verify the configuration:

1. Open **Settings** > **Network & internet** > **Wi-Fi** (or Ethernet)
2. Click on your network > **Hardware properties**
3. Scroll to **DNS server assignment** > **Edit**
4. Confirm the dropdowns show **"On (automatic)"** for DNS over HTTPS

Or verify via PowerShell:

```powershell
Get-DnsClientServerAddress -AddressFamily IPv4 | Select-Object InterfaceAlias, ServerAddresses
Get-DnsClientServerAddress -AddressFamily IPv6 | Select-Object InterfaceAlias, ServerAddresses
```

## Registry Paths

The script configures the following registry locations:

```
HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\InterfaceSpecificParameters\{GUID}\
├── DohInterfaceSettings\
│   ├── Doh\          # IPv4 DoH settings
│   └── Doh6\         # IPv6 DoH settings
└── DotInterfaceSettings\
    ├── Dot\          # IPv4 DoT settings
    └── Dot6\         # IPv6 DoT settings
```

## Troubleshooting

**Settings UI still shows "Off"?**
- Restart your computer, or
- Disable and re-enable your network adapter

**Script fails to find adapter?**
- Ensure you have an active network connection
- Virtual adapters (VPN, Hyper-V) are automatically excluded

**DNS not resolving?**
- Run `ipconfig /flushdns`
- Restart the DNS Client service: `Restart-Service Dnscache`

## License

MIT
