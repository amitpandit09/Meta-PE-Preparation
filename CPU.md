**CPU**

1. uptime
2. top (at this moment what's going on)

```
top - 14:32:10 up 5 days,  2:17,  3 users,  load average: 0.84, 0.72, 0.65
Tasks: 203 total,   1 running, 202 sleeping,   0 stopped,   0 zombie
%Cpu(s):  5.6 us,  2.3 sy,  0.0 ni, 91.5 id,  0.3 wa,  0.0 hi,  0.3 si,  0.0 st
MiB Mem :  32000 total,  31000 used,   1000 free,   2000 buff/cache
MiB Swap:   2048 total,     50 used,   1998 free.   2500 avail Mem
```
 - Many running tasks ? CPU bottleneck; many zombies = process cleanup issues.
   - High number of sleeping process ?
      - ps -eo pid,stat,wchan:32,cmd | awk '$2 ~ /^[SD]/' where
        - awk '$2 ~ /^[SD]/' just means "In each line, check if the second column starts with S or D"
 - CPU bound issue ? 
   - us → user-space CPU usage
     - check which is causing high CPU `ps -eo pid,ppid,user,%cpu,%mem,cmd --sort=-%cpu | head`
       ```
       PID  PPID USER     %CPU %MEM CMD
       12345  1020 alice    92.3  4.5 /usr/bin/python3 data_analysis.py
       23456  1020 bob      45.1  1.2 /usr/lib/firefox/firefox
       ```
        - check which thread is consuming high cpu `top -Hp 12345`
        - check kernal stack `cat /proc/12345/stack`. Shows the thread is waiting on a futex (lock).
          ```
          [<0>] futex_wait_queue_me+0xcd/0x120
          [<0>] futex_wait+0xf2/0x230
          ```
        - check system calls `strace -p 12345 -s 80 -c`. Most time in read() → maybe it’s processing a huge file.
          ```
          % time     seconds  usecs/call     calls    errors syscall
          ------ ----------- ----------- --------- --------- ----------------
           90.00    0.900000        3000       300           read
            5.00    0.050000         100       500           futex
          ```
          - write/read/fsync → disk I/O bottleneck
          - sendto/recvfrom → network-heavy
          - futex/sched_yield → thread contention
          - mmap/brk → memory allocation overhead
        - Short term mitigation :
          - check nice value `ps -o pid,comm,ni -p <PID>`
          - Renice `renice +10 -p 12345`
          - Monitor over the time `pidstat -u -p ALL 1`
   - sy → kernel/system time
   - wa → I/O wait (disk/network slowness indicator)
     - High interrupts ?
       - Check if interrupts or kernel threads are the cause `mpstat -P ALL 1`
         ```
         12:01:32 AM  CPU   %usr   %nice    %sys %iowait  %irq %soft  %idle
         12:01:33 AM  all   5.00    0.00   75.00    5.00  10.00  5.00   0.00
         12:01:33 AM    0   4.00    0.00   80.00    2.00  12.00  2.00   0.00
         12:01:33 AM    1   6.00    0.00   70.00    8.00   8.00  8.00   0.00
         ```
       - %irq and %soft also elevated — likely network or disk interrupts.
       - cat /proc/interrupts
         ```
                    CPU0       CPU1
           24:  123456789          0   PCI-MSI  eth0
           25:   98765432          0   PCI-MSI  nvme0q0
         ```
       - Correlate with I/O. `iostat -x 1 3`
         ```
         Device:         rrqm/s   wrqm/s     r/s     w/s  rkB/s  wkB/s  %util
         nvme0n1           0.00     5.00  100.00  500.00 2000.0 15000.0  95.00
         ```
       - Short term fix `renice +10 -p 12345`
     - High I/O wait %iowait ?
       - In top or mpstat,
         ```
         %Cpu(s): 10.0 us,  5.0 sy,  0.0 ni, 60.0 id, 25.0 wa,  0.0 hi,  0.0 si,  0.0 st
         ```
       - Confirm with vmstat 1
         ```
         procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
         r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
         1  5      0 204800 102400 512000    0    0 20480 10240 1200 1500  5  3 50 42  0
         ```
       - check further bottleneck via `iostat -x 1 5`
         ```
         Device:         r/s     w/s   rkB/s   wkB/s  await  svctm  %util
         nvme0n1     1000.0  2000.0  80000.0 160000.0   25.0   0.2   99.0
         ```
       - Find which processes are causing I/O `pidstat -d 1`
         ```
         Time     UID  PID   kB_rd/s  kB_wr/s kB_ccwr/s Command
         12:12:01 1000 12345 102400.0   1024.0      0.0  mysqld
         12:12:01 1000 23456      0.0 204800.0      0.0  backup
         ```
   - st → stolen time (VMs competing for CPU)
   - ps -eo pid,ppid,cmd,%cpu --sort=-%cpu | head
 - Memory:
   - used, free, and buff/cache → Linux uses cache aggressively; look at avail Mem to judge real availability.
 - Swap:
   - High used and active swap-in/out → possible memory pressure.
3. High context switiching ? `vmstat 1`
 - High cs (context switches) → possibly too many threads/processes or lock contention.
 - pidstat -w 1
 - Watch for CPU throttling / thermal limits `dmesg | grep -i thrott`
