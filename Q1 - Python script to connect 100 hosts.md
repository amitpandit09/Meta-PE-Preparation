**Question**

Write a script that connects to 100 hosts, looks for a particular process and sends an email with a report.

**Clarifiying questions**

1. What is the list of 100 hosts and how is it provided
2. What authentication method should we use to connect?
3. What process are we looking for?
4. What defines a process being "found"?
5. How should the email report be structured?
6. Can we assume SSH access is enabled and working on all hosts?
7. Should the script run in parallel or sequentially?

**Possible Solutions**
1. Sequential SSH Script
   Approach: Loop through 100 hosts one-by-one and check for the process.
   Time Complexity: O(n) — linear with number of hosts.
   Memory Usage: Very low (one SSH connection at a time).
   Pros: Simple to implement, low resource usage.
   Cons: Very slow for large n, not scalable to 1000+ hosts.
2. Multi-threaded SSH Script
   Approach: Use Python threading or concurrent.futures.ThreadPoolExecutor.
   Time Complexity: O(n / t), where t = number of threads.
   Memory Usage: Medium (depends on thread count and open connections).
   Pros: Faster than sequential; good for ~100–500 hosts.
   Cons: Threads are limited by Python GIL and network bottlenecks; not ideal for massive scale.
3. Multi-processing SSH Script
   Approach: Use Python multiprocessing to spawn processes per host.
   Time Complexity: O(n / p), where p = number of processes.
   Memory Usage: Higher — each process duplicates memory.
   Pros: Bypasses GIL; faster than threading.
   Cons: High memory usage; can overwhelm local system resources.
5. Asynchronous SSH (using asyncssh or asyncio)
   Approach: Use coroutines to handle SSH connections non-blockingly.
   Time Complexity: O(n) in time, but concurrent — practical wall time is low.
   Memory Usage: Very low; excellent for 1000+ hosts.
   Pros: Highly scalable, lightweight.
   Cons: Harder to write/debug; async libraries are less familiar to some developers.
5. Batch via Ansible
   Approach: Use Ansible playbook to check process and gather output.
   Time Complexity: Parallel (uses forks) — scales well.
   Memory Usage: Medium to high.
   Pros: Scalable, declarative, robust for ops teams.
   Cons: Requires inventory setup and Ansible knowledge.
7. Use parallel-ssh (pssh) or Fabric
   Approach: Third-party libraries for SSH in parallel.
   Time Complexity: O(n / workers)
   Memory Usage: Medium (based on worker count).
   Pros: Built for parallel SSH; simpler than custom threads/async.
   Cons: Dependency-heavy; less control over edge cases.
7. Cloud-native / Distributed Execution
   Approach: Push jobs to remote agents (e.g., Celery, AWS Lambda, Kubernetes Job per host).
   Time Complexity: O(1) wall-time if fully distributed.
   Memory Usage: Low locally, offloaded to infra.
   Pros: Scales to 10,000+ hosts, fault-tolerant.
   Cons: High setup complexity; overkill for 100 hosts.
9. Central Agent/Daemon on Each Host
    Approach: Instead of pulling via SSH, push logs or health to a central server.
   Time Complexity: O(1) per report; fully parallel.
   Memory Usage: Minimal.
   Pros: Efficient, real-time updates possible.
   Cons: Requires software deployment/agent installation.
9. SSH Bastion + Fan-out
    Approach: Connect to a bastion/jump host which fans out internally to other hosts.
   Time Complexity: Depends on internal fan-out efficiency.
   Memory Usage: Offloaded to bastion.
   Pros: Secure architecture, good for VPCs.
   Cons: Complex setup; limited by bastion performance.
11. Cron on Each Host + Centralized Email
    Approach: Each host runs a local cron job and reports status via mail or log to central host.
    Time Complexity: Constant.
    Memory Usage: Very low.
    Pros: Fully distributed, avoids central bottleneck.
    Cons: Needs cron setup and coordination; data gathering delay possible.

**Algorithm**

```
1. Loop through all 100 hosts.
2. Connect to each host via SSH.
3. Run a shell command (like pgrep or ps) to check if the process is running.
4. Store the result (found / not found / error).
5. After checking all hosts, send an email with the results.
```

**Code**

```
import paramiko
import smtplib
from email.mime.text import MIMEText

hosts = ['host1', 'host2', 'host3']  # Add up to 100 hosts here
username = 'your_ssh_user'
process_name = 'nginx'
results = []

def check_process(host):
    try:
        ssh = paramiko.SSHClient()
        ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
        ssh.connect(host, username=username, timeout=5)

        #stdin: input stream (if you want to send data to the process)
        #stdout: standard output of the command
        #stderr: error output of the command
        ##Reads the entire output (bytes), decodes it to a string (usually UTF-8), and strips leading/trailing whitespace.

        stdin, stdout, stderr = ssh.exec_command(f'pgrep -fl {process_name}')
        output = stdout.read().decode().strip()

        if output:
            results.append(f"{host}: FOUND - {output}")
        else:
            results.append(f"{host}: NOT FOUND")

        ssh.close()
    except Exception as e:
        results.append(f"{host}: ERROR - {e}")

def send_email(body):
    msg = MIMEText(body)
    msg['Subject'] = 'Process Report'
    msg['From'] = 'sender@example.com'
    msg['To'] = 'recipient@example.com'

    server = smtplib.SMTP('smtp.example.com', 587)
    server.starttls() ## Upgrade to TLS
    server.login('sender@example.com', 'your_password')
    server.send_message(msg)
    server.quit()

# Main logic
for host in hosts:
    check_process(host)

report = '\n'.join(results)
send_email(report)
print("Report sent.")

```
