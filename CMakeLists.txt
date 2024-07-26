cmake_minimum_required(VERSION 3.5)

project(grpc_example C CXX)

# apt install libprotobuf-dev libgrpc++-dev protobuf-compiler-grpc
find_package(Protobuf REQUIRED)
find_package(gRPC REQUIRED)

add_library(example
    common.proto
    ingestion.proto
)

target_link_libraries(example
    PUBLIC
        protobuf::libprotobuf
        gRPC::grpc
        gRPC::grpc++
)
target_include_directories(example
    PUBLIC ${CMAKE_CURRENT_BINARY_DIR}
)

get_target_property(grpc_cpp_plugin_location gRPC::grpc_cpp_plugin LOCATION)
protobuf_generate(TARGET example LANGUAGE cpp)
protobuf_generate(TARGET example LANGUAGE grpc
    GENERATE_EXTENSIONS .grpc.pb.h .grpc.pb.cc
    PLUGIN "protoc-gen-grpc=${grpc_cpp_plugin_location}"
)
