message("Euromap .txt")

set(NODESET_DIR ${CMAKE_CURRENT_SOURCE_DIR}/nodesets)

if(NOT CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
    # needed or cmake doesn't recognize dependencies of generated files
    set(PROJECT_BINARY_DIR ${CMAKE_BINARY_DIR})
endif()

message("build euromap")

message("NodeSet dir = ${NODESET_DIR}")

# PLCopen requires the full ns0 as basis
if(UA_NAMESPACE_ZERO STREQUAL "FULL")

    # Generate types and namespace for DI
    ua_generate_nodeset_and_datatypes(
        NAME "di"
        FILE_CSV "${NODESET_DIR}/OpcUaDiModel.csv"
        FILE_BSD "${NODESET_DIR}/Opc.Ua.Di.Types.bsd"
        NAMESPACE_IDX 2
        NAMESPACE_MAP "2:http://opcfoundation.org/UA/DI/"
        FILE_NS "${NODESET_DIR}/Opc.Ua.Di.NodeSet2.xml"
        INTERNAL
    )



    #############################################################################
    # generate euromap83 namespace which is using DI
    ua_generate_nodeset_and_datatypes(
        NAME "euromap83"
        FILE_CSV "${NODESET_DIR}/Opc_Ua.EUROMAP83.RC1_01.NodeId.csv"
        FILE_BSD "${NODESET_DIR}/Opc_Ua.EUROMAP83.1_01.NodeSet2.bsd"
        NAMESPACE_IDX 3
        NAMESPACE_MAP "3:http://www.euromap.org/euromap83/"
        FILE_NS "${NODESET_DIR}/Opc_Ua.EUROMAP83.1_01.NodeSet2.xml"
        DEPENDS "di"
        INTERNAL
    )

    # generate euromap77 namespace which is using euromap83
    ua_generate_nodeset_and_datatypes(
        NAME "euromap77"
        FILE_CSV "${NODESET_DIR}/Opc_Ua.EUROMAP77.1_00.NodeId.csv"
        FILE_BSD "${NODESET_DIR}/Opc_Ua.EUROMAP77.1_00.NodeSet2.bsd"
        NAMESPACE_IDX 4
        NAMESPACE_MAP "4:http://www.euromap.org/euromap77/"
        FILE_NS "${NODESET_DIR}/Opc_Ua.EUROMAP77.1_00.NodeSet2.xml"
        DEPENDS "euromap83"
        INTERNAL
    )

    add_example(server_nodeset_euromap server_nodeset_euromap83.c
                ${UA_NODESET_DI_SOURCES}
                ${UA_NODESET_EUROMAP83_SOURCES}
                ${UA_NODESET_EUROMAP77_SOURCES}
                ${UA_TYPES_DI_SOURCES}
                ${UA_TYPES_EUROMAP83_SOURCES}
                ${UA_TYPES_EUROMAP77_SOURCES}
                )
    add_dependencies(server_nodeset_euromap open62541-generator-ns-euromap77)


    #############################################################################
    #############################################################################
    # try again with new euromap nodesets


    set(NODESET_DIR_X ${CMAKE_CURRENT_SOURCE_DIR}/nodesetsX)

    #############################################################################
    # generate euromap83 namespace which is using DI
    ua_generate_nodeset_and_datatypes(
        NAME "euromap83_x"
        FILE_CSV "${NODESET_DIR_X}/Opc.Ua.PlasticsRubber.GeneralTypes.NodeSet2.csv"
        FILE_BSD "${NODESET_DIR_X}/Opc.Ua.PlasticsRubber.GeneralTypes.NodeSet2.bsd"
        NAMESPACE_IDX 3
        NAMESPACE_MAP "3:http://opcfoundation.org/UA/PlasticsRubber/GeneralTypes/"
        FILE_NS "${NODESET_DIR_X}/Opc.Ua.PlasticsRubber.GeneralTypes.NodeSet2.xml"
        DEPENDS "di"
        INTERNAL
    )

    # generate euromap77 namespace which is using euromap83
    ua_generate_nodeset_and_datatypes(
        NAME "euromap77_x"
        FILE_CSV "${NODESET_DIR_X}/Opc.Ua.PlasticsRubber.IMM2MES.NodeSet2.csv"
        FILE_BSD "${NODESET_DIR_X}/Opc.Ua.PlasticsRubber.IMM2MES.NodeSet2.bsd"
        NAMESPACE_IDX 4
        NAMESPACE_MAP "4:http://opcfoundation.org/UA/PlasticsRubber/IMM2MES/"
        FILE_NS "${NODESET_DIR_X}/Opc.Ua.PlasticsRubber.IMM2MES.NodeSet2.xml"
        DEPENDS "euromap83_x"
        INTERNAL
    )

    add_example(server_nodeset_euromap_x server_nodeset_euromap83_x.c
                ${UA_NODESET_DI_SOURCES}
                ${UA_NODESET_EUROMAP83_X_SOURCES}
                ${UA_NODESET_EUROMAP77_X_SOURCES}
                ${UA_TYPES_DI_SOURCES}
                ${UA_TYPES_EUROMAP83_X_SOURCES}
                ${UA_TYPES_EUROMAP77_X_SOURCES}
                )
    add_dependencies(server_nodeset_euromap_x open62541-generator-ns-euromap77_x)


endif()