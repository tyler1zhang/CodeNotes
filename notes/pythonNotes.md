## [Generator](https://realpython.com/introduction-to-python-generators/) Notes from RealPython

- `open(filename)` is one generator as below:

  ```python
  def csv_reader(file_name):
      for row in open(file_name, "r"):
          yield row
  ```

- Using `yield` will result in a generator object.
- Using `return` will result in the first line of the file *only*.
- generator comprehension. `csv_gen = (row for row in open(file_name))`. 
- use `next()` to call next element from generator, such as `next(gen)`.  

- if memory enough, list comprehension will be better than list generator.

- multiple `yield` is acceptable. 

- when the generator was exhausted, it will throw out `StopIteration` error.

- Use **`send()`** to provide the value to generator. example:

  ```python
  def is_palindrome(num):
      """Check one num, if it was palindrome, such as 121, return True. else return False."""
      pass
  
  def infinite_palindromes():
      num = 0
      while True:
          if is_palindrome(num): 
              # return num first, then check if any new_value provided by send(). 
              # if called send() outside, i = provided_value. else i = None
              # 'yield' not only return value, but also can assign new value. 
              i = (yield num) 
              
              if i is not None:
                  num = i
          num += 1
          
  pal_gen = infinite_palindromes()
  for i in pal_gen:  # call generator, return one number i, wait with status info
      digits = len(str(i))
      pal_gen.send(10 ** (digits)) # send new value to generator. 
  ```

- `.throw()` allows you to throw exceptions with the generator.

  ```python
  pal_gen = infinite_palindromes()
  for i in pal_gen:
      print(i)
      digits = len(str(i))
      if digits == 5:
          # throw error with msg
          pal_gen.throw(ValueError("We don't like large palindromes")) 
      pal_gen.send(10 ** (digits))
  ```

-  `.close()` allows you to stop a generator.

  ```python
  pal_gen = infinite_palindromes()
  for i in pal_gen:
      print(i)
      digits = len(str(i))
      if digits == 5:
          pal_gen.close() # Throw Error StopIteration
      pal_gen.send(10 ** (digits))
  ```



#### Creating Data Pipelines With Generators

```python
file_name = "techcrunch"
# better to use csv package for csv files
f = open(file_name) 								# open() create file generator
lines = (line for line in f) 						# generator comprehension
list_line = (s.rstrip().split(",") for s in lines) 	# restrip() removes space and '\n'
cols = next(list_line)  							# catch the columns names
company_dicts = (dict(zip(cols, data)) for data in list_line) 
funding = (
    int(company_dict["raisedAmt"])
    for company_dict in company_dicts
    if company_dict["round"] == "a"
)
total_series_a = sum(funding)
print(f"Total series A fundraising: ${total_series_a}")
```



## Some Useful function or Utilities

- Check obj memory size `sys.getsizeof()`

  ```python
  import sys
  sys.getsizeof(obj) # return the memory used by obj
  ```

- Monitor running time

  - `cProfile.run()`

    ```python
    import cProfile
    # list comprehension
    cProfile.run('sum([i * 2 for i in range(10000)])')
    # list generator
    cProfile.run('sum((i * 2 for i in range(10000)))')
    ```

     

