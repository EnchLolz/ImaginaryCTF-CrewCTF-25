# pyf❤️ck

Category: MISC

Points: 362

Solves: 19

>Do you really know python?

We are given a highly restricted python jail and the flag in `Flag.txt` with characters all on seperate lines:


```py
#!/usr/bin/python3

"""
The flag is stored at 'Flag.txt' across multiple lines e.g. c\nr\ne\nw\n{\n...
"""

allowed_builtins = {
    'next': next,
    'chr': chr,
    'ord': ord,
    'max': max,
    'min': min,
    'bin': bin,
    'int': int,
    'len': len,
    'str': str,
    'set': set,
    'hex': hex,
    'print': print,
    'range': range,
    'open': open
}

whitelist = "abcdefghijklmnopqrstuvwxyz()+"

security_check = lambda s: any(c not in whitelist for c in s) or s.count('+') > 12 or len(s) > 340 or 'print' in s

print('Welcome to pyfuck <3')
print('Good luck!')
print('- sealldev & KabirAcharya')
while True:
    cmd = input("Input: ")
    if security_check(cmd):
        print("No dice!")
    else:
        try:
            exec("print("+cmd+")", {'__builtins__': allowed_builtins}, {})
        except:
            print("No dice!")
```


It seems like we are only given some basic builtins to use and well as being able to use only the lowercase alphabet, parenthesis, and the `+` symbol. Further we see some limitations on only being able to have a payload that contains at most 12 `+`s and less than 340 characters.

If we look at their given resource https://pywtf.seall.dev/, it looks like we can represent many strings as some combination of builtins and `+` signs. For example plugging in `Flag.txt` we get:

```py
min(str(()in()))+chr(len(str(hash))+ord(min(str(not()))))+chr(len(str(ascii))+len(str(help)))+chr(len(str(type(quit)))+len(str(help)))+min(str(float()))+max(str(set))+max(str(hex))+max(str(set))
```

While this clearly contains builtins we aren't allowed to use, the premise of the challenge is clear, and we have to find a way to use our whitelisted builtins to get the flag.

Before we deal with the flag characters seperate lines, lets first see if we can access the flag. We can just use `Open` with the string of the flag, so we must find a way to construct `Flag.txt` like above. After some tinkering and optimization we get the following:

```py
F = min(str(not(str)))
l = chr(len(str(open))+ord(min(str(not()))))
a = chr(ord(str(int(not())))+ord(str(int())))
g = chr(ord(max(bin(int())))+int(str(len(str(set())))))
. = chr(len(str(max))+len(str(max)))
t = max(str(set))
x = max(hex(not()))
t = max(str(set))
```

So our full payload for `Flag.txt` would be:

```py
min(str(not(str)))+chr(len(str(open))+ord(min(str(not()))))+chr(ord(str(int(not())))+ord(str(int())))+chr(ord(max(bin(int())))+int(str(len(str(set())))))+chr(len(str(max))+len(str(max)))+max(str(set))+max(hex(not()))+max(str(set))
```
and this uses 11 of our 12 `+`s and has a length of 230

Just as a sanity check if we ran:

```py
Input: set(open(PAYLOAD))
```
```py
{'w\n', '_\n', 't\n', "Here's some junk data, enjoy! Love, sealldev & KabirAcharya :3\n", 'm\n', 'r\n', 's\n', '@\n', 'g\n', 'c\n', '1\n', '3\n', 'n\n', 'p\n', '}', '4\n', 'f\n', 'e\n', 'l\n', '{\n', 'u\n'}
```
and
```py
Input: next(open(PAYLOAD))
```
```
Here's some junk data, enjoy! Love, sealldev & KabirAcharya :3
```

Now, we have problem, while we have all the unique characters of the flag, we have no way of knowing their order or dealing with duplicate characters.

It would be nice if we could loop through each character seperately especially since we still haven't used `next` or `range` in our code yet. The only issue with `next` is that we must use `next` on a variable if we want persistence.

For example:
```py
next(open(FLAG)) # c
next(open(FLAG)) # c
# However
f = open(FLAG)
next(f) # c
next(f) # r
```

To do this we can use list comprehension:

We want to do something like:
```py
[next(x) for x in [open(FLAG)] for i in range(ITERATIONS)]
```

Here we put `open(FLAG)` into a list to which we assign `x` to. Then for however many iterations, we keep on adding `next(x)` to the list. Converting this to something we can actually run we get:

```py
set(next(x)for(x)in(open(FLAG)for(z)in(chr(int())))for(y)in(range(ITERATIONS)))
```

Now, we still have the issue of our final set not showing the order or duplicates but this can be easily fixed since we are now explicitely iterating over each line in the flag. Since we optimized our `FLAG` string to only use 11 `+`s, we can now use our final `+` to attach the index of the line with the value of the line:

```py
set(next(x)+str(y)for(x)in(open(FLAG)for(z)in(chr(int())))for(y)in(range(ITERATIONS)))
```

Now we just need to find how many lines there are in the `Flag.txt` file. After some testing we get that the length is between:

`32 = ord(min(str(min)))` and `40 = ord(min(str(())))`

So our partial flag is:

```py
data = {'1\n10', '3\n29', '4\n22', '1\n12', 's\n20', 'm\n6', 'r\n23', '_\n27', 'e\n3', 'r\n28', "Here's some junk data, enjoy! Love, sealldev & KabirAcharya :3\n0", '_\n15', 'f\n16', 'r\n2', 'l\n8', 'n\n13', '{\n5', 't\n9', 'u\n7', 'w\n4', '4\n30', 'c\n1', '_\n21', 'g\n19', 'l\n31', '4\n26', '3\n14', 'l\n11', '_\n25', '@\n18', 'l\n17', '3\n24'}


# Step 1: split into (char, index) pairs
pairs = [(s.split('\n')[0], int(s.split('\n')[1])) for s in data]

# Step 2: sort by index
pairs.sort(key=lambda x: x[1])

pairs = pairs[1:]

# Step 3: join characters in order
result = ''.join(char for char, _ in pairs)

print(result)
# crew{mult1l1n3_fl@gs_4r3_4_r34l
```

Then if we were to remove the `+str(y)` in the payload, we can easily test for the length (but we will lack the character position). From this we got that the flag is `38` characters long. Now instead of trying to figure out how to get the last 6 characters, what if we just guessed.

`crew{mult1l1n3_fl@gs_4r3_4_r34l??????`

We can safely assume

`crew{mult1l1n3_fl@gs_4r3_4_r34l_????}`

Now, there was one feeling going through my mind after spending hours on this problem:
 
**PAIN**

of course we must convert it to `p41n` to get our final flag.

Note that all the characters also fall within the original set, and also that the lowercase `p` also shows up in the intial `set(open(FLAG))` but is not yet used in the flag prefix.


## Flag

`crew{mult1l1n3_fl@gs_4r3_4_r34l_p41n}`
