# geoCFD

<img width="558" alt="building_set_1_angle1" src="https://user-images.githubusercontent.com/72781910/193407829-c08d1cbf-7cc6-4a71-8a62-010c2e9d73e7.PNG">

Process geometry for cfd simulation.

It's a `x64-windows10` version of [geocfd-Ubuntu](https://github.com/SEUZFY/geocfd-Ubuntu). Further development may be performed in this project since a discovery has been found 
that keeping using `WSL-Ubuntu` will occupy more and more space in C drive.

`Now`:

- Read a building set(containing 23 buildings(buildingparts)), process repeated vertices and build `nef polyhedra`.

- Union `nef polyhedra` into one `big nef polyhedron`.

- Perform [3D Minkowski Sum](https://doc.cgal.org/latest/Minkowski_sum_3/index.html#Chapter_3D_Minkowski_Sum_of_Polyhedra) with different parameters.

- Get the [convex hull](https://en.wikipedia.org/wiki/Convex_hull) of the `big nef polyhedron` and visualise it in [ninja](https://ninja.cityjson.org/), observe its exterior and interior.

- Export the `big nef polyhedron` as `.cityjson` file(with no repeated vertices) and visualise it in [ninja](https://ninja.cityjson.org/), observe its `exterior` and `interior`.

  <img width="361" alt="set_1_exterior_m=0 1" src="https://user-images.githubusercontent.com/72781910/193407856-042f6d65-5655-44df-bd39-969bf369b5c1.PNG">   <img width="361" alt="set_1_interior_m=0 1" src="https://user-images.githubusercontent.com/72781910/193408048-7be363a3-4067-4819-ae5b-076114c8478f.PNG">

`To do`:

- test more complicated buildings 

  - how much will the shape change?
  
  - [3D Minkowski Sum](https://doc.cgal.org/latest/Minkowski_sum_3/index.html#Chapter_3D_Minkowski_Sum_of_Polyhedra) - can we do sum in some specific direction?

  - test different LoD levels - for example `LoD 2.2`

 `long term`
 
  - `robust`
  
  - `val3dity`  - [validate](http://geovalidation.bk.tudelft.nl/val3dity/) the geometry
  
  - `validator` - [validate](https://validator.cityjson.org/) the `cityjson` file
  
  - `#include` - include multiple files, how to avoid possibly messy includings?

  - `precompiled headers` - add `pch.h` file and use [cmake command for precompiled headers](https://cmake.org/cmake/help/latest/command/target_precompile_headers.html)
 
## Prerequisite

[CGAL](https://www.cgal.org/) - The version should be above `5.0` since we can use the `header-only`, which means we don't have to manually compile `CGAL`.

install `CGAL` via [vcpkg](https://vcpkg.io/en/index.html):

check this -> 

[Download CGAL for Windows](https://www.cgal.org/download/windows.html)

[vcpkg Crash Course | C++ libraries simplified! A Visual Studio must-have!](https://www.youtube.com/watch?v=b7SdgK7Y510)

## How to use?

This project is built and executed on a `windows10(64 bit)` platform.

Clone this project and open it in [Visual Studio](https://visualstudio.microsoft.com/) **2019** or newer version, you should be able to directly build and run.

## Compile info

C++ Standard: `C++ 11`

Compiler: `MSVC`

Generator: `Ninja`

## Attention
1. **vertices repeatness**

	Using `CGAL::Polyhedron_3` can be tricky, since `Polyhedron_builder` doesn't like repeated vertices, and even if it workswith repeatness, the created `Polyhedron_3` is NOT closed(and thus can not be converted to `Nef_polyhedron`).Thus extra care needs to be taken when creating `Polyhedron_3`.

2. **geometry error**

	In `lod 2.2` geometries of buildings can get complicated, in our test there can be some geometry errors in the original dataset, for example:
	```console
	build nef for building: NL.IMBAG.Pand.0503100000018412-0

	CGAL::Polyhedron_incremental_builder_3<HDS>::
	lookup_halfedge(): input error: facet 29 has a self intersection at vertex 79.
	polyhedron closed? 0
	```
	A manual fix can be possible towards certain building set but not general, if we take the **universality** and **automation** into consideration, using `convex hull` to replace the corresponding building seems to be a good choice, yet this approach may lead to the compromisation of the original building shapes.

## Benchmark

| building set| number of buildings |    lod level    | minkowski param | run time |
| :---------: | :-----------------: | :-------------: | :-------------: | :------: |
| 1           | 23                  | 1.3             | 1.0             | 29.7634s |
| 1           | 23                  | 1.3             | 0.5             | 29.9599s |
| 1           | 23                  | 1.3             | 0.1             | 33.8505s |
| 1           | 23                  | 1.2             | 0.1             | 29.8471s |

## Other platforms

If you use other platforms (such as `Linux` or `MacOS`), you can refer to `CMakeLists.txt` file and use it to build a `CMake` project using `src`, `include` and `data` folder.
