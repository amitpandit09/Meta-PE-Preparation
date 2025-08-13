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

email_pattern = re.compile(r"[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+")


def read_file_count_email(file_path):
    email_counts = defaultdict(int)
    with open(file_path, "r") as f:
        print(f"f{f}")
        for line in f:
            emails = email_pattern.findall(line)
            for email in emails:
                email_counts[email] += 1
    print(email_counts)
    return email_counts

read_file_count_email("emails.text")

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

