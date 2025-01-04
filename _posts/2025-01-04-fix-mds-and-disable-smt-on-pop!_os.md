---
tags: popos,mds
---

## Mitigate Microarchitectural Data Sampling and disable Simultaneous Multithreading in POP!\_OS

Over the weekend I exchanged the battery of my System 76 machine when I realized a bug in my boot log `journalctl -b`. The following two lines made me curious:

```bash
Jan 03 21:36:02 kernel: MDS CPU bug present and SMT on, data leak possible. See https://www.kernel.org/doc/html/latest/admin-guide/hw-vuln/mds.html>
Jan 03 21:36:02 kernel: MMIO Stale Data CPU bug present and SMT on, data leak possible. See https://www.kernel.org/doc/html/latest/admin-guide/hw-vuln/mds.html->
```

To see if your processor is vulnerable to MDS and if SMT is enable, the guide suggests to run `cat /sys/devices/system/cpu/vulnerabilities/mmio_stale_data` which output the following in my case and means that mitigation was active but SMT is still vulnerable.

```bash
Mitigation: Clear CPU buffers; SMT vulnerable
```

To be on the safe side I decided to turn of SMT which comes with a performance loss. To turn SMT off, you need to add a boot parameter to the kernel command line. On Ubuntu you can edit your grub config to adjust the kernel command line. In POP!\_OS boot is managed through systemd-boot and can be edited as follows:

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

Finally, reboot and check if SMT has been disabled. The expected output should be:

```bash
Mitigation: Clear CPU buffers; SMT disabled
```
