# Troubleshooting Log

Problems I hit while building the lab, what I checked, and what fixed them.

## 1. No ARM64 version of Windows Server

- **Symptom:** My plan was VMware Fusion with Windows Server ARM64. Microsoft's official Server downloads are x64 only.
- **Fix:** Used UTM in Emulate mode (x86_64) for DC01 with the official x64 ISO.
- **Follow-on change:** DC01 and CLIENT01 must be on the same virtual network, and Fusion and UTM can't easily share one. So the whole lab moved to UTM.
- **Takeaway:** Check that your host CPU architecture matches the software before you plan the lab. Emulation works but is slower.

## 2. "Windows cannot find the Microsoft Software License Terms"

- **Symptom:** Setup started, then showed this error, canceled the installation and restarted. It happened on attempts 1 and 2.
- **Checked:**
  - ISO size was 5,044,094,976 bytes, which looked complete.
  - SHA-256 matched Microsoft's published value, so the file was not corrupted.
  - `hdiutil attach` failed on both ISOs with "Resource temporarily unavailable." That's a macOS mounting problem, not a bad ISO.
  - The UEFI boot prompt "Press any key to boot from CD or DVD" timed out until I pressed a key quickly. That got Setup loading, but the error came back.
- **Fix:** Created a new VM with SPICE tools unticked and the CD/DVD interface set to IDE. Setup then reached the license terms page and installed.
- **Takeaway:** The ISO was fine. The problem was how the emulated drive was attached. I changed two settings at once, so I can't say which one fixed it. Next time I'll change one at a time.

## 3. Setup showed existing partitions on "Where do you want to install"

- **Symptom:** Drive 0 already had three partitions (100 MB System, 16 MB MSR, 59.9 GB Primary with 51.0 GB free) when I expected a blank disk.
- **Cause:** The first install phase had already copied files, then the VM rebooted into the ISO instead of the installed disk.
- **Fix:** Ejected the ISO and booted from the disk. Setup continued to the Administrator password screen.
- **Takeaway:** After Windows Setup's first phase, remove the install media so the VM boots from the disk.

## Things that weren't problems

- **`Tentative` address state:** `New-NetIPAddress` showed the new IP as `Tentative`. That's Windows checking for a duplicate address, and it cleared on its own.
- **Old DNS suffix:** `mynetworksettings.com` stays listed from the DHCP lease until the server is promoted and gets `corp.local`.
