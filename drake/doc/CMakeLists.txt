set(DRAKE_DOCUMENTATION_DIR "${CMAKE_INSTALL_PREFIX}/share/doc/drake")

find_program(SPHINX_EXECUTABLE
  NAMES sphinx-build
  DOC "Sphinx documentation generator (sphinx-build)")

if(NOT SPHINX_EXECUTABLE)
  message(FATAL_ERROR "sphinx-build NOT found")
endif()

add_custom_command(
  COMMAND "${SPHINX_EXECUTABLE}"
    -b html
    -d ${CMAKE_CURRENT_BINARY_DIR}/doctrees
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_BINARY_DIR}/sphinx
  COMMENT "Building Sphinx documentation..."
  OUTPUT sphinx_output
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})
set_property(SOURCE sphinx_output PROPERTY SYMBOLIC ON)
install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/sphinx/
  DESTINATION ${DRAKE_DOCUMENTATION_DIR} OPTIONAL)

configure_file(Doxyfile_CXX.in Doxyfile_CXX @ONLY)
add_custom_command(
  COMMAND ${DOXYGEN_EXECUTABLE}
          ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile_CXX
  COMMENT "Building C++ Doxygen documentation..."
  OUTPUT doxygen_cxx_output
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})
set_property(SOURCE doxygen_cxx_output PROPERTY SYMBOLIC ON)
install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/doxygen_cxx/html/
  DESTINATION ${DRAKE_DOCUMENTATION_DIR}/doxygen_cxx OPTIONAL)

set(DRAKE_DOCUMENTATION_OUTPUTS sphinx_output doxygen_cxx_output)

if(DRAKE_DOCUMENTATION_OUTPUTS)
  # BUILD_DOCUMENTATION_ALWAYS is an undocumented option for use by CI builds.
  # When set to ON, documentation is included in the default build targets
  # "all" and "ALL_BUILD".
  # BUILD_DOCUMENTATION_ALWAYS is off by default so that developers do not have
  # to wait for the documentation to build every time they rebuild Drake itself.
  if(BUILD_DOCUMENTATION_ALWAYS)
    set(_DRAKE_DOCUMENTATION_ALL ALL)
  else()
    set(_DRAKE_DOCUMENTATION_ALL "")
  endif()

  add_custom_target(documentation ${_DRAKE_DOCUMENTATION_ALL}
    DEPENDS ${DRAKE_DOCUMENTATION_OUTPUTS})
  install(FILES .nojekyll CNAME DESTINATION ${DRAKE_DOCUMENTATION_DIR})
endif()
