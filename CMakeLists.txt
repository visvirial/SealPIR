cmake_minimum_required(VERSION 3.10)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

project(SealPIR VERSION 2.1 LANGUAGES CXX)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

add_executable(main
	main.cpp
)

add_subdirectory(./SEAL/native/src)
set(SEAL_DIR ./SEAL/native/src/cmake)
set(CMAKE_CXX_FLAGS -I./SEAL/native/src)
#find_package(SEAL 3.2.0 EXACT REQUIRED)

add_library(sealpir STATIC
  pir.cpp
  pir_client.cpp
  pir_server.cpp
)

# Add OpenMP dependency, if available.
find_package(OpenMP)
if(OpenMP_CXX_FOUND)
  target_link_libraries(sealpir PUBLIC OpenMP::OpenMP_CXX)
endif()

target_link_libraries(main sealpir seal)

install(TARGETS sealpir ARCHIVE DESTINATION lib LIBRARY DESTINATION lib)
install(FILES pir.hpp pir_client.hpp pir_server.hpp DESTINATION include/sealpir)
