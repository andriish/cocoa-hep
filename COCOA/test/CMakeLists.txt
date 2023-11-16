
if(CMAKE_SYSTEM_NAME MATCHES Darwin)
  set(STANDARDENVIRONMENT "DYLD_LIBRARY_PATH=${PROJECT_BINARY_DIR}:$ENV{DYLD_LIBRARY_PATH};PYTHIA8_XMLDOC_DIR=${PYTHIA8_XMLDOC_DIR}")
else()
  set(STANDARDENVIRONMENT "LD_LIBRARY_PATH=${PROJECT_BINARY_DIR}:$ENV{LD_LIBRARY_PATH};PYTHIA8_XMLDOC_DIR=${PYTHIA8_XMLDOC_DIR}")
endif()
foreach(t ${Geant4_DATASETS}) 
   set(STANDARDENVIRONMENT "${STANDARDENVIRONMENT};${Geant4_DATASET_${t}_ENVVAR}=${Geant4_DATASET_${t}_PATH}")
endforeach()
message(STATUS "COCOA test: STANDARDENVIRONMENT=${STANDARDENVIRONMENT}")

set(TESTS Wlep_Geant4Gun_geantino_scan Wlep_HepMC_hepmc_test Wlep_Pythia8_W)
foreach(t ${TESTS})
  foreach(_script ${COCOA_SCRIPTS})
    configure_file(${PROJECT_SOURCE_DIR}/${_script} ${CMAKE_CURRENT_BINARY_DIR}/${t}/${_script} COPYONLY)
    configure_file(${PROJECT_SOURCE_DIR}/${_script} ${CMAKE_CURRENT_BINARY_DIR}/${t}/${_script} COPYONLY)
  endforeach()
   FILE(COPY ${PROJECT_SOURCE_DIR}/tracking_configuration DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/${t}/)
   FILE(COPY ${PROJECT_SOURCE_DIR}/pflow_configuration DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/${t}/)
endforeach()

add_test(NAME Wlep_Geant4Gun_geantino_scan 
         COMMAND COCOA -c ${PROJECT_SOURCE_DIR}/config/config_Wlep.json --macro ${PROJECT_SOURCE_DIR}/macro/Geant4Gun/geantino_scan.in -s 5 -n 10
         WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/Wlep_Geant4Gun_geantino_scan
         )                               
set_tests_properties( Wlep_Geant4Gun_geantino_scan PROPERTIES TIMEOUT 500 ENVIRONMENT "${STANDARDENVIRONMENT}")


add_test(NAME Wlep_HepMC_hepmc_test
         COMMAND COCOA -c ${PROJECT_SOURCE_DIR}/config/config_Wlep.json --macro ${PROJECT_SOURCE_DIR}/macro/HepMC/hepmc_test.in -s 5 -n 10
         WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/Wlep_HepMC_hepmc_test
         )                               
set_tests_properties( Wlep_HepMC_hepmc_test PROPERTIES TIMEOUT 500 ENVIRONMENT "${STANDARDENVIRONMENT}")


add_test(NAME Wlep_Pythia8_W
         COMMAND COCOA -c ${PROJECT_SOURCE_DIR}/config/config_Wlep.json --macro ${PROJECT_SOURCE_DIR}/macro/Pythia8/W.in -s 5 -n 10
         WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/Wlep_Pythia8_W
         )                               
set_tests_properties( Wlep_Pythia8_W PROPERTIES TIMEOUT 500 ENVIRONMENT "${STANDARDENVIRONMENT}")
