cmake_minimum_required(VERSION 3.1)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake")

find_package(PythonInterp 3.4 REQUIRED)
find_package(PythonInclude REQUIRED)
find_package(Cython REQUIRED)

if(APPLE)
  list(APPEND CMAKE_MODULE_LINKER_FLAGS "-Wl,-undefined,dynamic_lookup")
endif()
if(WIN32)
  list(APPEND CMAKE_MODULE_LINKER_FLAGS "/LIBPATH:C:\Python34\libs")
endif()


include_directories("${PYTHON_INCLUDE_DIR}" "${CMAKE_CURRENT_SOURCE_DIR}")

add_custom_command(OUTPUT "${CMAKE_CURRENT_BINARY_DIR}/geoflow_py.c"
		COMMAND "${PYTHON_EXECUTABLE}" -m cython
			-o "${CMAKE_CURRENT_BINARY_DIR}/geoflow_py.c"
			"${CMAKE_CURRENT_SOURCE_DIR}/geoflow.pyx"
		MAIN_DEPENDENCY "${CMAKE_CURRENT_SOURCE_DIR}/geoflow.pyx")

add_library(geoflow MODULE
		geoflow_py.c
		Mat.h
		cpbconcz2.h
		cpbconcz2.cpp
		modules.h
		modules.cpp
		pbsolvercz.cpp
		solvationsub.cpp
		subfunction.cpp
		surfconcz.cpp)

set_target_properties(geoflow PROPERTIES PREFIX "")
