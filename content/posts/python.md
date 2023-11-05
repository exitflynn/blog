# rANDOM STUFF I LEARNT

for the longest time I'd been seeing `ctx` in Go and python code (looking at you [rexagod/newman](https://github.com/rexagod/newman) and [sunpy/sunpy](https://github.com/sunpy/sunpy)), definitely about time i found out what the hell they are.
# Context Managers
- You want to prevent resource leakage, to not take up extra available memory and ofc good security purposes.
- Can think of in terms of a setup phase and a teardown phase.
- `with` in python.
- For example, in
  ```python
  file = open("hello.txt", "w")
  file.write("Hello, World!")
  file.close()
  ```
  if file.write() errors, `file.close()` might not work.
