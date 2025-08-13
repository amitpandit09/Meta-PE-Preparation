Here’s a **GitHub-ready `.md` runbook** for troubleshooting a **“Too Many Open Files”** issue (`EMFILE` / `ulimit -n` errors).

---

# 📂 Runbook: Troubleshooting "Too Many Open Files"

## **Scenario**

A process or the whole system reports:

* `"Too many open files"`
* `EMFILE` errors in logs
* Service crashes or refuses new connections
* `ulimit -n` value reached

This typically means the process has exhausted its **file descriptor (FD) limit** — which includes **files, sockets, pipes, and network connections**.

---

## **1. Detect the problem**

### Check system-wide FD usage

```bash
lsof | wc -l
```

Example:

```
24563
```

* Large number indicates high FD usage.

### Check per-process FD usage

```bash
lsof -p <PID> | wc -l
```

Example:

```
ulimit_test  1234 user  cwd    DIR     8,1     4096 /home/user
...
```

### Find top FD consumers

```bash
lsof | awk '{print $1}' | sort | uniq -c | sort -nr | head
```

Example:

```
15000 java
4000  nginx
2500  postgres
```

---

## **2. Check FD limits**

```bash
ulimit -n
```

Example:

```
1024
```

Per-process limit:

```bash
cat /proc/<PID>/limits | grep "open files"
```

Example:

```
Max open files    1024    4096    files
```

---

## **3. Identify type of open files**

```bash
lsof -p <PID> | awk '{print $5}' | sort | uniq -c | sort -nr
```

Example output:

```
12000 IPv4
2000  REG
```

* **IPv4 / IPv6** → Many network sockets (likely leak or high connection load)
* **REG** → Many regular files open (possible file handle leak)

---

## **4. Short-Term Mitigation**

| Action                        | Command                                     | When to Use                                |
| ----------------------------- | ------------------------------------------- | ------------------------------------------ |
| Restart the service           | `systemctl restart <service>`               | Clears open FDs (temporary)                |
| Increase FD limit temporarily | `ulimit -n 65535` (before starting process) | Process needs more descriptors immediately |
| Kill runaway processes        | `kill -9 <PID>`                             | If process is leaking FDs                  |
| Block offending traffic       | Firewall rules                              | If high network connections are cause      |

---

## **5. Long-Term Remediation**

* **Increase FD limits permanently**

  * Edit `/etc/security/limits.conf`:

    ```
    user   soft  nofile  65535
    user   hard  nofile  65535
    ```
  * For systemd services, edit unit file:

    ```
    LimitNOFILE=65535
    ```

    Then reload:

    ```bash
    systemctl daemon-reexec
    systemctl restart <service>
    ```

* **Fix FD leaks in application**

  * Ensure files/sockets are **explicitly closed**.
  * Use monitoring to detect FD growth trend:

    ```bash
    watch -n 5 'ls /proc/<PID>/fd | wc -l'
    ```

* **Connection handling**

  * For servers, implement connection pooling.
  * Configure `keepalive` timeouts to release unused sockets.

---

## **6. Commands for Root Cause Analysis**

### Track FD count over time

```bash
watch -n 2 "ls /proc/<PID>/fd | wc -l"
```

### See what files are open

```bash
lsof -p <PID> | head
```

### Check network connections

```bash
ss -s
ss -ant | awk '{print $1}' | sort | uniq -c
```

---

## **7. Decision Flow**

1. **Check FD usage** → `lsof`, `/proc/<PID>/fd`
2. **Check limits** → `ulimit -n`, `/proc/<PID>/limits`
3. **Identify cause** → Many sockets? Many files?
4. **Mitigate short-term** → Restart, increase limit
5. **Fix long-term** → Permanent limits, code fix, connection management

---

If you want, I can also prepare a **combined runbook** for **CPU, Memory, Network, and File Descriptor** issues in one `.md` so you have a single on-call reference.
Do you want me to merge them?
