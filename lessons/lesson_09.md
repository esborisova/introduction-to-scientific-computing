# Lesson 9: Files i/o, Formats for Representing Structured Data (csv and json) #

[source](https://www.programiz.com/python-programming/file-operation)
### Learning Outcomes ###

1. Open a file
2. Read or write
3. Close the file
4. 1-3 for txt, csv, json


## Read and write files ##

Python has a built-in `open()` function to open a file. This function returns a file object, also called a handle, as it is used to read or modify the file accordingly.

```py
f = open("gauss.txt")
for ligne in f.readlines():
    print(ligne)
```

We can specify the mode while opening a file. In mode, we specify whether we want to read `r`, write `w` or append `a` to the file. We can also specify if we want to open the file in text mode or binary mode.

| Mode | Description |
| :-: | :- |
| r | Opens a file for reading. (default) |
| w | Opens a file for writing. Creates a new file if it does not exist or truncates the file if it exists. |
| x	| Opens a file for exclusive creation. If the file already exists, the operation fails. |
| a	| Opens a file for appending at the end of the file without truncating it. Creates a new file if it does not exist. |
| t |Opens in text mode. (default) |
| b	| Opens in binary mode. |
| +	| Opens a file for updating (reading and writing) |


So `open("gauss.txt")` is equivalent to `open("gauss.txt", 'r')` or `open("gauss.txt", 'rt')`. 

To be on the safe side, we may even specify the enconding

```py
f = open("gauss.txt", mode='r', encoding='utf-8')
```

#### Closing files ####

When we are done with performing operations on the file, we need to properly close the file.

Closing a file will free up the resources that were tied with the file. It is done using the `close()` method available in Python.

Python has a garbage collector to clean up unreferenced objects but we must not rely on it to close the file.

```py
f = open("gauss.txt", mode='r', encoding='utf-8')
for ligne in f.readlines():
    print(ligne)
f.close()
```

THis methods however may fail to close the file, if an error happens during some operation with the file. Instead we can use a `try ... finally` block.

```py
try:
    f = open("gauss.txt", mode='r', encoding='utf-8')
    for ligne in f.readlines():
        print(ligne)
finally:
    f.close()
```

The best way to close a file is by using the `with` statement. This ensures that the file is closed when the block inside the `with` statement is exited.

We don't need to explicitly call the `close()` method. It is done internally.

```py
with open("gauss.txt") as f:
    for ligne in f.readlines():
        print(ligne)
```

### Writing to files ###

In order to write into a file in Python, we need to open it in write `w`, `append` a or exclusive creation `x` mode. We need to be careful with the `w` mode, as it will overwrite into the file if it already exists. Due to this, all the previous data are erased. Writing a string or sequence of bytes (for binary files) is done using the write() method. This method returns the number of characters written to the file.

```py
with open("tesla.txt",'w',encoding = 'utf-8') as f:
   f.write("If you want to find the secrets of the universe, think in terms of energy, frequency and vibration.\n")
   f.write("Of all things, I liked books best.\n")
   f.write("Be alone, that is the secret of invention; be alone, that is when ideas are born.")
```
### Numpy arrays ###

We have already looked at how to read files to a `numpy` array, but how to write your array to a `csv` file


```py
import numpy as np

a = np.asarray([ 
    [1, 150, 10.5], 
    [2, 220, 3.1], 
    [3, 121, 10.1],
    [4, 300, 3.2], 
    [5, 541, 6.7], 
    [6, 321, 9.9],
])

np.savetxt('array.csv', a, delimiter=',')

np.savetxt('array.csv', a, delimiter=',', fmt='%f')# to avoid scientific notation in file
```

### Serializing a Python object ###

Python Pickle is used to `serialize` and `deserialize` a python object structure. Any object on python can be pickled so that it can be saved on disk. At first Python pickle serialize the object and then converts the object into a character stream so that this character stream contains all the information necessary to reconstruct the object in another python script. Note that the pickle module is not secure against erroneous or maliciously constructed data according to the documentation. So, never unpickle data received from an untrusted or unauthenticated source.

Python Pickle dump
In this section, we are going to learn, how to store data using Python pickle. To do so, we have to import the pickle module first. Then use `pickle.dump()` function to store the object data to the file. pickle.dump() function takes 3 arguments. The first argument is the object that you want to store. The second argument is the file object you get by opening the desired file in write-binary (wb) mode. And the third argument is the key-value argument. This argument defines the protocol. There are two type of protocol - `pickle.HIGHEST_PROTOCOL` and `pickle.DEFAULT_PROTOCOL`. See the sample code to know how to dump data using pickle.

Write ('pickle') to file

```py

import pickle

data = [1, 1, 2, 3, 5 8]
# open a file, where you ant to store the data
file = open('fibonacci.pcl', 'wb')
# dump information to that file
pickle.dump(data, file)
# close the file
file.close()
```

Read ('unpickle') file

```py
# open a file, where you stored the pickled data
file = open('fibonacci.pcl', 'rb')
# dump information to that file
data = pickle.load(file)
# close the file
file.close()
print('Showing the pickled data:')
counter = 0
for item in data:
    print(f'The data {counter} is: {item}')
    counter += 1
```

## `csv` files ##

A `csv` file is a plain text file. `csv` stands for 'comma-separated values' indicating that our data is separated by commas, structuring our data in 2d tabular form. Each line in the file represents a row in a table and the commas indicate the division into cells.

You might have worked with tables and spreadsheets in Excel or Libre office. You can read a `csv` file into spreadsheet editing software but the file itself lacks some of the information you will get from Excel, e.g., value types (everything is a string in a `cas` format). However, with `csv` files we gain _simplicity_!

Because a string can contain commas within it, `csv` files also have escape characters to distinguish between these and those making a boundary between two cells.
In Python, there exists a builtin `csv` module for reading and writing tabular data in `csv` format.

### The `csv` Reader ###

To read data from a CSV file we first need to create a `reader` object.
The `reader` object will iterate over the lines in the given CSV file.

```py
with open("world-happiness-report-2021.dat") as f:
    reader = csv.reader(f)
    data = list(reader)
    
print(data)
```

Notice that the file is a `.dat` file, but don't let that fool you, it is still comma-seperated. The `.dat` file extension is a generic format that can contain any information.

By calling the `list()` function on our `reader` object we get a list of lists. We can access data in list of lists, which you know from [lesson 3](), by specifying the row and column: `list[row][col]`

```py
print(data[0])# print column names
print(data[1])# print first row
print(data[2][0])# print second country name
```

Let us store the column names in a separate variable

```py
colname = data[0]
print(colname)
```

### `DictReader` and  `Writer` ###

From what is printed above, we see that the first row contains the variable names. This information can be passed to our `reader` obejct and used to access the columns. To do this we use `DictReader` and `DictWriter`.

Instead of reading and writing a `csv` file as lists, as the `reader` and `writer` objects, `DictReader` and `DictWriter` use a dictionary, `dict`, instead. The first row of a `csv` file is by convention used as the keys of the dictionaries.

```py
f = open("data/world-happiness-report-2021.dat")
    dictReader = csv.DictReader(f, colname)

out = list()
for row in dictReader:
    print(row['Country name'], row['Ladder score'], row['Generosity'])
    out.append([row['Country name'], row['Ladder score'], row['Generosity']])
print(out)
```

We extracted three variables and created a reduced data set `out`. We can now add the country Xanadu to the extracted variables and write it wo a file.

```py
with open('data/world-happiness-report-short-2021.dat', "a+", newline="") as f:
    writer = csv.writer(f)
    writer.writerow(out[0])
    writer.writerow(['Xanadu', '8', '0.9'])
    writer.writerows(out[1:])
```

The `writerow()`method takes a list as an argument. The list will be a row in the outputted `csv` file and each value in the list is placed in a cell.
