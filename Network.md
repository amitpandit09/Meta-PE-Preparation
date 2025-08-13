Got it ✅ — I’ll update the **Network Troubleshooting Runbook** so it includes explicit **bind address checks**.
Here’s the revised `.md` version you can paste into GitHub.

---

# 🌐 Network Troubleshooting Runbook

## **Scenario**

A service is running (`systemctl status` shows active) but it’s **not responding on its port**.
Possible causes include: incorrect bind address, firewall rules, SELinux restrictions, resource limits, or network path issues.

---

## **1. Initial Check — Is the process listening?**

```bash
ss -ltnp | grep <PORT>
```

Example:

```
LISTEN 0      128        0.0.0.0:8080       0.0.0.0:*    users:(("java",pid=1234,fd=45))
```

* **No output** → The process is not bound to the port (check app logs/config).
* **Bound to `127.0.0.1`** → Accessible only locally, not from remote hosts.
* **Bound to a specific IP** → Only accessible via that interface.

---

## **2. Confirm bind address in application config**

Check your service/application configuration file to see what IP it’s bound to.

Common cases:

* **Local-only bind (loopback)** → `127.0.0.1:8080` or `localhost:8080`
* **All interfaces** → `0.0.0.0:8080` (IPv4) or `::` (IPv6)
* **Specific interface** → e.g., `192.168.1.50:8080`

Example check:

```bash
grep -i "bind" /etc/<app>/<app>.conf
```

---

## **3. Confirm service status & logs**

```bash
systemctl status <service_name>
journalctl -u <service_name> --since "10 min ago"
```

Example:

```
● myapp.service - My Application
   Active: active (running) since Tue 2025-08-12 14:05:01 UTC; 10min ago
   Main PID: 1234 (java)
   Logs: Started HTTP server on 127.0.0.1:8080
```

* If logs show **127.0.0.1** → change config to `0.0.0.0` to allow external access.

---

## **4. Test local connectivity**

```bash
curl -v http://127.0.0.1:<PORT>
```

Example:

```
*   Trying 127.0.0.1:8080...
* Connected to 127.0.0.1 port 8080 (#0)
HTTP/1.1 200 OK
```

* If works locally → problem is in network path/firewall.

---

## **5. Test remote connectivity**

From another host:

```bash
curl -v http://<SERVER_IP>:<PORT>
```

or

```bash
telnet <SERVER_IP> <PORT>
```

Example:

```
Trying 192.168.1.50...
telnet: Unable to connect to remote host: Connection refused
```

* **Connection refused** → port closed locally.
* **Timeout** → firewall or routing issue.

---

## **6. Firewall / Security Group Checks**

### **Local firewall**

```bash
sudo iptables -L -n | grep <PORT>
sudo firewall-cmd --list-all
```

### **Cloud security groups** (AWS example)

```bash
aws ec2 describe-security-groups --group-ids <sg-id>
```

---

## **7. Check SELinux/AppArmor**

```bash
getenforce
```

If enforcing, allow port:

```bash
sudo semanage port -a -t http_port_t -p tcp <PORT>
```

---

## **8. Resource / Socket Limits**

```bash
ulimit -n
cat /proc/sys/net/ipv4/ip_local_port_range
```

---

## **9. Packet path debugging**

```bash
traceroute <SERVER_IP>
sudo tcpdump -i any port <PORT>
```

---

## **10. Short-Term Mitigation**

| Action                    | Command                                             | When to Use                  |
| ------------------------- | --------------------------------------------------- | ---------------------------- |
| Restart service           | `systemctl restart <service>`                       | Service hung or socket stuck |
| Bind to correct interface | Edit app config → `0.0.0.0:<PORT>`                  | Only bound to localhost      |
| Temporarily open firewall | `iptables -I INPUT -p tcp --dport <PORT> -j ACCEPT` | Port blocked                 |
| Change port               | Edit config & restart                               | Port conflict                |

---

## **11. Long-Term Remediation**

* Enforce correct **bind address** in configuration.
* Monitor listening ports via **Prometheus, Nagios, or netdata**.
* Audit firewall/security group rules regularly.
* Apply SELinux/AppArmor policies for needed ports.
* Scale service architecture for high load.

---

## **12. Decision Flow**

1. `ss -ltnp` → Check if listening & on correct IP
2. Review config → Bind address correct?
3. Local `curl` works?
4. Remote test works?
5. Firewall/security groups clear?
6. SELinux/AppArmor allow?
7. Network path OK?
8. Apply fix → plan long-term solution

---

If you want, I can also prepare a **single GitHub `.md` runbook** combining **CPU, Memory, and Network troubleshooting** so your on-call docs are in one place.
Do you want me to merge them into one file?
