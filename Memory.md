Got it ✅
Here’s a **Memory-Bound Troubleshooting Runbook** in **GitHub-friendly Markdown** format — complete with commands, sample outputs, and short/long-term mitigation steps, structured like the CPU one you liked earlier.

---

# 🐏 Memory Troubleshooting Runbook

## **1. Initial Assessment**

### **Check overall memory usage**

```bash
free -h
```

Example:

```
              total        used        free      shared  buff/cache   available
Mem:            32G         30G        500M        200M        1.5G        1.2G
Swap:            2G        1.0G        1.0G
```

* **`available`**: Memory that can be allocated without swapping.
* **`buff/cache`**: Page cache & buffers (can be reclaimed if needed).

---

### **Quick system snapshot**

```bash
vmstat 1
```

Example:

```
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free  buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0 1048576  500M  200M  1.3G    0    5    10    20  500  800 10  5 80  5  0
```

* **`si/so`**: Swap-in / Swap-out activity.
* **`free`** near zero with high swap-out → memory pressure.

---

## **2. Identify Memory-Heavy Processes**

```bash
ps -eo pid,ppid,user,%mem,%cpu,cmd --sort=-%mem | head
```

Example:

```
PID   PPID USER  %MEM %CPU CMD
1234  1010 web    42.3  5.4 /usr/bin/java -Xmx16g -jar app.jar
2345  1010 db     15.0  1.2 postgres: writer process
```

---

### **Per-process memory breakdown**

```bash
pmap -x <PID> | sort -k3 -n | tail
```

Example:

```
Address           Kbytes     RSS   Dirty Mode  Mapping
00007f9a1c000000  262144   260000   260000 rw---   [ anon ]
```

---

### **Smaps for detailed analysis**

```bash
grep -i rss /proc/<PID>/smaps | awk '{sum+=$2} END {print sum " KB"}'
```

* Shows total resident set size (RSS) for the process.

---

## **3. Detect Memory Leaks**

```bash
pidstat -r -p <PID> 5
```

* Increasing **RSS** over time with no drop indicates a possible leak.

---

```bash
valgrind --tool=massif ./myprogram
ms_print massif.out.xxxx
```

* For native C/C++ programs.

---

## **4. Swap Usage & Pressure**

### **Swap summary**

```bash
swapon --show
vmstat 1
```

### **Per-process swap usage**

```bash
for pid in $(ls /proc | grep -E '^[0-9]+$'); do
  if [ -r /proc/$pid/smaps ]; then
    swap=$(grep Swap /proc/$pid/smaps | awk '{sum+=$2} END {print sum}')
    if [ "$swap" -gt 0 ]; then
      echo "$pid: $swap KB $(ps -p $pid -o comm=)"
    fi
  fi
done | sort -k2 -n
```

---

## **5. Page Cache & Buffers**

```bash
cat /proc/meminfo | grep -E "Cached|Buffers|MemFree|MemAvailable"
```

Example:

```
MemFree:         500000 kB
MemAvailable:   1200000 kB
Buffers:         200000 kB
Cached:         1500000 kB
```

---

## **6. OOM Killer Events**

```bash
dmesg | grep -i "Out of memory"
journalctl -k | grep -i "killed process"
```

* If OOM Killer is triggered → check which process was killed.

---

## **7. Short-Term Mitigation**

| Action                        | Command                                                                                    | When to Use                          |
| ----------------------------- | ------------------------------------------------------------------------------------------ | ------------------------------------ |
| **Restart offending process** | `systemctl restart <service>`                                                              | Memory leak / runaway allocation     |
| **Increase swap temporarily** | `fallocate -l 4G /swapfile && chmod 600 /swapfile && mkswap /swapfile && swapon /swapfile` | Short-term breathing room            |
| **Drop caches** (⚠ not a fix) | `echo 3 > /proc/sys/vm/drop_caches`                                                        | Free page cache (safe but temporary) |
| **Reduce load**               | Scale down incoming traffic                                                                | Application overload                 |

---

## **8. Long-Term Mitigation**

* **Fix memory leaks** in applications (track via `pidstat`, `smem`, `massif`).
* **Right-size JVM/DB heap** configurations (`-Xmx`, shared buffers).
* **Add physical RAM** if workloads consistently need more.
* **Tune `vm.swappiness`** to balance RAM vs swap usage.
* **Optimize queries / caching** to reduce in-memory data footprint.
* **Use NUMA-aware allocations** for large-memory servers:

  ```bash
  numactl --interleave=all <cmd>
  ```

---

## **9. Decision Flow Summary**

1. **Check usage** → `free -h`, `vmstat`
2. **Find top consumers** → `ps --sort=-%mem`
3. **Analyze growth** → `pidstat -r`, `pmap`
4. **Check swap** → `swapon --show`, `/proc/<pid>/smaps`
5. **Look for OOM events** → `dmesg`, `journalctl`
6. **Apply short-term fix**
7. **Plan long-term remediation**
8. 

Fragmentation fix

echo 1 > /proc/sys/vm/compact_memory This forces the kernel to move pages around to free up large contiguous blocks.
Drop caches (if page cache is a big part of fragmentation):
sync; echo 3 > /proc/sys/vm/drop_caches

long term Enable Transparent Huge Pages (THP) — Kernel will try to keep large pages available:
echo always > /sys/kernel/mm/transparent_hugepage/enabled



