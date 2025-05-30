find_package(Qt6 REQUIRED COMPONENTS Core Quick)

qt_add_library(GStreamerReceiver STATIC GLVideoItemStub.h)

target_link_libraries(GStreamerReceiver PUBLIC Qt6::Quick)

target_include_directories(GStreamerReceiver PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

cmake_print_variables(QGC_ENABLE_GST_VIDEOSTREAMING)
if(NOT QGC_ENABLE_GST_VIDEOSTREAMING)
    return()
endif()

add_subdirectory(gstqml6gl)

if(NOT TARGET gstqml6gl)
    return()
endif()

target_sources(GStreamerReceiver
    PRIVATE
        gstqgc.cc
        gstqgcelement.cc
        gstqgcelements.h
        gstqgcvideosinkbin.cc
        gstqgcvideosinkbin.h
        GStreamer.cc
        GStreamer.h
        GstVideoReceiver.cc
        GstVideoReceiver.h
)

if(IOS)
    target_sources(GStreamerReceiver
        PRIVATE
            gst_ios_init.m
            gst_ios_init.h
    )
endif()

target_link_libraries(GStreamerReceiver
    PRIVATE
        Utilities
    PUBLIC
        Qt6::Core
        gstqml6gl
        Settings
        VideoReceiver
)

target_compile_definitions(GStreamerReceiver PUBLIC QGC_GST_STREAMING)
