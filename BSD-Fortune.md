**Question**

Implement BSD fortune

BOF
You will be lucky in love
%
Some other piece of wisdom from your favourite Chinese Restaurant
%
You will see ....
EOF

(Implement "fortune", which outputs a random fortune from the above file, 
which is to say, one of the pieces of text between a %, next %, or BOF or EOF, and outputs it.)

**Solution 1: Full File Read + In-Memory Split (Simple and Common)**

```
def full_read_fortune(filename):
    with open(filename, 'r') as f:
        content = f.read()
    fortunes = [f.strip() for f in content.split('%') if f.strip() and f.strip() not in ['BOF', 'EOF']]
    return random.choice(fortunes)

```

**Solution 2. Line-by-Line Parse with Block Accumulation (Streaming-Friendly)**
How it works: Read file line-by-line. Accumulate lines until you hit %, then store the block.

Pros:

More memory-efficient than full-read.

Better for larger files.

Cons:

Slightly more complex code.

```
def stream_parse_fortune(filename):
    fortunes = []
    block = []
    with open(filename, 'r') as f:
        for line in f:
            if line.strip() in ["BOF", "EOF"]:
                continue
            if line.strip() == "%":
                if block:
                    fortunes.append("".join(block).strip())
                    block = []
            else:
                block.append(line)
        if block:
            fortunes.append("".join(block).strip())  # Final block
    return random.choice(fortunes)

```


**Solution 3. Index-Based Seek with Preprocessing (Optimal for Large Files)**
How it works: First scan the file and store byte offsets of each fortune start. Then randomly seek and read only the required block.

Pros:

Very memory-efficient.

Super scalable for huge files.

Cons:

More complex.

Requires initial indexing (can be cached).

Fortune boundaries must be precisely detected.

```
import os

def build_index(filename):
    offsets = []
    with open(filename, 'rb') as f:
        start = f.tell()
        in_fortune = False
        for line in f:
            if line.strip() in [b'BOF', b'EOF']:
                continue
            if line.strip() == b'%':
                if in_fortune:
                    offsets.append((start, f.tell()))
                    in_fortune = False
                start = f.tell()
            else:
                in_fortune = True
        if in_fortune:
            offsets.append((start, f.tell()))
    return offsets

def read_fortune_by_index(filename):
    offsets = build_index(filename)
    if not offsets:
        return "No fortunes found."
    start, end = random.choice(offsets)
    with open(filename, 'rb') as f:
        f.seek(start)
        data = f.read(end - start)
    return data.decode('utf-8').strip()
```

**Solution 4. Shell Script with awk, sed, shuf, etc. (Unix-style)**
How it works: Use command-line tools to split fortunes and pick one.

Example:

```
awk -v RS="%" '{gsub(/\n+/, "\n"); gsub(/^ +| +$/, ""); if ($0 !~ /BOF|EOF/ && length($0) > 0) print $0}' fortunes.txt | shuf -n 1
```

Pros:

No code required if running on Unix/Linux.

Cons:

Not portable.

Harder to debug/extend.


**Solution 5. Lazy Generator-based Streaming (Pythonic, Elegant)**
How it works: Use generator to yield one fortune at a time, avoid keeping all in memory.

Pros:

Elegant.

Great for pipelining or streaming use.

Cons:

Still needs to convert to list if choosing randomly.

```
def fortune_generator(filename):
    with open(filename, 'r') as f:
        block = []
        for line in f:
            if line.strip() in ["BOF", "EOF"]:
                continue
            if line.strip() == "%":
                if block:
                    yield "".join(block).strip()
                    block = []
            else:
                block.append(line)
        if block:
            yield "".join(block).strip()

def random_fortune(filename):
    fortunes = list(fortune_generator(filename))
    return random.choice(fortunes)
```


