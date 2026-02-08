cmake_minimum_required(VERSION 3.12)

include(pico_sdk_import.cmake)

project(pico-rtc C CXX ASM)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

pico_sdk_init()

add_executable(pico-rtc 
            ds3231_example.c)

add_subdirectory(libraries)

target_link_libraries(pico-rtc pico_stdlib pico_ds3231)

pico_enable_stdio_uart(pico-rtc 1)
pico_enable_stdio_usb(pico-rtc 1)

pico_add_extra_outputs(pico-rtc)

file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/tmp/upload.sh
"#!/bin/bash
sudo openocd -f interface/cmsis-dap.cfg -f target/rp2040.cfg -c \"adapter speed 5000\" -c \"program ${CMAKE_PROJECT_NAME}.elf verify reset exit\"
")

file(
    COPY ${CMAKE_CURRENT_BINARY_DIR}/tmp/upload.sh
    DESTINATION ${CMAKE_CURRENT_BINARY_DIR}
    FILE_PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE
)