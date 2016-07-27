############################################################################
# CMakeLists.txt
# Copyright (C) 2014  Belledonne Communications, Grenoble France
#
############################################################################
#
# This program is free software; you can redistribute it and/or
# modify it under the terms of the GNU General Public License
# as published by the Free Software Foundation; either version 2
# of the License, or (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301, USA.
#
############################################################################

cmake_minimum_required(VERSION 3.0)
project(MSILBC C)


if(LINPHONE_BUILDER_GROUP_EXTERNAL_SOURCE_PATH_BUILDERS)
	include("${EP_ms2_CONFIG_DIR}/Mediastreamer2Config.cmake")
else()
	find_package(Mediastreamer2 REQUIRED)
endif()
find_package(IlbcRfc3951 REQUIRED)


option(ENABLE_STATIC "Build static library (default is shared library)." NO)


include_directories(
	${MEDIASTREAMER2_INCLUDE_DIRS}
	src
	${CMAKE_CURRENT_BINARY_DIR}
	${CMAKE_CURRENT_BINARY_DIR}/src
	${ILBCRFC3951_INCLUDE_DIRS}
)


set(LIBS
	${MEDIASTREAMER2_LIBRARIES}
	${ILBCRFC3951_LIBRARIES}
)

set(SOURCE_FILES ilbc.c)
if(ENABLE_STATIC)
	add_library(msilbc STATIC ${SOURCE_FILES})
else()
	add_library(msilbc SHARED ${SOURCE_FILES})
	set_target_properties(msilbc PROPERTIES VERSION 0)
	if(MSVC)
		if(CMAKE_BUILD_TYPE STREQUAL "Debug")
			install(FILES ${CMAKE_CURRENT_BINARY_DIR}/Debug/libmsilbc.pdb
				DESTINATION bin
				PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE
			)
		endif()
		set_target_properties(msilbc PROPERTIES PREFIX "lib")
	endif()
endif()
target_link_libraries(msilbc ${LIBS})

install(TARGETS msilbc
	RUNTIME DESTINATION lib/mediastreamer/plugins
	LIBRARY DESTINATION lib/mediastreamer/plugins
	ARCHIVE DESTINATION lib/mediastreamer/plugins
	PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE
)

