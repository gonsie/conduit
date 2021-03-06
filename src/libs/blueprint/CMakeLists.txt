###############################################################################
# Copyright (c) 2014-2019, Lawrence Livermore National Security, LLC.
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


################################
# Add the blueprint library
################################

ENABLE_WARNINGS()


configure_file ("${CMAKE_CURRENT_SOURCE_DIR}/conduit_blueprint_exports.h.in"
                "${CMAKE_CURRENT_BINARY_DIR}/conduit_blueprint_exports.h")


#
# Specify all headers
#
set(blueprint_headers
    conduit_blueprint.hpp
    conduit_blueprint_mesh.hpp
    conduit_blueprint_mesh_examples.hpp
    conduit_blueprint_mesh_examples_julia.hpp
    conduit_blueprint_mesh_examples_venn.hpp
    conduit_blueprint_mcarray.hpp
    conduit_blueprint_mcarray_examples.hpp
    conduit_blueprint_o2mrelation.hpp
    conduit_blueprint_o2mrelation_iterator.hpp
    conduit_blueprint_o2mrelation_examples.hpp
    conduit_blueprint_zfparray.hpp
    ${CMAKE_CURRENT_BINARY_DIR}/conduit_blueprint_exports.h
)

#
# Specify blueprint c sources
#
set(blueprint_c_headers
    c/conduit_blueprint.h
    c/conduit_blueprint_mcarray.h
    c/conduit_blueprint_mesh.h
    )


#
# Specify blueprint cpp sources
#
set(blueprint_sources
    conduit_blueprint.cpp
    conduit_blueprint_mesh.cpp
    conduit_blueprint_mesh_examples.cpp
    conduit_blueprint_mesh_examples_julia.cpp
    conduit_blueprint_mesh_examples_venn.cpp
    conduit_blueprint_mcarray.cpp
    conduit_blueprint_mcarray_examples.cpp
    conduit_blueprint_o2mrelation.cpp
    conduit_blueprint_o2mrelation_iterator.cpp
    conduit_blueprint_o2mrelation_examples.cpp
    conduit_blueprint_zfparray.cpp
    )

#
# Specify blueprint c sources
#
set(blueprint_c_sources
    c/conduit_blueprint_c.cpp
    c/conduit_blueprint_mcarray_c.cpp
    c/conduit_blueprint_mesh_c.cpp
    )

#################################
# Add fortran interface
# if fortran support was selected
#################################

if(FORTRAN_FOUND)
    #
    # Specify fortran sources
    #
    set(blueprint_fortran_sources
        fortran/conduit_blueprint_fortran.f90
        fortran/conduit_blueprint_mcarray_fortran.f90
        fortran/conduit_blueprint_mesh_fortran.f90)

endif()

#
# Setup conduit blueprint lib
#
add_compiled_library(NAME conduit_blueprint
                     EXPORT conduit
                     HEADERS ${blueprint_headers} ${blueprint_c_headers}
                     SOURCES ${blueprint_sources} ${blueprint_c_sources} ${blueprint_fortran_sources}
                     DEPENDS_ON conduit
                     HEADERS_DEST_DIR include/conduit
                     FOLDER libs)


if(FORTRAN_FOUND)
    ###############################################################################
    # Special install targets for conduit fortran modules
    ###############################################################################

    set(conduit_blueprint_fortran_modules
        ${CMAKE_Fortran_MODULE_DIRECTORY}/conduit_blueprint.mod
        ${CMAKE_Fortran_MODULE_DIRECTORY}/conduit_blueprint_mesh.mod
        ${CMAKE_Fortran_MODULE_DIRECTORY}/conduit_blueprint_mcarray.mod)

    # Setup install to copy the fortran modules
    install(FILES
            ${conduit_blueprint_fortran_modules}
            DESTINATION include/conduit)

endif()


###################################
# add conduit_blueprint_verify exe
###################################

if(ENABLE_UTILS)
    blt_add_executable(
        NAME        conduit_blueprint_verify
        SOURCES     conduit_blueprint_verify_exe.cpp
        DEPENDS_ON  conduit conduit_relay conduit_blueprint
        OUTPUT_DIR ${CMAKE_CURRENT_BINARY_DIR}
        FOLDER utils)

    # add install target
    install(TARGETS conduit_blueprint_verify
            RUNTIME DESTINATION bin)
endif()

################################
# Add python wrappers if python
# support was selected
################################
if(PYTHON_FOUND)
    add_subdirectory(python)
endif()


################################################################
# If we have mpi, add the conduit blueprint mpi library
################################################################
if(MPI_FOUND)
#
# Specify blueprint mpi sources
#
set(conduit_blueprint_mpi_sources
    conduit_blueprint_mpi.cpp
    conduit_blueprint_mpi_mesh.cpp
    conduit_blueprint_mpi_mesh_examples.cpp)

#
# Specify blueprint mpi headers
#
set(conduit_blueprint_mpi_headers
    conduit_blueprint_mpi.hpp
    conduit_blueprint_mpi_mesh.hpp
    conduit_blueprint_mpi_mesh_examples.hpp
${CMAKE_CURRENT_BINARY_DIR}/conduit_blueprint_exports.h)


#
# Specify blueprint mpi deps
#
set(conduit_blueprint_mpi_deps conduit conduit_blueprint mpi conduit_relay_mpi)

#
# setup conduit blueprint mpi lib
#
add_compiled_library(NAME conduit_blueprint_mpi
                     EXPORT conduit
                     HEADERS ${conduit_blueprint_mpi_headers}
                     SOURCES ${conduit_blueprint_mpi_sources}
                     DEPENDS_ON ${conduit_blueprint_mpi_deps}
                     HEADERS_DEST_DIR include/conduit
                     FOLDER libs)

blt_add_target_compile_flags(TO conduit_blueprint_mpi FLAGS "-DCONDUIT_BLUEPRINT_MPI_ENABLED")


endif() # end if MPI_FOUND


