#
# Copyright (C) 2016 The Android Open Source Project
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

# Build one shared lib. When we build, we set the bin to $user/tmp, and
# remove it after build complete, so only the distribution place could be
# used for application linking.

cmake_minimum_required(VERSION 3.5.0)

set(CMAKE_VERBOSE_MAKEFILE on)
SET( CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -Wall -D ST_NO_EXCEPTION_HANDLING" )
message(${CMAKE_CXX_FLAGS})
add_library(soundtouch
            SHARED
            src/SoundTouch/AAFilter.cpp
            src/SoundTouch/FIFOSampleBuffer.cpp
            src/SoundTouch/FIRFilter.cpp
            src/SoundTouch/cpu_detect_x86.cpp
            src/SoundTouch/RateTransposer.cpp
            src/SoundTouch/SoundTouch.cpp
            src/SoundTouch/TDStretch.cpp
            src/SoundTouch/BPMDetect.cpp
            src/SoundTouch/PeakFinder.cpp
            src/SoundTouch/sse_optimized.cpp)

include_directories(src/include)
file (GLOB EXPORT_HEADERS "src/include/*.h")
message(${EXPORT_HEADERS})
# copy out the lib binary and remove generated files
set(distribution_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../../../../distribution)
set_target_properties(soundtouch
                      PROPERTIES
                      LIBRARY_OUTPUT_DIRECTORY
                      "${distribution_DIR}/soundtouch/lib/${ANDROID_ABI}")

add_custom_command(TARGET soundtouch POST_BUILD
                   COMMAND "${CMAKE_COMMAND}" -E make_directory "${distribution_DIR}/soundtouch/include"
                   DEPENDS "${distribution_DIR}/soundtouch/include"
                   COMMENT "Creating directory because of no one")

foreach( file_i ${EXPORT_HEADERS})
add_custom_command(TARGET soundtouch POST_BUILD
                   COMMAND "${CMAKE_COMMAND}" -E copy "${file_i}" "${distribution_DIR}/soundtouch/include/"
                   COMMENT "Copying soundtouch to output directory")
endforeach( file_i )