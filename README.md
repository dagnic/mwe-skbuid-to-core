This small project demonstrates how CXX is built for

* scikit-build
* scikit-build-core

with a submodule built using autotools.

Each build backend has its own directory, and the project is built using the `pyproject.toml` file in each directory. A sub library is built using autotools, and a pybind11 module is built using CMake.

Run the following commands to build the project:

```bash
# scikit-build
cd scikit-build
[uv] pip install . -v

# scikit-build-core
cd scikit-build-core
[uv] pip install . -v
```

In the two cases, I observed different values for CXX that is passed to the autotools build system:

* scikit-build: CXX=
* scikit-build-core: CXX=/usr/bin/c++ -pthread

CXX_FLAGS being empty in both cases.

```
# scikit-build
DEBUG [1/11] Creating directories for 'mycpplib_ext'
DEBUG [2/11] No download step for 'mycpplib_ext'
DEBUG [3/11] No update step for 'mycpplib_ext'
DEBUG [4/11] No patch step for 'mycpplib_ext'
DEBUG [5/11] Building CXX object CMakeFiles/example.dir/src/example.cpp.o
DEBUG [6/11] Performing configure step for 'mycpplib_ext'
DEBUG configure: 0: CXX=
DEBUG configure: 0: CXXFLAGS=


# scikit-build-core
DEBUG *** Building project with Ninja...
DEBUG [1/10] Creating directories for 'mycpplib_ext'
DEBUG [2/10] No download step for 'mycpplib_ext'
DEBUG [3/10] No update step for 'mycpplib_ext'
DEBUG [4/10] No patch step for 'mycpplib_ext'
DEBUG [5/10] Building CXX object CMakeFiles/example.dir/example.cpp.o
DEBUG [6/10] Performing configure step for 'mycpplib_ext'
DEBUG configure: 0: CXX=c++ -pthread
DEBUG configure: 0: CXXFLAGS=
```

In a clean docker image `docker run -it --rm -v .:/mwe-skbuid-to-core python:3.12 bash`, I observed empty `CXX` for `scikit-build` and `CXX=g++` for `scikit-build-core`.