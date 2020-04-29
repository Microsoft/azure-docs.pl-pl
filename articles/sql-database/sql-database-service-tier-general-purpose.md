---
title: Warstwa usługi ogólnego przeznaczenia
description: Dowiedz się więcej na temat Azure SQL Database warstwie ogólnego przeznaczenia
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: 7c57755ae63f8af5a2a4faa4764bc6a9597e8c2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79255888"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Warstwa usługi ogólnego przeznaczenia — Azure SQL Database

> [!NOTE]
> Warstwa usług ogólnego przeznaczenia w modelu zakupu opartego na rdzeń wirtualny jest nazywana warstwą usługi standardowej w modelu zakupu opartym na jednostkach DTU. Aby zapoznać się z porównaniem modelu zakupu opartego na rdzeń wirtualny z modelem zakupów opartym na jednostkach DTU, zobacz [Azure SQL Database kupowanie modeli i zasobów](sql-database-purchase-models.md).

Azure SQL Database jest oparta na architekturze SQL Server Database Engine przystosowanej do środowiska chmury w celu zapewnienia dostępności 99,99% nawet w przypadku awarii infrastruktury. Istnieją trzy warstwy usług, które są używane w Azure SQL Database, z których każdy ma różne modele architektury. Następujące warstwy usług:

- Zastosowania ogólne
- Krytyczne dla działania firmy
- Hiperskala

Model architektoniczny dla warstwy usług ogólnego przeznaczenia jest oparty na rozdzieleniu zasobów obliczeniowych i magazynu. Ten model architektury polega na wysokiej dostępności i niezawodności magazynu obiektów blob platformy Azure, który w sposób przezroczysty replikuje pliki bazy danych i nie gwarantuje utraty danych, jeśli wystąpi awaria infrastruktury.

Poniższy rysunek przedstawia cztery węzły w standardowym modelu architektury z oddzielnymi warstwami obliczeniowymi i magazynem.

![Rozdzielenie zasobów obliczeniowych i magazynu](media/sql-database-managed-instance/general-purpose-service-tier.png)

W modelu architektury dla warstwy usług ogólnego przeznaczenia istnieją dwie warstwy:

- Warstwa obliczeń bezstanowych, która uruchamia `sqlservr.exe` proces i zawiera tylko dane przejściowe i buforowane (na przykład pamięć podręczna planu, pula buforów, pula magazynu kolumn). Ten bezstanowy węzeł SQL Server jest obsługiwany przez platformę Azure Service Fabric, która inicjuje proces, kontroluje kondycję węzła i przeprowadza przejście w tryb failover w razie potrzeby w inne miejsce.
- Warstwa danych stanowych z plikami bazy danych (. mdf/. ldf), które są przechowywane w usłudze Azure Blob Storage. Usługa Azure Blob Storage gwarantuje, że nie utracie danych żadnego rekordu, który jest umieszczany w pliku bazy danych. Usługa Azure Storage ma wbudowaną dostępność i nadmiarowość danych, która zapewnia, że każdy rekord w pliku dziennika lub stronie w pliku danych zostanie zachowany nawet w przypadku awarii procesu SQL Server.

Po uaktualnieniu aparatu bazy danych lub systemu operacyjnego część podstawowej infrastruktury nie powiedzie się lub w przypadku wykrycia błędu krytycznego w procesie SQL Server usługa Azure Service Fabric przeniesie proces bezstanowy SQL Server do innego bezstanowego węzła obliczeniowego. Istnieje zestaw węzłów zapasowych, które oczekują na uruchomienie nowej usługi obliczeniowej, jeśli tryb failover węzła podstawowego wystąpi w celu zminimalizowania czasu pracy w trybie failover. Nie dotyczy to danych w warstwie usługi Azure Storage, a pliki danych/dziennika są dołączone do nowo zainicjowanego procesu SQL Server. Ten proces gwarantuje dostępność na 99,99%, ale może mieć wpływ na wydajność dużego obciążenia, które jest uruchomione z powodu czasu przejścia, a fakt, że nowy węzeł SQL Server rozpoczyna się od zimnej pamięci podręcznej.

## <a name="when-to-choose-this-service-tier"></a>Kiedy należy wybrać tę warstwę usług

Ogólnego przeznaczenia warstwa usługi jest domyślną warstwą usług w Azure SQL Database, która jest przeznaczona dla większości obciążeń zwykłych. Jeśli potrzebujesz w pełni zarządzanego aparatu bazy danych z umową SLA na 99,99% z opóźnieniem magazynu od 5 do 10 ms, które są zgodne z usługą Azure SQL IaaS w większości przypadków Ogólnego przeznaczenia, jest to opcja.

## <a name="next-steps"></a>Następne kroki

- Znajdź charakterystyki zasobów (liczbę rdzeni, operacji we/wy, pamięci) Ogólnego przeznaczenia/warstwy standardowej w [wystąpieniu zarządzanym](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), pojedynczą bazę danych w [modelu rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) lub [modelu DTU](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)lub elastyczną pulę w [modelu rdzeń wirtualny](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose---provisioned-compute---gen4) i [modelu DTU](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Dowiedz się więcej na temat warstw [krytyczne dla działania firmy](sql-database-service-tier-business-critical.md) i [skalowania](sql-database-service-tier-hyperscale.md) .
- Dowiedz się więcej na temat [Service Fabric](../service-fabric/service-fabric-overview.md).
- Aby uzyskać więcej opcji dotyczących wysokiej dostępności i odzyskiwania po awarii, zobacz [ciągłość](sql-database-business-continuity.md)działania.
