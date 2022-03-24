cmake_minimum_required(VERSION 3.19)
project(hikvision_camera_tool)

set(CMAKE_CXX_STANDARD 11)
SET(CMAKE_BUILD_TYPE RELEASE)
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DPATH=\"\\\"${PROJECT_SOURCE_DIR}\\\"\"")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D${CMAKE_SYSTEM_NAME}")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -pthread")
SET(BIN_NAME "run")
set(HIKVISION_CAMERA_LIB_DIR "${PROJECT_SOURCE_DIR}/camera/hikvision")


FIND_PACKAGE(OpenCV 4 REQUIRED) # 数字根据实际使用opencv版本更改,例如opencv3.X就改为3，opencv4.X就改为4

# 指定相机动态链接库所在路径
LINK_DIRECTORIES(${HIKVISION_CAMERA_LIB_DIR})
LINK_DIRECTORIES(${HIKVISION_CAMERA_LIB_DIR}/64)

# 指定相机库目录
INCLUDE_DIRECTORIES(${HIKVISION_CAMERA_LIB_DIR}/include)

FILE(GLOB_RECURSE CAMERA_INCLUDE_FILES "${HIKVISION_CAMERA_LIB_DIR}/include/*.h")
add_executable(${BIN_NAME}  main.cpp ${CAMERA_INCLUDE_FILES}) #

# 链接到目标
TARGET_LINK_LIBRARIES(${BIN_NAME} ${OpenCV_LIBS})
TARGET_LINK_LIBRARIES(${BIN_NAME} MvCameraControl)

# 在不同系统导入库的方法（以实际项目相机动态连接库所在路径为准！）
# 本示例动态连接库位于 项目根目录下的 /camera/hikvision/
IF (CMAKE_SYSTEM_NAME MATCHES "Linux")          # Linux系统
    MESSAGE(STATUS "current platform: Linux ")
    TARGET_LINK_LIBRARIES(${BIN_NAME} "${PROJECT_SOURCE_DIR}/camera/hikvision/libMVSDK.so")
ELSEIF (CMAKE_SYSTEM_NAME MATCHES "Windows")
    MESSAGE(STATUS "current platform: Windows") # Windows系统
    TARGET_LINK_LIBRARIES(${BIN_NAME} "${PROJECT_SOURCE_DIR}/camera/hikvision/MVCAMSDK_X64.lib")
ELSEIF (CMAKE_SYSTEM_NAME MATCHES "Darwin")
    MESSAGE(STATUS "current platform: Mac")     # Mac系统
    TARGET_LINK_LIBRARIES(${BIN_NAME} "${PROJECT_SOURCE_DIR}/camera/hikvision/libmvsdk.dylib")
ELSE ()
    MESSAGE(STATUS "Unsupport platform: ${CMAKE_SYSTEM_NAME}")
ENDIF()