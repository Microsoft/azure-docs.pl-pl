---
title: Słownik
titleSuffix: Azure SQL
description: Słownik terminów związanych z pracą z Azure SQL Database, wystąpieniem zarządzanym usługi Azure SQL i SQL na maszynie wirtualnej platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/22/2020
ms.openlocfilehash: 18ff2c9690de1708cd8382d83a0c01662a8e6fb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619785"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL Database słownik terminów
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

## <a name="azure-sql-database"></a>Azure SQL Database

|Kontekst|Okres|Więcej informacji|
|:---|:---|:---|
|Usługa platformy Azure|Azure SQL Database lub SQL Database|[Azure SQL Database](database/sql-database-paas-overview.md)|
|Model zakupów|Model zakupu oparty na jednostkach DTU|[Model zakupu oparty na jednostkach DTU](database/service-tiers-dtu.md)|
||Model zakupów oparty na rdzeniach wirtualnych|[Model zakupów oparty na rdzeniach wirtualnych](database/service-tiers-vcore.md)|
|Opcja wdrażania |Pojedyncza baza danych|[Pojedyncze bazy danych](database/single-database-overview.md)|
||Pula elastyczna|[Pula elastyczna](database/elastic-pool-overview.md)|
|Warstwa usługi|Basic, standard, Premium, Ogólnego przeznaczenia, skalowanie Krytyczne dla działania firmy|Aby uzyskać warstwy usług w modelu rdzeń wirtualny, zobacz [SQL Database warstwy usług](database/service-tiers-vcore.md#service-tiers). Aby uzyskać warstwy usług w modelu DTU, zobacz [model DTU](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers).|
|Warstwa zasobów obliczeniowych|Bezserwerowe usługi obliczeniowe|[Bezserwerowe usługi obliczeniowe](database/service-tiers-vcore.md#compute-tiers)
||Zainicjowane obliczenie|[Zainicjowane obliczenie](database/service-tiers-vcore.md#compute-tiers)
|Generowanie obliczeń|Seria 5 rdzeń, Seria M, Fsv2|[Generacja sprzętu](database/service-tiers-vcore.md#hardware-generations)
|Jednostka serwera| Serwer |[Serwery logiczne SQL](database/logical-servers.md)|
|Typ zasobu|Rdzenie wirtualne|Rdzeń procesora CPU udostępniany zasobowi obliczeniowemu dla pojedynczej bazy danych, elastycznej puli. |
||Rozmiar i wielkość magazynu obliczeń|Rozmiar obliczeń to maksymalna ilość zasobów procesora CPU, pamięć i inne zasoby niezwiązane z magazynem dostępne dla pojedynczej bazy danych lub puli elastycznej.  Rozmiar magazynu to maksymalna ilość dostępnego miejsca w magazynie dla pojedynczej bazy danych lub puli elastycznej. Aby uzyskać opcje ustalania wielkości w modelu rdzeń wirtualny, zobacz [rdzeń wirtualnye pojedyncze bazy danych](database/resource-limits-vcore-single-databases.md)i [elastyczne pule rdzeń wirtualny](database/resource-limits-vcore-elastic-pools.md).  (.. /managed-instance/resource-limits.md).  Aby uzyskać opcje ustalania wielkości w modelu DTU, zobacz [pojedyncze bazy danych jednostek DTU](database/resource-limits-dtu-single-databases.md) i [elastyczne pule jednostek DTU](database/resource-limits-dtu-elastic-pools.md).

## <a name="azure-sql-managed-instance"></a>Wystąpienie zarządzane Azure SQL

|Kontekst|Okres|Więcej informacji|
|:---|:---|:---|
|Usługa platformy Azure|Wystąpienie zarządzane Azure SQL|[Wystąpienie zarządzane SQL](managed-instance/sql-managed-instance-paas-overview.md)|
|Model zakupów|Model zakupów oparty na rdzeniach wirtualnych|[Model zakupów oparty na rdzeniach wirtualnych](database/service-tiers-vcore.md)|
|Opcja wdrażania |Jedno wystąpienie|[Pojedyncze wystąpienie](managed-instance/sql-managed-instance-paas-overview.md)|
||Pula wystąpień (wersja zapoznawcza)|[Pule wystąpień](managed-instance/instance-pools-overview.md)|
|Warstwa usługi|Ogólnego przeznaczenia, Krytyczne dla działania firmy|[Warstwy usługi wystąpienia zarządzanego SQL](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|Warstwa zasobów obliczeniowych|Zainicjowane obliczenie|[Zainicjowane obliczenie](database/service-tiers-vcore.md#compute-tiers)|
|Generowanie obliczeń|5 rdzeń|[Generacja sprzętu](database/service-tiers-vcore.md#hardware-generations)
|Jednostka serwera|Wystąpienie zarządzane lub wystąpienie| Nie dotyczy, ponieważ wystąpienie zarządzane SQL należy do serwera |
|Typ zasobu|Rdzenie wirtualne|Rdzeń procesora CPU udostępniany zasobowi obliczeniowemu dla wystąpienia zarządzanego SQL.|
||Rozmiar i wielkość magazynu obliczeń|Rozmiar obliczeń to maksymalna ilość danych procesora CPU, pamięci i innych zasobów niezwiązanych z magazynem dla wystąpienia zarządzanego SQL.  Rozmiar magazynu to maksymalna ilość miejsca dostępnego dla wystąpienia zarządzanego SQL.  W przypadku opcji ustalania wielkości, [wystąpienia zarządzane SQL](managed-instance/resource-limits.md). |

## <a name="sql-on-azure-vm"></a>SQL na maszynie wirtualnej platformy Azure

potrzebujesz tutaj więcej rzeczy
