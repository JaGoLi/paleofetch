target_sources(${PROJECT_NAME}
	PRIVATE
		paleofetch.c
)

# Fill @DISTRO_LOGO@
configure_file(config.h.in config.h @ONLY)
target_include_directories(${PROJECT_NAME}
	PRIVATE
		"${CMAKE_CURRENT_BINARY_DIR}"
		"${CMAKE_CURRENT_SOURCE_DIR}"
)
