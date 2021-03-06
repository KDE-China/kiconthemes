add_subdirectory(tools/kiconfinder)
if (APPLE)
    add_subdirectory(tools/ksvg2icns)
endif()

set(kiconthemes_SRCS
  kiconbutton.cpp
  kicondialog.cpp
  kiconeffect.cpp
  kiconengine.cpp
  kiconloader.cpp
  kicontheme.cpp
)
qt5_add_resources(kiconthemes_SRCS
  hicolor.qrc
)
ecm_qt_declare_logging_category(kiconthemes_SRCS HEADER debug.h IDENTIFIER  KICONTHEMES CATEGORY_NAME kf5.kiconthemes)

add_library(KF5IconThemes ${kiconthemes_SRCS})
generate_export_header(KF5IconThemes BASE_NAME KIconThemes)
add_library(KF5::IconThemes ALIAS KF5IconThemes)

target_include_directories(KF5IconThemes INTERFACE "$<INSTALL_INTERFACE:${KDE_INSTALL_INCLUDEDIR_KF5}/KIconThemes>")

target_link_libraries(KF5IconThemes
  PUBLIC
    Qt5::Widgets
  PRIVATE
    Qt5::DBus
    Qt5::Svg
    KF5::Archive       # for KCompressionDevice
    KF5::I18n          # for i18n in KIconDialog
    KF5::WidgetsAddons # for KPixmapSequence family
    KF5::ItemViews      # for KListWidgetSearchLine
    KF5::ConfigWidgets # for KColorScheme
    KF5::CoreAddons    # for kshareddatacache.h
)

set_target_properties(KF5IconThemes PROPERTIES VERSION   ${KICONTHEMES_VERSION_STRING}
                                               SOVERSION ${KICONTHEMES_SOVERSION}
                                               EXPORT_NAME IconThemes
)

ecm_generate_headers(KIconThemes_HEADERS
  HEADER_NAMES
  KIconButton
  KIconDialog
  KIconEffect
  KIconLoader
  KIconTheme
  KIconEngine

  REQUIRED_HEADERS KIconThemes_HEADERS
)

install(TARGETS KF5IconThemes EXPORT KF5IconThemesTargets ${KF5_INSTALL_TARGETS_DEFAULT_ARGS})

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/kiconthemes_export.h
  ${KIconThemes_HEADERS}
  DESTINATION ${KDE_INSTALL_INCLUDEDIR_KF5}/KIconThemes COMPONENT Devel
)

if(BUILD_QCH)
    ecm_add_qch(
        KF5IconThemes_QCH
        NAME KIconThemes
        BASE_NAME KF5IconThemes
        VERSION ${KF5_VERSION}
        ORG_DOMAIN org.kde
        SOURCES # using only public headers, to cover only public API
            ${KIconThemes_HEADERS}
        MD_MAINPAGE "${CMAKE_SOURCE_DIR}/README.md"
        IMAGE_DIRS "${CMAKE_SOURCE_DIR}/docs/pics"
        LINK_QCHS
            Qt5Widgets_QCH
        BLANK_MACROS
            KICONTHEMES_EXPORT
            KICONTHEMES_DEPRECATED
            KICONTHEMES_DEPRECATED_EXPORT
        TAGFILE_INSTALL_DESTINATION ${KDE_INSTALL_QTQCHDIR}
        QCH_INSTALL_DESTINATION ${KDE_INSTALL_QTQCHDIR}
        COMPONENT Devel
    )
endif()

include(ECMGeneratePriFile)
ecm_generate_pri_file(BASE_NAME KIconThemes LIB_NAME KF5IconThemes DEPS "widgets" FILENAME_VAR PRI_FILENAME INCLUDE_INSTALL_DIR ${KDE_INSTALL_INCLUDEDIR_KF5}/KIconThemes)
install(FILES ${PRI_FILENAME} DESTINATION ${ECM_MKSPECS_INSTALL_DIR})
