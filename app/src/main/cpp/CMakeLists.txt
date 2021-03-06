cmake_minimum_required(VERSION 3.4.1)

## 支持gnu++11
set(CMAKE_CXX_FLAGSXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11")
if(CMAKE_COMPILER_IS_GNUCXX)
    set(CMAKE_CXX_FLAGS "-std=c++11 ${CMAKE_CXX_FLAGS}")
    message(STATUS "optional:-std=c++11")
endif(CMAKE_COMPILER_IS_GNUCXX)

# 1. 定义so库和头文件所在目录，方面后面使用
set(ffmpeg_lib_dir ${CMAKE_SOURCE_DIR}/../jniLibs/${ANDROID_ABI})
set(ffmpeg_head_dir ${CMAKE_SOURCE_DIR}/ffmpeg)
set(sdl_head_dir ${CMAKE_SOURCE_DIR}/sdl)

# 2. 添加头文件目录
include_directories(${ffmpeg_head_dir}/include)
include_directories(${sdl_head_dir}/include)
include_directories(${sdl_head_dir}/src)


# 3. 添加ffmpeg相关的so库---------------------------
add_library( avutil
        SHARED
        IMPORTED )
set_target_properties( avutil
        PROPERTIES IMPORTED_LOCATION
        ${ffmpeg_lib_dir}/libavutil.so )

add_library( swresample
        SHARED
        IMPORTED )
set_target_properties( swresample
        PROPERTIES IMPORTED_LOCATION
        ${ffmpeg_lib_dir}/libswresample.so )

add_library( avcodec
        SHARED
        IMPORTED )
set_target_properties( avcodec
        PROPERTIES IMPORTED_LOCATION
        ${ffmpeg_lib_dir}/libavcodec.so )

add_library( avfilter
        SHARED
        IMPORTED)
set_target_properties( avfilter
        PROPERTIES IMPORTED_LOCATION
        ${ffmpeg_lib_dir}/libavfilter.so )

add_library( swscale
        SHARED
        IMPORTED)
set_target_properties( swscale
        PROPERTIES IMPORTED_LOCATION
        ${ffmpeg_lib_dir}/libswscale.so )

add_library( avformat
        SHARED
        IMPORTED)
set_target_properties( avformat
        PROPERTIES IMPORTED_LOCATION
        ${ffmpeg_lib_dir}/libavformat.so )

add_library( avdevice
        SHARED
        IMPORTED)
set_target_properties( avdevice
        PROPERTIES IMPORTED_LOCATION
        ${ffmpeg_lib_dir}/libavdevice.so )

#add_library( postproc
#        SHARED
#        IMPORTED)
#set_target_properties( postproc
#        PROPERTIES IMPORTED_LOCATION
#        ${ffmpeg_lib_dir}/libpostproc.so )

# 4. 添加ffmpeg相关的so库---------------------------
# SDL核心库(最重要的库)
add_library(
        SDL2
        SHARED
        IMPORTED)
set_target_properties(
        SDL2
        PROPERTIES IMPORTED_LOCATION
        ${ffmpeg_lib_dir}/libSDL2.so)

add_library(
        hidapi
        SHARED
        IMPORTED)
set_target_properties(
        hidapi
        PROPERTIES IMPORTED_LOCATION
        ${ffmpeg_lib_dir}/libhidapi.so)

add_library(
        libmain
        SHARED
        IMPORTED)
set_target_properties(
        libmain
        PROPERTIES IMPORTED_LOCATION
        ${ffmpeg_lib_dir}/libmain.so)

add_library(
        libcppos
        SHARED
        IMPORTED)
set_target_properties(
        libcppos
        PROPERTIES IMPORTED_LOCATION
        ${ffmpeg_lib_dir}/libc++_shared.so)




# ---------------------------
# 查找代码中使用到的系统库
find_library( # Sets the name of the path variable.
        log-lib

        # Specifies the name of the NDK library that
        # you want CMake to locate.
        log )



# 配置目标so库编译信息
add_library( # Sets the name of the library.
        native-lib

        # Sets the library as a shared library.
        SHARED

        # Provides a relative path to your source file(s).
#        sdl/src/main/android/SDL_android_main.c
#        sdl/src/core/android/SDL_android.c
#        sdl/src/SDL.c
#        sdl/src/dynapi/SDL_dynapi.c


        # fftool

        # 自定义功能
        native-lib.cpp
        ffmpegAudio.cpp
        ffmpeg_sdl.cpp
#        audioPlay.cpp
        ffmpegCompress.cpp
        #  SDLTest.cpp

        )


# 指定编译目标库时，cmake要链接的库
target_link_libraries(

        # 指定目标库，native-lib 是在上面 add_library 中配置的目标库
        native-lib

        # 4. 连接 FFmpeg 相关的库
        avutil
        swresample
        avcodec
        avfilter
        swscale
        avformat
        avdevice
        # 5. 链接 sdl 数据库
        SDL2
        libmain
        hidapi
        libcppos
        -landroid
        # Links the target library to the log library
        # included in the NDK.
        ${log-lib} )