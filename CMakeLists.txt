#[===[

2024-01-11 / imdongye

cmake -B build .
cmake --build build --config Debug

실행경로는 main 폴더, assets폴더를 사용했다면 실행파일을 배포할때 assets 폴더도 같이 배포해야한다.

Todo:
    CTest CPack

]===]


cmake_minimum_required(VERSION 3.0.0...3.27 FATAL_ERROR)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 99)

set(CMAKE_VERBOSE_MAKEFILE true)
set(CMAKE_GENERATOR_PLATFORM x64)


project(
    imdx 
    VERSION 0.1.0 
    LANGUAGES C CXX
)


set(lib_name "imgui")
set(lib_dir "${CMAKE_SOURCE_DIR}/vendor/imgui")
file(GLOB_RECURSE lib_files "${lib_dir}/*.*")
list(REMOVE_ITEM lib_files "${lib_dir}/misc/freetype/imgui_freetype.cpp")
list(REMOVE_ITEM lib_files "${lib_dir}/misc/freetype/imgui_freetype.h")
add_library(${lib_name} STATIC ${lib_files})
source_group(TREE "${lib_dir}" FILES ${lib_files})
set_target_properties(imgui PROPERTIES FOLDER "vendor")
target_include_directories(${lib_name} PUBLIC ${lib_dir} ${lib_dir}/backends)
target_compile_definitions(${lib_name} 
    PRIVATE
        ImTextureID=ImU64
)

set(exe_name "imdx_exe")
set(main_dir "${CMAKE_SOURCE_DIR}/main")
file(GLOB_RECURSE main_files CONFIGURE_DEPENDS "${main_dir}/*.*")
add_executable(${exe_name} ${main_files})
source_group(TREE "${CMAKE_SOURCE_DIR}/main" FILES ${main_files})
target_link_libraries(${exe_name} 
    PRIVATE
        imgui
        d3d12.lib
        d3dcompiler.lib
        dxgi.lib
)
add_dependencies(${exe_name} 
    imgui
)
target_compile_options(${exe_name} 
    PRIVATE
        /W4
        #/WX
        /utf-8
)
target_compile_definitions(${exe_name} 
    PRIVATE
        WINDOW_WIDTH=600
        WINDOW_HEIGHT=400
)


set_property(DIRECTORY ${CMAKE_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT ${exe_name})


set(working_dir "${CMAKE_SOURCE_DIR}/main")
# From: https://stackoverflow.com/questions/41864259/how-to-set-working-directory-for-visual-studio-2017-rc-cmake-project
set_target_properties(${exe_name} PROPERTIES VS_DEBUGGER_WORKING_DIRECTORY ${working_dir})