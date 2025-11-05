cmake_minimum_required(VERSION 3.10)
project(lazyfoo CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

#
# No hard-coded paths needed!
# find_package will automatically find the libraries
# in Homebrew's location (/opt/homebrew).
#
find_package(SDL2 REQUIRED)
find_package(SDL2_image REQUIRED)
find_package(SDL2_ttf REQUIRED)
find_package(SDL2_mixer REQUIRED)

add_executable(lazyfoo "lessons/21_sound_effects_and_music.cpp")

# This part is still correct
target_link_libraries(lazyfoo PRIVATE
        SDL2::SDL2main
        SDL2::SDL2
        SDL2_image::SDL2_image
        SDL2_ttf::SDL2_ttf
        SDL2_mixer::SDL2_mixer
)

#
# The .dll copying section is removed.
# On macOS, the dynamic linker automatically knows
# where to find Homebrew's .dylib files.
#

# This resource copying logic is perfect, leave it as-is!
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