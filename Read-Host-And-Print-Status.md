Reading from STDIN and receiving two parameters which was host and ports that I need to reach and print status,what if this input is the file with 1tb(here interaction with large inputs & optimization)

```
import sys
import socket
from concurrent.futures import ThreadPoolExecutor, as_completed

MAX_WORKERS = 100  # You can tune this based on your system and network

def check_host_port(line):
    line = line.strip()
    if not line:
        return None
    try:
        host, port_str = line.split()
        port = int(port_str)
    except ValueError:
        return f"[INVALID] Line skipped: {line}"

    try:
        with socket.create_connection((host, port), timeout=3):
            return f"[OK] {host}:{port} is reachable"
    except socket.timeout:
        return f"[TIMEOUT] {host}:{port} timed out"
    except Exception as e:
        return f"[ERROR] {host}:{port} unreachable: {e}"

def main():
    with ThreadPoolExecutor(max_workers=MAX_WORKERS) as executor:
        future_to_line = {
            executor.submit(check_host_port, line): line
            for line in sys.stdin
        }

        for future in as_completed(future_to_line):
            result = future.result()
            if result:
                print(result)

if __name__ == "__main__":
    main()

```

**Input**
```
python3 check_hosts.py < huge_input_file.txt
```

**Output**

```
google.com 80
localhost 22
invalidhost 1234

```
