cmake_minimum_required(VERSION 3.15)
project(MARL_Framework VERSION 0.1.0 LANGUAGES CXX)

# Specify the C++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Options
option(BUILD_TESTS "Build the tests" ON)
option(USE_OPENMP "Use OpenMP for parallelization" ON)
option(USE_VULKAN "Use Vulkan for visualization" OFF)

# Find required packages
find_package(Threads REQUIRED)
if(USE_OPENMP)
    find_package(OpenMP)
    if(NOT OpenMP_CXX_FOUND)
        message(WARNING "OpenMP not found. Continuing without OpenMP support.")
        set(USE_OPENMP OFF)
    endif()
endif()

# Include directories
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)

# External dependencies
include(FetchContent)

# Eigen for linear algebra
FetchContent_Declare(
    eigen
    GIT_REPOSITORY https://gitlab.com/libeigen/eigen.git
    GIT_TAG 3.4.0
)
FetchContent_MakeAvailable(eigen)

# spdlog for logging
FetchContent_Declare(
    spdlog
    GIT_REPOSITORY https://github.com/gabime/spdlog.git
    GIT_TAG v1.9.2
)
FetchContent_MakeAvailable(spdlog)

# JSON for Modern C++ for configuration files
FetchContent_Declare(
    json
    GIT_REPOSITORY https://github.com/nlohmann/json.git
    GIT_TAG v3.11.2
)
FetchContent_MakeAvailable(json)

# Find Python for Python bindings
set(Python3_FIND_STRATEGY LOCATION)
find_package(Python3 COMPONENTS Interpreter Development)
if(NOT Python3_FOUND)
    message(WARNING "Python3 not found. Continuing without Python support.")
else()
    message(STATUS "Found Python3: ${Python3_EXECUTABLE}")
    message(STATUS "Python3 Include Dirs: ${Python3_INCLUDE_DIRS}")
    message(STATUS "Python3 Libraries: ${Python3_LIBRARIES}")
    
    # PyBind11 for Python bindings
    FetchContent_Declare(
        pybind11
        GIT_REPOSITORY https://github.com/pybind/pybind11.git
        GIT_TAG v2.9.2
    )
    FetchContent_MakeAvailable(pybind11)
endif()

# OpenGL and GLFW for visualization
find_package(OpenGL)
if(NOT OpenGL_FOUND)
    message(WARNING "OpenGL not found. Continuing without OpenGL support.")
else()
    FetchContent_Declare(
        glfw
        GIT_REPOSITORY https://github.com/glfw/glfw.git
        GIT_TAG 3.3.6
    )
    FetchContent_MakeAvailable(glfw)
endif()

if(USE_VULKAN)
    find_package(Vulkan)
    if(NOT Vulkan_FOUND)
        message(WARNING "Vulkan not found. Continuing without Vulkan support.")
        set(USE_VULKAN OFF)
    endif()
endif()

# Set up global compile definitions
add_compile_definitions(SPDLOG_ACTIVE_LEVEL=SPDLOG_LEVEL_DEBUG)

# Add subdirectories
add_subdirectory(src)
# if(BUILD_TESTS)
#     enable_testing()
#     add_subdirectory(tests)
# endif()

# Print summary
message(STATUS "")
message(STATUS "MARL Framework Configuration Summary")
message(STATUS "======================================")
message(STATUS "CMAKE_SYSTEM_NAME          = ${CMAKE_SYSTEM_NAME}")
message(STATUS "CMAKE_CXX_COMPILER_ID      = ${CMAKE_CXX_COMPILER_ID}")
message(STATUS "CMAKE_CXX_COMPILER_VERSION = ${CMAKE_CXX_COMPILER_VERSION}")
message(STATUS "CMAKE_BUILD_TYPE           = ${CMAKE_BUILD_TYPE}")
message(STATUS "BUILD_TESTS                = ${BUILD_TESTS}")
message(STATUS "USE_OPENMP                 = ${USE_OPENMP}")
message(STATUS "USE_VULKAN                 = ${USE_VULKAN}")
if(Python3_FOUND)
    message(STATUS "Python3_VERSION            = ${Python3_VERSION}")
    message(STATUS "Python3_INCLUDE_DIRS       = ${Python3_INCLUDE_DIRS}")
    message(STATUS "Python3_LIBRARIES          = ${Python3_LIBRARIES}")
else()
    message(STATUS "Python3                    = Not Found")
endif()
message(STATUS "OpenGL                     = ${OpenGL_FOUND}")
message(STATUS "======================================")
message(STATUS "")