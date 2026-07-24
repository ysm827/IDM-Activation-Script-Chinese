# IDM Activation Script — Simplified Chinese Edition (v1.4.0)

[![Windows validation](https://github.com/tytsxai/IDM-Activation-Script-Chinese/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/tytsxai/IDM-Activation-Script-Chinese/actions/workflows/ci.yml)
[![License: GPL v3](https://img.shields.io/badge/License-GPL_v3-blue.svg)](./LICENSE)
[![Version](https://img.shields.io/badge/version-v1.4.0-brightgreen.svg)](./CHANGELOG.md)
[![Platform](https://img.shields.io/badge/platform-Windows%207%20%7C%208%20%7C%2010%20%7C%2011-blue.svg)](#system-requirements)

[简体中文 README](README.md) · [llms.txt for AI search](llms.txt) · [Docs](docs/README.md) · [Changelog](CHANGELOG.md) · [Issues](https://github.com/tytsxai/IDM-Activation-Script-Chinese/issues)

**IDM Activation Script Chinese** is a GPL-3.0 open-source Windows batch toolkit that wraps the
[lstprjct/IDM-Activation-Script](https://github.com/lstprjct/IDM-Activation-Script) workflow for
**Simplified Chinese Windows users**. Every script is GBK (CP936) encoded, forces `chcp 936` at
runtime, ships Chinese menus and error messages, backs up the registry before any change, and runs
offline from a single `.cmd` file — no installer, no runtime dependency, no IDM binary patching.

> This page is an English summary. The authoritative, always-current documentation is the
> [Simplified Chinese README](README.md).

## Project overview

| Field | Answer |
| --- | --- |
| What it is | A Chinese-localized batch (`.cmd`) toolkit for Internet Download Manager (IDM) on Windows |
| Problem solved | English IDM scripts garble in Chinese CMD/PowerShell consoles; users don't know which file to run, whether admin rights are needed, or how to handle SmartScreen/Defender prompts |
| Who it is for | Simplified-Chinese Windows 7/8/8.1/10/11 users, and developers studying Windows batch, registry backup, GBK console compatibility, and Windows CI |
| Tech stack | Windows Batch/CMD, PowerShell (UAC elevation + environment probes), Windows Registry / WMI / CIM, GBK / Code Page 936 + CRLF, GitHub Actions `windows-latest` |
| Modes | `[2]` activate (recommended), `[1]` freeze trial (fallback), `[3]` reset activation/trial, `[4]`/`[5]` disable or restore IDM's auto-update check (v1.4.0+) |
| Entry point | Double-click `开始激活.cmd` ("start activation") — the only file a first-time user needs |
| License | GPL-3.0, must stay public and redistributable (see [OPEN_SOURCE_POLICY.md](./OPEN_SOURCE_POLICY.md)) |
| Not included | IDM itself is not bundled; no binary patching; no enterprise policy (WDAC/AppLocker) bypass |

## Quick start

1. Download the latest ZIP from
   [Releases](https://github.com/tytsxai/IDM-Activation-Script-Chinese/releases/latest), or directly:
   [`release/IDM-Activation-Script-v1.4.0.zip`](https://github.com/tytsxai/IDM-Activation-Script-Chinese/raw/main/release/IDM-Activation-Script-v1.4.0.zip)
   ([SHA256](https://github.com/tytsxai/IDM-Activation-Script-Chinese/raw/main/release/IDM-Activation-Script-v1.4.0.zip.sha256)).
2. Optional but recommended — verify the download in PowerShell:

   ```powershell
   Get-FileHash .\IDM-Activation-Script-v1.4.0.zip -Algorithm SHA256
   ```

3. Extract, then **double-click `开始激活.cmd`** and approve the UAC prompt. The script runs an
   environment self-check first, then opens the menu.
4. Pick **`[2]` activate** — it works immediately and needs no IDM account or trial. If IDM still
   reports "unregistered" afterwards, run `[3]` reset and then `[1]` freeze instead.

### Command line (advanced)

`IAS.cmd` is the engine behind the menu and accepts flags directly from an elevated CMD:

```cmd
IAS.cmd /act                                   :: activate (recommended)
IAS.cmd /frz                                   :: freeze the current 30-day trial
IAS.cmd /res                                   :: reset activation / trial state
IAS.cmd /noupd                                 :: disable IDM's automatic update check
IAS.cmd /reupd                                 :: restore IDM's automatic update check
IAS.cmd /act /silent /log="C:\Temp\ias.log"    :: unattended run with a log file
```

`/silent` without one of `/frz` `/act` `/res` `/noupd` `/reupd` exits with code `2`.

## Activation modes — which one do I pick?

| Mode | Menu | Use it when | Trade-off |
| --- | --- | --- | --- |
| Activate | `[2]` | You never claimed the 30-day trial, or you just want IDM working now | Writes a random serial; IDM's online check may flag it as a fake serial (see FAQ Q13 in the Chinese README) |
| Freeze trial | `[1]` | You already claimed/started the 30-day trial, **or** `[2]` left IDM showing "unregistered" | Writes no serial at all, so it never triggers fake-serial detection — most stable on IDM 6.42+ |
| Reset | `[3]` | Activation state is broken, or you want to switch between the two modes above | Clears activation info back to the initial state |
| Disable update check | `[4]` | IDM keeps popping up "a new version is available" — or you want to stay on the build your activation works with | Sets `CheckUpdtVM=0`; you stop getting official fixes until you restore it with `[5]` |

## Core features

- **Single entry file** — since v1.3.6 the four legacy scripts are merged into `开始激活.cmd`;
  it self-elevates, self-checks, and shows the menu.
- **Environment self-check** — verifies admin rights, PowerShell language mode, the Null service,
  network egress, console code page, WMI/CIM, the IDM install path, and script-directory write access.
- **Automatic registry backup** — `.reg` snapshots are written before every change to
  `C:\Windows\Temp\_Backup_HKCU_CLSID_[timestamp].reg` and the matching `HKU-[SID]` file;
  double-click a `.reg` file to restore.
- **Update-nag switch (v1.4.0+)** — menu `[4]` / `[5]` (`/noupd` / `/reupd`) flips IDM's own
  `CheckUpdtVM` registry switch to stop or restore the automatic update check and its popup, which
  also prevents an auto-upgrade from silently breaking the activation.
- **No binary patching** — only IDM's registry configuration is touched; `IDMan.exe` and friends are
  never modified.
- **Chinese console correctness** — GBK source encoding, forced `chcp 936`, CRLF pinned via
  `.gitattributes` so batch parsing never breaks on LF line endings.
- **Auditable and open** — plain-text batch plus a small PowerShell helper, GPL-3.0, CI-validated on
  `windows-latest`.

## System requirements

| Item | Requirement |
| --- | --- |
| OS | Windows 7 / 8 / 8.1 / 10 / 11 (including 24H2) |
| Privileges | Administrator (requested automatically via UAC) |
| Dependencies | PowerShell (built into Windows) |
| Network | Access to `internetdownloadmanager.com` (disable VPN/proxy if the check fails) |
| Encoding | Chinese console; the script runs `chcp 936` for you |

## Known issues and limitations

- **Windows only.** The `.cmd` scripts do not run on macOS or Linux.
- **SmartScreen / antivirus warnings are expected.** Unsigned batch files that write to the registry
  and elevate privileges trigger heuristic detection. Verify the published SHA256, then use
  "More info → Run anyway", and unblock the file via Properties → "Unblock" if needed.
- **Enterprise policy layers (WDAC / AppLocker) will block execution.** That is an IT policy
  decision — ask your administrator rather than trying to work around it.
- **Viewing the source on GitHub's web UI shows garbled text**, because the files are GBK, not UTF-8.
  Use an editor that supports GBK, or `iconv -f GBK -t UTF-8 IAS.cmd`.
- **Legal use only.** Use this on hardware you control, at your own risk, in accordance with local
  law and the IDM license agreement.

## Troubleshooting highlights

| Symptom | Fix |
| --- | --- |
| Stuck at "正在初始化…" (initializing) | Upgrade to v1.3.9+ — it prefers `Get-CimInstance` over the legacy `Get-WmiObject` DCOM path that hangs on some Win11 24H2/25H2 machines. If it persists, run `winmgmt /verifyrepository` and `winmgmt /salvagerepository` from an elevated CMD |
| "Fake serial blocked / please buy IDM" after `[2]` | Run `[3]` reset, then `[1]` freeze — freeze writes no serial |
| Still "unregistered" after activation | Reset (`[3]`), then freeze (`[1]`); if it persists, reinstall IDM and retry |
| Some web pages stop loading after activation | This is IDM's browser integration module, not the script (it never touches hosts, firewall, or proxy). Disable the IDM browser extension or add the domain to IDM's "do not capture" list |
| "IDM not installed" | Confirm `IDMan.exe` exists under `C:\Program Files (x86)\Internet Download Manager\`; a portable/incomplete install may not have written the registry path |
| IDM keeps prompting to update | Menu `[4]` (`IAS.cmd /noupd`) disables IDM's update check; `[5]` (`/reupd`) restores it. Fully exit IDM (tray icon included) before reopening it |

Full FAQ (15 entries, in Chinese): [README.md#-常见问题](README.md#-常见问题).

## Files

| File | Purpose |
| --- | --- |
| `开始激活.cmd` | The only file a new user double-clicks: UAC elevation → environment self-check → activation menu |
| `IAS.cmd` | Core engine (GBK batch), also accepts `/frz` `/act` `/res` `/noupd` `/reupd` `/silent` `/log=` |
| `使用说明.txt` | Minimal quick-start guide in UTF-8, readable in Notepad |
| `release/` | Published ZIPs plus their `.sha256` checksums |
| `docs/` | Documentation map, maintenance checklist, per-version release notes |

## Credits and license

Chinese localization and maintenance fork by [tytsxai](https://github.com/tytsxai). The original tool
is [lstprjct/IDM-Activation-Script](https://github.com/lstprjct/IDM-Activation-Script). Licensed under
**GPL-3.0** — see [LICENSE](./LICENSE). Any redistribution must keep the license, copyright notice,
and a record of modifications.

## Disclaimer

This repository is published for research, localization, and educational purposes. It does not
distribute IDM, and it does not modify IDM program files. Internet Download Manager is a commercial
product of Tonec Inc.; if you use it long-term, please buy a license. You are responsible for
complying with the laws of your jurisdiction and with the IDM license agreement.

---

**Search keywords**: IDM Activation Script Chinese, IDM activation script, Internet Download Manager
activation, IDM freeze trial, IDM trial reset, IDM Windows 11 activation, IDM Windows 10 activation,
IDM batch script, GBK CP936 console, Chinese localization fork, registry backup batch script.
