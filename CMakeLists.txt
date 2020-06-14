cmake_minimum_required(VERSION 3.11)
project(
  log
  VERSION 1.0.0
  LANGUAGES C)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  set(DEFAULT_BUILD_TYPE "Debug")
  message(
    STATUS
      "Setting build type to '${DEFAULT_BUILD_TYPE}' as none was specified.")
  set(CMAKE_BUILD_TYPE
      "${DEFAULT_BUILD_TYPE}"
      CACHE STRING "Choose the type of build." FORCE)
  # Set the possible values of build type for cmake-gui
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release"
                                               "MinSizeRel" "RelWithDebInfo")
endif()

include(GNUInstallDirs)
set(SOURCE_FILES src/log.c)

add_library(${PROJECT_NAME} STATIC ${SOURCE_FILES})
add_library(${PROJECT_NAME}-shared SHARED ${SOURCE_FILES})

set_property(TARGET ${PROJECT_NAME} PROPERTY POSITION_INDEPENDENT_CODE ON)
set_property(TARGET ${PROJECT_NAME}-shared PROPERTY POSITION_INDEPENDENT_CODE
                                                    ON)

target_include_directories(${PROJECT_NAME}
                           PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include/)
target_include_directories(${PROJECT_NAME}-shared
                           PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include/)
target_include_directories(
  ${PROJECT_NAME}
  PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
         $<INSTALL_INTERFACE:include>
  PRIVATE src)

set_target_properties(${PROJECT_NAME} PROPERTIES VERSION ${PROJECT_VERSION}
                                                 SOVERSION 1)
set_target_properties(${PROJECT_NAME} PROPERTIES OUTPUT_NAME log
                                                 CLEAN_DIRECT_OUTPUT 1)
set_target_properties(${PROJECT_NAME}-shared PROPERTIES OUTPUT_NAME log
                                                        CLEAN_DIRECT_OUTPUT 1)
install(
  TARGETS ${PROJECT_NAME} ${PROJECT_NAME}-shared
  EXPORT log
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
install(DIRECTORY include/ DESTINATION ${PROJECT_NAME})
install(EXPORT log DESTINATION share/${PROJECT_NAME}/cmake)

export(TARGETS ${PROJECT_NAME} FILE ${PROJECT_NAME}.cmake)

if(NOT TARGET uninstall)
  configure_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake_uninstall.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake" IMMEDIATE @ONLY)

  add_custom_target(
    uninstall COMMAND ${CMAKE_COMMAND} -P
                      ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake)
endif()
