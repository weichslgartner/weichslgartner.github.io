---
layout: page
title: Projects
subtitle:  Concat strings in C++
comments: true
---
## Benchmarking string concatenation in C and C++

There is one early [Abseil C++ tip of the week](https://abseil.io/tips/3) about string concatention and why using the + overload for three strings is bad.


We first generate a `conanfile.txt` with the c++ packages we want to benchmark:

```
[requires]
fmt/7.0.3
gtest/1.10.0
ms-gsl/3.1.0
abseil/20200923
boost/1.74.0
benchmark/1.5.2 
#folly/2020.08.10.00


[generators]
cmake
cmake_find_package
cmake_paths
```

We see that the Facebook library folly is commented out as I had problems to use it from conan and had to install it from source. (I need to test if that has changed in the meantime)

To use conan we can easily install it through the Python package manager pip:

```
pip install conan  
```
