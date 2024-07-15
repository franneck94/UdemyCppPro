cmake_minimum_required(VERSION 3.22)

project(
    LibExample
    VERSION 1.0.0
    LANGUAGES C CXX)

set(LIBRARY_NAME LibExample)

add_library(${LIBRARY_NAME} STATIC src/lib.cc)
target_include_directories(${LIBRARY_NAME} PUBLIC inc/)

# if(CMAKE_CXX_COMPILER_ID MATCHES "Clang" OR CMAKE_CXX_COMPILER_ID MATCHES "GNU")
#     if(CMAKE_BUILD_TYPE STREQUAL "Debug")
#         target_compile_options(${LIBRARY_NAME} PRIVATE "-Wall")
#     else()
#         target_compile_options(${LIBRARY_NAME} PRIVATE "-Wall -Werror")
#     endif()
# endif()

# if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
#     if(CMAKE_BUILD_TYPE STREQUAL "Debug")
#         target_compile_options(${LIBRARY_NAME} PRIVATE "/W4")
#     else()
#         target_compile_options(${LIBRARY_NAME} PRIVATE "/W4 /WX")
#     endif()
# endif()

# if(CMAKE_CXX_COMPILER_ID MATCHES "Clang" OR CMAKE_CXX_COMPILER_ID MATCHES "GNU")
#     target_compile_options(
#         ${LIBRARY_NAME} PRIVATE $<$<CONFIG:Debug>:-Wall>
#                                 $<$<CONFIG:Release>:-Wall -Werror>)
# endif()

# if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
#     target_compile_options(${LIBRARY_NAME} PRIVATE $<$<CONFIG:Debug>:/W4>
#                                                    $<$<CONFIG:Release>:/W4 /WX>)
# endif()

target_compile_options(
    ${LIBRARY_NAME}
    PRIVATE
        $<$<AND:$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:GNU>>,$<CONFIG:Debug>>:-Wall>
        $<$<AND:$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:GNU>>,$<CONFIG:Release>>:-Wall -Werror>)

target_compile_options(
    ${LIBRARY_NAME}
    PRIVATE $<$<CXX_COMPILER_ID:MSVC>:$<$<CONFIG:Debug>:/W4>>
            $<$<CXX_COMPILER_ID:MSVC>:$<$<CONFIG:Release>:/W4 /WX>>)

get_target_property(RESULT ${LIBRARY_NAME} COMPILE_OPTIONS)
message("-- Target compiler flags are: ${RESULT}")
