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


path1 = "dataset1.csv"
path2 = "dataset2.csv"

g = 9.8
post_dia = {}

# Read bipedal dinosaurs and their stride lengths
with open(path2, "r") as reader2:
    reader2.readline()  # skip header
    line = reader2.readline()
    while line:
        parts = line.strip().split(",")
        if len(parts) >= 3:
            name = parts[0]
            stride_length = parts[1]
            stance = parts[2].strip().lower()
            if stance == "bipedal":
                post_dia[name] = float(stride_length)
        line = reader2.readline()

speed_res = {}

# Read leg lengths and compute speed
with open(path1, "r") as reader1:
    reader1.readline()  # skip header
    line = reader1.readline()
    while line:
        parts = line.strip().split(",")
        if len(parts) >= 2:
            name = parts[0]
            leg_length = float(parts[1])
            if name in post_dia:
                stride_len = post_dia[name]
                speed = ((stride_len / leg_length) - 1) * math.sqrt(leg_length * g)
                speed_res[name] = speed
        line = reader1.readline()

# Sort and print dinosaur names by speed in descending order
sorted_speeds = sorted(speed_res.items(), key=lambda x: x[1], reverse=True)

for name, _ in sorted_speeds:
    print(name)
```

