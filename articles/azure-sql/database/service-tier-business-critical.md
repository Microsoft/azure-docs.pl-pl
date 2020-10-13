---
title: Krytyczne dla działania firmy warstwy usług
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Dowiedz się więcej na temat warstwy usługi krytycznej dla firmy Azure SQL Database i wystąpienia zarządzanego Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 0067811316a8afd26828050d81215ecb5748c841
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85986695"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Krytyczne dla działania firmy warstwy Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Warstwa Krytyczne dla działania firmy jest nazywana Premium w modelu zakupu jednostek DTU. Aby zapoznać się z porównaniem modelu zakupu opartego na rdzeń wirtualny z modelem zakupów opartym na jednostkach DTU, zobacz [Azure SQL Database kupowanie modeli i zasobów](purchasing-models.md).

Azure SQL Database i wystąpienie zarządzane SQL platformy Azure są oparte na architekturze SQL Server Database Engine, które są dostosowane do środowiska chmury w celu zapewnienia dostępności 99,99% nawet w przypadku awarii infrastruktury. Istnieją trzy modele architektury, które są używane:
- Ogólnego przeznaczenia/Standard 
- Krytyczne dla działania firmy/Premium
- Hiperskala

Model warstwy usług premium/Krytyczne dla działania firmy jest oparty na klastrze procesów aparatu bazy danych. Ten model architektury polega na tym, że zawsze jest kworum dostępnych węzłów aparatu bazy danych i ma minimalny wpływ na wydajność, nawet w trakcie czynności konserwacyjnych. Warstwa usługi do skalowania w górę jest obecnie dostępna tylko dla Azure SQL Database (nie wystąpienia zarządzanego SQL) i jest wysoce skalowalną warstwą wydajności magazynu i obliczeń, która wykorzystuje architekturę platformy Azure do skalowania magazynu i zasobów obliczeniowych dla bazy danych w Azure SQL Database znacznie przekraczające limity dostępne dla Ogólnego przeznaczenia i Krytyczne dla działania firmy warstw usług.

Platforma Azure uaktualnia i aktualizuje podstawowy system operacyjny, sterowniki oraz aparat SQL Server Database w sposób niewidoczny dla użytkowników końcowych w minimalnym czasie. 

Dostępność w warstwie Premium jest włączana w warstwach usług premium i Krytyczne dla działania firmy i jest przeznaczona do intensywnych obciążeń, które nie mogą tolerować wpływu na wydajność wynikające z trwających operacji konserwacyjnych.

Obliczenia i magazyn są zintegrowane z pojedynczym węzłem w modelu Premium. Wysoka dostępność w tym modelu architektury jest osiągana przez replikację procesu obliczeniowego (SQL Server procesie aparatu bazy danych) i magazynu (lokalnie dołączony dysk SSD) wdrożonego w klastrze z czterema węzłami przy użyciu technologii podobnej do SQL Server [zawsze włączone grupy dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Klaster węzłów aparatu bazy danych](./media/service-tier-business-critical/business-critical-service-tier.png)

