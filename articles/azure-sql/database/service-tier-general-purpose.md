---
title: Ogólnego przeznaczenia warstwy usług
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Dowiedz się więcej na temat Ogólnego przeznaczenia warstwy usług dla Azure SQL Database i wystąpienia zarządzanego Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: ee218253309995e721c97f4a7f7b4547b32f7c36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85986645"
---
# <a name="general-purpose-service-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Ogólnego przeznaczenia warstwy usług Azure SQL Database i wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Warstwa usługi Ogólnego przeznaczenia w modelu zakupu opartego na rdzeń wirtualny jest nazywana warstwą usługi standardowej w modelu zakupu opartym na jednostkach DTU. Porównanie modelu zakupu opartego na rdzeń wirtualny z modelem zakupów opartym na jednostkach DTU można znaleźć w temacie [kupowanie modeli i zasobów](purchasing-models.md).

Azure SQL Database i wystąpienie zarządzane SQL platformy Azure są oparte na architekturze SQL Server Database Engine przystosowanej do środowiska chmury w celu zapewnienia dostępności 99,99% nawet w przypadku awarii infrastruktury. 

Istnieją dwie warstwy usług używane przez Azure SQL Database i wystąpienie zarządzane SQL: 

- Ogólnego przeznaczenia
- Krytyczne dla działania firmy

Azure SQL Database ma także trzecią warstwę usług, która jest obecnie niedostępna dla wystąpienia zarządzanego usługi Azure SQL:

- Hiperskala

Model architektoniczny dla warstwy usług Ogólnego przeznaczenia jest oparty na rozdzieleniu zasobów obliczeniowych i magazynowych. Ten model architektury polega na wysokiej dostępności i niezawodności magazynu obiektów blob platformy Azure, który w sposób przezroczysty replikuje pliki bazy danych i nie gwarantuje utraty danych, jeśli wystąpi awaria infrastruktury.

Poniższy rysunek przedstawia cztery węzły w standardowym modelu architektury z oddzielnymi warstwami obliczeniowymi i magazynem.

![Rozdzielenie zasobów obliczeniowych i magazynu](./media/service-tier-general-purpose/general-purpose-service-tier.png)

W modelu architektury dla Ogólnego przeznaczenia warstwy usług istnieją dwie warstwy:

- Warstwa obliczeń bezstanowych, która uruchamia `sqlservr.exe` proces i zawiera tylko dane przejściowe i buforowane (na przykład pamięć podręczna planu, pula buforów, pula magazynu kolumn). Ten bezstanowy węzeł jest obsługiwany przez usługę Azure Service Fabric, która inicjuje proces, kontroluje kondycję węzła i przeprowadza przejście w tryb failover w razie potrzeby w inne miejsce.
- Warstwa danych stanowych z plikami bazy danych (. mdf/. ldf), które są przechowywane w usłudze Azure Blob Storage. Usługa Azure Blob Storage gwarantuje, że nie utracie danych żadnego rekordu, który jest umieszczany w pliku bazy danych. Usługa Azure Storage ma wbudowaną dostępność i nadmiarowość danych, która zapewnia, że każdy rekord w pliku dziennika lub stronie w pliku danych zostanie zachowany nawet w przypadku awarii procesu.

Za każdym razem, gdy aparat bazy danych lub system operacyjny jest uaktualniany, część podstawowej infrastruktury nie powiedzie się lub w przypadku wykrycia błędu krytycznego w `sqlservr.exe` procesie usługa Azure Service Fabric przeniesie proces bezstanowy do innego bezstanowego węzła obliczeniowego. Istnieje zestaw węzłów zapasowych, które oczekują na uruchomienie nowej usługi obliczeniowej, jeśli tryb failover węzła podstawowego wystąpi w celu zminimalizowania czasu pracy w trybie failover. Nie dotyczy to danych w warstwie usługi Azure Storage, a pliki danych/dziennika są dołączone do nowo zainicjowanego procesu. Ten proces gwarantuje dostępność na 99,99%, ale może mieć wpływ na wydajność dużych obciążeń, które są uruchomione z powodu czasu przejścia, a fakt, że nowy węzeł rozpoczyna się od zimnej pamięci podręcznej.

## <a name="when-to-choose-this-service-tier"></a>Kiedy należy wybrać tę warstwę usług

Warstwa usługi Ogólnego przeznaczenia to domyślna warstwa usługi w Azure SQL Database i wystąpienie zarządzane usługi Azure SQL, które jest przeznaczone dla większości obciążeń zwykłych. Jeśli potrzebujesz w pełni zarządzanego aparatu bazy danych z umową SLA na 99,99% z opóźnieniem magazynu wynoszącym od 5 do 10 ms, które pasują SQL Server na maszynie wirtualnej platformy Azure w większości przypadków, warstwa Ogólnego przeznaczenia jest opcją dla Ciebie.

## <a name="next-steps"></a>Następne kroki

- Znajdź charakterystykę zasobu (liczbę rdzeni, operacji we/wy, pamięci) Ogólnego przeznaczenia/warstwy standardowej w [wystąpieniu zarządzanym SQL](../managed-instance/resource-limits.md#service-tier-characteristics), pojedynczą bazę danych w [modelu rdzeń wirtualny](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) lub [modelu DTU](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)lub pulę elastyczną w [modelu rdzeń wirtualny](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4) i [modelu DTU](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits).
- Dowiedz się więcej na temat warstw [krytyczne dla działania firmy](service-tier-business-critical.md) i [skalowania](service-tier-hyperscale.md) .
- Dowiedz się więcej na temat [Service Fabric](../../service-fabric/service-fabric-overview.md).
- Aby uzyskać więcej opcji dotyczących wysokiej dostępności i odzyskiwania po awarii, zobacz [ciągłość](business-continuity-high-availability-disaster-recover-hadr-overview.md)działania.
