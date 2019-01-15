project(SecurityTokend)

set(MIG_USER_SOURCE_SUFFIX "Client.cpp")
set(MIG_SERVER_SOURCE_SUFFIX "Server.cpp")

include_directories(
	${CMAKE_CURRENT_SOURCE_DIR}/mig
	${CMAKE_CURRENT_SOURCE_DIR}/lib
)

add_compile_options(
	-iquote ${CMAKE_CURRENT_BINARY_DIR}/mig
	-Wno-gcc-compat
	-Wno-parenthesis
)

mig(mig/tokend.defs
	MIG_FLAGS "-cpp"
)

add_darling_static_library(security_tokend_client FAT
	SOURCES
		${CMAKE_CURRENT_BINARY_DIR}/mig/tokendClient.cpp
		lib/tdclient.cpp
		lib/tdtransit.cpp
)

set(DYLIB_COMPAT_VERSION "1.0.0")
set(DYLIB_CURRENT_VERSION "57740.51.3")

add_framework(SecurityTokend
        FAT
	PRIVATE
        CURRENT_VERSION
        VERSION "A"

        SOURCES
		lib/SecTokend.cpp
		lib/server.cpp
		${CMAKE_CURRENT_BINARY_DIR}/mig/tokendServer.cpp
		lib/transition.cpp
		lib/transit.cpp

        DEPENDENCIES
		Security
		security_tokend_client
		security_cdsa_utilities
		securityd_client
		security_utilities
		CoreFoundation
		system
		cxx
)

set_property(TARGET SecurityTokend APPEND_STRING PROPERTY LINK_FLAGS
	" -Wl,-dead_strip -exported_symbols_list ${CMAKE_CURRENT_SOURCE_DIR}/lib/SecurityTokend.exp")