Zarówno proces aparatu bazy danych SQL Server, jak i powiązane pliki MDF/ldf są umieszczane w tym samym węźle, w którym lokalnie dołączony magazyn SSD zapewnia małe opóźnienia w obciążeniu. Wysoka dostępność jest implementowana przy użyciu technologii podobnego do SQL Server [zawsze włączonymi grupami dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Każda baza danych jest klastrem węzłów bazy danych z jedną podstawową bazą danych, która jest dostępna dla obciążeń klientów, oraz trzech procesów pomocniczych zawierających kopie danych. Węzeł podstawowy ciągle przechodzi zmiany do węzłów pomocniczych w celu upewnienia się, że dane są dostępne w replikach pomocniczych, jeśli węzeł podstawowy zakończy się niepowodzeniem z jakiegokolwiek powodu. Tryb failover jest obsługiwany przez aparat bazy danych SQL Server — jedna replika pomocnicza jest węzłem podstawowym, a Nowa replika pomocnicza jest tworzona w celu zapewnienia wystarczającej liczby węzłów w klastrze. Obciążenie jest automatycznie przekierowywane do nowego węzła podstawowego.

Ponadto klaster Krytyczne dla działania firmy ma wbudowaną funkcję [odczytu](read-scale-out.md) w poziomie, która zapewnia bezpłatny, wbudowany węzeł tylko do odczytu, który może służyć do uruchamiania zapytań tylko do odczytu (na przykład raportów), które nie powinny wpływać na wydajność podstawowego obciążenia.

## <a name="when-to-choose-this-service-tier"></a>Kiedy należy wybrać tę warstwę usług

Krytyczne dla działania firmy warstwa usług została zaprojektowana dla aplikacji, które wymagają odpowiedzi o małym opóźnieniu z bazowego magazynu SSD (średnio 1-2 ms), szybkiego odzyskiwania w przypadku awarii podstawowej infrastruktury lub konieczności wyłączania raportów, analiz i zapytań tylko do odczytu w celu uzyskania bezpłatnej repliki pomocniczej podstawowej bazy danych.

Najważniejsze przyczyny wyboru Krytyczne dla działania firmy warstwy usług zamiast warstwy Ogólnego przeznaczenia są następujące:
-   **Wymagania niskiego opóźnienia we/wy** — obciążenia wymagające szybkiej odpowiedzi z warstwy magazynowania (średnia 1-2 milisekund) powinny używać warstwy krytyczne dla działania firmy. 
-   **Częsta komunikacja między aplikacjami i bazami danych**. Aplikacje, które nie mogą korzystać z buforowania warstwy aplikacji lub [żądania wsadowego](../performance-improve-use-batching.md) i muszą wysyłać wiele zapytań SQL, które muszą być szybko przetwarzane, są dobrymi kandydatami do warstwy krytyczne dla działania firmy.
-   **Duża liczba aktualizacji** — operacje INSERT, Update i DELETE modyfikują strony danych w pamięci (zanieczyszczoną stronę), które muszą zostać zapisane w plikach danych z `CHECKPOINT` operacją. Potencjalna awaria procesu aparatu bazy danych lub przełączenia w tryb failover bazy danych z dużą liczbą zanieczyszczonych stron może zwiększyć czas odzyskiwania w warstwie Ogólnego przeznaczenia. Użyj warstwy Krytyczne dla działania firmy, jeśli masz obciążenie, które powoduje wiele zmian w pamięci. 
-   **Długotrwałe transakcje, które modyfikują dane**. Transakcje, które są otwarte przez dłuższy czas, uniemożliwiają Obcinanie pliku dziennika, co może zwiększyć rozmiar dziennika i liczbę [wirtualnych plików dziennika (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). Duża liczba VLFs może spowolnić odzyskiwanie bazy danych po przejściu do trybu failover.
-   **Obciążenie przy użyciu zapytań i raportów analitycznych** , które można przekierować do bezpłatnej repliki tylko do odczytu.
- **Wyższa odporność i szybsze odzyskiwanie po awarii**. W przypadku awarii systemu baza danych w wystąpieniu podstawowym zostanie wyłączona, a jedna z replik pomocniczych stanie się natychmiast nową podstawową bazą danych do odczytu i zapisu, która jest gotowa do przetwarzania zapytań. Aparat bazy danych nie musi analizować i ponawiać transakcji z pliku dziennika i ładować wszystkich danych w buforze pamięci.
- **Zaawansowana ochrona przed uszkodzeniem danych**. Warstwa Krytyczne dla działania firmy korzysta z replik bazy danych w tle w celu zapewnienia ciągłości działania, a więc usługa używa automatycznej naprawy strony, która jest tą samą technologią, która jest używana do SQL Server dublowania baz danych [i grup dostępności](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring). W przypadku, gdy replika nie może odczytać strony z powodu problemu z integralnością danych, Nowa kopia strony zostanie pobrana z innej repliki, zastępując nieczytelną stronę bez utraty danych lub przestoju klienta. Ta funkcja ma zastosowanie w warstwie Ogólnego przeznaczenia, jeśli baza danych ma replikę geograficzną.
- **Wyższa dostępność** — warstwa krytyczne dla działania firmy w ramach konfiguracji z obsługą wiele-AZ — zapewnia dostępność na 99,995% w porównaniu do 99,99% warstwy ogólnego przeznaczenia.
- **Szybka replikacja geograficzna** — warstwa krytyczne dla działania firmy skonfigurowana z replikacją geograficzną ma gwarantowany cel punktu odzyskiwania (RPO) wynoszący 5 sekund i cel czasu odzyskiwania (RTO) wynoszący 30 sekund przez 100% czasu wdrożenia.

## <a name="next-steps"></a>Następne kroki

- Znajdź charakterystykę zasobu (liczbę rdzeni, operacji we/wy, pamięci) warstwy Krytyczne dla działania firmy w [wystąpieniu zarządzanym SQL](../managed-instance/resource-limits.md#service-tier-characteristics), pojedynczą bazę danych w [modelu rdzeń wirtualny](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) lub [modelu DTU](resource-limits-dtu-single-databases.md#premium-service-tier)lub elastyczną pulę w [modelu rdzeń wirtualny](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4) i [modelu DTU](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits).
- Dowiedz się więcej na temat warstw [ogólnego przeznaczenia](service-tier-general-purpose.md) i [skalowania](service-tier-hyperscale.md) .
- Dowiedz się więcej na temat [Service Fabric](../../service-fabric/service-fabric-overview.md).
- Aby uzyskać więcej opcji dotyczących wysokiej dostępności i odzyskiwania po awarii, zobacz [ciągłość](business-continuity-high-availability-disaster-recover-hadr-overview.md)działania.
