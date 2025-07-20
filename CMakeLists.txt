include(platform/platform.cmake)

add_subdirectory(cegui)

file(GLOB FCT_CEGUI_RENDERER_MODULE_SOURCES
        LIST_DIRECTORIES false
        CONFIGURE_DEPENDS
        "./src/Renderer/*.cpp"
)
file(GLOB FCT_CEGUI_RENDERER_MODULE_HEADERS
        LIST_DIRECTORIES false
        CONFIGURE_DEPENDS
        "./src/Renderer/*.h"
        "./src/Renderer/*.hpp"
)

file(GLOB FCT_CEGUI_SRC_HEADERS
        LIST_DIRECTORIES false
        CONFIGURE_DEPENDS
        "./src/*.h"
        "./src/*.hpp"
)

set(ALL_FCT_CEGUI_HEADERS
        ${FCT_CEGUI_RENDERER_MODULE_HEADERS}
        ${FCT_CEGUI_SRC_HEADERS}
)

list(FILTER ALL_FCT_CEGUI_HEADERS EXCLUDE REGEX ".*FCT_CEGUI\\.h$")

add_library(FCT_CEGUI STATIC
        ${FCT_CEGUI_RENDERER_MODULE_SOURCES}
)

file(MAKE_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include)
file(MAKE_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/FCT_CEGUI)

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/src/FCT_CEGUI.h ${CMAKE_CURRENT_SOURCE_DIR}/include/FCT_CEGUI.h COPYONLY)

foreach(header_file ${ALL_FCT_CEGUI_HEADERS})
    file(RELATIVE_PATH relative_path ${CMAKE_CURRENT_SOURCE_DIR}/src ${header_file})
    get_filename_component(header_dir ${relative_path} DIRECTORY)

    if(header_dir)
        file(MAKE_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/FCT_CEGUI/${header_dir})
    endif()

    configure_file(${header_file} ${CMAKE_CURRENT_SOURCE_DIR}/include/FCT_CEGUI/${relative_path} COPYONLY)
endforeach()

find_package(glm CONFIG REQUIRED)

target_include_directories(FCT_CEGUI PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/cegui/cegui/include
        ${CMAKE_CURRENT_BINARY_DIR}/cegui/cegui/include
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>
)

target_link_libraries(FCT_CEGUI PRIVATE
        ${CEGUI_BASE_LIBNAME}
        glm::glm
        FCT
)
