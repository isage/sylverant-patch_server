cmake_minimum_required(VERSION 2.8)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake/Modules)

project(patch_server)
set(patch_server_VERSION_MAJOR 1)
set(patch_server_VERSION_MINOR 0)

set(patch_server_VERSION_STRING "${patch_server_MAJOR_VERSION}.${patch_server_MINOR_VERSION}.${patch_server_PATCH_VERSION}")


option(IPV6 "build application with IPv6 Support" ON) # ON is the default

include_directories(src)

set(LIBSYLVERANT_LIBRARY sylverant)
include_directories(${CMAKE_SOURCE_DIR}/lib/libsylverant/include)

find_package(Iconv REQUIRED)
include_directories(${ICONV_INCLUDE_DIR})

find_package(LibXml2)
include_directories(${LIBXML2_INCLUDE_DIR})


IF(ICONV_SECOND_ARGUMENT_IS_CONST)
  add_definitions (-DICONV_CONST=const)
ELSE()
  add_definitions (-DICONV_CONST=)
ENDIF()

file(GLOB SOURCES src/*.c)

add_definitions (-Wall)

add_definitions (-DVERSION="${patch_server_VERSION_STRING}")

if(IPV6)
  add_definitions(-DENABLE_IPV6)
endif(IPV6)


add_subdirectory(lib/libsylverant)

add_executable(sylverant-patch ${SOURCES})
target_link_libraries(sylverant-patch ${LIBSYLVERANT_LIBRARY} ${LIBXML2_LIBRARIES})

if(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
    install(TARGETS sylverant-patch RUNTIME DESTINATION bin)
endif()