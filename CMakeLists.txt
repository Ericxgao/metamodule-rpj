cmake_minimum_required(VERSION 3.24)

if(NOT "${METAMODULE_SDK_DIR}" STREQUAL "")
	message("METAMODULE_SDK_DIR set by CMake variable ${METAMODULE_SDK_DIR}")
elseif (DEFINED ENV{METAMODULE_SDK_DIR})
    set(METAMODULE_SDK_DIR "$ENV{METAMODULE_SDK_DIR}")
	message("METAMODULE_SDK_DIR set by environment variable ${METAMODULE_SDK_DIR}")
else()
    set(METAMODULE_SDK_DIR "${CMAKE_CURRENT_LIST_DIR}/../metamodule-plugin-sdk")
	message("METAMODULE_SDK_DIR set to default: ${METAMODULE_SDK_DIR}")
endif()
include(${METAMODULE_SDK_DIR}/plugin.cmake)

project(RPJ VERSION 1.0.0 DESCRIPTION "RPJ for Rack" LANGUAGES C CXX)

# Set C++17 standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Add option for virtual build
option(BUILD_VIRTUAL "Build for virtual environment" ON)

# Define source directories
set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/RPJ)

set(SIMDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/extern/simde")

# Main library target
add_library(RPJ STATIC)

# Compiler definitions
target_compile_definitions(RPJ PRIVATE 
    __V1x 
    __PLUGIN
    $<$<BOOL:${BUILD_VIRTUAL}>:BUILD_VIRTUAL>
    _MB_CAPABLE=1
    SIMDE_ENABLE_NATIVE_ALIASES
)

# Compiler options
target_compile_options(RPJ PRIVATE
    -fpermissive
    -fdata-sections
    -ffunction-sections
    -fno-exceptions
    -fno-threadsafe-statics
    -fno-use-cxa-atexit
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility=hidden>
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility-inlines-hidden>
)

# Linker options
target_link_options(RPJ PRIVATE
    -Wl,--gc-sections
)

# Source files
set(RPJ_SOURCES
    # "${SOURCE_DIR}/src/Audio.cpp"
    "${SOURCE_DIR}/src/AudioDelay.cpp"
    "${SOURCE_DIR}/src/AudioDetector.cpp"
    "${SOURCE_DIR}/src/AudioFilter.cpp"
    "${SOURCE_DIR}/src/BiQuad.cpp"
    "${SOURCE_DIR}/src/BlindCurve.cpp"
    "${SOURCE_DIR}/src/Brave.cpp"
    "${SOURCE_DIR}/src/CircularRide.cpp"
    # "${SOURCE_DIR}/src/Display.cpp"
    "${SOURCE_DIR}/src/DrillingHoles.cpp"
    "${SOURCE_DIR}/src/DryLand.cpp"
    "${SOURCE_DIR}/src/Easter.cpp"
    "${SOURCE_DIR}/src/EnvelopeFollower.cpp"
    "${SOURCE_DIR}/src/Essence.cpp"
    "${SOURCE_DIR}/src/Estonia.cpp"
    "${SOURCE_DIR}/src/Gaza.cpp"
    "${SOURCE_DIR}/src/Gazpacho.cpp"
    "${SOURCE_DIR}/src/Genie.cpp"
    # "${SOURCE_DIR}/src/GenieExpander.cpp"
    "${SOURCE_DIR}/src/Grendel.cpp"
    "${SOURCE_DIR}/src/LadyNina.cpp"
    "${SOURCE_DIR}/src/Lavender.cpp"
    "${SOURCE_DIR}/src/Montreal.cpp"
    "${SOURCE_DIR}/src/PigeonPlink.cpp"
    "${SOURCE_DIR}/src/RPJ.cpp"
    "${SOURCE_DIR}/src/SugarMice.cpp"
    "${SOURCE_DIR}/src/TheWeb.cpp"
    # "${SOURCE_DIR}/src/TuxOn.cpp"
    "${SOURCE_DIR}/src/WDF.cpp"
    "${SOURCE_DIR}/src/ZVA.cpp"
    "${SOURCE_DIR}/dsp/utils/ObjectCache.cpp"
    "${SOURCE_DIR}/dsp/filters/ButterworthFilterDesigner.cpp"
    "${SOURCE_DIR}/dsp/third-party/falco/DspFilter.cpp"
    "${SOURCE_DIR}/dsp/utils/AudioMath.cpp"
)

target_sources(RPJ PRIVATE
    ${RPJ_SOURCES}
)

# Include directories
target_include_directories(RPJ PRIVATE 
    ${SOURCE_DIR}/src
    ${SOURCE_DIR}/dsp/utils
    ${SOURCE_DIR}/dsp/filters
    ${SOURCE_DIR}/dsp/third-party/falco
    ${SOURCE_DIR}/dsp/third-party/src
    ${SOURCE_DIR}/dsp/
    ${SIMDE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/../metamodule-plugin-sdk/metamodule-plugin-libc/libstdc++-v3/include/experimental/bits
)

# Create the plugin
create_plugin(
    SOURCE_LIB      RPJ
    PLUGIN_NAME     RPJ
    PLUGIN_JSON     ${SOURCE_DIR}/plugin.json
    SOURCE_ASSETS   ${CMAKE_CURRENT_LIST_DIR}/assets
    DESTINATION     ${CMAKE_CURRENT_LIST_DIR}/metamodule-plugins
) 