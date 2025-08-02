How would you read the standard out of an application and print a line when a particular field when it changes?

**Algorithm**

```
1.Start the application process and read its stdout line by line.
2. Extract the value of the specific field from each line.
3. Keep track of the previous value of that field.
4. Print the line only when the field's value changes.
```

**Code**

```
import subprocess

def watch_field_change(command, field_name):
    process = subprocess.Popen(command, stdout=subprocess.PIPE, text=True)

    previous_value = None

    for line in process.stdout:
        line = line.strip()
        if not line:
            continue

        # Simple parsing: assume key=value pairs in each line
        fields = dict(part.split('=') for part in line.split() if '=' in part)

        if field_name in fields:
            current_value = fields[field_name]

            if current_value != previous_value:
                print(f"[CHANGED] {field_name}: {previous_value} -> {current_value}")
                print(f"Line: {line}")
                previous_value = current_value

# Example usage
# Suppose your app prints lines like: "time=10 status=OK temp=70"
watch_field_change(["your_app", "--some-arg"], "status")

```
