---
title: Widoki systemowe
description: Łącza do dokumentacji widoków systemowych obsługiwanych w języku Synapse SQL.
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 96a2cce5d0e8abfd1f22b5fc64633eebc41aca39
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431568"
---
# <a name="system-views-supported-in-synapse-sql"></a>Widoki systemowe obsługiwane w języku Synapse SQL
Łącza do dokumentacji instrukcji T-SQL obsługiwanych w języku Synapse SQL.

> [!NOTE]
> Synapse SQL na żądanie obsługuje tylko widoki wykazu programu SQL Server.  

## <a name="sql-pool-catalog-views"></a>Widoki wykazu puli SQL

* [sys.pdw_column_distribution_properties](/sql/relational-databases/system-catalog-views/sys-pdw-column-distribution-properties-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_distributions](/sql/relational-databases/system-catalog-views/sys-pdw-distributions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_index_mappings](/sql/relational-databases/system-catalog-views/sys-pdw-index-mappings-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_loader_backup_run_details](/sql/relational-databases/system-catalog-views/sys-pdw-loader-backup-run-details-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_loader_backup_runs](/sql/relational-databases/system-catalog-views/sys-pdw-loader-backup-runs-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_materialized_view_column_distribution_properties](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-column-distribution-properties-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (Wersja zapoznawcza)
* [sys.pdw_materialized_view_distribution_properties](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-distribution-properties-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (wersja zapoznawcza)
* [sys.pdw_materialized_view_mappings](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-mappings-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (Wersja zapoznawcza)
* [sys.pdw_nodes_column_store_dictionaries](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-column-store-dictionaries-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_nodes_column_store_row_groups](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-column-store-row-groups-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_nodes_column_store_segments](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-column-store-segments-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_nodes_columns](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_nodes_indexes](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-indexes-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_nodes_partitions](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-partitions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_nodes_pdw_physical_databases](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-pdw-physical-databases-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_nodes_tables](/sql/relational-databases/system-catalog-views/sys-pdw-nodes-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_table_distribution_properties](/sql/relational-databases/system-catalog-views/sys-pdw-table-distribution-properties-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.pdw_table_mappings](/sql/relational-databases/system-catalog-views/sys-pdw-table-mappings-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.resource_governor_workload_groups](/sql/relational-databases/system-catalog-views/sys-resource-governor-workload-groups-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (wersja zapoznawcza)
* [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (Wersja zapoznawcza)

## <a name="sql-pool-dynamic-management-views-dmvs"></a>Widoki dynamicznego zarządzania pulą SQL (DMV)

* [sys.dm_pdw_dms_cores](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-cores-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_dms_external_work](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-external-work-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_errors](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-errors-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-connections-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_hadoop_operations](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-hadoop-operations-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_lock_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-lock-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_os_threads](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-os-threads-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_resource_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-resource-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_sys_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sys-info-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-wait-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="sql-server-dmvs-applicable-to-sql-pool"></a>Moduły DMV programu SQL Server mające zastosowanie do puli SQL

Następujące dmvs mają zastosowanie do puli SQL, ale muszą być wykonywane przez połączenie z **główną** bazą danych.

* [sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.fn_helpcollations()](/sql/relational-databases/system-functions/sys-fn-helpcollations-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="sql-server-catalog-views"></a>Widoki wykazu programu SQL Server

* [sys.all_columns](/sql/relational-databases/system-catalog-views/sys-all-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.all_objects](/sql/relational-databases/system-catalog-views/sys-all-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.all_parameters](/sql/relational-databases/system-catalog-views/sys-all-parameters-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.all_sql_modules](/sql/relational-databases/system-catalog-views/sys-all-sql-modules-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.all_views](/sql/relational-databases/system-catalog-views/sys-all-views-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.assemblies](/sql/relational-databases/system-catalog-views/sys-assemblies-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.assembly_modules](/sql/relational-databases/system-catalog-views/sys-assembly-modules-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.assembly_types](/sql/relational-databases/system-catalog-views/sys-assembly-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.certificates](/sql/relational-databases/system-catalog-views/sys-certificates-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.check_constraints](/sql/relational-databases/system-catalog-views/sys-check-constraints-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.computed_columns](/sql/relational-databases/system-catalog-views/sys-computed-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.credentials](/sql/relational-databases/system-catalog-views/sys-credentials-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.data_spaces](/sql/relational-databases/system-catalog-views/sys-data-spaces-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.database_credentials](/sql/relational-databases/system-catalog-views/sys-database-credentials-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.database_permissions](/sql/relational-databases/system-catalog-views/sys-database-permissions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.database_query_store_options](/sql/relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.database_role_members](/sql/relational-databases/system-catalog-views/sys-database-role-members-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.default_constraints](/sql/relational-databases/system-catalog-views/sys-default-constraints-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.external_data_sources](/sql/relational-databases/system-catalog-views/sys-external-data-sources-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.external_file_formats](/sql/relational-databases/system-catalog-views/sys-external-file-formats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.external_tables](/sql/relational-databases/system-catalog-views/sys-external-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [grupy plików sys.file](/sql/relational-databases/system-catalog-views/sys-filegroups-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.foreign_key_columns](/sql/relational-databases/system-catalog-views/sys-foreign-key-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.foreign_keys](/sql/relational-databases/system-catalog-views/sys-foreign-keys-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.index_columns](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.indexes](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.key_constraints](/sql/relational-databases/system-catalog-views/sys-key-constraints-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.numbered_procedures](/sql/relational-databases/system-catalog-views/sys-numbered-procedures-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.parameters](/sql/relational-databases/system-catalog-views/sys-parameters-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.partition_functions](/sql/relational-databases/system-catalog-views/sys-partition-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.partition_parameters](/sql/relational-databases/system-catalog-views/sys-partition-parameters-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.partition_range_values](/sql/relational-databases/system-catalog-views/sys-partition-range-values-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.partition_schemes](/sql/relational-databases/system-catalog-views/sys-partition-schemes-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.partitions](/sql/relational-databases/system-catalog-views/sys-partitions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.procedures](/sql/relational-databases/system-catalog-views/sys-procedures-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.query_context_settings](/sql/relational-databases/system-catalog-views/sys-query-context-settings-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.query_store_plan](/sql/relational-databases/system-catalog-views/sys-query-store-plan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.query_store_query_text](/sql/relational-databases/system-catalog-views/sys-query-store-query-text-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.query_store_runtime_stats_interval](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-interval-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.schemas](/sql/relational-databases/system-catalog-views/schemas-catalog-views-sys-schemas?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.securable_classes](/sql/relational-databases/system-catalog-views/sys-securable-classes-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.sql_expression_dependencies](/sql/relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.sql_modules](/sql/relational-databases/system-catalog-views/sys-sql-modules-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.symmetric_keys](/sql/relational-databases/system-catalog-views/sys-symmetric-keys-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.synonimy](/sql/relational-databases/system-catalog-views/sys-synonyms-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [zestawy sys.syscharsets](/sql/relational-databases/system-compatibility-views/sys-syscharsets-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [syscolumns sys.syscolumns](/sql/relational-databases/system-compatibility-views/sys-syscolumns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.sysdanabases](/sql/relational-databases/system-compatibility-views/sys-sysdatabases-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.syslanguages](/sql/relational-databases/system-compatibility-views/sys-syslanguages-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.sysobjects](/sql/relational-databases/system-compatibility-views/sys-sysobjects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.sysreferences](/sql/relational-databases/system-compatibility-views/sys-sysreferences-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.system_columns](/sql/relational-databases/system-catalog-views/sys-system-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.system_objects](/sql/relational-databases/system-catalog-views/sys-system-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.system_parameters](/sql/relational-databases/system-catalog-views/sys-system-parameters-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.system_sql_modules](/sql/relational-databases/system-catalog-views/sys-system-sql-modules-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.system_views](/sql/relational-databases/system-catalog-views/sys-system-views-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.systypes](/sql/relational-databases/system-compatibility-views/sys-systypes-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.sysusers](/sql/relational-databases/system-compatibility-views/sys-sysusers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.types](/sql/relational-databases/system-catalog-views/sys-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Sys.views](/sql/relational-databases/system-catalog-views/sys-views-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="sql-server-dmvs-available-in-sql-pool"></a>Dmv programu SQL Server dostępne w puli SQL

Pula SQL udostępnia wiele widoków zarządzania dynamicznego programu SQL Server (DMV). Te widoki, gdy kwerendy w puli SQL, zgłaszają stan baz danych SQL uruchomionych w dystrybucjach.

Pula SQL i system platformy analitycznej (PARALLEL Data Warehouse) (PDW) używają tych samych widoków systemowych. Każdy DMV ma kolumnę o nazwie pdw_node_id, który jest identyfikatorem węzła Compute.

> [!NOTE]
> Aby użyć tych widoków, wstaw nazwę "pdw_nodes_", jak pokazano w poniższej tabeli:

| Nazwa DMV w puli SQL | Sql Server Transact-SQL artykuł|
|:--- |:--- |
| sys.dm_pdw_nodes_db_column_store_row_group_physical_stats | [sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)|
| sys.dm_pdw_nodes_db_column_store_row_group_operational_stats | [sys.dm_db_column_store_row_group_operational_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-operational-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)|
| sys.dm_pdw_nodes_db_file_space_usage |[Sys.dm_db_file_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-file-space-usage-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_db_index_usage_stats |[sys.dm_db_index_usage_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-index-usage-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_db_partition_stats |[sys.dm_db_partition_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_db_session_space_usage |[sys.dm_db_session_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-session-space-usage-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_db_task_space_usage |[sys.dm_db_task_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-task-space-usage-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_exec_background_job_queue |[sys.dm_exec_background_job_queue](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-background-job-queue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_exec_background_job_queue_stats |[sys.dm_exec_background_job_queue_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-background-job-queue-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_exec_cached_plans |[Sys.dm_exec_cached_plans](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-cached-plans-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_exec_connections |[sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_exec_procedure_stats |[Sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_exec_query_memory_grants |[Sys.dm_exec_query_memory_grants](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-memory-grants-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_exec_query_optimizer_info |[sys.dm_exec_query_optimizer_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-optimizer-info-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_exec_query_resource_semaphores |[Sys.dm_exec_query_resource_semaphores](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-resource-semaphores-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_exec_query_stats |[Sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_exec_requests |[Sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_exec_sessions |[sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_io_pending_io_requests |[sys.dm_io_pending_io_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-pending-io-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_io_virtual_file_stats |[Sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_buffer_descriptors |[sys.dm_os_buffer_descriptors](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-buffer-descriptors-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_child_instances |[sys.dm_os_child_instances](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-child-instances-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_cluster_nodes |[sys.dm_os_cluster_nodes](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-cluster-nodes-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_dispatcher_pools |[sys.dm_os_dispatcher_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-dispatcher-pools-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_dispatchers |Dokumentacja transact-SQL jest niedostępna. |
| sys.dm_pdw_nodes_os_hosts |[sys.dm_os_hosts](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-hosts-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_latch_stats |[Sys.dm_os_latch_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-latch-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_memory_brokers |[sys.dm_os_memory_brokers](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-brokers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_memory_cache_clock_hands |[sys.dm_os_memory_cache_clock_hands](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-cache-clock-hands-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_memory_cache_counters |[sys.dm_os_memory_cache_counters](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-cache-counters-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_memory_cache_entries |[sys.dm_os_memory_cache_entries](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-cache-entries-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_memory_cache_hash_tables |[sys.dm_os_memory_cache_hash_tables](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-cache-hash-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_memory_clerks |[Sys.dm_os_memory_clerks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_memory_node_access_stats |Dokumentacja transact-SQL jest niedostępna. |
| sys.dm_pdw_nodes_os_memory_nodes |[sys.dm_os_memory_nodes](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-nodes-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_memory_objects |[Sys.dm_os_memory_objects](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_memory_pools |[sys.dm_os_memory_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-pools-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_nodes |[sys.dm_os_nodes](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-nodes-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_performance_counters |[sys.dm_os_performance_counters](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_process_memory |[sys.dm_os_process_memory](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-process-memory-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_schedulers |[sys.dm_os_schedulers](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-schedulers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_spinlock_stats |Dokumentacja transact-SQL jest niedostępna. |
| sys.dm_pdw_nodes_os_sys_info |[Sys.dm_os_sys_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-sys-info-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_sys_memory |[sys.dm_os_memory_nodes](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-nodes-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_tasks |[Sys.dm_os_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_threads |[sys.dm_os_threads](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-threads-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_virtual_address_dump |[sys.dm_os_virtual_address_dump](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-virtual-address-dump-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_wait_stats |[Sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_waiting_tasks |[Sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_os_workers |[sys.dm_os_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_tran_active_snapshot_database_transactions |[Sys.dm_tran_active_snapshot_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-snapshot-database-transactions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_tran_active_transactions |[sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_tran_commit_table |[sys.dm_tran_commit_table](/sql/relational-databases/system-dynamic-management-views/change-tracking-sys-dm-tran-commit-table?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_tran_current_snapshot |[sys.dm_tran_current_snapshot](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-current-snapshot-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_tran_current_transaction |[sys.dm_tran_current_transaction](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-current-transaction-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_tran_database_transactions |[sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_tran_locks |[Sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_tran_session_transactions |[sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| sys.dm_pdw_nodes_tran_top_version_generators |[sys.dm_tran_top_version_generators](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-top-version-generators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |

## <a name="sql-server-2016-polybase-dmvs-available-in-sql-pool"></a>Moduły DMV polybase programu SQL Server 2016 dostępne w puli SQL

Następujące dmvs mają zastosowanie do puli SQL, ale muszą być wykonywane przez połączenie z **główną** bazą danych.

* [sys.dm_exec_compute_node_errors](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-compute-node-errors-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_exec_compute_node_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-compute-node-status-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_exec_compute_nodes](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-compute-nodes-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_exec_distributed_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-distributed-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_exec_distributed_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-distributed-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_exec_distributed_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-distributed-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_exec_dms_services](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-dms-services-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_exec_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_exec_external_operations](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-external-operations-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_exec_external_work](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-external-work-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="sql-server-information_schema-views"></a>Widoki INFORMATION_SCHEMA programu SQL Server

* [Check_constraints](/sql/relational-databases/system-information-schema-views/check-constraints-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Kolumny](/sql/relational-databases/system-information-schema-views/columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Parametry](/sql/relational-databases/system-information-schema-views/parameters-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Procedur](/sql/relational-databases/system-information-schema-views/routines-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [SCHEMATA](/sql/relational-databases/system-information-schema-views/schemata-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Tabel](/sql/relational-databases/system-information-schema-views/tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [VIEW_COLUMN_USAGE](/sql/relational-databases/system-information-schema-views/view-column-usage-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [VIEW_TABLE_USAGE](/sql/relational-databases/system-information-schema-views/view-table-usage-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
* [Widoki](/sql/relational-databases/system-information-schema-views/views-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Instrukcje T-SQL w synapse SQL](/sql-data-warehouse/sql-data-warehouse-reference-tsql-language-elements)i [elementy języka T-SQL w synapse SQL](/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements).

 