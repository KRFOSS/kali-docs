---
title: Verifying USB Write
description:
icon:
weight: 75
author: ["serval123",]
---

After writing the Kali ISO to a USB, it is a good practice to verify that :

1. Files were copied correctly to the USB

2. The USB is bootable.

3. Making sure that the ISO wasn't corrupted.

## Check USB Contents

### On Linux/MacOS

```console
kali@kali:~$ lsblk
```

You should see partitions like boot/efi,live after running the above command.

### On Windows

You can use disk manager to view the partitions created on the USB.
**Note that windows will not recognize the file system used in the USB and will mark it as corrupted and will prompt you to format it. Do not format it.**

## Verifying the Checksum

### On Linux/MacOS

```console
kali@kali:~$ shasum -a 256 kali-linux-2025.1-live-amd64.iso 
```
Replace the iso file name with the one you downloaded.Verify the checksum with the one provided on the page from which you downloaded the iso.

### On Windows

If certutil is available you can run :

```
certutil -hashfile kali-linux-2025.1-live-amd64.iso sha256
```

To verify your download.Certain versions of Windows do not have the native ability to calculate SHA256 checksums. If you do not have certutil installed, you can use a utility such as Microsoft File Checksum Integrity Verifier or Hashtab to verify your download.

Finally try booting from the USB.

## Troubleshooting

### USB not booting

1. Ensure that you used Balena Etcher to flash the iso.
2. Disable secure boot in BIOS if it is enabled.

### Checksum mismatch

This means that your iso file was corrupted during download. Re-download the kali iso and then write it again on the USB.








