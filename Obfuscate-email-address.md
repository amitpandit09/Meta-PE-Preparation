Obfuscate the local part of email addresses (everything before @) in all .html files under ## a folder
##hello.john@example.com to ********@example.com

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

# Step 1: Email regex pattern (captures local and domain parts)
email_pattern = re.compile(r'([a-zA-Z0-9._%+-]+)@([a-zA-Z0-9.-]+\.[a-zA-Z]{2,})')

# Step 2: Loop through .html files in the folder
folder_path = "your_folder_path_here"  # Replace with actual path

for filename in os.listdir(folder_path):
    if filename.endswith(".html"):
        full_path = os.path.join(folder_path, filename)
        
        # Step 3: Read file content
        with open(full_path, 'r') as f:
            content = f.read()

        # Step 4: Replace emails with obfuscated version
        updated_content = email_pattern.sub(lambda m: '*' * len(m.group(1)) + '@' + m.group(2), content)

        # Step 5: Write back to the file
        with open(full_path, 'w') as f:
            f.write(updated_content)

```
