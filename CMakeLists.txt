cmake_minimum_required(VERSION 3.10)  # 指定最低CMake版本，推荐使用较新的版本以支持更多功能

project(MySuperProject 
    VERSION 1.0.0  # 项目版本号
    DESCRIPTION "A super awesome project"
    LANGUAGES C CXX  # 这里设置了 C 和 C++，如果只用 C++ 可去掉 C
)

# 输出调试信息
message(STATUS "Project Name: ${PROJECT_NAME}")
message(STATUS "Project Version: ${PROJECT_VERSION}")
message(STATUS "Project Directory: ${PROJECT_SOURCE_DIR}")

# 指定 C++ 标准版本
set(CMAKE_CXX_STANDARD 17)  # 你可以换成 11/14/20 甚至 23
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)  # 关闭非标准扩展

# 设定编译类型（Debug / Release）
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug CACHE STRING "Choose the type of build." FORCE)
endif()
message(STATUS "Build Type: ${CMAKE_BUILD_TYPE}")

# 设定可执行文件输出目录
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

# 设定源码路径
set(SRC_DIR ${PROJECT_SOURCE_DIR}/src)
set(INC_DIR ${PROJECT_SOURCE_DIR}/include)

# 方法 1: 手动列举源码文件（适合小型项目）
# set(SOURCE_FILES
#     ${SRC_DIR}/main.c
#     # ${SRC_DIR}/module1.cpp
    
# )

# 方法 2: 自动查找所有 .cpp 文件（适合中大型项目）
# file(GLOB SOURCE_FILES ${SRC_DIR}/*.c)  # 只搜索 src 目录下的 .cpp 文件
file(GLOB_RECURSE SOURCE_FILES ${SRC_DIR}/*.c)  # 递归搜索所有子目录
# file(GLOB SOURCE_FILES ${SRC_DIR}/*.cpp)  # 只搜索 src 目录下的 .cpp 文件
# file(GLOB_RECURSE SOURCE_FILES ${SRC_DIR}/*.cpp)  # 递归搜索所有子目录

# 方法 3: aux_source_directory（适用于 C 项目）
# aux_source_directory(${SRC_DIR} SOURCE_FILES)  # 仅适用于单层目录

# 打印所有找到的源码文件
message(STATUS "Source Files: ${SOURCE_FILES}")

# 设定头文件路径
include_directories(${INC_DIR})

# 生成可执行文件
add_executable(${PROJECT_NAME} ${SOURCE_FILES})

# 链接系统库
# target_link_libraries(${PROJECT_NAME} pthread)  # 例子：链接 pthread 库

# 添加子目录（适用于大型项目，子目录里通常会有自己的 CMakeLists.txt）
# add_subdirectory(third_party/some_lib)
# add_subdirectory(src/some_module)

# 设定编译选项
target_compile_options(${PROJECT_NAME} PRIVATE
    -Wall       # 启用所有警告
    -Wextra     # 启用额外警告
    -Wpedantic  # 启用严格标准
    $<$<CONFIG:Debug>:-O0 -g>  # Debug 模式下不优化并生成调试信息
    $<$<CONFIG:Release>:-O2>   # Release 模式下优化
)

# 设定预定义宏
target_compile_definitions(${PROJECT_NAME} PRIVATE 
    PROJECT_VERSION=${PROJECT_VERSION}
    USE_FEATURE_X  # 示例：启用某个功能
)

# 设定安装规则（适用于打包发布）
install(TARGETS ${PROJECT_NAME} DESTINATION bin)
install(DIRECTORY ${INC_DIR} DESTINATION include)

# 自定义命令（例如自动拷贝文件）
# add_custom_command(
#     TARGET ${PROJECT_NAME}
#     POST_BUILD
#     COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_SOURCE_DIR}/config.json ${CMAKE_BINARY_DIR}/bin/
# )

# 开启测试（适用于单元测试）
enable_testing()
# add_test(NAME MyTest COMMAND ${PROJECT_NAME} --test)  # 添加测试命令

# 包含外部库（例如使用 FetchContent 进行依赖管理）
# include(FetchContent)
# FetchContent_Declare(
#     some_lib
#     GIT_REPOSITORY https://github.com/someone/some_lib.git
#     GIT_TAG master
# )
# FetchContent_MakeAvailable(some_lib)
# target_link_libraries(${PROJECT_NAME} some_lib)

# 生成编译数据库（方便 Clangd, VSCode, etc.）
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# 添加代码格式化工具支持（例如 clang-format）
# find_program(CLANG_FORMAT "clang-format")
# if(CLANG_FORMAT)
#     add_custom_target(format
#         COMMAND ${CLANG_FORMAT} -i ${SOURCE_FILES}
#         WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
#         COMMENT "Formatting source files..."
#     )
# endif()

# 添加静态分析工具支持（例如 clang-tidy）
# find_program(CLANG_TIDY "clang-tidy")
# if(CLANG_TIDY)
#     set(CMAKE_CXX_CLANG_TIDY ${CLANG_TIDY})
# endif()

# 添加代码覆盖率支持（适用于 gcov/lcov）
# if(CMAKE_BUILD_TYPE STREQUAL "Debug")
#     target_compile_options(${PROJECT_NAME} PRIVATE --coverage)
#     target_link_libraries(${PROJECT_NAME} PRIVATE --coverage)
# endif()

# 添加 sanitizer 支持（适用于内存泄漏检测等）
# if(CMAKE_BUILD_TYPE STREQUAL "Debug")
#     target_compile_options(${PROJECT_NAME} PRIVATE -fsanitize=address,undefined)
#     target_link_libraries(${PROJECT_NAME} PRIVATE -fsanitize=address,undefined)
# endif()

# 添加跨平台支持（例如 Windows 和 Linux 的不同配置）
# if(WIN32)
#     target_compile_definitions(${PROJECT_NAME} PRIVATE PLATFORM_WINDOWS)
# elseif(UNIX)
#     target_compile_definitions(${PROJECT_NAME} PRIVATE PLATFORM_LINUX)
# endif()

# 添加自定义目标（例如生成文档）
# find_package(Doxygen)
# if(DOXYGEN_FOUND)
#     add_custom_target(docs
#         COMMAND ${DOXYGEN_EXECUTABLE} ${CMAKE_SOURCE_DIR}/Doxyfile
#         WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
#         COMMENT "Generating documentation..."
#     )
# endif()

message(STATUS "CMake Configuration Complete!")