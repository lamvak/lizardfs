if(NOT PYTHONINTERP_FOUND)
  message(FATAL_ERROR "No python interpreter found!")
else()
  message(STATUS "Using python ${PYTHON_VERSION_STRING}")
endif()

set(CGI_FILES err.gif favicon.ico index.html logomini.png mfs.css)
set(CGI_SCRIPTS
    ${CMAKE_CURRENT_BINARY_DIR}/mfs.cgi
    ${CMAKE_CURRENT_BINARY_DIR}/chart.cgi)
set(CGI_SERVER
    ${CMAKE_CURRENT_BINARY_DIR}/cgiserv.py)

set(PYTHON_SRCS cgiserv.py chart.cgi mfs.cgi)
foreach(SRC ${PYTHON_SRCS})
  configure_file(${SRC}.in ${SRC})
endforeach()

if(${PYTHON_VERSION_MAJOR} EQUAL 3)
  message(STATUS "Converting python sources with 2to3")
  execute_process(COMMAND 2to3 -n -w --no-diffs ${PYTHON_SRCS}
    RESULT_VARIABLE RESULT
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})
  if(NOT "${RESULT}" STREQUAL "0")
    message(FATAL_ERROR "2to3 failed")
  endif()
endif()

install(FILES ${CGI_FILES} DESTINATION ${CGI_SUBDIR})
install(PROGRAMS ${CGI_SCRIPTS} DESTINATION ${CGI_SUBDIR})
install(PROGRAMS ${CGI_SERVER} DESTINATION ${SBIN_SUBDIR} RENAME mfscgiserv)
