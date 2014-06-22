project (kitemviews)

# Project header
project_cxx_library_header_default ()

# 3rdparty
if (WIN32)
    find_package (Qt4 4.7.1 COMPONENTS QtCore QtGui QtXml REQUIRED)
else (UNIX)
    find_package (Qt4 4.7.1 COMPONENTS QtCore QtGui QtXml QtDbus REQUIRED)
endif ()
include (${QT_USE_FILE})
list (APPEND ${PROJECT_NAME}_LIBS ${QT_LIBRARIES})
add_definitions (${QT_DEFINITIONS})# -DQT_NO_CAST_FROM_ASCII)

find_package (KDE4 REQUIRED)
include_directories (${KDE4_INCLUDE_DIR})
add_definitions (${KDE4_DEFINITIONS})
list (APPEND ${PROJECT_NAME}_LIBS ${KDE4_KDECORE_LIBS} ${KDE4_KDEUI_LIBS} ${KDE4_KIO_LIBS})

# Sources
include_directories (${CMAKE_CURRENT_SOURCE_DIR}/src
                     ${CMAKE_CURRENT_BINARY_DIR}/src
                     ${CMAKE_CURRENT_SOURCE_DIR}/stubs
                     ${CMAKE_CURRENT_BINARY_DIR}/src/private)

add_subdirectory (src)

QT4_WRAP_CPP (${PROJECT_NAME}_MOC_SOURCES ${${PROJECT_NAME}_HEADERS} OPTIONS "-nw")
foreach (file ${${PROJECT_NAME}_MOC_SOURCES})
    string (REGEX REPLACE "^(.+)/moc_(.+)\\.cxx$" "\\1/\\2.moc" file2 "${file}")
    add_custom_command (OUTPUT ${file2}
                        COMMAND cmake -E copy ${file} ${file2} 
                        DEPENDS ${file})
    list (APPEND ${PROJECT_NAME}_SOURCES ${file2})
endforeach ()

# Target - kitemviews
add_library(kitemviews STATIC ${${PROJECT_NAME}_SOURCES})
target_link_libraries (kitemviews ${${PROJECT_NAME}_LIBS})

# Version
build_version_file (kitemviews 0.0.1)

# Documentation
add_documentation ("LVFS-Core" 0.0.1 "LVFS core library")

# Install rules
install_header_files (kitemviews "src" "src/private:private/")
install_target (kitemviews)
