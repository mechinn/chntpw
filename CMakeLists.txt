cmake_minimum_required(VERSION 3.27)

project(chntpw VERSION 1.0.1
                  DESCRIPTION "The Offline Windows Password Editor"
                  LANGUAGES C)

find_package(OpenSSL)

add_library(edlib STATIC edlib.c)

add_library(ntreg STATIC ntreg.c)
set_target_properties(ntreg PROPERTIES INCLUDES "ntreg.h")
install(TARGETS ntreg
        EXPORT ntreg
        ARCHIVE DESTINATION lib
        INCLUDES DESTINATION include
        )

add_library(libsam STATIC libsam.c)
set_target_properties(ntreg PROPERTIES INCLUDES "sam.h")
install(TARGETS libsam
        EXPORT libsam
        ARCHIVE DESTINATION lib
        INCLUDES DESTINATION include
        )

add_executable(chntpw chntpw.c)
target_link_libraries(chntpw PUBLIC ntreg edlib libsam)
if(OPENSSL_FOUND)
    target_link_libraries(chntpw PUBLIC OpenSSL::Crypto)
    target_compile_definitions(chntpw PUBLIC DOCRYPTO)
endif()

add_executable(cpnt cpnt.c)

add_executable(reged reged.c)
target_link_libraries(reged PUBLIC ntreg edlib)

add_executable(samusrgrp samusrgrp.c)
target_link_libraries(samusrgrp PUBLIC ntreg libsam)

add_executable(sampasswd sampasswd.c)
target_link_libraries(sampasswd PUBLIC ntreg libsam)

install(TARGETS chntpw cpnt reged samusrgrp sampasswd RUNTIME DESTINATION bin)

set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "The Offline Windows Password Editor")
set(CPACK_PACKAGE_VERSION_MAJOR ${PROJECT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR ${PROJECT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH ${PROJECT_VERSION_PATCH})
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/GPL.txt")
set(CPACK_RESOURCE_FILE_README "${CMAKE_CURRENT_SOURCE_DIR}/README.txt")
set(CPACK_SOURCE_GENERATOR "TGZ;ZIP")
set(CPACK_SOURCE_IGNORE_FILES
    /.git
    /.vscode
    /dist
    /.*build.*
    /\\\\.DS_Store
)
include(CPack)