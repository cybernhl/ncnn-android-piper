project(piperncnn)
cmake_minimum_required(VERSION 3.10)

# =====================================================================
# == 接收來自 Gradle 的 NCNN 路徑
# =====================================================================

# 檢查 Gradle 是否傳入了路徑參數 'ncnn_DIR_FROM_GRADLE'
if(DEFINED ncnn_DIR_FROM_GRADLE)
    # 如果有，則使用 Gradle 傳來的路徑
    # ${ANDROID_ABI} 變數確保能正確找到對應 CPU 架構的函式庫
    set(ncnn_DIR ${ncnn_DIR_FROM_GRADLE}/${ANDROID_ABI}/lib/cmake/ncnn)
    message(STATUS "Using NCNN path from Gradle: ${ncnn_DIR}")
else()
    # 如果 Gradle 沒有傳入（例如直接從命令列執行 cmake），則使用一個備用的相對路徑
    # 這樣可以保持向下相容，但會提示使用者同步 Gradle
    set(ncnn_DIR ${CMAKE_SOURCE_DIR}/ncnn-android-vulkan/${ANDROID_ABI}/lib/cmake/ncnn)
    message(WARNING "ncnn_DIR_FROM_GRADLE not set. Falling back to default path. Please sync project with Gradle.")
endif()

# 尋找 ncnn 套件
find_package(ncnn REQUIRED)

# 加入您的 C++ 原始碼並建立共享函式庫
add_library(piperncnn SHARED piperncnn.cpp piper.cpp simpleg2p.cpp audioplayer.cpp)

# 連結 ncnn 和 OpenSLES 函式庫
target_link_libraries(piperncnn ncnn OpenSLES)
