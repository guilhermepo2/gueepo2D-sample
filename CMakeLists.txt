cmake_minimum_required(VERSION 3.11)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)
project(gueepo2D-sample)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# dependencies
add_subdirectory(src/lib/gueepo2D/gueepo2D/thirdparty/glad)
add_subdirectory(src/lib/gueepo2D/gueepo2D/engine)
set_target_properties(glad gueepo2D-engine PROPERTIES FOLDER thirdparty)

add_executable(${PROJECT_NAME}
    src/main.cpp
)

target_link_directories(${PROJECT_NAME}
    PUBLIC
        ${SDL2_PATH}/lib/x64
)

target_link_libraries(${PROJECT_NAME} gueepo2D-engine)

if(WIN32)
    if(EXISTS ${SDL2_PATH}/lib/x64/SDL2.dll)
        message("copying SDL2... " ${SDL2_PATH} "/lib/x64/SDL2/dll")
        add_custom_command(
            TARGET ${PROJECT_NAME} POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy ${SDL2_PATH}/lib/x64/SDL2.dll $<TARGET_FILE_DIR:${PROJECT_NAME}>/../
        )
    endif()

    if(EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/src/assets)
    add_custom_command(
        TARGET ${PROJECT_NAME} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR}/src/assets $<TARGET_FILE_DIR:${PROJECT_NAME}>/../assets
    )
    endif()
endif()

if(APPLE)
    add_compile_definitions(GUEEPO2D_MACOS)

    # on mac we have to copy a little bit differently
    if(EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/src/assets)
    add_custom_command(
        TARGET ${PROJECT_NAME} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR}/src/assets $<TARGET_FILE_DIR:${PROJECT_NAME}>/../build/assets
    )
    endif()
endif()


if(MSVC)
    set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT ${PROJECT_NAME})
endif()

set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -DGUEEPO2D_DEBUG")