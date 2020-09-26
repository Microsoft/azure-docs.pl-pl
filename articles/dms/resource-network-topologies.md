---
title: Topologie sieci dla migracji wystąpienia zarządzanego SQL
titleSuffix: Azure Database Migration Service
description: Zapoznaj się z konfiguracją źródłową i docelową dla migracji wystąpienia zarządzanego usługi Azure SQL przy użyciu Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: 5839de1fde8e4a4d5e661d232ae91099a9483bcb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91291575"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Topologie sieci dla migracji wystąpienia zarządzanego usługi Azure SQL przy użyciu Azure Database Migration Service

W tym artykule omówiono różne topologie sieci, które Azure Database Migration Service mogą współpracować z programem w celu zapewnienia kompleksowego środowiska migracji z serwerów SQL do wystąpienia zarządzanego Azure SQL.

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>Wystąpienie zarządzane Azure SQL skonfigurowane dla obciążeń hybrydowych 

Użyj tej topologii, jeśli wystąpienie zarządzane Azure SQL jest połączone z siecią lokalną. Takie podejście zapewnia najbardziej uproszczony Routing sieciowy i oferuje maksymalną przepływność danych podczas migracji.

![Topologia sieci dla obciążeń hybrydowych](media/resource-network-topologies/hybrid-workloads.png)

**Wymagania**

- W tym scenariuszu wystąpienie zarządzane SQL i wystąpienie Azure Database Migration Service są tworzone w tym samym Microsoft Azure Virtual Network, ale używają różnych podsieci.  
- Sieć wirtualna używana w tym scenariuszu jest również połączona z siecią lokalną za pomocą usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>Wystąpienie zarządzane SQL odizolowane od sieci lokalnej

Tej topologii sieci należy użyć, jeśli środowisko wymaga co najmniej jednego z następujących scenariuszy:

- Wystąpienie zarządzane SQL jest odizolowane od łączności lokalnej, ale wystąpienie Azure Database Migration Service jest połączone z siecią lokalną.
- Jeśli są stosowane zasady kontroli dostępu opartej na rolach (Azure RBAC) i trzeba ograniczyć użytkownikom dostęp do tej samej subskrypcji, która obsługuje wystąpienie zarządzane SQL.
- Sieci wirtualne używane dla wystąpienia zarządzanego SQL i Azure Database Migration Service znajdują się w różnych subskrypcjach.

![Topologia sieci dla wystąpienia zarządzanego izolowanego od sieci lokalnej](media/resource-network-topologies/mi-isolated-workload.png)

**Wymagania**

- Sieć wirtualna, której Azure Database Migration Service używa w tym scenariuszu, musi być również podłączona do sieci lokalnej przy użyciu ( https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)).
- Skonfiguruj [komunikację równorzędną sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) między siecią wirtualną używaną dla wystąpienia zarządzanego SQL i Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migracje z chmury do chmury: udostępniona Sieć wirtualna

Użyj tej topologii, jeśli SQL Server źródłowa jest hostowana na maszynie wirtualnej platformy Azure i współużytkuje tę samą sieć wirtualną z wystąpieniem zarządzanym SQL i Azure Database Migration Service.

![Topologia sieci na potrzeby migracji między chmurą i chmurą przy użyciu wspólnej sieci wirtualnej](media/resource-network-topologies/cloud-to-cloud.png)

**Wymagania**

- Brak dodatkowych wymagań.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migracje chmury do chmury: izolowana Sieć wirtualna

Tej topologii sieci należy użyć, jeśli środowisko wymaga co najmniej jednego z następujących scenariuszy:

- Wystąpienie zarządzane SQL jest inicjowane w izolowanej sieci wirtualnej.
- Jeśli są stosowane zasady kontroli dostępu opartej na rolach (Azure RBAC) i trzeba ograniczyć użytkownikom dostęp do tej samej subskrypcji, która obsługuje wystąpienie zarządzane SQL.
- Sieci wirtualne używane do wystąpienia zarządzanego SQL i Azure Database Migration Service znajdują się w różnych subskrypcjach.

![Topologia sieci dla migracji między chmurą i chmurą i izolowaną siecią wirtualną](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Wymagania**

- Skonfiguruj [komunikację równorzędną sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) między siecią wirtualną używaną dla wystąpienia zarządzanego SQL i Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Reguły zabezpieczeń dla ruchu przychodzącego

| **NAZWA**   | **PRZEWOŻĄC** | **PROTOKOL** | **ZEWNĘTRZ** | **PUNKTU** | **TRANSAKCJI** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Dowolne      | Dowolne          | PODSIEĆ DMS | Dowolne             | Zezwalaj      |

## <a name="outbound-security-rules"></a>Reguły zabezpieczeń dla ruchu wychodzącego

| **NAZWA**                  | **PRZEWOŻĄC**                                              | **PROTOKOL** | **ZEWNĘTRZ** | **PUNKTU**           | **TRANSAKCJI** | **Przyczyna reguły**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| zarządzanie                | 443, 9354                                              | TCP          | Dowolne        | Dowolne                       | Zezwalaj      | Komunikacja płaszczyzny zarządzania za pomocą Service Bus i usługi Azure Blob Storage. <br/>(Jeśli włączono komunikację równorzędną firmy Microsoft, ta reguła może nie być potrzebna).                                                             |
| Diagnostyka               | 12000                                                 | TCP          | Dowolne        | Dowolne                       | Zezwalaj      | DMS używa tej reguły do zbierania informacji diagnostycznych na potrzeby rozwiązywania problemów.                                                                                                                      |
| Serwer źródłowy SQL         | 1433 (lub port IP TCP, do którego nasłuchuje SQL Server) | TCP          | Dowolne        | Przestrzeń adresów lokalnych | Zezwalaj      | SQL Server łączności źródłowej z usługi DMS <br/>(Jeśli masz połączenie lokacja-lokacja, ta reguła może nie być potrzebna).                                                                                       |
| SQL Server nazwane wystąpienie | 1434                                                  | UDP          | Dowolne        | Przestrzeń adresów lokalnych | Zezwalaj      | SQL Server o nazwie połączenie źródłowe wystąpienia z usługi DMS <br/>(Jeśli masz połączenie lokacja-lokacja, ta reguła może nie być potrzebna).                                                                        |
| Udział SMB                 | 445                                                   | TCP          | Dowolne        | Przestrzeń adresów lokalnych | Zezwalaj      | Udział sieciowy SMB dla systemu DMS do przechowywania plików kopii zapasowej bazy danych na potrzeby migracji do Azure SQL Database MI i programu SQL Server na maszynie wirtualnej platformy Azure <br/>(Jeśli masz połączenie lokacja-lokacja, ta reguła może nie być potrzebna). |
| DMS_subnet                | Dowolne                                                   | Dowolne          | Dowolne        | DMS_Subnet                | Zezwalaj      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Zobacz też

- [Migrowanie SQL Server do wystąpienia zarządzanego SQL](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Przegląd wymagań wstępnych dotyczących używania Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem Azure Database Migration Service, zobacz artykuł [co to jest Azure Database Migration Service?](dms-overview.md).
- Bieżące informacje o dostępności regionalnej Azure Database Migration Service można znaleźć na stronie [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) .
