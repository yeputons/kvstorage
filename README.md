# kvstore
## Building and running tests
Requirements:

* CMake 3.24+ with a C++ compiler
* Boost 1.71+

```bash
cd build
cmake ..
cmake --build .
```

Run tests:

```bash
cmake --build . --target test
```

## TODO
* Test on non-default systems (32-bit? 64-bit?)
