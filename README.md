# dedisp

This repositry is derived from Ben Barsdell's original GPU De-dedispersion library (code.google.com/p/dedisp)

Installation Instructions:

1.  git clone https://github.com/GReX-Telescope/dedisp.git
2.  mkdir build && cd build
3.  cmake .. && make install

This will build a shared object library named libdedisp.so which is a
prerequisite for Heimdall. The dedisp header files will be installed the
standard path set by CMake, which can of course be adjusted.
