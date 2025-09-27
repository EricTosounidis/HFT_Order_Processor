cmake_minimum_required(VERSION 4.0)
project(HFT_Order_Processor)

set(CMAKE_CXX_STANDARD 20)

add_executable(HFT_Order_Processor
        hft_client.cpp)
