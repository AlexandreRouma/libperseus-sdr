cmake_minimum_required(VERSION 3.13)
project(perseus-sdr)

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

file(GLOB_RECURSE SRC "src/*.c")

add_library(perseus-sdr SHARED ${SRC})

if (MSVC)
    find_package(libusb CONFIG REQUIRED)
    target_include_directories(perseus-sdr PRIVATE ${LIBUSB_INCLUDE_DIRS})
    target_link_libraries(perseus-sdr PRIVATE ${LIBUSB_LIBRARIES})
elseif (ANDROID)
    target_link_libraries(perseus-sdr PUBLIC
        /sdr-kit/${ANDROID_ABI}/lib/libusb1.0.so
    )
else (MSVC)
    find_package(PkgConfig)

    pkg_check_modules(LIBUSB REQUIRED libusb-1.0)

    target_include_directories(perseus-sdr PRIVATE ${LIBUSB_INCLUDE_DIRS})
    target_link_directories(perseus-sdr PRIVATE ${LIBUSB_LIBRARY_DIRS})
    target_link_libraries(perseus-sdr PRIVATE ${LIBUSB_LIBRARIES})
endif ()

# cmake .. "-DCMAKE_TOOLCHAIN_FILE=C:/dev/vcpkg/scripts/buildsystems/vcpkg.cmake"

