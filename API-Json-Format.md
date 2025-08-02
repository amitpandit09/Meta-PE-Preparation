Read data from an API call that gives structured data in JSON format and arrange that data in the requested format.

**Algorithm**


```
1. Make an HTTP GET request to the API endpoint.

2. Parse the JSON response into a Python dictionary/list using json or response.json().

3. Extract and arrange the data into the required format (e.g., dictionary, table, list).

4. Print or return the formatted data.
```

**Code**

```
import requests

# Step 1: Make the API call
response = requests.get("https://api.example.com/users")

# Step 2: Parse JSON response
data = response.json()  # assuming response is a list of users

# Step 3: Arrange data in desired format
formatted_data = []
for user in data:
    name = user.get("name")
    email = user.get("email")
    formatted_data.append(f"{name} - {email}")

# Step 4: Print the formatted result
for item in formatted_data:
    print(item)

```

**Input**

```
[
  {"id": 1, "name": "Alice", "email": "alice@example.com"},
  {"id": 2, "name": "Bob", "email": "bob@example.com"}
]

```


**Output**

```
Alice - alice@example.com
Bob - bob@example.com

```
