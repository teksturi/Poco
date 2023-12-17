# Sources
file(GLOB SRCS_G "src/*.cpp")
POCO_SOURCES_AUTO(SRCS ${SRCS_G})

# Headers
file(GLOB_RECURSE HDRS_G "include/*.h")
POCO_HEADERS_AUTO(SRCS ${HDRS_G})

# If POCO_UNBUNDLED is enabled we try to find the required packages
# The configuration will fail if the packages are not found
if(POCO_UNBUNDLED)
	find_package(ZLIB REQUIRED)
else()
	# zlib
	POCO_SOURCES(SRCS zlib
		src/external/zlib/adler32.c
		src/external/zlib/compress.c
		src/external/zlib/crc32.c
		src/external/zlib/deflate.c
		src/external/zlib/gzio.c
		src/external/zlib/infback.c
		src/external/zlib/inffast.c
		src/external/zlib/inflate.c
		src/external/zlib/inftrees.c
		src/external/zlib/trees.c
		src/external/zlib/zutil.c
	)
endif(POCO_UNBUNDLED)

# TODO: Currently only bundled is supported, in future this should also be possible
# with an unbundled version of hpdf
# hpdf
POCO_SOURCES(SRCS hpdf
	src/external/libharu/hpdf_3dmeasure.c
	src/external/libharu/hpdf_annotation.c
	src/external/libharu/hpdf_array.c
	src/external/libharu/hpdf_binary.c
	src/external/libharu/hpdf_boolean.c
	src/external/libharu/hpdf_catalog.c
	src/external/libharu/hpdf_destination.c
	src/external/libharu/hpdf_dict.c
	src/external/libharu/hpdf_doc.c
	src/external/libharu/hpdf_doc_png.c
	src/external/libharu/hpdf_encoder.c
	src/external/libharu/hpdf_encoder_cns.c
	src/external/libharu/hpdf_encoder_cnt.c
	src/external/libharu/hpdf_encoder_jp.c
	src/external/libharu/hpdf_encoder_kr.c
	src/external/libharu/hpdf_encoder_utf.c
	src/external/libharu/hpdf_encrypt.c
	src/external/libharu/hpdf_encryptdict.c
	src/external/libharu/hpdf_error.c
	src/external/libharu/hpdf_exdata.c
	src/external/libharu/hpdf_ext_gstate.c
	src/external/libharu/hpdf_font.c
	src/external/libharu/hpdf_font_cid.c
	src/external/libharu/hpdf_font_tt.c
	src/external/libharu/hpdf_font_type1.c
	src/external/libharu/hpdf_fontdef.c
	src/external/libharu/hpdf_fontdef_base14.c
	src/external/libharu/hpdf_fontdef_cid.c
	src/external/libharu/hpdf_fontdef_cns.c
	src/external/libharu/hpdf_fontdef_cnt.c
	src/external/libharu/hpdf_fontdef_jp.c
	src/external/libharu/hpdf_fontdef_kr.c
	src/external/libharu/hpdf_fontdef_tt.c
	src/external/libharu/hpdf_fontdef_type1.c
	src/external/libharu/hpdf_gstate.c
	src/external/libharu/hpdf_image.c
	src/external/libharu/hpdf_image_ccitt.c
	src/external/libharu/hpdf_image_png.c
	src/external/libharu/hpdf_info.c
	src/external/libharu/hpdf_list.c
	src/external/libharu/hpdf_mmgr.c
	src/external/libharu/hpdf_name.c
	src/external/libharu/hpdf_namedict.c
	src/external/libharu/hpdf_null.c
	src/external/libharu/hpdf_number.c
	src/external/libharu/hpdf_objects.c
	src/external/libharu/hpdf_outline.c
	src/external/libharu/hpdf_page_label.c
	src/external/libharu/hpdf_page_operator.c
	src/external/libharu/hpdf_pages.c
	src/external/libharu/hpdf_pdfa.c
	src/external/libharu/hpdf_real.c
	src/external/libharu/hpdf_streams.c
	src/external/libharu/hpdf_string.c
	src/external/libharu/hpdf_u3d.c
	src/external/libharu/hpdf_utils.c
	src/external/libharu/hpdf_xref.c
)

# TODO: Currently only bundled is supported, in future this should also be possible
# with an unbundled version of libpng
POCO_SOURCES(SRCS libpng
	src/external/libpng/png.c
	src/external/libpng/pngerror.c
	src/external/libpng/pnggccrd.c
	src/external/libpng/pngget.c
	src/external/libpng/pngmem.c
	src/external/libpng/pngpread.c
	src/external/libpng/pngread.c
	src/external/libpng/pngrio.c
	src/external/libpng/pngrtran.c
	src/external/libpng/pngrutil.c
	src/external/libpng/pngset.c
	src/external/libpng/pngtest.c
	src/external/libpng/pngtrans.c
	src/external/libpng/pngvcrd.c
	src/external/libpng/pngwio.c
	src/external/libpng/pngwrite.c
	src/external/libpng/pngwtran.c
	src/external/libpng/pngwutil.c
)

# Version Resource
if(MSVC AND BUILD_SHARED_LIBS)
	source_group("Resources" FILES ${PROJECT_SOURCE_DIR}/DLLVersion.rc)
	list(APPEND SRCS ${PROJECT_SOURCE_DIR}/DLLVersion.rc)
endif()

#TODO: Can we put this with the below includes? PRIVAT eg.
include_directories("include/Poco/PDF") # zip src
include_directories("include/Poco/PDF/External/libharu")
include_directories("include/Poco/PDF/External/libpng")
include_directories("include/Poco/PDF/External/zlib")

add_library(PDF ${SRCS})
add_library(Poco::PDF ALIAS PDF)
set_target_properties(PDF
	PROPERTIES
	VERSION ${SHARED_LIBRARY_VERSION} SOVERSION ${SHARED_LIBRARY_VERSION}
	OUTPUT_NAME PocoPDF
	DEFINE_SYMBOL PDF_EXPORTS
)

if(POCO_UNBUNDLED)
	target_link_libraries(PDF PUBLIC ZLIB::ZLIB)
	target_compile_definitions(PDF PUBLIC POCO_UNBUNDLED)
endif(POCO_UNBUNDLED)
target_link_libraries(PDF PUBLIC Poco::XML Poco::Util)
target_include_directories(PDF
	PUBLIC
		$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
		$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
	PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src
)

POCO_INSTALL(PDF)
POCO_GENERATE_PACKAGE(PDF)

if(ENABLE_SAMPLES)
	add_subdirectory(samples)
endif()

if(ENABLE_TESTS)
	add_subdirectory(testsuite)
endif()

