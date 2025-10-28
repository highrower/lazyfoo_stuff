cmake_minimum_required(VERSION 3.10)
project(lazyfoo CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(SDL2_DIR "C:/mingw_dev_lib/SDL2-2.32.10/x86_64-w64-mingw32/lib/cmake/SDL2")
set(SDL2_image_DIR "C:/mingw_dev_lib/SDL2_image-2.8.8/x86_64-w64-mingw32/lib/cmake/SDL2_image")
set(SDL2_ttf_DIR "C:/mingw_dev_lib/SDL2_ttf-2.24.0/x86_64-w64-mingw32/lib/cmake/SDL2_ttf")
set(SDL2_mixer_DIR "C:/mingw_dev_lib/SDL2_mixer-2.8.1/x86_64-w64-mingw32/lib/cmake/SDL2_mixer")

find_package(SDL2 REQUIRED)
find_package(SDL2_image REQUIRED)
find_package(SDL2_ttf REQUIRED)
find_package(SDL2_mixer REQUIRED)

add_executable(lazyfoo "lessons/21_sound_effects_and_music.cpp")

target_link_libraries(lazyfoo PRIVATE
        SDL2::SDL2main
        SDL2::SDL2
        SDL2_image::SDL2_image
        SDL2_ttf::SDL2_ttf
        SDL2_mixer::SDL2_mixer
)

set(DLL_FILES
        "C:/mingw_dev_lib/SDL2-2.32.10/x86_64-w64-mingw32/bin/SDL2.dll"
        "C:/mingw_dev_lib/SDL2_image-2.8.8/x86_64-w64-mingw32/bin/SDL2_image.dll"
        "C:/mingw_dev_lib/SDL2_ttf-2.24.0/x86_64-w64-mingw32/bin/SDL2_ttf.dll"
        "C:/mingw_dev_lib/SDL2_mixer-2.8.1/x86_64-w64-mingw32/bin/SDL2_mixer.dll"
)

foreach(dll_file IN LISTS DLL_FILES)
    add_custom_command(TARGET lazyfoo POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy_if_different
            "${dll_file}"
            "$<TARGET_FILE_DIR:lazyfoo>"
    )
endforeach()

file(GLOB RESOURCE_FILES "media/*")
set(RESOURCE_FILES_BINDIR)

foreach(resource_file IN LISTS RESOURCE_FILES)
    get_filename_component(res_file_name ${resource_file} NAME)
    set(resource_file_bindir "${CMAKE_CURRENT_BINARY_DIR}/media/${res_file_name}")

    add_custom_command(OUTPUT "${resource_file_bindir}"
            COMMAND ${CMAKE_COMMAND} -E copy_if_different
            "${resource_file}"
            "${resource_file_bindir}"
            DEPENDS "${resource_file}"
    )
    list(APPEND RESOURCE_FILES_BINDIR "${resource_file_bindir}")
endforeach()

add_custom_target(copy-resources DEPENDS "${RESOURCE_FILES_BINDIR}")
add_dependencies(lazyfoo copy-resources)