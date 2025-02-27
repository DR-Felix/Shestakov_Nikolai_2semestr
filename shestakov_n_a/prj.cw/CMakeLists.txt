cmake_minimum_required(VERSION 3.18)   

project(Niblack_binarization)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
include(build_prefix)

# Add build flags for documentation and sample program
set(SAMPLE ${SAMPLE})
set(DOXYGEN ${DOXYGEN})

if(SAMPLE)
    set(SAMPLE $ENV{SAMPLE})
endif()

if(DOXYGEN)
    set(DOXYGEN $ENV{DOXYGEN})
endif()

set(CMAKE_POSITION_INDEPENDENT_CODE ON)
set(CMAKE_C_EXTENSIONS OFF)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

include(GNUInstallDirs)
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-deprecated-anon-enum-enum-conversion")
endif()

if(CMAKE_COMPILER_IS_GNUCXX)
    set(CMAKE_CXX_FLAGS "-Wall -Wextra")
endif(CMAKE_COMPILER_IS_GNUCXX)

find_package(OpenCV REQUIRED)
include_directories( ${OpenCV_INCLUDE_DIRS} )

if(DOXYGEN)
    find_package(Doxygen)
    if (DOXYGEN_FOUND)
        set(niblack_DOXYGEN_IN ${CMAKE_CURRENT_SOURCE_DIR}/docs/Doxyfile.in)
        set(niblack_DOXYGEN_OUT ${CMAKE_BINARY_DIR}/Doxyfile)
        configure_file(${niblack_DOXYGEN_IN} ${niblack_DOXYGEN_OUT})
        add_custom_target(niblack_doc_doxygen ALL
                COMMAND ${DOXYGEN_EXECUTABLE} ${niblack_DOXYGEN_OUT}
                WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
                COMMENT "Generating API documentation with Doxygen"
                VERBATIM)
        install (DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/niblack_doc
            DESTINATION ./docs)
    else ()
        message(STATUS "Couldn't find Doxygen")
    endif ()
endif()


add_subdirectory(src)
