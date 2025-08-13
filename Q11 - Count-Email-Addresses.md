Read innate file and parse the strings to count how many times an email address is found 


**Algorithm**

```
1. Open and read the file line by line to avoid memory issues.
2. Use a regular expression to find all email addresses in each line.
3. Count the occurrences of each email address using a dictionary.
4. Print the final counts after processing the file.
```

**Code**

```
import random


def solution_1(file):
    fortunes = []
    with open(file, "r") as file:
        content = file.read()
        for part in content.split("%"):
            if part.strip():
                fortunes.append(part)
    print(random.choice(fortunes))


def solution_2(file):
    fortunes = []
    block = []
    with open(file, "r") as file:
        for line in file:
            if line.strip() == "%":
                if block:
                    fortunes.append("".join(block).strip())
                    block = []
            else:
                block.append(line)
        if block:
            fortunes.append(block)
    print(random.choice(fortunes))


def build_offsets(file_path):
    offsets = []
    with open(file_path, "r") as f:
        start = f.tell()
        print(f"start {start}")
        in_fortune = False

        while True:
            pos = f.tell()
            line = f.readline()
            if not line:
                break

            if line.strip() == "%":
                if in_fortune:
                    end = pos
                    if end > start:
                        offsets.append((start, end))
                    in_fortune = False
                start = f.tell()
            else:
                if not in_fortune:
                    in_fortune = True
        if in_fortune and end > start:
            offsets.append((start, end))
    print(offsets)
    return offsets


def solution_2_offsets(file_path):
    offsets = build_offsets(file_path)
    if not offsets:
        return "No fortunes found"
    start, end = random.choice(offsets)
    with open(file_path, "r") as f:
        f.seek(start)
        data = f.read(end - start)
    print(data)


solution_2_offsets("fortunes.txt")


```

**Input**

```
Contact us at support@example.com or admin@example.com.
For help, email support@example.com again.
```

**Output**

```
support@example.com: 2
admin@example.com: 1

```

