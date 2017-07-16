cmake_minimum_required(VERSION 3.8 FATAL_ERROR)
project(uWebSockets)

find_package(Threads REQUIRED)

add_library(uWS
  src/Extensions.cpp
  src/Group.cpp
  src/Networking.cpp
  src/Hub.cpp
  src/Node.cpp
  src/WebSocket.cpp
  src/HTTPSocket.cpp
  src/Socket.cpp
  src/Epoll.cpp
)

set(HEADERS
  src/Asio.h
  src/Backend.h
  src/Epoll.h
  src/Extensions.h
  src/Group.h
  src/HTTPSocket.h
  src/Hub.h
  src/Libuv.h
  src/Networking.h
  src/Node.h
  src/Socket.h
  src/uWS.h
  src/WebSocket.h
  src/WebSocketProtocol.h
)

target_include_directories(uWS
  PUBLIC
    "${CMAKE_CURRENT_SOURCE_DIR}"
)

target_link_libraries(uWS
  PUBLIC
    z
    ssl
    crypto
    Threads::Threads
)

set_target_properties(uWS
  PROPERTIES
    CXX_STANDARD 17
    POSITION_INDEPENDENT_CODE ON
    PUBLIC_HEADER "${HEADERS}"
)

install(
  TARGETS uWS
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  PUBLIC_HEADER DESTINATION include/uWS
)
