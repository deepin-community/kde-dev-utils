# Bump KUIVIEWER_BASE_VERSION once new features are added
set(KUIVIEWER_BASE_VERSION "0.3")
set(KUIVIEWER_VERSION "${KUIVIEWER_BASE_VERSION}.${RELEASE_SERVICE_COMPACT_VERSION}")

include(ECMAddAppIcon)

find_package(Qt5 ${QT_MIN_VERSION} REQUIRED NO_MODULE
    COMPONENTS
    Core
    Designer
    UiTools
)

find_package(KF5 ${KF_MIN_VERSION} REQUIRED
    COMPONENTS
    CoreAddons
    I18n
    KIO
    Parts
)

ecm_setup_version(${KUIVIEWER_VERSION}
    VARIABLE_PREFIX KUIVIEWER
    VERSION_HEADER kuiviewer_version.h
)

add_executable(kuiviewer)

target_sources(kuiviewer PRIVATE
    main.cpp
    kuiviewer.cpp

    kuiviewer.qrc
)

file(GLOB ICONS_SRCS "${CMAKE_CURRENT_SOURCE_DIR}/*-apps-kuiviewer.png")
ecm_add_app_icon(kuiviewer ICONS ${ICONS_SRCS})

target_link_libraries(kuiviewer KF5::I18n KF5::Parts )

install(TARGETS kuiviewer  ${KDE_INSTALL_TARGETS_DEFAULT_ARGS} )


configure_file(kuiviewer_part.desktop.in ${CMAKE_CURRENT_BINARY_DIR}/kuiviewer_part.desktop @ONLY)

add_library(kuiviewerpart MODULE)

target_sources(kuiviewerpart PRIVATE
    kuiviewer_part.cpp

    kuiviewer_part.qrc
)

ecm_qt_declare_logging_category(kuiviewerpart
    HEADER kuiviewer_part_debug.h
    IDENTIFIER KUIVIEWERPART
    CATEGORY_NAME "kuiviewerpart"
)

kcoreaddons_desktop_to_json(kuiviewerpart ${CMAKE_CURRENT_BINARY_DIR}/kuiviewer_part.desktop)

target_link_libraries(kuiviewerpart
    KF5::I18n
    KF5::Parts
    Qt5::Designer
    Qt5::UiTools
)

install(TARGETS kuiviewerpart  DESTINATION ${KDE_INSTALL_PLUGINDIR}/kf5/parts )
install(FILES org.kde.kuiviewerpart.metainfo.xml DESTINATION ${KDE_INSTALL_METAINFODIR})


add_library(quithumbnail MODULE)

target_sources(quithumbnail PRIVATE
    quicreator.cpp
)

target_link_libraries(quithumbnail
    KF5::KIOWidgets
    Qt5::Designer
    Qt5::UiTools
)

install(TARGETS quithumbnail  DESTINATION ${KDE_INSTALL_PLUGINDIR} )

install( PROGRAMS org.kde.kuiviewer.desktop  DESTINATION  ${KDE_INSTALL_APPDIR} )
install(FILES
    designerthumbnail.desktop
    ${CMAKE_CURRENT_BINARY_DIR}/kuiviewer_part.desktop
    DESTINATION  ${KDE_INSTALL_KSERVICES5DIR}
)
install(FILES org.kde.kuiviewer.metainfo.xml DESTINATION ${KDE_INSTALL_METAINFODIR})

ecm_install_icons(ICONS
    16-apps-kuiviewer.png
    32-apps-kuiviewer.png
    48-apps-kuiviewer.png
    64-apps-kuiviewer.png
    128-apps-kuiviewer.png
    sc-apps-kuiviewer.svg
    DESTINATION ${KDE_INSTALL_ICONDIR}
    THEME hicolor
)
