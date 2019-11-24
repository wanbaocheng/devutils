https://github.com/Microsoft/vcpkg/blob/master/docs/users/integration.md  
目录结构
```
CMakeDemo
├── CMakeLists.txt
├── main.cpp
└── math
    ├── CMakeLists.txt
    ├── math2.h
    └── math.cpp
```


CMakeDemo/CMakeLists.txt
```
cmake_minimum_required (VERSION 3.0)
project (TEST)
set(CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} -std=c++11)
set(MATH_LIB "my_math")
add_subdirectory (math)
include_directories (./math)
add_executable (Test main.cpp)
find_package( OpenCV REQUIRED )
if( OpenCV_FOUND )
  include_directories( ${OpenCV_INCLUDE_DIRS} )
  target_link_libraries (Test  ${MATH_LIB} ${OpenCV_LIBS})
endif()
```
CMakeDemo/main.cpp

```
#include <iostream>
#include <opencv2/opencv.hpp>
#include <string>
#include <thread>
#include <functional>
#include <math2.h>

using namespace std;

static void square_int(int i) {
	cout << "square(" << i << ")=" << square(i) << endl;
}

static void square_double(double d) {
	cout << "square(" << d << ")=" << square(d) << endl;
}

int main(int argc, char **argv) {

	int array[10] = { 5, 20, 30, 6, 0, 40, 3, 100, 9, 88 };
	cout << "number less than 10:" << count(array, 10, 10) << endl;
	cv::Mat a(2, 32, CV_32FC1);
	cout << a.size() << endl;
	std::cin.get();
	return 0;
}
```
CMakeDemo/math/ CMakeLists.txt
```
cmake_minimum_required (VERSION 3.8)
add_library (${MATH_LIB} STATIC math.cpp)
```
CMakeDemo/math/ math2.h

```
template <typename T>
auto square(const T& t) -> decltype(t * t) {
	return t * t;
}
extern int count(int *arr, int len, int val);
```
CMakeDemo/math/ math.cpp

```
#include "math2.h"

extern int count(int *arr, int len, int val) {
	return 0;
}
```
Linux版命令:
```
cmake -DCMAKE_TOOLCHAIN_FILE=/mnt/d/ubuntusoftware/vcpkg/scripts/buildsystems/vcpkg.cmake  -DCMAKE_BUILD_TYPE=Release ..;cmake --build .
```
Windows版命令:
```
cmake -G"Visual Studio 15 2017 Win64" ..;cmake --build . --config Release
```


这个是关于CGAL的CMakeList:

```
cmake_minimum_required (VERSION 3.0)
project (TEST)
set(CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} -std=c++11)
add_executable (demo demo.cpp)
find_package(CGAL CONFIG REQUIRED)
find_package(Boost REQUIRED)
if(CGAL_FOUND)
  include_directories( ${CGAL_INCLUDE_DIRS} )
  target_link_libraries(demo ${CGAL_LIBRARIES})
endif()
```
demo.cpp

```
// Example program for the linear_least_square_fitting function
// on a set of 3D triangles
#include <CGAL/Simple_cartesian.h>
#include <CGAL/linear_least_squares_fitting_3.h>
#include <vector>
typedef double                      FT;
typedef CGAL::Simple_cartesian<FT>  K;
typedef K::Line_3                   Line;
typedef K::Plane_3                  Plane;
typedef K::Point_3                  Point;
typedef K::Triangle_3               Triangle;
int main(void)
{
	std::vector<Triangle> triangles;
	Point a(1.0, 2.0, 3.0);
	Point b(4.0, 0.0, 6.0);
	Point c(7.0, 8.0, 9.0);
	Point d(8.0, 7.0, 6.0);
	Point e(5.0, 3.0, 4.0);
	triangles.push_back(Triangle(a, b, c));
	triangles.push_back(Triangle(a, b, d));
	triangles.push_back(Triangle(d, e, c));
	Line line;
	Plane plane;
	// fit plane to whole triangles
	linear_least_squares_fitting_3(triangles.begin(), triangles.end(), plane, CGAL::Dimension_tag<2>());

	// fit line to triangle vertices
	linear_least_squares_fitting_3(triangles.begin(), triangles.end(), line, CGAL::Dimension_tag<0>());

	return 0;
}
```

怎样把vcpkg中的Boost库加入CMakeLists.txt?
参见
https://stackoverflow.com/questions/6646405/how-do-you-add-boost-libraries-in-cmakelists-txt 

- 在链接阶段提示某某库函数未引用  
  以freetype为例，需要在CMakeLists.txt中加入
```text
find_package(Freetype REQUIRED)
target_link_libraries(main PRIVATE Freetype::Freet
```
通过如下命令可获取上述语句：
```shell script
$ sudo ./vcpkg install freetype
```