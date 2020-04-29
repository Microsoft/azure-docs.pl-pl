---
title: Topologie sieci dla migracji wystąpienia zarządzanego SQL
titleSuffix: Azure Database Migration Service
description: Zapoznaj się z konfiguracją źródłową i docelową dla Azure SQL Database migracji wystąpień zarządzanych przy użyciu Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 48485b7ba0f846afa737454b092a6c1ee986b737
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78254955"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Topologie sieci dla migracji wystąpienia zarządzanego usługi Azure SQL DB przy użyciu Azure Database Migration Service

W tym artykule omówiono różne topologie sieci, które Azure Database Migration Service mogą współpracować z programem w celu zapewnienia kompleksowego środowiska migracji z lokalnych serwerów SQL do Azure SQL Database wystąpienia zarządzanego.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Database zarządzane wystąpienie skonfigurowane dla obciążeń hybrydowych 

Użyj tej topologii, jeśli Azure SQL Database wystąpienie zarządzane jest połączone z siecią lokalną. Takie podejście zapewnia najbardziej uproszczony Routing sieciowy i oferuje maksymalną przepływność danych podczas migracji.

![Topologia sieci dla obciążeń hybrydowych](media/resource-network-topologies/hybrid-workloads.png)

**Wymagania**

- W tym scenariuszu Azure SQL Database wystąpienie zarządzane i wystąpienie Azure Database Migration Service są tworzone w tym samym Microsoft Azure Virtual Network, ale używają różnych podsieci.  
- Sieć wirtualna używana w tym scenariuszu jest również połączona z siecią lokalną za pomocą usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL Database wystąpienie zarządzane izolowane od sieci lokalnej

Tej topologii sieci należy użyć, jeśli środowisko wymaga co najmniej jednego z następujących scenariuszy:

- Wystąpienie zarządzane Azure SQL Database jest odizolowane od łączności lokalnej, ale wystąpienie Azure Database Migration Service jest połączone z siecią lokalną.
- Jeśli są stosowane zasady kontroli dostępu opartej Access Control na rolach (RBAC) i musisz ograniczyć użytkownikom dostęp do tej samej subskrypcji, która obsługuje Azure SQL Database wystąpienie zarządzane.
- Sieci wirtualne używane dla Azure SQL Database wystąpienia zarządzanego i Azure Database Migration Service znajdują się w różnych subskrypcjach.

![Topologia sieci dla wystąpienia zarządzanego izolowanego od sieci lokalnej](media/resource-network-topologies/mi-isolated-workload.png)

**Wymagania**

- Sieć wirtualna, której Azure Database Migration Service używa w tym scenariuszu, musi być również podłączona do sieci lokalnej przy użyciu (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)).
- Skonfiguruj [komunikację równorzędną sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) między siecią wirtualną używaną dla Azure SQL Database wystąpienia zarządzanego i Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migracje z chmury do chmury: udostępniona Sieć wirtualna

Użyj tej topologii, jeśli SQL Server źródłowa jest hostowana na maszynie wirtualnej platformy Azure i współużytkuje tę samą sieć wirtualną z wystąpieniem zarządzanym Azure SQL Database i Azure Database Migration Service.

![Topologia sieci na potrzeby migracji między chmurą i chmurą przy użyciu wspólnej sieci wirtualnej](media/resource-network-topologies/cloud-to-cloud.png)

**Wymagania**

- Brak dodatkowych wymagań.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migracje chmury do chmury: izolowana Sieć wirtualna

Tej topologii sieci należy użyć, jeśli środowisko wymaga co najmniej jednego z następujących scenariuszy:

- Zarządzane wystąpienie Azure SQL Database jest inicjowane w izolowanej sieci wirtualnej.
- Jeśli są stosowane zasady kontroli dostępu opartej Access Control na rolach (RBAC) i musisz ograniczyć użytkownikom dostęp do tej samej subskrypcji, która obsługuje Azure SQL Database wystąpienie zarządzane.
- Sieci wirtualne używane dla Azure SQL Database wystąpienia zarządzanego i Azure Database Migration Service znajdują się w różnych subskrypcjach.

![Topologia sieci dla migracji między chmurą i chmurą i izolowaną siecią wirtualną](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Wymagania**

- Skonfiguruj [komunikację równorzędną sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) między siecią wirtualną używaną dla Azure SQL Database wystąpienia zarządzanego i Azure Database Migration Service.

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

## <a name="see-also"></a>Zobacz także

- [Migrowanie SQL Server do Azure SQL Database wystąpienia zarządzanego](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Przegląd wymagań wstępnych dotyczących używania Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem Azure Database Migration Service, zobacz artykuł [co to jest Azure Database Migration Service?](dms-overview.md).
- Bieżące informacje o dostępności regionalnej Azure Database Migration Service można znaleźć na stronie [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) .
