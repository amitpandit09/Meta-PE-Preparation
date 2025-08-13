Command cheatsheet

**1. Disk related issue**
 
 1. df -h /var (Confirm)
    ```
    Filesystem  Size  Used Avail Use% Mounted on
    /dev/xvdf   100G   92G  8.0G  92%  /var
    ```
 2. df -i /var (Check inode usage..if 100%..then too many files)
    ```
    Filesystem 512-blocks Used Available Capacity iused ifree %iused  Mounted on
    devfs             407  407         0   100%     704     0  100%   /dev
    ```
 3. du -xh --max-depth=1 /var 2>/dev/null | sort -hr | head -n 10 (Check which directories are occupying disk..the top 10 dirs)
    ```
    1.5G    /var
    1.2G    /var/log
    850M    /var/log/journal
    420M    /var/lib
    ..
    ```
    can add `--max-depth=1` if incase we feel above command will be slow
 4. find /var/cache -type f -size +100M -exec ls -lh {} + | sort -k5 -h | tail -20 (list the files having higest size..)
    + -exec → Run a command on every file found.
    + ls -lh → List files in long format (-l) with human-readable sizes (-h).
    + {} → Placeholder replaced by file path(s) from find.
    + Pass multiple files to ls in one go (faster than running ls for each file individually).
    + -k5 → Sort by 5th column of the ls -lh output (that’s the file size column).
    + -h → Sort human-readable numbers (e.g., 1K < 1M < 1G instead of lexicographic order).

    ```
    -rw-r--r--  1 root root  105M Jan 15  2025 /var/cache/package1.tar.gz
    -rw-r--r--  1 root root  110M Feb  8  2025 /var/cache/app/cachefile.db
    -rw-r--r--  1 root root  115M Dec 12  2024 /var/cache/fonts/cache.bin
    -rw-r--r--  1 root root  120M Feb 27  2025 /var/cache/images/img01.iso
    ```
 5. Mitigate temporarily.
    1. *Delete* : `rm /var/cache/some-bigfile`
    2. *Move them to another filesystem* : `mv /var/cache/bigfile /mnt/backupdisk/`
    3. *Truncate logs if it’s log files:* : `: > /var/log/huge.log`

 
