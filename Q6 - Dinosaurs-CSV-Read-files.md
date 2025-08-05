You will be supplied with two data files in CSV format . The first file contains statistics about various dinosaurs. The second file contains additional data. Given the following formula, speed = ((STRIDE_LENGTH / LEG_LENGTH) - 1) * SQRT(LEG_LENGTH * g) Where g = 9.8 m/s^2 (gravitational constant)

Write a program to read in the data files from disk, it must then print the names of only the bipedal dinosaurs from fastest to slowest. Do not print any other information.

```
$ cat dataset1.csv
NAME,LEG_LENGTH,DIET
Hadrosaurus,1.4,herbivore
Struthiomimus,0.72,omnivore
Velociraptor,1.8,carnivore
Triceratops,0.47,herbivore
Euoplocephalus,2.6,herbivore
Stegosaurus,1.50,herbivore
Tyrannosaurus Rex,6.5,carnivore
```
```
$ cat dataset2.csv 
NAME,STRIDE_LENGTH,STANCE
Euoplocephalus,1.97,quadrupedal
Stegosaurus,1.70,quadrupedal
Tyrannosaurus Rex,4.76,bipedal
Hadrosaurus,1.3,bipedal
Deinonychus,1.11,bipedal
Struthiomimus,1.24,bipedal
Velociraptorr,2.62,bipedal
```

**Solution**

```python
import math

g = 9.8

bipedal_dia = {}

# Read dataset2: NAME,STRIDE_LENGTH,STANCE
with open(file2, "r") as f2:
    next(f2)  # Skip header
    for line in f2:
        parts = line.strip().split(",")
        if len(parts) == 3:  # <-- Fix: should check length of list
            name = parts[0]
            stride_len = parts[1]
            stance = parts[2]
            if stance.strip().lower() == 'bipedal':  # case-insensitive match
                try:
                    bipedal_dia[name] = float(stride_len)
                except ValueError:
                    continue  # ignore bad data

res = []

# Read dataset1: NAME,LEG_LENGTH,DIET
with open(file1, "r") as f1:
    next(f1)  # Skip header
    for line in f1:
        parts = line.strip().split(",")
        if len(parts) == 3:
            name = parts[0]
            try:
                leg_len = float(parts[1])
                if leg_len > 0 and name in bipedal_dia:
                    stride_len = bipedal_dia[name]
                    ratio = stride_len / leg_len
                    if ratio > 1:
                        speed = (ratio - 1) * math.sqrt(leg_len * g)
                        res.append((name, speed))
            except ValueError:
                continue  # ignore bad data

# Sort dinosaurs by speed (descending)
res.sort(key=lambda x: x[1], reverse=True)

# Print only names
for name, _ in res:
    print(name)

```

