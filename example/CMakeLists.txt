cmake_minimum_required (VERSION 3.1.3)

project (ZXingExamples)

set (ENABLE_ENCODERS ON CACHE BOOL "Check to include encoders")
set (ENABLE_DECODERS ON CACHE BOOL "Check to include decoders")

add_definitions (-DUNICODE -D_UNICODE)

if (MSVC)
    add_definitions (-D_SCL_SECURE_NO_WARNINGS -D_CRT_SECURE_NO_WARNINGS -D_CRT_NONSTDC_NO_WARNINGS -DNOMINMAX)

    set (CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} /Oi /GS-")
    set (CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /Oi /GS-")
else()
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra")
    set (CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -D_DEBUG")
    set (CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -D_DEBUG")
endif()

add_subdirectory (${CMAKE_CURRENT_SOURCE_DIR}/../core ${CMAKE_BINARY_DIR}/ZXingCore)

if (ENABLE_DECODERS)
    add_executable (scan_image
        scan_image.cpp
    )
    
    target_include_directories (scan_image
        PRIVATE ../thirdparty/stb
    )

    target_compile_options (scan_image PRIVATE -DLODEPNG_NO_COMPILE_ENCODER)

    if ("${CMAKE_CXX_COMPILER_ID}" MATCHES "GNU")
        target_compile_options (scan_image PRIVATE -std=c++11)
    elseif (APPLE)
        target_compile_options (scan_image PRIVATE -std=c++11 -stdlib=libc++)
    endif()

    target_link_libraries (scan_image ZXingCore)

endif()

if (ENABLE_ENCODERS)
    add_executable (generate_image
        generate_image.cpp
    )
    
    target_include_directories (generate_image
        PRIVATE ../thirdparty/stb
    )

    target_compile_options (generate_image PRIVATE -DLODEPNG_NO_COMPILE_DECODER)
    
    if ("${CMAKE_CXX_COMPILER_ID}" MATCHES "GNU")
        target_compile_options (generate_image PRIVATE -std=c++11)
    elseif (APPLE)
        target_compile_options (generate_image PRIVATE -std=c++11 -stdlib=libc++)
    endif()

    target_link_libraries (generate_image ZXingCore)

endif()
