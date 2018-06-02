project(mpc)

cmake_minimum_required (VERSION 3.5)

# -g allows for gdb debugging
# turn on -03 for best performance
add_definitions(-std=c++11)

set(CXX_FLAGS "-Wall")
set(CMAKE_CXX_FLAGS, "${CXX_FLAGS}")

set(sources src/MPC.cpp)

include_directories(src/Eigen-3.3)
include_directories(/usr/local/include)
link_directories(/usr/local/lib)

if (MINGW)
set(CMAKE_CXX_USE_RESPONSE_FILE_FOR_INCLUDES 0)
endif()

add_executable(mpc ${sources})

if(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
target_link_libraries(mpc ipopt python2.7)
elseif (MINGW)
target_link_libraries(mpc ipopt coinasl coinblas coinasl coinmumps coinlapack gfortran ws2_32 python2.7)
else()
target_link_libraries(mpc ipopt python)
endif()