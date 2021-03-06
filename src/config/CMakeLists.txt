###############################################################################
# Copyright (c) 2015-2019, Lawrence Livermore National Security, LLC.
#
# Produced at the Lawrence Livermore National Laboratory
#
# LLNL-CODE-666778
#
# All rights reserved.
#
# This file is part of Conduit.
#
# For details, see: http://software.llnl.gov/conduit/.
#
# Please also read conduit/LICENSE
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
#
# * Redistributions of source code must retain the above copyright notice,
#   this list of conditions and the disclaimer below.
#
# * Redistributions in binary form must reproduce the above copyright notice,
#   this list of conditions and the disclaimer (as noted below) in the
#   documentation and/or other materials provided with the distribution.
#
# * Neither the name of the LLNS/LLNL nor the names of its contributors may
#   be used to endorse or promote products derived from this software without
#   specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED. IN NO EVENT SHALL LAWRENCE LIVERMORE NATIONAL SECURITY,
# LLC, THE U.S. DEPARTMENT OF ENERGY OR CONTRIBUTORS BE LIABLE FOR ANY
# DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
# DAMAGES  (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS
# OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
# HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT,
# STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING
# IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
# POSSIBILITY OF SUCH DAMAGE.
#
###############################################################################

###############################################################################
# create config.mk for makefile-based build systems
###############################################################################

configure_file ("${CMAKE_CURRENT_SOURCE_DIR}/conduit_config.mk.in"
                "${CMAKE_CURRENT_BINARY_DIR}/conduit_config.mk")

# install our config.mk
install(FILES
        "${CMAKE_CURRENT_BINARY_DIR}/conduit_config.mk"
        DESTINATION share/conduit/)

###############################################################################
# export everything for a cmake build to be able to import with find_package
###############################################################################

if (NOT DEFINED CONDUIT_INSTALL_INCLUDE_DIR)
    set(CONDUIT_INSTALL_INCLUDE_DIR "include")
endif()

if (NOT DEFINED CONDUIT_INSTALL_CONFIG_DIR)
    set(CONDUIT_INSTALL_CONFIG_DIR "lib")
endif()

if (NOT DEFINED CONDUIT_INSTALL_LIB_DIR)
    set(CONDUIT_INSTALL_LIB_DIR "lib")
endif()

if (NOT DEFINED CONDUIT_INSTALL_BIN_DIR)
    set(CONDUIT_INSTALL_BIN_DIR "bin")
endif()

if (NOT DEFINED CONDUIT_INSTALL_SHARED_RESOURCES_DIR)
  set(CONDUIT_INSTALL_SHARED_RESOURCES_DIR "share/conduit")
endif()

if (NOT DEFINED CONDUIT_INSTALL_CMAKE_MODULE_DIR)
  set(CONDUIT_INSTALL_CMAKE_MODULE_DIR "${CONDUIT_INSTALL_CONFIG_DIR}/cmake")
endif()

if (NOT DEFINED CONDUIT_INSTALL_PYTHON_MODULE_DIR)
    if(PYTHON_MODULE_INSTALL_PREFIX)
        set(CONDUIT_INSTALL_PYTHON_MODULE_DIR ${PYTHON_MODULE_INSTALL_PREFIX})
    else()
        set(CONDUIT_INSTALL_PYTHON_MODULE_DIR ${CMAKE_INSTALL_PREFIX}/python-modules/)
    endif()
endif()

include(CMakePackageConfigHelpers)

# write version heler
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/ConduitConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)


# setup cmake package config file
configure_package_config_file(
  ConduitConfig.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/ConduitConfig.cmake
  INSTALL_DESTINATION ${CONDUIT_INSTALL_CONFIG_DIR}
  PATH_VARS
    CONDUIT_INSTALL_INCLUDE_DIR
    CONDUIT_INSTALL_LIB_DIR
    CONDUIT_INSTALL_BIN_DIR
    CONDUIT_INSTALL_SHARED_RESOURCES_DIR
    CONDUIT_INSTALL_CMAKE_MODULE_DIR
    )


# install everything needed

# install exports to comply with standard find_package search path expectations
install(FILES
        ${CMAKE_CURRENT_BINARY_DIR}/ConduitConfig.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/ConduitConfigVersion.cmake
        conduit_setup_deps.cmake
        conduit_setup_targets.cmake
        DESTINATION ${CONDUIT_INSTALL_CMAKE_MODULE_DIR}/conduit/)

# also install to support our old export directory layout 
# (preserving for now to avoid issues with downstream packages using old layout)
install(FILES
        ${CMAKE_CURRENT_BINARY_DIR}/ConduitConfig.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/ConduitConfigVersion.cmake
        conduit_setup_deps.cmake
        conduit_setup_targets.cmake
        DESTINATION ${CONDUIT_INSTALL_CMAKE_MODULE_DIR})

