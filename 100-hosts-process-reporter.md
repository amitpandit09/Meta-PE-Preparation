Write a script that connects to 100 hosts, looks for a particular process and sends an email with a report.

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
