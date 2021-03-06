cmake_minimum_required(VERSION 2.8.3)
project(dji_guidance_sdk)

find_package(catkin QUIET)

set(DJI_guidance_LIBRARIES DJI_guidance usb-1.0)

message("System is: " ${CMAKE_SYSTEM_PROCESSOR})
if (${CMAKE_SYSTEM_NAME} MATCHES "Linux")
  if (CMAKE_SIZEOF_VOID_P EQUAL 4)
    message("-- 32bit detected")
    set(DJI_guidance_LIBRARY_DIR so/x86)
  elseif (CMAKE_SIZEOF_VOID_P EQUAL 8)
    message("-- 64bit detected")
    set(DJI_guidance_LIBRARY_DIR so/x64)
  endif ()
else()
  message("-- Non-linux platform detected but sorry we do not support :D")
  unset(DJI_guidance_LIBRARY_DIR)
  unset(DJI_guidance_LIBRARIES)
endif ()

if (${CMAKE_SYSTEM_PROCESSOR} MATCHES "armv7l" )
  set(DJI_guidance_LIBRARY_DIR so/XU3)
elseif (${CMAKE_SYSTEM_PROCESSOR} MATCHES "aarch64" )
  message("-- ${CMAKE_SYSTEM_PROCESSOR} architecture detected but sorry we do not support :D")
  unset(DJI_guidance_LIBRARY_DIR)
  unset(DJI_guidance_LIBRARIES)
endif ()

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES ${DJI_guidance_LIBRARIES}
)

if(catkin_FOUND)
  if(DJI_guidance_LIBRARY_DIR)
    file(
     COPY ${DJI_guidance_LIBRARY_DIR}/libDJI_guidance.so
      DESTINATION ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_LIB_DESTINATION}
    )
  endif()
else()
  set(CATKIN_PACKAGE_LIB_DESTINATION lib)
  set(CATKIN_GLOBAL_INCLUDE_DESTINATION include)
endif()

if(DJI_guidance_LIBRARY_DIR)
  install(
    FILES ${DJI_guidance_LIBRARY_DIR}/libDJI_guidance.so
    DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  )
endif()

install(
  DIRECTORY include/
  DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
)
