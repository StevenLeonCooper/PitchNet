# Unit tests.
#
# Each test is a standalone executable with its own main(), matching the style
# these files were already written in. They link pitchnet_core, which exposes
# the JUCE modules PUBLIC, so a test gets every include path it needs from that
# one target.
#
# Built only when PITCHNET_BUILD_TESTS=ON, so a normal build and CI are
# unaffected:
#
#   cmake -B build -DPITCHNET_BUILD_TESTS=ON
#   cmake --build build
#   ctest --test-dir build --output-on-failure
#
# Note that each target compiles the JUCE module sources it links, so enabling
# this costs a JUCE build per test the first time.

set(PITCHNET_TEST_SOURCES
    AraRegionMergeTests.cpp
    AraRegionSliceTests.cpp
    FormantShiftTests.cpp
    NoteGainCurveTests.cpp
    PitchDriftTests.cpp
    VisualWaveformEnvelopeTests.cpp)

foreach(test_source IN LISTS PITCHNET_TEST_SOURCES)
    get_filename_component(test_name ${test_source} NAME_WE)

    add_executable(${test_name} ${test_source})
    target_link_libraries(${test_name} PRIVATE pitchnet_core)
    target_compile_features(${test_name} PRIVATE cxx_std_17)

    # CHECK() exits non-zero on failure, which CTest reports as a failed test.
    add_test(NAME ${test_name} COMMAND ${test_name})
endforeach()
