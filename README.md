# v3x4
Intel(R) Xeon(R) Processor v3 (Haswell-E/EP) Full Turbo Boost DXE driver

Description: Programs Haswell-E/EP Xeon(R) processors (cpuid = 306F2h) on X99 (single) and C612 (dual) platforms to allow for maximum all-core turbo boost for all cores regardless of whether there are motherboard options present for overclocking/voltage control or not. {For example, the 18-core Xeon(R) E5-2696 v3 processor has set from the factory an all-core turbo of 2.8GHz. This driver programs the highest un-fused ratio (i.e. the 1C Turbo bin) as the new Turbo bin for all boost configurations including all-core turbo. In other words, the 1C turbo bin becomes the all-core turbo bin and the E5-2696 v3 processor now demonstrates an all-core turbo of 3.8GHz.} Allows for dynamically undervolting (retains PCU control while applying a fixed negative Vcore offset) Core, CLR (CBo/LLC/Ring), and System Agent (SA) voltage domains independently per package which provides for higher all-core sustained clocks during heavy workloads, including AVX2 workloads. Allows for setting static Uncore ratio (typical 30x) for maximum performance (lowest typical access latency and accompanying maximum throughput). Driver is designed to work on up to 4-way SMP system. Verified functional on 2-way E5-2696 v3 on ASUS Z10PE-D8 WS with accompanying modified BIOS (remove an microcode revision update patches). May work for other Intel Xeon steppings including Broadwell-E/EP (untested as of yet), and possibly even IVY-E/EP  and SNB-E/EP (also, untested as of yet).

Successful use requirements:

- Haswell-E/EP processor (cpuid = 306F2h) or processors. This can be overriden with special build flag at compile time

- CPU microcode revision patch must not be loaded during POST process (requires modified BIOS)

-- instructions on how to modify BIOS to remove microcode will not be provided here

- Use EFI Shellx64 or other suitable UEFI shell to set automatic load (and execution) of v3x4.efi during system boot

-- use cmd ' bcfg driver add 0 fs1:\EFI\Boot\v3x4.efi "V3 Full Turbo" ' where 'fs1:\EFI\Boot\v3x4.efi' is path to DXE driver file on UEFI boot partition (use cmd 'mountvol x: /s' to mount in Windows as drive X: for writing) {Note: there is a bug which prevents accessing the mounted drive through File Explorer in Windows 10 RS2. Workaround is to open Task Manager and use new process launch dialog to navigate to mounted drive and copy directly}

-- toggle enable/disable in BIOS (if presented as an option; default: enabled)

"Features:"
- Programming MSRs can be a fickle thing... you may see BSOD during boot immediately following toggle enable/disable or other situations where you attempt to write to locked MSRs during runtime (e.g. in OS).  Don't panic... a cold reboot is often the cure
- If you program too low an offset voltage for any of the enabled domains, this is the equivalent of setting too low a VID in BIOS and expecting your system to be remain stable. Since this is always done (no recovery), then to recover you would need to temporarily disconnect the disk source for the drive binary (preventing load with automatic bypass). Similiar to the trial-and-error method of overclocking, so will there be here, too
- Driver will abort if microcode revision update patch detected and there is no point in defeating this feature as this is a hard requirement for successful driver execution
- Driver will abort if not expected CPUID. This can be programmed to whatever you want or entirely overridden using special build flag at compile time. At this time, only Haswell-E/EP has been verified workable

To compile for point-releases, in Windows you will need:

1) UDK2015: http://www.tianocore.org/udk/udk2015/

2) Any C-compiler that is supported by UDK2015. Currently verified working with Visual Studio 2015.

3) In file [UDK2015]\BaseTools\Conf\target.txt change next parameters to:

ACTIVE_PLATFORM = MdeModulePkg/MdeModulePkg.dsc

TARGET = RELEASE

TARGET_ARCH = X64

TOOL_CHAIN_TAG = VS2015x86 (or VS2013x86, etc.)

4) open command prompt (as admin) and go to folder [UDK2015]\BaseTools

5) execute edksetup.bat

6) execute build

If it prints - Done - all is fine and UDK2015 is functioning properly...
 
7) unpack source into [UDK2015]\BaseTools\MdeModulePkg\v3x4

8) in file [UDK2015]\BaseTools\MdeModulePkg\MdeModulePkg.dsc, in section [Components], add string MdeModulePkg/v3x4/v3x4.inf

9) execute build again

10) wait for - Done - and take compiled EFI DVX driver from
[UDK2015]\BaseTools\Build\MdeModule\RELEASE_VS2013x86\X64\MdeModulePkg\v3x4\v3x4\OUTPUT\v3x4.efi
