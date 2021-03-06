CMAKE_MINIMUM_REQUIRED(VERSION 2.8 FATAL_ERROR)
CMAKE_POLICY(VERSION 2.8)

SET(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake" "${CMAKE_MODULE_PATH}")

FIND_PACKAGE(Torch REQUIRED)
FIND_PACKAGE(CUDA 7.5 REQUIRED)

LIST(APPEND CUDA_NVCC_FLAGS "--gpu-architecture=compute_52 --gpu-code=compute_52")

INCLUDE_DIRECTORIES("${Torch_INSTALL_INCLUDE}/THC")

LINK_DIRECTORIES("${Torch_INSTALL_LIB}")

FILE(GLOB luasrc ${CMAKE_CURRENT_SOURCE_DIR}/*.lua)
FILE(GLOB cudasrc ${CMAKE_CURRENT_SOURCE_DIR}/*.cu ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cu)

CUDA_ADD_LIBRARY(nnex MODULE
	${cudasrc}
)

TARGET_LINK_LIBRARIES(nnex luaT TH THC)

SET_TARGET_PROPERTIES(nnex PROPERTIES
	PREFIX "lib"
	IMPORT_PREFIX "lib"
)

INSTALL(TARGETS nnex
	RUNTIME DESTINATION "${Torch_INSTALL_LUA_CPATH_SUBDIR}"
	LIBRARY DESTINATION "${Torch_INSTALL_LUA_CPATH_SUBDIR}"
)

INSTALL(FILES ${luasrc}
	DESTINATION "${Torch_INSTALL_LUA_PATH_SUBDIR}/nnex"
)