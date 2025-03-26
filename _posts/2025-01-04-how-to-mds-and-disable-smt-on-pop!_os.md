---
tags: pop-os security
---

## How to mitigate MDS and disable SMT in POP!\_OS

Over the weekend I exchanged the battery of my System76 machine when I realized a bug in my boot log `journalctl -b`. The following two lines made me curious:

```bash
Jan 03 21:36:02 kernel: MDS CPU bug present and SMT on, data leak possible. See https://www.kernel.org/doc/html/latest/admin-guide/hw-vuln/mds.html>
Jan 03 21:36:02 kernel: MMIO Stale Data CPU bug present and SMT on, data leak possible. See https://www.kernel.org/doc/html/latest/admin-guide/hw-vuln/mds.html->
```

To see if your processor is vulnerable to Microarchitectural Data Sampling (MDS) and if Simultaneous Multithreading (SMT) is enabled, the [above linked guide](https://www.kernel.org/doc/html/next/x86/mds.html) suggests to run `cat /sys/devices/system/cpu/vulnerabilities/mmio_stale_data` that outputs the following in my case and means that mitigation was active, but SMT is still enabled and vulnerable.

```bash
Mitigation: Clear CPU buffers; SMT vulnerable
```

Although MDS vulnerability is very hard to exploit, as discussed [here](https://askubuntu.com/a/1383267) keeping an open door to be exploited does not feel good. Also, in the light of increasing presence of Webassembly and unknown possibilites with JavaScript in the context of MDS, it makes sense to close the door completely - taking a performance penalty into account.

To be on the safe side I decided to turn off SMT (and agreeing to a decreased CPU node number from 8 to 4). To turn SMT off, you need to add a boot parameter to the kernel command line. On Ubuntu you can edit your grub config to adjust the kernel command line. In POP!\_OS boot loading is managed through systemd-boot and can be edited as follows:

```bash
sudo nano /etc/kernelstub/configuration
```

Add the following parameter `mds=full,nosmt` to mitigate MDS and disable SMT under `user`:

```json
  "user": {
    "kernel_options": [
      "mds=full,nosmt"
    ],
  }

```

Finally, save your changes and reboot your machine to check if SMT has been disabled. The expected output should be:

```bash
Mitigation: Clear CPU buffers; SMT disabled
```
