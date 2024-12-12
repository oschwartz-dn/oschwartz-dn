cmake_minimum_required(VERSION 3.9)
project("cheetah_proj")

set(CMAKE_CXX_STANDARD 17)

# Include the root 'cheetah' directory
include_directories("${CMAKE_SOURCE_DIR}/cheetah")

# Include 'cheetah/services/control/quagga' directory
include_directories("${CMAKE_SOURCE_DIR}/cheetah/services/control/quagga")

# Collect all source files under 'cheetah'
file(GLOB_RECURSE SOURCE_FILES
        "${CMAKE_SOURCE_DIR}/cheetah/**/*.cpp"
        "${CMAKE_SOURCE_DIR}/cheetah/**/*.cc"
        "${CMAKE_SOURCE_DIR}/cheetah/**/*.cxx"
        "${CMAKE_SOURCE_DIR}/cheetah/**/*.c"
)

# Exclude protobuf files (*.pb.*) from source files
list(FILTER SOURCE_FILES EXCLUDE REGEX ".*\\.pb\\..*")

# Filter out non-existent source files (broken symlinks)
set(VALID_SOURCE_FILES)
foreach(file ${SOURCE_FILES})
    if(EXISTS "${file}")
        list(APPEND VALID_SOURCE_FILES "${file}")
    else()
        message(STATUS "Excluding non-existent source file: ${file}")
    endif()
endforeach()
set(SOURCE_FILES "${VALID_SOURCE_FILES}")

# Ensure SOURCE_FILES is not empty
if(NOT SOURCE_FILES)
    message(FATAL_ERROR "No valid source files found for target 'cheetah'")
endif()

# Add the executable with all the collected source files
add_executable("cheetah" ${SOURCE_FILES})

# Collect header files separately for IDE indexing
file(GLOB_RECURSE HEADER_FILES
        "${CMAKE_SOURCE_DIR}/cheetah/**/*.hpp"
        "${CMAKE_SOURCE_DIR}/cheetah/**/*.h"
        "${CMAKE_SOURCE_DIR}/cheetah/**/*.hh"
        "${CMAKE_SOURCE_DIR}/cheetah/**/*.hxx"
)

# Filter out non-existent header files (broken symlinks)
set(VALID_HEADER_FILES)
foreach(file ${HEADER_FILES})
    if(EXISTS "${file}")
        list(APPEND VALID_HEADER_FILES "${file}")
    else()
        message(STATUS "Excluding non-existent header file: ${file}")
    endif()
endforeach()
set(HEADER_FILES "${VALID_HEADER_FILES}")

# Add headers to the target for IDE indexing
target_sources(cheetah PRIVATE ${HEADER_FILES})

# Path to googletest-release-1.10.0
set(GTEST_SOURCE_DIR "${CMAKE_SOURCE_DIR}/cheetah/services/control/quagga/libcpp/googletest-release-1.10.0")

# Add googletest to the build
add_subdirectory(${GTEST_SOURCE_DIR} EXCLUDE_FROM_ALL)

# Include directories for GTest and GMock
include_directories(
        ${GTEST_SOURCE_DIR}/googletest/include
        ${GTEST_SOURCE_DIR}/googlemock/include
)