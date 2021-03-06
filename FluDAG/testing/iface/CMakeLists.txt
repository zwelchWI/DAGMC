cmake_minimum_required(VERSION 2.8)
project (TestFluDAG)
find_package(GTest REQUIRED)

# If FLUKA is installed, $FLUPRO is probably an environment varaiable
IF (NOT DEFINED FLUPRO)
	set (FLUPRO "$ENV{FLUPRO}")
ENDIF (NOT DEFINED FLUPRO)

IF (NOT DEFINED MOAB_HOME OR NOT DEFINED TOPDIR)
	INCLUDE(local.cmake)
ENDIF (NOT DEFINED MOAB_HOME OR NOT DEFINED TOPDIR)

IF (NOT DEFINED TOPDIR)
	message("Please define the directory containing DAGMC/FluDAG.")
	message("Definition can be done with the -D switch or in local.cmake.")
ENDIF (NOT DEFINED TOPDIR)

IF (NOT DEFINED FLUDAG)
	set (FLUDAG  ${TOPDIR}/DAGMC/FluDAG)
	set (FLUDAG_SRC ${FLUDAG}/src)
	set (FLUDAG_TST	${FLUDAG}/testing)
ENDIF (NOT DEFINED FLUDAG)

set(MOAB_INCLUDE ${MOAB_HOME}/include)
set(MOAB_LIB_DIR ${MOAB_HOME}/lib)
set (moablibs ${MOAB_LIB_DIR}/libMOAB.so ${MOAB_LIB_DIR}/libdagmc.so)

include_directories(${GTEST_INCLUDE_DIRS})
include_directories(${FLUDAG}/src/include)
include_directories(${MOAB_INCLUDE})

set (gmain      ${FLUDAG_TST}/iface/gtest_main.cc)
set (ifacefiles ${FLUDAG_TST}/iface/tests/issue22.cpp)
set (issuefiles ${FLUDAG_SRC}/cpp/Input.cpp ${FLUDAG_SRC}/cpp/fluka_funcs.cpp)
add_executable(issuetest ${gmain} ${ifacefiles} ${issuefiles} ${gmain})
target_link_libraries(issuetest ${PROJECT_NAME_STR} ${moablibs} ${GTEST_BOTH_LIBRARIES} pthread)

set (srcfiles ${FLUDAG_SRC}/cpp/fluka_funcs.cpp ${gmain})
set (wrappers ${FLUDAG_TST}/iface/tests/nrml_test.cpp)
add_executable(nrml ${srcfiles} ${wrappers})
target_link_libraries(nrml ${PROJECT_NAME_STR} ${moablibs} ${GTEST_BOTH_LIBRARIES} pthread)

ENABLE_TESTING()
#add_test(${PROJECT_TEST_NAME} ${PROJECT_TEST_NAME})

