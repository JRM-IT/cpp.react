### Configuration
cmake_minimum_required (VERSION 2.6)

project (CppReact)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

if(MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /bigobj /W3 /WX")
else()
    set(CMAKE_CXX_FLAGS "-Wall -g -Wextra -Wshadow -pedantic -Wold-style-cast ${CMAKE_CXX_FLAGS}")
endif()
if(CMAKE_VERSION VERSION_LESS 3.1)
	set(CMAKE_CXX_FLAGS "-std=c++11 ${CMAKE_CXX_FLAGS}")
else()
	if(NOT DEFINED CMAKE_CXX_STANDARD OR CMAKE_CXX_STANDARD STREQUAL "98")
		set(CMAKE_CXX_STANDARD 11)
	endif()
	set(CMAKE_CXX_STANDARD_REQUIRED ON)
endif()

include_directories ("${PROJECT_SOURCE_DIR}/include")

### CppReact
add_library(CppReact 
	src/engine/PulsecountEngine.cpp
	src/engine/SubtreeEngine.cpp
	src/engine/ToposortEngine.cpp
	src/logging/EventLog.cpp
	src/logging/EventRecords.cpp)

### TBB
option(find_tbb_package "Search for TBB using find_package?" OFF)

if (find_tbb_package AND NOT DEFINED TBB_FOUND)
	find_package(TBB REQUIRED tbb)
endif()

if (DEFINED TBB_IMPORTED_TARGETS)
	set(tbb_lib ${TBB_IMPORTED_TARGETS})
else()
	set(tbb_lib tbb)
endif()

target_link_libraries(CppReact ${tbb_lib})

### examples/ 
option(build_examples "Build examples?" ON)
if(build_examples)
	add_subdirectory(examples)
endif()

### benchmarks/ 
option(build_benchmarks "Build benchmarks?" OFF)
if(build_benchmarks)
	add_subdirectory(benchmarks)
endif()

### tests/ 
option(build_tests "Build unit tests?" OFF)
if(build_tests)
	add_subdirectory(tests)
endif()
