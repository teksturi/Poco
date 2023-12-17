# Sources
file(GLOB SRCS_G "src/*.cpp")
POCO_SOURCES_AUTO(SRCS ${SRCS_G})

# Headers
file(GLOB_RECURSE HDRS_G "include/*.h")
POCO_HEADERS_AUTO(SRCS ${HDRS_G})

# TODO: Currently only bundled is supported, in future this should also be possible
# with an unbundled version of 7z
POCO_SOURCES(SRCS 7z
	src/external/7z/7zAlloc.c
	src/external/7z//7zBuf2.c
	src/external/7z//7zBuf.c
	src/external/7z//7zCrc.c
	src/external/7z//7zCrcOpt.c
	src/external/7z//7zDec.c
	src/external/7z//7zFile.c
	src/external/7z//7zIn.c
	src/external/7z//7zStream.c
	src/external/7z//Alloc.c
	src/external/7z//Bcj2.c
	src/external/7z//Bra86.c
	src/external/7z//Bra.c
	# src/external/7z//BraIA64.c
	src/external/7z//CpuArch.c
	# src/external/7z//Delta.c
	# src/external/7z//LzFind.c
	# src/external/7z//LzFindMt.c
	src/external/7z//Lzma2Dec.c
	# src/external/7z/Lzma2Enc.c
	src/external/7z/Lzma86Dec.c
	# src/external/7z/Lzma86Enc.c
	src/external/7z/LzmaDec.c
	# src/external/7z/LzmaEnc.c
	# src/external/7z/LzmaLib.c
	# src/external/7z/MtCoder.c
	src/external/7z/Ppmd7.c
	src/external/7z/Ppmd7Dec.c
	# src/external/7z/Ppmd7Enc.c
	# src/external/7z/Sha256.c
	# src/external/7z/Threads.c
	# src/external/7z/Xz.c
	# src/external/7z/XzCrc64.c
	# src/external/7z/XzDec.c
	# src/external/7z/XzEnc.c
	# src/external/7z/XzIn.c
)

# Version Resource
if(MSVC AND BUILD_SHARED_LIBS)
	source_group("Resources" FILES ${PROJECT_SOURCE_DIR}/DLLVersion.rc)
	list(APPEND SRCS ${PROJECT_SOURCE_DIR}/DLLVersion.rc)
endif()

add_library(SevenZip ${SRCS})
add_library(Poco::SevenZip ALIAS SevenZip)
set_target_properties(SevenZip
	PROPERTIES
	VERSION ${SHARED_LIBRARY_VERSION} SOVERSION ${SHARED_LIBRARY_VERSION}
	OUTPUT_NAME PocoSevenZip
	DEFINE_SYMBOL SevenZip_EXPORTS
)

target_link_libraries(SevenZip PUBLIC Poco::Foundation)
target_include_directories(SevenZip
	PUBLIC
		$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
		$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
	PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src
)

POCO_INSTALL(SevenZip)
POCO_GENERATE_PACKAGE(SevenZip)

if(ENABLE_TESTS)
	add_subdirectory(samples)
endif()

