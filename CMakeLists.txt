CMAKE_MINIMUM_REQUIRED(VERSION 2.8)

PROJECT(uv)

SET(LIBUVDIR .)

INCLUDE_DIRECTORIES(
    ${LIBUVDIR}/include
    ${LIBUVDIR}/src
)

IF("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
    SET(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} /MTd")
    SET(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MTd")

    SET(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} /MT")
    SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MT")
ENDIF("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")

SET(SOURCES
    ${LIBUVDIR}/include/uv.h
    ${LIBUVDIR}/include/tree.h
    ${LIBUVDIR}/include/uv-errno.h
    ${LIBUVDIR}/include/uv-threadpool.h
    ${LIBUVDIR}/include/uv-version.h
    ${LIBUVDIR}/src/fs-poll.c
    ${LIBUVDIR}/src/heap-inl.h
    ${LIBUVDIR}/src/inet.c
    ${LIBUVDIR}/src/queue.h
    ${LIBUVDIR}/src/threadpool.c
    ${LIBUVDIR}/src/uv-common.c
    ${LIBUVDIR}/src/uv-common.h
    ${LIBUVDIR}/src/version.c
)

IF(WIN32)
    ADD_DEFINITIONS(
        -DWIN32_LEAN_AND_MEAN
        -D_WIN32_WINNT=0x0600
        -D_CRT_SECURE_NO_WARNINGS
        -D_GNU_SOURCE
    )

    INCLUDE_DIRECTORIES(${LIBUVDIR}/src/win)

    SET(SOURCES ${SOURCES}
        ${UVLIBDIR}/include/uv-win.h
        ${UVLIBDIR}/src/win/async.c
        ${UVLIBDIR}/src/win/atomicops-inl.h
        ${UVLIBDIR}/src/win/core.c
        ${UVLIBDIR}/src/win/dl.c
        ${UVLIBDIR}/src/win/error.c
        ${UVLIBDIR}/src/win/fs.c
        ${UVLIBDIR}/src/win/fs-event.c
        ${UVLIBDIR}/src/win/getaddrinfo.c
        ${UVLIBDIR}/src/win/getnameinfo.c
        ${UVLIBDIR}/src/win/handle.c
        ${UVLIBDIR}/src/win/handle-inl.h
        ${UVLIBDIR}/src/win/internal.h
        ${UVLIBDIR}/src/win/loop-watcher.c
        ${UVLIBDIR}/src/win/pipe.c
        ${UVLIBDIR}/src/win/thread.c
        ${UVLIBDIR}/src/win/poll.c
        ${UVLIBDIR}/src/win/process.c
        ${UVLIBDIR}/src/win/process-stdio.c
        ${UVLIBDIR}/src/win/req.c
        ${UVLIBDIR}/src/win/req-inl.h
        ${UVLIBDIR}/src/win/signal.c
        ${UVLIBDIR}/src/win/stream.c
        ${UVLIBDIR}/src/win/stream-inl.h
        ${UVLIBDIR}/src/win/tcp.c
        ${UVLIBDIR}/src/win/tty.c
        ${UVLIBDIR}/src/win/timer.c
        ${UVLIBDIR}/src/win/udp.c
        ${UVLIBDIR}/src/win/util.c
        ${UVLIBDIR}/src/win/winapi.c
        ${UVLIBDIR}/src/win/winapi.h
        ${UVLIBDIR}/src/win/winsock.c
        ${UVLIBDIR}/src/win/winsock.h
    )
ELSE()
    SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} --std=gnu89 -pedantic -Wall -Wextra -Wno-unused-parameter")

    # -Wstrict-aliasing on all but Xcode

    IF(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
        ADD_DEFINITIONS(-D_GNU_SOURCE)

        SET(SOURCES ${SOURCES}
            ${LIBUVDIR}/src/unix/linux-core.c
            ${LIBUVDIR}/src/unix/linux-inotify.c
            ${LIBUVDIR}/src/unix/linux-syscalls.c
            ${LIBUVDIR}/src/unix/linux-syscalls.h
            ${LIBUVDIR}/src/unix/proctitle.c
        )
    ELSEIF(${CMAKE_SYSTEM_NAME} STREQUAL "Darwin")
        ADD_DEFINITIONS(
            -D_DARWIN_USE_64_BIT_INODE=1
            -D_DARWIN_UNLIMITED_SELECT=1
        )

        SET(SOURCES ${SOURCES}
            ${LIBUVDIR}/src/unix/darwin.c
            ${LIBUVDIR}/src/unix/fsevents.c
            ${LIBUVDIR}/src/unix/darwin-proctitle.c
            ${LIBUVDIR}/src/unix/proctitle.c
            ${LIBUVDIR}/src/unix/kqueue.c
        )
    ELSEIF(${CMAKE_SYSTEM_NAME} STREQUAL "FreeBSD")
        SET(SOURCES ${SOURCES}
            ${LIBUVDIR}/src/unix/freebsd.c
            ${LIBUVDIR}/src/unix/kqueue.c
        )
    ELSEIF(${CMAKE_SYSTEM_NAME} STREQUAL "OpenBSD")
        SET(SOURCES ${SOURCES}
            ${LIBUVDIR}/src/unix/openbsd.c
            ${LIBUVDIR}/src/unix/kqueue.c
        )
    ELSEIF(${CMAKE_SYSTEM_NAME} STREQUAL "NetBSD")
        SET(SOURCES ${SOURCES}
            ${LIBUVDIR}/src/unix/netbsd.c
            ${LIBUVDIR}/src/unix/kqueue.c
        )
    ELSEIF(${CMAKE_SYSTEM_NAME} STREQUAL "SunOS")
        ADD_DEFINITIONS(
            -D__EXTENSIONS__
            -D_XOPEN_SOURCE=500
        )

        SET(SOURCES ${SOURCES}
            ${LIBUVDIR}/src/unix/sunos.c
        )
    ENDIF()

    INCLUDE_DIRECTORIES(${LIBUVDIR}/src/unix)

    SET(SOURCES ${SOURCES}
        ${LIBUVDIR}/include/uv-unix.h
        ${LIBUVDIR}/include/uv-linux.h
        ${LIBUVDIR}/include/uv-sunos.h
        ${LIBUVDIR}/include/uv-darwin.h
        ${LIBUVDIR}/include/uv-bsd.h
        ${LIBUVDIR}/include/uv-aix.h
        ${LIBUVDIR}/src/unix/async.c
        ${LIBUVDIR}/src/unix/atomic-ops.h
        ${LIBUVDIR}/src/unix/core.c
        ${LIBUVDIR}/src/unix/dl.c
        ${LIBUVDIR}/src/unix/fs.c
        ${LIBUVDIR}/src/unix/getaddrinfo.c
        ${LIBUVDIR}/src/unix/getnameinfo.c
        ${LIBUVDIR}/src/unix/internal.h
        ${LIBUVDIR}/src/unix/loop.c
        ${LIBUVDIR}/src/unix/loop-watcher.c
        ${LIBUVDIR}/src/unix/pipe.c
        ${LIBUVDIR}/src/unix/poll.c
        ${LIBUVDIR}/src/unix/process.c
        ${LIBUVDIR}/src/unix/signal.c
        ${LIBUVDIR}/src/unix/spinlock.h
        ${LIBUVDIR}/src/unix/stream.c
        ${LIBUVDIR}/src/unix/tcp.c
        ${LIBUVDIR}/src/unix/thread.c
        ${LIBUVDIR}/src/unix/timer.c
        ${LIBUVDIR}/src/unix/tty.c
        ${LIBUVDIR}/src/unix/udp.c
    )
ENDIF()

ADD_LIBRARY(uv STATIC ${SOURCES})

IF(WIN32)
    TARGET_LINK_LIBRARIES(uv advapi32 iphlpapi psapi shell32 userenv ws2_32)
ELSE()
    TARGET_LINK_LIBRARIES(uv m pthreads)

    IF(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
        TARGET_LINK_LIBRARIES(uv dl rt)
    ELSEIF(${CMAKE_SYSTEM_NAME} STREQUAL "SunOS")
        TARGET_LINK_LIBRARIES(uv kstat nsl sendfile socket)
    ELSEIF((${CMAKE_SYSTEM_NAME} STREQUAL "FreeBSD") OR (${CMAKE_SYSTEM_NAME} STREQUAL "OpenBSD") OR (${CMAKE_SYSTEM_NAME} STREQUAL "NetBSD"))
        TARGET_LINK_LIBRARIES(uv kvm)
    ENDIF()
ENDIF()

INSTALL(TARGETS uv DESTINATION lib)

FILE(GLOB HEADERS ${LIBUVDIR}/include/*.h)

INSTALL(FILES ${HEADERS} DESTINATION include)

