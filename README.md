# Lab 0 — Inventory Your Own Machine

## System Information

- Operating system: Windows 11
- Collection method: PowerShell command-line queries
- Evidence: saved command outputs in `evidence/`

## Hardware Inventory

| What | Value | Where I got it (command) |
|---|---|---|
| CPU model | AMD Ryzen 7 7700 8-Core Processor | `Get-CimInstance Win32_Processor` |
| CPU physical cores | 8 | `Get-CimInstance Win32_Processor` (`NumberOfCores`) |
| CPU logical threads | 16 | `Get-CimInstance Win32_Processor` (`NumberOfLogicalProcessors`) |
| Total installed RAM | 32 GB | `Get-CimInstance Win32_PhysicalMemory` (`Capacity`, 2 x 16 GB) |
| Installed RAM modules | 2 x 16 GB, manufacturer Unknown | `Get-CimInstance Win32_PhysicalMemory` (`Manufacturer`, `Capacity`) |
| RAM module speed / configured speed | 4800 / 5600 MT/s (as reported by Windows) | `Get-CimInstance Win32_PhysicalMemory` (`Speed`, `ConfiguredClockSpeed`) |
| SSD | HS-SSD-FUTURE Pro 2048G, 1907.73 GiB | `Get-PhysicalDisk` (`FriendlyName`, `MediaType`, `Size`) |
| HDD | ST2000DM008-2UB102, 1863.02 GiB | `Get-PhysicalDisk` (`FriendlyName`, `MediaType`, `Size`) |
| C: volume total / free | 1906.73 / 1064.15 GiB | `Get-Volume` (`Size`, `SizeRemaining`) |
| D: volume total / free | 1863.00 / 1862.84 GiB | `Get-Volume` (`Size`, `SizeRemaining`) |
| Firmware mode | UEFI | `Get-ComputerInfo | Select-Object BiosFirmwareType` |
| BIOS manufacturer | American Megatrends International, LLC. | `Get-CimInstance Win32_BIOS` (`Manufacturer`) |
| BIOS version | B3.9D | `Get-CimInstance Win32_BIOS` (`SMBIOSBIOSVersion`) |
| BIOS release date | 2025-02-13 | `Get-CimInstance Win32_BIOS` (`ReleaseDate`) |
| Virtualization enabled in firmware | True | `Get-CimInstance Win32_Processor` (`VirtualizationFirmwareEnabled`) |
| VM monitor mode extensions reported | False | `Get-CimInstance Win32_Processor` (`VMMonitorModeExtensions`) |
| SLAT extensions reported | False | `Get-CimInstance Win32_Processor` (`SecondLevelAddressTranslationExtensions`) |
| Hypervisor running | True | `Get-CimInstance Win32_ComputerSystem | Select-Object HypervisorPresent` |

*Storage values are displayed in GiB (the PowerShell evidence calculates `Size / 1GB`, where PowerShell `1GB` equals 2^30 bytes).*

## Firmware

The system uses **UEFI** firmware. The BIOS manufacturer is American Megatrends International, LLC.; version **B3.9D**, release date **2025-02-13**. Firmware information was collected using `Get-ComputerInfo` and `Get-CimInstance Win32_BIOS`. See `evidence/firmware.txt`.

## Virtualization

The Windows PowerShell queries report `VirtualizationFirmwareEnabled = True` and `HypervisorPresent = True`. The same processor query reports `VMMonitorModeExtensions = False` and `SecondLevelAddressTranslationExtensions = False`. A running hypervisor can affect how those feature flags are exposed. These `False` results alone do not demonstrate that AMD Ryzen 7 7700 lacks hardware virtualization or SLAT. See `evidence/virtualization.txt` and `evidence/hypervisor.txt`.

**Conclusion:** Firmware virtualization is enabled and Windows detects a running hypervisor. No BIOS change is indicated by the collected evidence.

## Virtual Machine Storage

I plan to store VMs on the **C:** SSD because it offers faster storage than the HDD and has **1064.15 GiB free**. The D: HDD has 1862.84 GiB free and is an alternative for bulk storage.

## Evidence Files

- `evidence/cpu.txt` — output of `Get-CimInstance Win32_Processor | Select-Object Name,NumberOfCores,NumberOfLogicalProcessors | Format-List`
- `evidence/memory.txt` — output of `Get-CimInstance Win32_PhysicalMemory` with manufacturer, slot, capacity and speed fields
- `evidence/disk.txt` — output of `Get-PhysicalDisk` and `Get-Volume` with selected size and free-space fields
- `evidence/firmware.txt` — output of `Get-ComputerInfo` and `Get-CimInstance Win32_BIOS`
- `evidence/virtualization.txt` — output of `Get-CimInstance Win32_Processor` with virtualization fields
- `evidence/hypervisor.txt` — output of `Get-CimInstance Win32_ComputerSystem | Select-Object HypervisorPresent | Format-List`

All measurements in this report come from saved PowerShell command outputs, not graphical system information screens.
