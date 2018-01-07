# cmake_minimum_required(VERSION 2.8.12)
cmake_minimum_required(VERSION 3.1)
project(DEMO)



!!! SAD. CMake document not say every command's minimun required version.



## show message
message(STATUS "message write here")



## next's command use after this
add_executable(${BINARY_NAME} {CMAKE_CURRENT_SOURCE_DIR}/main.c)
add_library(${BINARY_NAME} STATIC ${CMAKE_CURRENT_SOURCE_DIR}/main.c)



## platform
if(APPLE)
    # no warning
    # https://stackoverflow.com/questions/31561309/cmake-warnings-under-os-x-macosx-rpath-is-not-specified-for-the-following-targe
    set(CMAKE_MACOSX_RPATH 0)
endif()
if(WIN32)
 # no utf-8 warning in Visual Studio https://github.com/fooofei/cpp_notes/blob/master/visual_sutdio_warning_C4819_utf-8.md
 target_compile_options(${BINARY_NAME} PRIVATE /source-charset:utf-8 /execution-charset:utf-8)
endif()



## set all after target CXX_STANDARD property, equivalent with add -std=gnu++11
# to the compile flags, or -std=c++11
# see this use way
#
# TARGET_A ...
#  set(CMAKE_CXX_STANDARD 11)
# TARGET_B ...
#
# it will add -std=gnu++11 to TARGET_B, not TARGET_A
#
# see this use way
#
#  set(CMAKE_CXX_STANDARD 11)
# TARGET_A ...
# TARGET_B ...
#
# it will add -std=gnu++11 to TARGET_B and TARGET_A
# ref https://cmake.org/cmake/help/v3.1/prop_tgt/CXX_STANDARD.html#prop_tgt:CXX_STANDARD
set(CMAKE_CXX_STANDARD 11)
# recommand use this
set_property(TARGET ${TARGET2} PROPERTY CXX_STANDARD 11)
# or
set_target_properties(${TARGET2} PROPERTIES  CXX_STANDARD 11)
# this is only use on non-Windows



## Visual Studio special
# add files to Visual Studio project files tree
# if want to add .h files to visual studio, must add .h files to compile, such as
# set(Source_files_files )
# add_library(${PROJECT_NAME} STATIC ${Source_files_files})
# or
# target_sources(${PROJECT_NAME} PRIVATE ${Source_files_files})
# if not use above, only use source_group(), will not show .h files
# first arg `files` is the name in Visual Studio
source_group(files FILES ${Source_files_files})



## add .h headers located directorys
# not include_directories(${CMAKE_CURRENT_SOURCE_DIR})
target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/inlcude)
#
# if the target is a static linked library, and want other library who linked the library
# to use the library direct by the header, not by relative header path,then write this:
target_include_directories(${PROJECT_NAME} INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/inlcude)



## add .cpp src files, target_sources is required 3.1 and above
target_sources(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src/u16string_bytes.cpp)



## add libs located directorys,
# besides you must use target_link_libraries() to specific lib
link_directories(${CMAKE_CURRENT_SOURCE_DIR})



## add source code compile defines
if (WIN32)
    target_compile_definitions(${TARGET1} PRIVATE "UNICODE")
    target_compile_definitions(${TARGET1} PRIVATE "_UNICODE")
endif ()



## add source code compile options, same with gcc -fPIC
# compile defines / compile options is different thing
# most time use PRIVATE
# https://stackoverflow.com/questions/23995019/what-is-the-modern-method-for-setting-general-compile-flags-in-cmake
# not use set(CMAKE_CXX_FLAGS "")
# not use add_compile_options()
if (NOT WIN32)
target_compile_options(${PROJECT_NAME} PRIVATE -fPIC)
# for multi define global variables
target_compile_options(${BINARY_NAME} PRIVATE -fno-common )
endif()


## <SAFETY> no export functions on gcc
if (NOT WIN32)
target_compile_options(${PROJECT_NAME} PRIVATE -fvisibility=hidden)
endif()



## generate .pdb files when build with MSVC
# target_compile_options(${TARGET1} PRIVATE /Zi) is not work
if(MSVC)
    set_property(TARGET ${TARGET1}  APPEND PROPERTY LINK_FLAGS /DEBUG:FULL)
endif()



## link other librarys, same with -ldl -liconv
target_link_libraries(${PROJECT_NAME} dl)
target_link_libraries(${PROJECT_NAME} iconv)



## add other library for static link
# first add_subdirectory(), then target_link_libraries()
if (NOT TARGET other_library)
    add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../  build_other_library)
endif ()
target_link_libraries(${PROJECT_NAME} other_library)



## copy generate binary file, at the time post build
add_custom_command(
TARGET ${PROJECT_NAME} POST_BUILD/PRE_BUILD
COMMAND ${CMAKE_COMMAND} -E copy_if_different $<TARGET_FILE:${PROJECT_NAME}> ../>
)



