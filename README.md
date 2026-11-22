<div align="center">

# Windows 11 COM/DCOM Diagnostics & Repair

### PowerShell tools for investigating COM/DCOM events, registry state, and Explorer handler issues.

[![Windows](https://img.shields.io/badge/Windows-11-0078D4?logo=windows&logoColor=white)](#requirements)
[![PowerShell](https://img.shields.io/badge/PowerShell-Administrator-5391FE?logo=powershell&logoColor=white)](#read-before-running)
[![Diagnostics](https://img.shields.io/badge/Focus-COM%20%2F%20DCOM-6B4FBB)](#tools-at-a-glance)
[![License](https://img.shields.io/badge/License-GPL--3.0-2EA44F)](LICENSE)

<p>
  <a href="WIN11_FULL_UNI_REPAIR_DCOM_V3.ps1">
    <img alt="Open the COM and DCOM script" src="https://img.shields.io/badge/View-COM%2FDCOM%20Script-0078D4?style=for-the-badge&logo=powershell&logoColor=white">
  </a>
  <a href="FULL_DIAG_REPAIR_DCOM.ps1">
    <img alt="Open the Explorer diagnostics script" src="https://img.shields.io/badge/View-Explorer%20Script-6B4FBB?style=for-the-badge&logo=powershell&logoColor=white">
  </a>
  <a href="#read-before-running">
    <img alt="Read safety notes" src="https://img.shields.io/badge/Read-Safety%20Notes-C62828?style=for-the-badge&logo=github&logoColor=white">
  </a>
</p>

**⚠️ Advanced-use scripts: both perform system changes automatically. Read the warnings before running either one.**

</div>

---

> [!CAUTION]
> These scripts are **not read-only diagnostics** and are not safe to run casually. The DCOM script actively removes OLE security values, re-registers COM DLLs, and runs WMI/WinRM commands. The Explorer script can delete registry handler keys and forcibly restart Explorer. Registry backups and logs do not guarantee that changes can be rolled back.

## 🧭 Tools at a glance

| Script | Main purpose | Important behavior |
|---|---|---|
| [`WIN11_FULL_UNI_REPAIR_DCOM_V3.ps1`](WIN11_FULL_UNI_REPAIR_DCOM_V3.ps1) | Collects COM/DCOM service and event diagnostics, exports selected registry keys, then runs repair operations. | **Actively changes OLE security, COM registrations, WMI, and WinRM when run.** |
| [`FULL_DIAG_REPAIR_DCOM.ps1`](FULL_DIAG_REPAIR_DCOM.ps1) | Inspects Explorer events, selected CLSID data, and shell-extension handlers. | **Deletes context-menu handler keys with empty default values and force-restarts Explorer.** |

Both scripts require an elevated PowerShell session. Neither asks for confirmation before its active changes.

## 🔎 `WIN11_FULL_UNI_REPAIR_DCOM_V3.ps1`

### Diagnostics and attempted backups

The script checks the status of RPC, DCOM, COM+ System Application, and VSS services, and searches the System event log for DCOM event IDs **10010, 10016, 10005, and 10009** from the previous seven days.

It attempts to export selected OLE, AppID, and CLSID registry areas, plus four OLE security values. Logs and exports are written under:

- `C:\DCOM-Repair-<timestamp>.log`
- `C:\DCOM-Repair-Backup-<timestamp>\`

### Active changes

As currently written, running the script also:

1. Removes `DefaultAccessPermission`, `DefaultLaunchPermission`, `MachineAccessRestriction`, and `MachineLaunchRestriction` from `HKLM\SOFTWARE\Microsoft\Ole` when present.
2. Runs `regsvr32 /s` for `ole32.dll`, `oleaut32.dll`, `actxprxy.dll`, and `comsvcs.dll`.
3. Runs `winmgmt /salvagerepository` and `winmgmt /resetrepository`.
4. Runs `winrm quickconfig -q` and `winrm quickconfig -transport:http`.

The comments and log messages around some sections claim these actions are optional or not run by default, but the commands above are active in the script. The AppID permission helper is defined, while its example invocation remains commented out.

## 🗂️ `FULL_DIAG_REPAIR_DCOM.ps1`

This script attempts to back up selected CLSID and shell-extension registry areas to `C:\Explorer-Fix-Backup-<timestamp>\`, and writes a log to `C:\Explorer-Fix-<timestamp>.log`. It scans recent Explorer crash/hang events and uses heuristic checks for CLSID and handler entries.

It also **deletes shell-extension handler subkeys whose default value is empty**, then forcibly stops and restarts `explorer.exe`. There is no preview or confirmation prompt before these changes.

## ⚠️ Read before running

- DCOM event **10016** can be benign; an event by itself does not prove that a repair is needed.
- OLE security resets, COM DLL registration, WMI repository resets, and WinRM configuration can affect applications, services, remote management, and domain policy.
- Explorer handler cleanup can remove registry entries that an application expects, and restarting Explorer closes its current process.
- The scripts attempt to create backups, but export errors may occur and there is no automatic restore workflow.
- Test only on a machine you control. Prefer a VM or a verified system backup, and inspect the script before each run.

## ▶️ Usage

1. Review the active actions above and the corresponding script.
2. Open PowerShell as Administrator.
3. Run only the script whose effects you intend to apply:

   ```powershell
   .\WIN11_FULL_UNI_REPAIR_DCOM_V3.ps1
   ```

   or:

   ```powershell
   .\FULL_DIAG_REPAIR_DCOM.ps1
   ```

4. Review the timestamped log and backup directory. Do not treat a completion message as proof that every operation succeeded.

## 📋 Requirements

- Windows with PowerShell.
- Administrator privileges.
- A restore point or other verified recovery plan is strongly recommended before system changes.

## 📦 Repository contents

| File | Description |
|---|---|
| [`WIN11_FULL_UNI_REPAIR_DCOM_V3.ps1`](WIN11_FULL_UNI_REPAIR_DCOM_V3.ps1) | COM/DCOM diagnostics, selected registry exports, and active repair commands. |
| [`FULL_DIAG_REPAIR_DCOM.ps1`](FULL_DIAG_REPAIR_DCOM.ps1) | Explorer and shell-extension diagnostics with active handler cleanup. |
| [`LICENSE`](LICENSE) | GNU General Public License v3.0. |

## 📄 License

Distributed under the [GNU General Public License v3.0](LICENSE).

---

<div align="center">

### 🛡️ Investigate first. Back up carefully. Apply only changes you understand.

**Windows · PowerShell · COM/DCOM · Explorer**

<a href="#read-before-running">
  <img alt="Review the safety notes" src="https://img.shields.io/badge/Review-Safety%20Notes-C62828?style=for-the-badge&logo=github&logoColor=white">
</a>

</div>
