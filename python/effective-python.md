# Effective Python - Brett Slatkin
## Pythonic Thinking
### Item 1: Know which version of python you're using
- Python2 is EOL use python3
  - What version am I running?
    - cmd line: `python --version` or `python3 --version`
    - runtime: 
```python
import sys
print(sys.version_info)
print(sys.version)
```
### Item 2: Follow the PEP 8 Style Guide
- [PEP: Python Enhancement Proposal 8](https://peps.python.org/pep-0008/)
- Style guide for python most developers follow
- Use a linter following this guide to catch most issues
- Some highlights a linter might not catch easily
  - Prefer `if not some_list` to `if len(some_list) == 0`
  - Protected attributes should have a leading _
  - Private instance attributes should have a leading __
  - Prefer multiline espressions with () rather than using \

### Item 3: Know the differences between _bytes_ and _str_

- bytes: raw unsigned 8bit values `b'h\x65llo`
  - convert to utf-8 str using `decode('utf-8')`
- str: unicode code points `hello`
  - convert utf-8 str to bytes using `encode('utf-8')`
- If read/writing binary make sure to set the file read/write method to bytes `rb` or `wb`
- To avoid even more issues make sure to set your file encoding when reading/writing files

### Item 4: Prefer Interpolated F-Strings over C-style format strings and `str.format`

- There are a lot of methods to format python strings, but if you are on 3.6+ you should use f-strings
- [PEP-498](https://peps.python.org/pep-0498/)
- ex: `f'{key:<10} = {value:.2f}'`
- ex: `f'Hi my name is {name}'`

### Item 5: Write helper functions instead of complex expressions

- If code is complex or will be reused move into a helper function
- An if/else can be easier to read than a one-liner with or/and

### Item 6: Prefer multiple assignment unpacking over indexing

- Rather than accessing variables of an iterable by index, they can be unpacked

```python
food_list = ['apple', 'carrot', 'almond']
fruit, veggie, nut = food_list
```

### Item 7: Prefer _enumerate_ over _range_

Use this:
```python
flavor_list = ['chocolate','strawberry','vanilla']
for i, flavor in enumerate(flavor_list, 1):
    print(f'{i}: {flavor}')
```
Instead of:
```python
flavor_list = ['chocolate','strawberry','vanilla']
for i in range(len(flavor_list)):
    print(f'{i + 1}: {flavor_list[i]}')
```

### Item 8: Use _zip_ to process iterators in parallel

- zip can be used to iterate over multiple iterators in parallel
- It uses the shortest one if they are a different length unless you use _zip_longest_

### Item 9: Avoid _else_ blocks after _for_ and _while_ loops

- The else after a loop runs unless a break is encountered in the loop which can be confusing behavior so avoid it

### Item 10: Prevent repetition with assignment expressions

- `:=` the walrus operator in python can be used to assign and evaluate a variable name in a single expression
  - Released in 3.8 https://docs.python.org/3/whatsnew/3.8.html
- It Can be handy for creating behavior like a _switch_ statement or to evaluate and assign a var in a _if_ statement