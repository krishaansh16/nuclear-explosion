#----------------------------------------------------------------------------
# Setup the project
cmake_minimum_required(VERSION 3.8...3.18)
if(${CMAKE_VERSION} VERSION_LESS 3.12)
  cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
endif()
project(sauna)

#----------------------------------------------------------------------------
# Find Geant4 package, activating all available UI and Vis drivers by default
# You can set WITH_GEANT4_UIVIS to OFF via the command line or ccmake/cmake-gui
# to build a batch mode only executable
#
option(WITH_GEANT4_UIVIS "Build example with Geant4 UI and Vis drivers" ON)
if(WITH_GEANT4_UIVIS)
  find_package(Geant4 REQUIRED ui_all vis_all)
else()
  find_package(Geant4 REQUIRED)
endif()

#----------------------------------------------------------------------------
# Setup Geant4 include directories and compile definitions
# Setup include directory for this project
#
include(${Geant4_USE_FILE})
include_directories(${PROJECT_SOURCE_DIR}/include)

#----------------------------------------------------------------------------
# Locate sources and headers for this project
# NB: headers are included so they will show up in IDEs
#
file(GLOB sources ${PROJECT_SOURCE_DIR}/src/*.cc)
file(GLOB headers ${PROJECT_SOURCE_DIR}/include/*.hh)

#----------------------------------------------------------------------------
# Add the executable, and link it to the Geant4 libraries
#
add_executable(main main.cc ${sources} ${headers})
target_link_libraries(main ${Geant4_LIBRARIES})

#----------------------------------------------------------------------------
# Copy all scripts to the build directory, i.e. the directory in which we
# build sauna. This is so that we can run the executable directly because it
# relies on these scripts being in the current working directory.
#
set(sauna_SCRIPTS
  sauna.in
  sauna.out
  init_vis.mac
  gps.mac
  run1.mac
  run2.mac
  vis.mac
  scoring.mac
  draw.mac
  big.mac
  cesium.mac
  test.mac
  testVol.mac
  xe133beta.mac
  hist_xe133.mac
  xe133.mac
  xe133dual.mac
  xe135.mac
  hist_xe135.mac
  xe135beta.mac
  xe135dual.mac
  xe133CE.mac
  gamma.mac
  xe135dual250.mac
  xe133dual80.mac
  )

foreach(_script ${sauna_SCRIPTS})
  configure_file(
    ${PROJECT_SOURCE_DIR}/${_script}
    ${PROJECT_BINARY_DIR}/${_script}
    COPYONLY
    )
endforeach()

#----------------------------------------------------------------------------
# For internal Geant4 use - but has no effect if you build this
# example standalone
#
add_custom_target(sauna DEPENDS main)

#----------------------------------------------------------------------------
# Install the executable to 'bin' directory under CMAKE_INSTALL_PREFIX
#
install(TARGETS main DESTINATION bin)
