Obfuscate the local part of email addresses (everything before @) in all .html files under ## a folder
##hello.john@example.com to ********@example.com

**Possible ways to solve**
1. Regex-Based File Scan (Standard Way)
2. Using python's in built email.utils.parseaddr() for Safety
3. Line-by-Line Streaming (Memory Efficient)

**Algorithm**

```
1. Loop through all .html files in the given folder.
2. Read the file content as text.
3. Use regex to find email addresses and replace the local part with asterisks.
4. Overwrite the file with the updated content.
```

**Code**

```
import os
import re

# Define the folder where HTML files reside
folder_path = "/home"

# Regex pattern to match emails (captures username and domain separately)
email_pattern = re.compile(r'([a-zA-Z0-9._%+-])([a-zA-Z0-9._%+-]*)(@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})')

# Iterate over all files in the given directory
for filename in os.listdir(folder_path):
    if filename.endswith(".html"):
        full_path = os.path.join(folder_path, filename)

        # Open the file and read contents
        with open(full_path, "r") as f1:
            content = f1.read()

        # Replace each email's local part with a masked version
        def mask_email(match):
            first = match.group(1)
            mid = match.group(2)
            domain = match.group(3)
            masked = first + ('*' * len(mid)) + domain
            return masked

        # Apply the substitution using regex
        updated_content = email_pattern.sub(mask_email, content)

        # Write back the updated content to the same file
        with open(full_path, "w") as f2:
            f2.write(updated_content)

```
