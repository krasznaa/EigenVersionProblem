# Copyright (C) 2002-2020 CERN for the benefit of the ATLAS collaboration

# Set up the project.
cmake_minimum_required( VERSION 3.6 )
project( EigenVersionProblem VERSION 1.0.0 LANGUAGES CXX )

# Use the GNU directory layout.
include( GNUInstallDirs )

# Set up the "old" library.
add_library( OldLibrary SHARED
   oldLibrary/oldLibrary.h oldLibrary/oldLibrary.cxx )
target_include_directories( OldLibrary
   PUBLIC $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/oldLibrary>
          $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
   PRIVATE ${CMAKE_SOURCE_DIR}/oldEigen )
set_target_properties( OldLibrary PROPERTIES
   PUBLIC_HEADER "oldLibrary/oldLibrary.h" )

# Set up the "new" library.
add_library( NewLibrary SHARED
   newLibrary/newLibrary.h newLibrary/newLibrary.cxx )
target_include_directories( NewLibrary
   PUBLIC $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/newLibrary>
          $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
   PRIVATE ${CMAKE_SOURCE_DIR}/newEigen )
set_target_properties( NewLibrary PROPERTIES
   PUBLIC_HEADER "newLibrary/newLibrary.h" )

# Set up RPATH for the executables.
set( CMAKE_INSTALL_RPATH )
if( APPLE )
   # On MacOS rely on @loader_path for relocatability.
   list( APPEND CMAKE_INSTALL_RPATH "@loader_path/../${CMAKE_INSTALL_LIBDIR}" )
elseif( UNIX )
   # On Linux rely on $ORIGIN instead.
   list( APPEND CMAKE_INSTALL_RPATH "\$ORIGIN/../${CMAKE_INSTALL_LIBDIR}" )
else()
   # If we're on something else, give up on relocatability...
   list( APPEND CMAKE_INSTALL_RPATH
      "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}" )
   message( WARNING "Binaries will not be relocatable" )
endif()


# Set up the test executables.
add_executable( testOld testOld.cxx )
target_link_libraries( testOld PRIVATE OldLibrary )

add_executable( testNew testNew.cxx )
target_link_libraries( testNew PRIVATE NewLibrary )

add_executable( testBoth1 testBoth.cxx )
target_link_libraries( testBoth1 PRIVATE OldLibrary NewLibrary )

add_executable( testBoth2 testBoth.cxx )
target_link_libraries( testBoth2 PRIVATE NewLibrary OldLibrary )

# Install everything.
install( TARGETS OldLibrary NewLibrary testOld testNew testBoth1 testBoth2
   RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
   LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
   PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR} )
