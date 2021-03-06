cmake_minimum_required(VERSION 2.8)

# VitaSDK defines
if( NOT DEFINED CMAKE_TOOLCHAIN_FILE )
  if( DEFINED ENV{VITASDK} )
    set(CMAKE_TOOLCHAIN_FILE "$ENV{VITASDK}/share/vita.toolchain.cmake" CACHE PATH "toolchain file")
  else()
    message(FATAL_ERROR "Please define VITASDK to point to your SDK path!")
  endif()
endif()

# Project start
set(VITA_APPNAME ZeldaNSQ)
set(VITA_TITLEID "ZELDANSQ1")

project(${VITA_APPNAME})
include("${VITASDK}/share/vita.cmake" REQUIRED)

# Flags and includes
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D_REENTRANT -fno-exceptions")

include_directories(
  ${VITASDK}/arm-vita-eabi/include/SDL
)

link_directories(
  ${CMAKE_CURRENT_BINARY_DIR}
)

# Builds
file(GLOB_RECURSE SRCS src/*.cpp)
add_executable(${VITA_APPNAME}
  ${SRCS}
)

target_link_libraries(${VITA_APPNAME}
  SDL_gfx
  SDL_mixer-modplug
  SDL_image
  SDL
  vorbisfile
  vorbis
  ogg
  mikmod
  modplug
  jpeg
  png
  imgui
  vitaGL
  vitashark
  mathneon
  z
  SceAppMgr_stub
  SceAppUtil_stub
  SceAudio_stub
  SceCommonDialog_stub
  SceCtrl_stub
  SceDisplay_stub
  SceGxm_stub
  SceHid_stub
  ScePower_stub
  SceShaccCg_stub
  SceSysmodule_stub
  SceTouch_stub
)

file(GLOB_RECURSE ASSETS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} data/images/* data/maps/* data/musics/* data/sounds/*)

set(DATA "")

FOREACH(ITEM ${ASSETS})
  list(APPEND DATA "FILE")
  list(APPEND DATA ${ITEM})
  list(APPEND DATA ${ITEM})
ENDFOREACH()

# Create Vita artifacts
vita_create_self(eboot.bin ${VITA_APPNAME} UNSAFE)
vita_create_vpk(${VITA_APPNAME}.vpk ${VITA_TITLEID} eboot.bin
  NAME ${VITA_APPNAME}
  FILE res/icon0.png sce_sys/icon0.png
  FILE res/bg.png sce_sys/livearea/contents/bg.png
  FILE res/startup.png sce_sys/livearea/contents/startup.png
  FILE res/template.xml sce_sys/livearea/contents/template.xml
  ${DATA}
  FILE data/texts/english_vita.txt data/texts/english.txt
  FILE data/texts/french_vita.txt data/texts/french.txt
)
