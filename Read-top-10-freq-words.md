Write some code that lists the top 10 most frequent words in a file

**Algorithm**

```
1. Converts all text to lowercase to avoid treating "The" and "the" as different.
2. Uses re.findall() to extract words (ignores punctuation).
3. Uses collections.Counter to count how many times each word appears.
4. Retrieves the top 10 with .most_common(10)
```

**Code**

```
from collections import Counter
import re

def top_10_words(file_path):
    with open(file_path, 'r', encoding='utf-8') as f:
        text = f.read().lower()  # Convert to lowercase for uniformity

    # Use regex to extract words (alphanumeric only)
    words = re.findall(r'\b\w+\b', text)

    # Count word frequencies
    word_counts = Counter(words)

    # Get the 10 most common words
    top_10 = word_counts.most_common(10)

    # Print results
    print("Top 10 most frequent words:")
    for word, freq in top_10:
        print(f"{word}: {freq}")

# Example usage:
top_10_words("/path/to/your/file.txt")

```