## <SAFETY> remove symbol if release
if (NOT WIN32 AND CMAKE_BUILD_TYPE)
    string(TOUPPER ${CMAKE_BUILD_TYPE} CMAKE_BUILD_TYPE_UPPER)
    if (CMAKE_BUILD_TYPE_UPPER MATCHES RELEASE)
        add_custom_command(TARGET ${TARGET1} POST_BUILD
                COMMAND ${CMAKE_STRIP} --strip-unneeded $<TARGET_FILE:${TARGET1}>
                )

    endif ()
endif ()



## msvc static link runtime
build static link runtime library, so the generate executable file can run
on other machine, not occur runtime error
How can I build my MSVC application with a static runtime?
## when use this static link runtime, the sub modules also use static link,
## we donnot need to set every sub module to static link
https://cmake.org/Wiki/CMake_FAQ#How_can_I_build_my_MSVC_application_with_a_static_runtime.3F
test it works
or install "C:\Program Files (x86)\Microsoft Visual Studio\2017\Community
\VC\Redist\MSVC\14.11.25325\vcredist_x86.exe"

also when need to build run on Windows XP, need pass options to cmake:
cmake -G "Visual Studio 15 2017" -T v141_xp  ..



## set the generate binary file extension /rename
set(CMAKE_SHARED_LIBRARY_SUFFIX ".pyd")
# ref https://cmake.org/Wiki/CMake_Useful_Variables#System_.26_Compiler_Information
# ref https://cmake.org/cmake/help/v3.0/manual/cmake-variables.7.html



## PUBLIC PRIVATE INTERFACE
# https://docs.google.com/presentation/d/18fY0zDtJCMUW5WdY2ZOfKtvb7lXEbBPFe_I6MNJC0Qw/edit#slide=id.g15c9891529_0_31
# same with https://github.com/toeb/moderncmake/blob/772c45522e4fcc48f3d6cfdc9386981dfb75a8dc/Modern%20CMake.pdf
# https://stackoverflow.com/questions/26037954/cmake-target-link-libraries-interface-dependencies
# https://cmake.org/pipermail/cmake/2016-May/063400.html



## python dev
# use `python-config --prefix` to get python path, not use
# find_package() which is cmake builtin module.
# a useage to see:
# https://github.com/fooofei/py_string_address/blob/57991beef2a0379adeb252c6225b17dc1fb61588/CMakeLists.txt
#
# notice macOS'python installation with framework is
# env PYTHON_CONFIGURE_OPTS="--enable-framework" pyenv install 2.7.13
# macOS's native python cannot use to develop for SDK.



## builds
# mkdir build
# cd build
# cmake -DCMAKE_BUILD_TYPE=Debug ..



## build posix config
-DCMAKE_BUILD_TYPE=Debug/Release
-DCMAKE_CXX_COMPILER=/usr/local/bin/afl-clang++
-DCMAKE_C_COMPILER=/usr/local/bin/afl-clang



## build on Windows/x64
cmake -G "Visual Studio 15 2017 Win64" ..
cmake --build . --config Release
cmake -G "Visual Studio 15 2017" -T v141_xp  ..


## current CMakeLists.txt directory
# CMAKE_HOME_DIRECTORY not reliable, it can be other CMakeLists.txt directory
add_library(${PROJECT_NAME} STATIC ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)



## helper function
function(print_all_variables)
    get_cmake_property(_variableNames VARIABLES)
    foreach (_variableName ${_variableNames})
        message(STATUS "${_variableName}=${${_variableName}}")
    endforeach()
endfunction()



## helper macros
macro(append_variable dest args)
    set(${dest} "${${dest}}${args}")
endmacro()

macro(append_cflags args)
    #string(APPEND CMAKE_C_FLAGS ${args})
    append_variable(CMAKE_C_FLAGS ${args})
endmacro()

macro(append_cxxflags args)
    #string(APPEND CMAKE_CXX_FLAGS ${args})
    append_variable(CMAKE_CXX_FLAGS ${args})
endmacro()

macro(append_cflags_debug args)
    #string(APPEND CMAKE_C_FLAGS_DEBUG ${args})
    append_variable(CMAKE_C_FLAGS_DEBUG ${args})
endmacro()

macro(append_cflags_release args)
    #string(APPEND CMAKE_C_FLAGS_RELEASE ${args})
    append_variable(CMAKE_C_FLAGS_RELEASE ${args})
endmacro()

macro(append_cxxflags_debug args)
    #string(APPEND CMAKE_CXX_FLAGS_DEBUG ${args})
    append_variable(CMAKE_CXX_FLAGS_DEBUG ${args})
endmacro()

macro(append_cxxflags_release args)
    #string(APPEND CMAKE_CXX_FLAGS_RELEASE ${args})
    append_variable(CMAKE_CXX_FLAGS_RELEASE ${args})
endmacro()


## install CMake

one way to compile & install CMake is 

download from https://cmake.org/files/v3.10/cmake-3.10.1.tar.gz

```shell
tar xvf ./cmake-3.10.1.tar.gz
cd cmake-3.10.1
./configure
make -j8
```

or district download binary from https://cmake.org/files/v3.10/cmake-3.10.1-Linux-x86_64.tar.gz