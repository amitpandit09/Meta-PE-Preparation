Write some code that lists the top 10 most frequent words in a file

**Algorithm**

```
1. Read the file line by line.
2. Split each line into words, convert to lowercase, and clean punctuation.
3. Use a HashMap to count word frequencies.
```

**Code**

```
import java.io.*;
import java.util.*;
import java.util.Map.Entry;

public class TopWords {

    public static void main(String[] args) {
        String filePath = "your_file.txt";  // Replace with your file name

        Map<String, Integer> wordCount = new HashMap<>();

        try (BufferedReader reader = new BufferedReader(new FileReader(filePath))) {
            String line;

            // Step 1: Read each line
            while ((line = reader.readLine()) != null) {
                // Step 2: Split and clean words
                String[] words = line.toLowerCase().split("\\s+");

                for (String word : words) {
                    word = word.replaceAll("[^a-z0-9]", ""); // remove punctuation
                    if (!word.isEmpty()) {
                        wordCount.put(word, wordCount.getOrDefault(word, 0) + 1);
                    }
                }
            }

        } catch (IOException e) {
            e.printStackTrace();
        }

        // Step 3: Sort by frequency and print top 10
        List<Entry<String, Integer>> sorted = new ArrayList<>(wordCount.entrySet());
        sorted.sort((a, b) -> b.getValue() - a.getValue());

        System.out.println("Top 10 most frequent words:");
        for (int i = 0; i < Math.min(10, sorted.size()); i++) {
            Entry<String, Integer> entry = sorted.get(i);
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
    }
}

```
