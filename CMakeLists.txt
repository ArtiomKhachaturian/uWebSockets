cmake_minimum_required(VERSION 3.8 FATAL_ERROR)
set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/misc/cmake;${CMAKE_MODULE_PATH}")
project(uWebSockets)

set(UWS_BACKEND "" CACHE STRING "Backend to use. Can be ASIO, EPOLL, LIBUV")

enable_testing()

# Dependencies
find_package(Threads REQUIRED)
find_package(ZLIB REQUIRED)
find_package(OpenSSL REQUIRED)

# Define the uWS target
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

if(${UWS_BACKEND} MATCHES "ASIO")
  find_package(Boost REQUIRED COMPONENTS system)
  target_compile_definitions(uWS PUBLIC USE_ASIO)
  target_link_libraries(uWS PUBLIC Boost::boost Boost::system)
elseif(${UWS_BACKEND} MATCHES "LIBUV")
  find_package(LibUV REQUIRED)
  target_compile_definitions(uWS PUBLIC USE_LIBUV)
  target_include_directories(uWS PUBLIC "${LIBUV_INCLUDE_DIRS}")
  target_link_libraries(uWS PUBLIC "${LIBUV_LIBRARIES}")
elseif(${UWS_BACKEND} MATCHES "EPOLL")
  target_compile_definitions(uWS PUBLIC USE_EPOLL)
endif()

target_link_libraries(uWS
  PUBLIC
    Threads::Threads
    OpenSSL::SSL
    OpenSSL::Crypto
    ZLIB::ZLIB
)

target_compile_features(uWS
  PUBLIC
    cxx_std_11
)

set_target_properties(uWS
  PROPERTIES
    POSITION_INDEPENDENT_CODE ON
    PUBLIC_HEADER "${HEADERS}"
)

install(
  TARGETS uWS
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  PUBLIC_HEADER DESTINATION include/uWS
)

# Tests
add_executable(uWS_test tests/main.cpp)
target_link_libraries(uWS_test PRIVATE uWS)
target_include_directories(uWS_test PRIVATE src)
