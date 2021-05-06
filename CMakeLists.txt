# Build for LuaBind
# Ryan Pavlik <rpavlik@iastate.edu>
# http://academic.cleardefinition.com/
# Iowa State University HCI Graduate Program/VRAC

cmake_minimum_required(VERSION 3.11)
project(LuaBind)

set(CPACK_PACKAGE_VERSION_MAJOR "0")
set(CPACK_PACKAGE_VERSION_MINOR "9")
set(CPACK_PACKAGE_VERSION_PATCH "1")
set(CPACK_PACKAGE_VERSION
	"${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR}.${CPACK_PACKAGE_VERSION_PATCH}")

set(CMAKE_DEBUG_POSTFIX "d")

# =============================================================================
SET(CMAKE_CXX_FLAGS " /D_WINDOWS /W3 /GR /EHsc /arch:SSE2" CACHE STRING "" FORCE)
SET(CMAKE_CXX_FLAGS_DEBUG "/D_DEBUG /MDd /Zi /Ob0 /Od /RTC1" CACHE STRING "" FORCE)
SET(CMAKE_CXX_FLAGS_MINSIZEREL "/MD /O1 /Ob1 /D NDEBUG /GS-" CACHE STRING "" FORCE)
SET(CMAKE_CXX_FLAGS_RELEASE "/MD /O2 /Ob2 /D NDEBUG /GS-" CACHE STRING "" FORCE)
SET(CMAKE_CXX_FLAGS_RELWITHDEBINFO "/MD /Zi /O2 /Ob2 /D NDEBUG /Oi /Ot /GS- /GL" CACHE STRING "" FORCE)

IF(CMAKE_CL_64)
  SET(CMAKE_EXE_LINKER_FLAGS "/machine:X64" CACHE STRING "" FORCE)
ELSE(CMAKE_CL_64)
  SET(CMAKE_EXE_LINKER_FLAGS "/machine:X86" CACHE STRING "" FORCE)
ENDIF()
SET(CMAKE_EXE_LINKER_FLAGS_DEBUG "/debug /INCREMENTAL" CACHE STRING "" FORCE)
SET(CMAKE_EXE_LINKER_FLAGS_MINSIZEREL "/INCREMENTAL:NO" CACHE STRING "" FORCE)
SET(CMAKE_EXE_LINKER_FLAGS_RELEASE "/INCREMENTAL:NO" CACHE STRING "" FORCE)
SET(CMAKE_EXE_LINKER_FLAGS_RELWITHDEBINFO "/debug /INCREMENTAL:NO /LTCG" CACHE STRING "" FORCE)

IF(CMAKE_CL_64)
SET(CMAKE_SHARED_LINKER_FLAGS "/machine:X64" CACHE STRING "" FORCE)
ELSE(CMAKE_CL_64)
SET(CMAKE_SHARED_LINKER_FLAGS "/machine:X86" CACHE STRING "" FORCE)
ENDIF()
SET(CMAKE_SHARED_LINKER_FLAGS_DEBUG "/debug /INCREMENTAL" CACHE STRING "" FORCE)
SET(CMAKE_SHARED_LINKER_FLAGS_MINSIZEREL "/INCREMENTAL:NO" CACHE STRING "" FORCE)
SET(CMAKE_SHARED_LINKER_FLAGS_RELEASE "/INCREMENTAL:NO" CACHE STRING "" FORCE)
SET(CMAKE_SHARED_LINKER_FLAGS_RELWITHDEBINFO "/debug /INCREMENTAL:NO /LTCG" CACHE STRING "" FORCE)

SET(CMAKE_STATIC_LINKER_FLAGS "" CACHE STRING "" FORCE)
SET(CMAKE_STATIC_LINKER_FLAGS_DEBUG "" CACHE STRING "" FORCE)
SET(CMAKE_STATIC_LINKER_FLAGS_MINSIZEREL "" CACHE STRING "" FORCE)
SET(CMAKE_STATIC_LINKER_FLAGS_RELEASE "" CACHE STRING "" FORCE)
SET(CMAKE_STATIC_LINKER_FLAGS_RELWITHDEBINFO "/LTCG" CACHE STRING "" FORCE)
# =============================================================================

set (LUABIND_USE_CXX11 ON)
set (LUABIND_NO_EXCEPTIONS OFF)
set (LUABIND_CPLUSPLUS_LUA ON)
set (LUABIND_NO_STD_SHARED_PTR OFF)
set (LUABIND_DYNAMIC_LINK ON)

set(LUA_FOUND 1)
set(LUA_INCLUDE_DIRS d:/library/lua_5_3_4/include )

IF(MSVC12)
  IF(CMAKE_CL_64)
    set(BOOST_ROOT d:/library/boost_1_67_0)
    set(LUA_LIBRARIES debug d:/code/vs/_main/third_party/trunk/lua/lib/vs12_x64/lua5.3d.lib optimized d:/code/vs/_main/third_party/trunk/lua/lib/vs12_x64/lua5.3.lib)
  ELSE(CMAKE_CL_64)
    set(BOOST_ROOT d:/library/boost_1_67_0)
    set(LUA_LIBRARIES debug d:/code/vs/_main/third_party/trunk/lua/lib/vs12/lua5.3d.lib optimized d:/code/vs/_main/third_party/trunk/lua/lib/vs12/lua5.3.lib)
  ENDIF(CMAKE_CL_64)
ELSE(MSVC12)
  IF(CMAKE_CL_64)
    set(BOOST_ROOT d:/library/boost_1_67_0)
    set(LUA_LIBRARIES debug d:/code/vs/_main/third_party/trunk/lua/lib/vs14_x64/lua5.3d.lib optimized d:/code/vs/_main/third_party/trunk/lua/lib/vs14_x64/lua5.3.lib)
  ELSE(CMAKE_CL_64)
    set(BOOST_ROOT d:/library/boost_1_67_0)
    set(LUA_LIBRARIES debug d:/code/vs/_main/third_party/trunk/lua/lib/vs14/lua5.3d.lib optimized d:/code/vs/_main/third_party/trunk/lua/lib/vs14/lua5.3.lib)
  ENDIF(CMAKE_CL_64)
ENDIF(MSVC12)

include_directories(BEFORE
    "${CMAKE_CURRENT_SOURCE_DIR}"
    "${CMAKE_CURRENT_BINARY_DIR}")

if(LUABIND_NO_STD_SHARED_PTR)
    add_definitions(-DLUABIND_NO_STD_SHARED_PTR)
endif()

file(MAKE_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/luabind")
configure_file(build_information.hpp.cmake_in "${CMAKE_CURRENT_BINARY_DIR}/luabind/build_information.hpp")

add_definitions(-DLUABIND_CPLUSPLUS_LUA)

set(BUILD_SHARED_LIBS ${LUABIND_DYNAMIC_LINK})
    
if(NOT Boost_FOUND)
	find_package(Boost REQUIRED)
endif()

if(NOT LUA_FOUND)
	find_package(Lua REQUIRED)
	set(LUA_INCLUDE_DIRS "${LUA_INCLUDE_DIR}")
endif()

include_directories(${Boost_INCLUDE_DIRS}
	"${CMAKE_CURRENT_SOURCE_DIR}"
	${LUA_INCLUDE_DIRS})

add_subdirectory(src)

set_property(GLOBAL PROPERTY CTEST_TARGETS_ADDED 1)
include(CTest)
if(BUILD_TESTING)
    add_subdirectory(test)
endif()
