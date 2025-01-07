cmake_minimum_required(VERSION 3.30)
project(setup_avr_gcc C)

set(CMAKE_C_STANDARD 11)
# Set AVR-GCC as the compiler
set(CMAKE_C_COMPILER avr-gcc)
set(CMAKE_CXX_COMPILER avr-g++)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -mmcu=attiny412")

add_executable(setup_avr_gcc main.c)
