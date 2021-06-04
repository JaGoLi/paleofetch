cmake_minimum_required(VERSION 3.16.0)

project(paleofetch
	VERSION 0.0.0
	DESCRIPTION "Neofetch, but written in C"
	HOMEPAGE_URL "https://github.com/JaGoLi/paleofetch"
)

# Without this, paths are not relative in the sources list
cmake_policy(SET CMP0076 NEW)

include(GNUInstallDirs)
include(FindPkgConfig)

# The program itself
add_executable(${PROJECT_NAME})

# Distro logo
set(DISTRO_LOGO "arch" CACHE STRING "The logo that will be displayed.")

add_subdirectory(src) # Sources list

# Shared libraries
pkg_check_modules(libs
	REQUIRED IMPORTED_TARGET
		libpci
		x11
)

# Linking
target_link_libraries(${PROJECT_NAME}
	PRIVATE
		PkgConfig::libs
)

# Battery directory
execute_process(COMMAND
	find
		-L
		/sys/class/power_supply/
		-maxdepth 1
		-iname "bat*"
		-print0

	OUTPUT_VARIABLE BATTERY_DIRECTORY
)

target_compile_definitions(${PROJECT_NAME}
	PRIVATE
		"BATTERY_DIRECTORY=\"${BATTERY_DIRECTORY}\""
)

# Default flags
if(UNIX)
	if(NOT (DEFINED ENV{CFLAGS} OR CMAKE_C_FLAGS))
		set(CMAKE_C_FLAGS "-Wall -Wextra -g")
	endif()
	if(NOT (DEFINED ENV{CXXFLAGS} OR CMAKE_CXX_FLAGS))
		set(CMAKE_CXX_FLAGS "-Wall -Wextra -g")
	endif()
endif()

# Install target
install(TARGETS ${PROJECT_NAME}
	DESTINATION ${CMAKE_INSTALL_BINDIR}
)
