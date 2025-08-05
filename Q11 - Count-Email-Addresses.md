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
import re
from collections import defaultdict

# Step 1: Prepare a regex for email
email_pattern = re.compile(r"[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+")

# Step 2: Initialize a dictionary to count emails
email_counts = defaultdict(int)

# Step 3: Read file line by line and find emails
with open("innate.txt", "r") as file:
    for line in file:
        emails = email_pattern.findall(line)
        for email in emails:
            email_counts[email] += 1

# Step 4: Print results
for email, count in email_counts.items():
    print(f"{email}: {count}")

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

