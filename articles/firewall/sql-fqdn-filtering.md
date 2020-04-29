---
title: Konfigurowanie reguł aplikacji zapory platformy Azure przy użyciu nazw FQDN programu SQL
description: W tym artykule dowiesz się, jak skonfigurować nazwy FQDN SQL w regułach aplikacji zapory platformy Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79501502"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Konfigurowanie reguł aplikacji zapory platformy Azure przy użyciu nazw FQDN programu SQL

> [!IMPORTANT]
> Reguły aplikacji zapory platformy Azure z nazwami FQDN programu SQL są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Teraz można skonfigurować reguły aplikacji zapory platformy Azure przy użyciu nazw FQDN programu SQL. Pozwala to ograniczyć dostęp z sieci wirtualnych tylko do określonych wystąpień programu SQL Server.

Za pomocą nazw FQDN programu SQL można filtrować ruch:

- Z sieci wirtualnych do Azure SQL Database lub Azure SQL Data Warehouse. Na przykład: Zezwalaj na dostęp tylko do *SQL-server1.Database.Windows.NET*.
- Z usług lokalnych do usługi Azure SQL Managed Instances lub SQL IaaS uruchomionych w sieci wirtualnych.
- Od satelity do współdziałania z wystąpieniami zarządzanymi usługi Azure SQL lub SQL IaaS uruchomionymi w sieci wirtualnych.

W publicznej wersji zapoznawczej filtrowanie FQDN programu SQL jest obsługiwane tylko w [trybie proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (port 1433). Jeśli używasz SQL w domyślnym trybie przekierowywania, możesz filtrować dostęp przy użyciu znacznika usługi SQL jako części [reguł sieci](overview.md#network-traffic-filtering-rules).
Jeśli używasz portów innych niż domyślne dla ruchu SQL IaaS, możesz skonfigurować te porty w regułach aplikacji zapory.

Reguły aplikacji z nazwami FQDN programu SQL są obecnie dostępne we wszystkich regionach za pośrednictwem Azure Portal, interfejsu wiersza polecenia platformy Azure, REST i szablonów.

## <a name="configure-using-azure-cli"></a>Konfigurowanie przy użyciu interfejsu wiersza polecenia platformy Azure

1. Wdróż [zaporę platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](deploy-cli.md).
2. W przypadku filtrowania ruchu do Azure SQL Database, SQL Data Warehouse lub wystąpienia zarządzanego SQL upewnij się, że tryb łączności SQL jest ustawiony na **serwer proxy**. Aby dowiedzieć się, jak przełączyć tryb łączności SQL, zobacz [Ustawienia łączności usługi Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

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
2. W przypadku filtrowania ruchu do Azure SQL Database, SQL Data Warehouse lub wystąpienia zarządzanego SQL upewnij się, że tryb łączności SQL jest ustawiony na **serwer proxy**. Aby dowiedzieć się, jak przełączyć tryb łączności SQL, zobacz [Ustawienia łączności usługi Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > Tryb *serwera proxy* SQL może powodować większe opóźnienia w porównaniu z *przekierowaniem*. Jeśli chcesz kontynuować korzystanie z trybu przekierowania, który jest domyślnym ustawieniem dla klientów łączących się na platformie Azure, możesz filtrować dostęp przy użyciu [znacznika usługi](service-tags.md) SQL w [regułach sieci](tutorial-firewall-deploy-portal.md#configure-a-network-rule)zapory.
3. Dodaj regułę aplikacji z odpowiednim Protokołem, portem i nazwą FQDN programu SQL Server, a następnie wybierz pozycję **Zapisz**.
   ![Reguła aplikacji z nazwą FQDN programu SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Uzyskaj dostęp do programu SQL z maszyny wirtualnej w sieci wirtualnej, która filtruje ruch przez zaporę. 
5. Sprawdź, czy [Dzienniki zapory platformy Azure](log-analytics-samples.md) pokazują, że ruch jest dozwolony.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze SQL proxy i trybach przekierowywania, zobacz [Architektura łączności usługi Azure SQL Database](../sql-database/sql-database-connectivity-architecture.md).