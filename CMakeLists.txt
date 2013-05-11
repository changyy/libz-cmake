cmake_minimum_required(VERSION 2.8)
project (libz)

if (DEFINED CMAKE_TOOLCHAIN_FILE)
	message("Include Cross Platform cmake: " ${CROSS_PLATFORM_CMAKE})
	get_filename_component(COMPILER_NAME ${CMAKE_C_COMPILER} NAME)
	message( "CMAKE_C_COMPILER: ${COMPILER_NAME}")
	string(REGEX REPLACE "(.*)-gcc" "\\1" target_platform "${COMPILER_NAME}")
	message( "target_platform: ${target_platform}")
	set(target_platform_preconfigure_options "CC=${target_platform}-gcc;AR=${target_platform}-ar;LD=${target_platform}-ld;RANLIB=${target_platform}-ranlib;NM=${target_platform}-nm;AS=${target_platform}-as;OBJDUMP=${target_platform}-objdump;OBJCOPY=${target_platform}-objcopy;STRIP=${target_platform}-strip;STRINGS=${target_platform}-strings;SIZE=${target_platform}-size")
endif(DEFINED CMAKE_TOOLCHAIN_FILE)

set(TOOL_LIBZ_SOURCE zlib-1.2.8.tar.gz)
set(TOOL_LIBZ_DIR zlib-1.2.8)
set(CONFIG_ADD_OPTIONS "--prefix;${CMAKE_BINARY_DIR}")
	if(DEFINED CMAKE_TOOLCHAIN_FILE)
		add_custom_command(
			OUTPUT BUILD_LIBZ
			#COMMAND wget -qO- http://zlib.net/${TOOL_LIBZ_SOURCE} | tar -zxvf - 
			COMMAND test -e ${CMAKE_CURRENT_SOURCE_DIR}/${TOOL_LIBZ_SOURCE} || wget http://zlib.net/${TOOL_LIBZ_SOURCE} -O ${CMAKE_CURRENT_SOURCE_DIR}/${TOOL_LIBZ_SOURCE} 
			COMMAND tar -zxvf ${CMAKE_CURRENT_SOURCE_DIR}/${TOOL_LIBZ_SOURCE} 
			COMMAND cd ${TOOL_LIBZ_DIR} && ${target_platform_preconfigure_options} ./configure ${CONFIG_ADD_OPTIONS}
			COMMAND cd ${TOOL_LIBZ_DIR} && make -j4 && make install
			WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
			COMMENT "Build libz"
		)
	else(DEFINED CMAKE_TOOLCHAIN_FILE)
		add_custom_command(
			OUTPUT BUILD_LIBZ
			#COMMAND wget -qO- http://zlib.net/${TOOL_LIBZ_SOURCE} | tar -zxvf -
			COMMAND test -e ${CMAKE_CURRENT_SOURCE_DIR}/${TOOL_LIBZ_SOURCE} || wget http://zlib.net/${TOOL_LIBZ_SOURCE} -O ${CMAKE_CURRENT_SOURCE_DIR}/${TOOL_LIBZ_SOURCE} 
			COMMAND tar -zxvf ${CMAKE_CURRENT_SOURCE_DIR}/${TOOL_LIBZ_SOURCE} 
			COMMAND cd ${TOOL_LIBZ_DIR} && ./configure ${CONFIG_ADD_OPTIONS} 
			COMMAND cd ${TOOL_LIBZ_DIR} && make -j4 && make install
			WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
			COMMENT "Build libz" 
		)
	endif(DEFINED CMAKE_TOOLCHAIN_FILE)
	add_custom_target(build_libz ALL DEPENDS BUILD_LIBZ )
