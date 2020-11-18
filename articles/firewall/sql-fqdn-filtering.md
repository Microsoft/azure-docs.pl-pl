---
title: Konfigurowanie reguł aplikacji usługi Azure Firewall przy użyciu nazw FQDN SQL
description: W tym artykule dowiesz się, jak skonfigurować nazwy FQDN SQL w regułach aplikacji zapory platformy Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/18/2020
ms.author: victorh
ms.openlocfilehash: 7256f94b8e8376cf98a279d085a131a4ce84826f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658626"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Konfigurowanie reguł aplikacji usługi Azure Firewall przy użyciu nazw FQDN SQL

Teraz można skonfigurować reguły aplikacji zapory platformy Azure przy użyciu nazw FQDN programu SQL. Pozwala to ograniczyć dostęp z sieci wirtualnych tylko do określonych wystąpień programu SQL Server.

Za pomocą nazw FQDN usługi SQL można filtrować następujący ruch:

- Z usługi sieci wirtualnych do Azure SQL Database lub analizy Synapse Azure. Na przykład: Zezwalaj na dostęp tylko do *SQL-server1.Database.Windows.NET*.
- Z usług lokalnych do usługi Azure SQL Managed Instances lub SQL IaaS uruchomionych w sieci wirtualnych.
- Od satelity do współdziałania z wystąpieniami zarządzanymi usługi Azure SQL lub SQL IaaS uruchomionymi w sieci wirtualnych.

Filtrowanie nazwy FQDN SQL jest obsługiwane tylko w [trybie proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) (port 1433). Jeśli używasz SQL w domyślnym trybie przekierowywania, możesz filtrować dostęp przy użyciu znacznika usługi SQL jako części [reguł sieci](features.md#network-traffic-filtering-rules).
Jeśli dla ruchu SQL IaaS używasz portów innych niż domyślne, możesz skonfigurować te porty w regułach aplikacji zapory.

## <a name="configure-using-azure-cli"></a>Konfigurowanie przy użyciu interfejsu wiersza polecenia platformy Azure

1. Wdróż [zaporę platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](deploy-cli.md).
2. W przypadku filtrowania ruchu do Azure SQL Database, usługi Azure Synapse Analytics lub wystąpienia zarządzanego SQL upewnij się, że tryb łączności SQL jest ustawiony na **serwer proxy**. Aby dowiedzieć się, jak przełączyć tryb łączności SQL, zobacz [Ustawienia łączności usługi Azure SQL](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).

   > [!NOTE]
   > Tryb *serwera proxy* SQL może powodować większe opóźnienia w porównaniu z *przekierowaniem*. Jeśli chcesz kontynuować korzystanie z trybu przekierowania, który jest domyślnym ustawieniem dla klientów łączących się na platformie Azure, możesz filtrować dostęp przy użyciu [znacznika usługi](service-tags.md) SQL w [regułach sieci](tutorial-firewall-deploy-portal.md#configure-a-network-rule)zapory.

3. Skonfiguruj regułę aplikacji z nazwą FQDN programu SQL, aby zezwolić na dostęp do programu SQL Server:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Konfigurowanie przy użyciu witryny Azure Portal
1. Wdróż [zaporę platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](deploy-cli.md).
2. W przypadku filtrowania ruchu do Azure SQL Database, usługi Azure Synapse Analytics lub wystąpienia zarządzanego SQL upewnij się, że tryb łączności SQL jest ustawiony na **serwer proxy**. Aby dowiedzieć się, jak przełączyć tryb łączności SQL, zobacz [Ustawienia łączności usługi Azure SQL](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).  

   > [!NOTE]
   > Tryb *serwera proxy* SQL może powodować większe opóźnienia w porównaniu z *przekierowaniem*. Jeśli chcesz kontynuować korzystanie z trybu przekierowania, który jest domyślnym ustawieniem dla klientów łączących się na platformie Azure, możesz filtrować dostęp przy użyciu [znacznika usługi](service-tags.md) SQL w [regułach sieci](tutorial-firewall-deploy-portal.md#configure-a-network-rule)zapory.
3. Dodaj regułę aplikacji z odpowiednim Protokołem, portem i nazwą FQDN programu SQL Server, a następnie wybierz pozycję **Zapisz**.
   ![Reguła aplikacji z nazwą FQDN programu SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Uzyskaj dostęp do programu SQL z maszyny wirtualnej w sieci wirtualnej, która filtruje ruch przez zaporę. 
5. Sprawdź, czy [Dzienniki zapory platformy Azure](log-analytics-samples.md) pokazują, że ruch jest dozwolony.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze SQL proxy i trybach przekierowywania, zobacz [Azure SQL Database architektura łączności](../azure-sql/database/connectivity-architecture.md).