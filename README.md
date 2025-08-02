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

```

import csv
import math

def compute_bipedal_dinosaur_speeds(dataset1, dataset2):
    leg_length_map = {}

    with open(dataset1, "r") as file1:
        reader1 = csv.DictReader(file1)
        for row in reader1:
            name = row["NAME"]
            leg_length = float(row['LEG_LENGTH'])
            leg_length_map[name] = leg_length

    speeds = []

    with open(dataset2, "r") as file2:
        reader2 = csv.DictReader(file2)
        for row in reader2:
            name = row['NAME']
            stance = row['STANCE'].lower()
            if stance == 'bipedal' and name in leg_length_map:
                stride_length = float(row['STRIDE_LENGTH'])
                leg_length = leg_length_map[name]
                speed = ((stride_length / leg_length) - 1) * math.sqrt(leg_length * 9.8)
                speeds.append((name, speed))

    # Sort by speed descending
    speeds.sort(key=lambda x: x[1], reverse=True)

    # Print only the names
    for name, _ in speeds:
        print(name)
```

