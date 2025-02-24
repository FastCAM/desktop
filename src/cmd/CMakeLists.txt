project(cmd)
set(CMAKE_AUTOMOC TRUE)

add_library(cmdCore STATIC simplesslerrorhandler.cpp netrcparser.cpp)

target_link_libraries(cmdCore
  PUBLIC
  nextcloudsync
  Qt5::Core
  Qt5::Network
  )

# Need tokenizer for netrc parser
target_include_directories(cmdCore PRIVATE ${CMAKE_SOURCE_DIR}/src/3rdparty/qtokenizer)

if(UNIX AND NOT APPLE)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIE")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIE")
  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -pie")
endif()

if(CMAKE_SYSTEM_NAME STREQUAL "FreeBSD")
  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -L/usr/local/lib")
endif()

if(NOT BUILD_LIBRARIES_ONLY)
  add_executable(nextcloudcmd cmd.cpp)
  set_target_properties(nextcloudcmd PROPERTIES
    RUNTIME_OUTPUT_NAME "${APPLICATION_EXECUTABLE}cmd")

  target_link_libraries(nextcloudcmd cmdCore)

  if(BUILD_OWNCLOUD_OSX_BUNDLE)
    set_target_properties(nextcloudcmd PROPERTIES
      RUNTIME_OUTPUT_DIRECTORY "${BIN_OUTPUT_DIRECTORY}/${OWNCLOUD_OSX_BUNDLE}/Contents/MacOS")
  else()
    set_target_properties(nextcloudcmd PROPERTIES
      RUNTIME_OUTPUT_DIRECTORY ${BIN_OUTPUT_DIRECTORY})

    install(TARGETS nextcloudcmd
	  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
	  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
	  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})
  endif()
endif()
