#********************************************************************
#        _       _         _
#  _ __ | |_  _ | |  __ _ | |__   ___
# | '__|| __|(_)| | / _` || '_ \ / __|
# | |   | |_  _ | || (_| || |_) |\__ \
# |_|    \__|(_)|_| \__,_||_.__/ |___/
#
# www.rt-labs.com
# Copyright 2018 rt-labs AB, Sweden.
#
# This software is dual-licensed under GPLv3 and a commercial
# license. See the file LICENSE.md distributed with this software for
# full license information.
#*******************************************************************/

option(TEST_DEBUG "Add trace output from test case code" OFF)

set_target_properties (pf_test
  PROPERTIES
  C_STANDARD 99
  CXX_STANDARD 11
  )

target_sources(pf_test PRIVATE
  # Unit tests
  test_alarm.cpp
  test_block_reader.cpp
  test_cmdev.cpp
  test_cmdmc.cpp
  test_cmina.cpp
  test_cmio.cpp
  test_cmpbe.cpp
  test_cmrdr.cpp
  test_cmrpc.cpp
  test_cmrs.cpp
  test_cmsm.cpp
  test_cmsu.cpp
  test_cmwdr.cpp
  test_cpm.cpp
  test_dcp.cpp
  test_diag.cpp
  test_eth.cpp
  test_file.cpp
  test_fspm.cpp
  test_lldp.cpp
  test_pnetapi.cpp
  test_port.cpp
  test_ppm.cpp
  test_ptcp.cpp
  test_scheduler.cpp
  $<$<BOOL:${PNET_OPTION_SNMP}>:${PROFINET_SOURCE_DIR}/test/test_snmp.cpp>
  utils_for_testing.h
  utils_for_testing.cpp

  # Mocks
  mocks.h
  mocks.cpp

  # Testrunner
  pf_test.cpp
  )

# Rebuild units to be tested with UNIT_TEST flag set. This is used to
# mock external dependencies.
target_sources(pf_test PRIVATE
  # Units to be tested
  ${PROFINET_SOURCE_DIR}/src/device/pf_block_reader.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_block_writer.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_fspm.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_diag.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_cmdev.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_cmdmc.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_cmina.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_cmio.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_cmpbe.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_cmrdr.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_cmrpc.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_cmrpc_helpers.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_cmrs.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_cmsm.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_cmsu.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_cmwrr.c
  ${PROFINET_SOURCE_DIR}/src/device/pf_pdport.c
  ${PROFINET_SOURCE_DIR}/src/device/pnet_api.c
  ${PROFINET_SOURCE_DIR}/src/common/pf_alarm.c
  ${PROFINET_SOURCE_DIR}/src/common/pf_bg_worker.c
  ${PROFINET_SOURCE_DIR}/src/common/pf_cpm_driver_sw.c
  ${PROFINET_SOURCE_DIR}/src/common/pf_dcp.c
  ${PROFINET_SOURCE_DIR}/src/common/pf_eth.c
  ${PROFINET_SOURCE_DIR}/src/common/pf_lldp.c
  ${PROFINET_SOURCE_DIR}/src/common/pf_file.c
  ${PROFINET_SOURCE_DIR}/src/common/pf_ppm.c
  ${PROFINET_SOURCE_DIR}/src/common/pf_ppm_driver_sw.c
  ${PROFINET_SOURCE_DIR}/src/common/pf_ptcp.c
  ${PROFINET_SOURCE_DIR}/src/common/pf_scheduler.c
  $<$<BOOL:${PNET_OPTION_SNMP}>:${PROFINET_SOURCE_DIR}/src/common/pf_snmp.c>
  ${PROFINET_SOURCE_DIR}/src/common/pf_udp.c
  )

# Set logging from the stack in tests
get_target_property(PROFINET_OPTIONS profinet COMPILE_OPTIONS)
target_compile_options(pf_test PRIVATE
  -DUNIT_TEST
  $<$<BOOL:${TEST_DEBUG}>:-DTEST_DEBUG>
  $<$<NOT:$<BOOL:${TEST_DEBUG}>>:-DPF_ETH_LOG=LOG_STATE_OFF>
  $<$<NOT:$<BOOL:${TEST_DEBUG}>>:-DPF_CPM_LOG=LOG_STATE_OFF>
  $<$<NOT:$<BOOL:${TEST_DEBUG}>>:-DPF_PPM_LOG=LOG_STATE_OFF>
  $<$<NOT:$<BOOL:${TEST_DEBUG}>>:-DPF_DCP_LOG=LOG_STATE_OFF>
  $<$<NOT:$<BOOL:${TEST_DEBUG}>>:-DPF_RPC_LOG=LOG_STATE_OFF>
  $<$<NOT:$<BOOL:${TEST_DEBUG}>>:-DPF_ALARM_LOG=LOG_STATE_OFF>
  $<$<NOT:$<BOOL:${TEST_DEBUG}>>:-DPF_AL_BUF_LOG=LOG_STATE_OFF>
  $<$<NOT:$<BOOL:${TEST_DEBUG}>>:-DPF_LLDP_LOG=LOG_STATE_OFF>
  $<$<NOT:$<BOOL:${TEST_DEBUG}>>:-DPF_SNMP_LOG=LOG_STATE_OFF>
  $<$<NOT:$<BOOL:${TEST_DEBUG}>>:-DPF_PNAL_LOG=LOG_STATE_OFF>
  $<$<NOT:$<BOOL:${TEST_DEBUG}>>:-DPNET_LOG=LOG_STATE_OFF>
  ${PROFINET_OPTIONS}
  )

target_include_directories(pf_test
  PRIVATE
  ${PROFINET_SOURCE_DIR}/src
  ${PROFINET_SOURCE_DIR}/src/common
  ${PROFINET_SOURCE_DIR}/src/device
  ${PROFINET_BINARY_DIR}/src
  )

# Link against profinet target to get the cmake properties for that
# target, such as include paths and libraries.
target_link_libraries(pf_test
  PRIVATE
  profinet
  )

# No need for gmock
set(BUILD_GMOCK OFF CACHE BOOL "")
