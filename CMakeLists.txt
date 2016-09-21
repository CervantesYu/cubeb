# https://docs.google.com/presentation/d/18fY0zDtJCMUW5WdY2ZOfKtvb7lXEbBPFe_I6MNJC0Qw/edit#slide=id.g15c79ae86d_0_55
# https://github.com/ttroy50/cmake-examples
# https://www.jetbrains.com/help/clion/2016.1/quick-cmake-tutorial.html?origin=old_help
# https://cmake.org/cmake/help/v3.4/manual/cmake-buildsystem.7.html
# http://www.slideshare.net/DanielPfeifer1/cmake-48475415

# TODO
# - backend selection by OS/cmdline
# - split out speex build
#   CMakeLists.txt in speex subdir, link dep in parent

cmake_minimum_required(VERSION 3.5 FATAL_ERROR)
project(cubeb
        VERSION 0.0.0)
#        LANGUAGES CXX) # causes .c to be built as c++, breaking externs

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(cubeb src/cubeb.c
                  src/cubeb_audiounit.cpp
                  src/cubeb_resampler.cpp
                  src/cubeb_panner.cpp
                  src/cubeb_osx_run_loop.c
                  src/speex/resample.c)
target_include_directories(cubeb PUBLIC include)
target_include_directories(cubeb PRIVATE src)

target_compile_definitions(cubeb PRIVATE USE_AUDIOUNIT)
target_link_libraries(cubeb INTERFACE "-framework AudioUnit" "-framework CoreAudio" "-framework CoreServices")

#add_subdirectory(src/speex)

# speex stuff
target_compile_definitions(cubeb PRIVATE OUTSIDE_SPEEX)
target_compile_definitions(cubeb PRIVATE FLOATING_POINT)
target_compile_definitions(cubeb PRIVATE EXPORT=)
target_compile_definitions(cubeb PRIVATE RANDOM_PREFIX=speex)

enable_testing()

add_executable(test_sanity test/test_sanity.cpp)
target_link_libraries(test_sanity PRIVATE cubeb)
add_test(sanity test_sanity)

add_executable(test_tone test/test_tone.cpp)
target_link_libraries(test_tone PRIVATE cubeb)
add_test(tone test_tone)

add_executable(test_audio test/test_audio.cpp)
target_link_libraries(test_audio PRIVATE cubeb)
add_test(audio test_audio)

add_executable(test_record test/test_record.cpp)
target_link_libraries(test_record PRIVATE cubeb)
add_test(record test_record)

add_executable(test_devices test/test_devices.c)
target_link_libraries(test_devices PRIVATE cubeb)
add_test(devices test_devices)

add_executable(test_resampler test/test_resampler.cpp)
target_compile_definitions(test_resampler PRIVATE OUTSIDE_SPEEX)
target_compile_definitions(test_resampler PRIVATE FLOATING_POINT)
target_compile_definitions(test_resampler PRIVATE EXPORT=)
target_compile_definitions(test_resampler PRIVATE RANDOM_PREFIX=speex)
target_include_directories(test_resampler PRIVATE src)
target_link_libraries(test_resampler PRIVATE cubeb)
add_test(resampler test_resampler)

add_executable(test_duplex test/test_duplex.cpp)
target_link_libraries(test_duplex PRIVATE cubeb)
add_test(duplex test_duplex)

add_executable(test_latency test/test_latency.cpp)
target_link_libraries(test_latency PRIVATE cubeb)
add_test(latency test_latency)

add_executable(test_ring_array test/test_ring_array.cpp)
target_include_directories(test_ring_array PRIVATE src)
target_link_libraries(test_ring_array PRIVATE cubeb)
add_test(ring_array test_ring_array)

add_executable(test_utils test/test_utils.cpp)
target_include_directories(test_utils PRIVATE src)
target_link_libraries(test_utils PRIVATE cubeb)
add_test(utils test_utils)

