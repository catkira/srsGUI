########################################################################
# Setup Qt and Qwt
########################################################################

find_package(Qt5 COMPONENTS Widgets REQUIRED)

FIND_PACKAGE(Qwt)
IF(Qt5_FOUND AND QWT_FOUND)
    INCLUDE_DIRECTORIES(${QWT_INCLUDE_DIRS})
ENDIF(Qt5_FOUND AND QWT_FOUND)
 
########################################################################
# Build the graphics library
########################################################################

file(GLOB modules *) 

SET(SOURCES_ALL "")
FILE(GLOB tmp "*.cpp")
LIST(APPEND SOURCES_ALL ${tmp})
FOREACH (_module ${modules})
	IF(IS_DIRECTORY ${_module})
		FILE(GLOB tmp "${_module}/*.cpp")
		LIST(APPEND SOURCES_ALL ${tmp})		
	ENDIF(IS_DIRECTORY ${_module})
ENDFOREACH(_module ${modules})

IF(QWT_MAJOR_VERSION LESS 6)
	MESSAGE(STATUS "QWT6 is required.")
ENDIF(QWT_MAJOR_VERSION LESS 6)

IF(Qt5_FOUND AND QWT_FOUND AND QWT_MAJOR_VERSION EQUAL 6)
  QT5_WRAP_CPP(lineplotwraps ../include/srsgui/common/Lineplot.h)
  QT5_WRAP_CPP(pointplotwraps ../include/srsgui/common/Pointplot.h)
  QT5_WRAP_CPP(spectrogramplotwraps ../include/srsgui/common/Spectrogramplot.h)
  QT5_WRAP_CPP(complex complexplot/ComplexWidget.h complexplot/ComplexplotWrapper.h)
  QT5_WRAP_CPP(real ../include/srsgui/widget/RealWidget.h realplot/RealplotWrapper.h)
  QT5_WRAP_CPP(scatter ../include/srsgui/widget/ScatterWidget.h scatterplot/ScatterplotWrapper.h)
  QT5_WRAP_CPP(waterfall waterfallplot/WaterfallWidget.h waterfallplot/WaterfallplotWrapper.h)
  QT5_WRAP_CPP(textedit textedit/TextEditWidget.h textedit/TextEditWrapper.h)
  QT5_WRAP_CPP(keyvalue keyvalue/KeyValueWidget.h keyvalue/KeyValueWrapper.h)
  
  ADD_LIBRARY(srsgui_static STATIC ${eventwraps} ${lineplotwraps} ${pointplotwraps} ${spectrogramplotwraps} ${complex} ${real} ${scatter} ${waterfall} ${textedit} ${keyvalue} ${SOURCES_ALL} )
  ADD_LIBRARY(srsgui SHARED ${eventwraps} ${lineplotwraps} ${pointplotwraps} ${spectrogramplotwraps} ${complex} ${real} ${scatter} ${waterfall} ${textedit} ${keyvalue} ${SOURCES_ALL} )

  TARGET_INCLUDE_DIRECTORIES(srsgui_static PUBLIC common complexplot realplot scatterplot waterfallplot textedit keyvalue ${Boost_INCLUDE_DIRS})
  TARGET_INCLUDE_DIRECTORIES(srsgui PUBLIC common complexplot realplot scatterplot waterfallplot textedit keyvalue ${Boost_INCLUDE_DIRS})

  TARGET_LINK_LIBRARIES(srsgui_static pthread Qt5::Widgets ${QWT_LIBRARIES})
  TARGET_LINK_LIBRARIES(srsgui pthread Qt5::Widgets ${QWT_LIBRARIES})
  INSTALL(TARGETS srsgui_static DESTINATION ${LIBRARY_DIR})
  INSTALL(TARGETS srsgui DESTINATION ${LIBRARY_DIR})
  SG_SET_PIC(srsgui)
  SG_SET_PIC(srsgui_static)

  set_target_properties(srsgui_static PROPERTIES OUTPUT_NAME srsgui)
  set_target_properties(srsgui PROPERTIES OUTPUT_NAME srsgui)

  MESSAGE(STATUS "   srsGUI library will be installed.")

ELSE(Qt5_FOUND AND QWT_FOUND AND AND QWT_MAJOR_VERSION EQUAL 6) 

  MESSAGE(STATUS "   QT5 or Qwt6 not found. srsGUI library is not generated")
  
ENDIF(Qt5_FOUND AND QWT_FOUND AND QWT_MAJOR_VERSION EQUAL 6)




