---
title: Przykład aplikacji wielodostępnej — Wingtip SaaS
description: Dowiedz się, jak używać przykładowej aplikacji wielodostępnej, która używa Azure SQL Database Wingtip SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: d291adcf53469825f32b664cb7f4ffdb0bbf244b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441021"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Wprowadzenie do wielodostępnej aplikacji SaaS, która używa wzorca bazy danych na dzierżawcę z Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Aplikacja Wingtip SaaS to przykładowa aplikacja wielodostępna. Aplikacja używa wzorca aplikacji SaaS bazy danych na dzierżawcę do obsługi wielu dzierżawców. Aplikacja zawiera funkcje Azure SQL Database, które umożliwiają SaaS scenariusze przy użyciu kilku wzorców SaaS projektowania i zarządzania. Aby szybko rozpocząć pracę, aplikacja Wingtip SaaS jest wdrażana w mniej niż pięć minut.

Kod źródłowy aplikacji i skrypty zarządzania są dostępne w repozytorium GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Przed rozpoczęciem programu zapoznaj się z [ogólnymi wskazówkami](saas-tenancy-wingtip-app-guidance-tips.md) dotyczącymi kroków pobierania i odblokowywania skryptów zarządzania biletami Wingtip.

## <a name="application-architecture"></a>Architektura aplikacji

Aplikacja Wingtip SaaS korzysta z modelu bazy danych dla dzierżawy. Używa pul elastycznych SQL do maksymalizowania wydajności. W przypadku aprowizacji i mapowania dzierżawców do ich danych jest używana baza danych wykazu. Podstawowa aplikacja Wingtip SaaS używa puli z trzema przykładowymi dzierżawcami oraz bazą danych wykazu. Usługa katalogowa i serwery dzierżawy zostały udostępnione przy użyciu aliasów DNS. Te aliasy są używane do obsługi odwołań do aktywnych zasobów używanych przez aplikację Wingtip. Te aliasy są aktualizowane w taki sposób, aby wskazywały zasoby odzyskiwania w samouczkach odzyskiwania po awarii. Wykonanie wielu samouczków Wingtip SaaS powoduje dodanie dodatków do wstępnego wdrożenia. Wprowadzono dodatki, takie jak analityczne bazy danych i zarządzanie schematami między bazami danych.


![Architektura Wingtip SaaS](./media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Podczas przeglądania samouczków i pracy z aplikacją należy skoncentrować się na wzorcach SaaS, gdy są one powiązane z warstwą danych. Innymi słowy, należy skoncentrować się na warstwie danych i nie przeanalizować samej aplikacji. Zrozumienie implementacji tych wzorców SaaS jest kluczem do wdrożenia tych wzorców w aplikacjach. Należy również wziąć pod uwagę wszelkie niezbędne modyfikacje dotyczące konkretnych wymagań firmy.

## <a name="sql-database-wingtip-saas-tutorials"></a>Samouczki dotyczące SQL Database Wingtip SaaS

Po wdrożeniu aplikacji zapoznaj się z poniższymi samouczkami, które kompilują się po wstępnym wdrożeniu. Te samouczki eksplorują typowe wzorce SaaS, które korzystają z wbudowanych funkcji SQL Database, Azure Synapse Analytics (dawniej SQL Data Warehouse) i innych usług platformy Azure. Samouczki zawierają skrypty programu PowerShell ze szczegółowymi wyjaśnieniami. Wyjaśnienia upraszczają zrozumienie i implementację tych samych wzorców zarządzania SaaS w aplikacjach.


| Samouczek | Opis |
|:--|:--|
| [Wskazówki i porady dotyczące SQL Database przykładowej aplikacji wielodostępnej SaaS](saas-tenancy-wingtip-app-guidance-tips.md) | Pobierz i Uruchom skrypty programu PowerShell w celu przygotowania części aplikacji. |
|[Wdrażanie i eksplorowanie aplikacji Wingtip SaaS](../../sql-database/saas-dbpertenant-get-started-deploy.md)|  Wdróż i Eksploruj aplikację Wingtip SaaS z subskrypcją platformy Azure. |
|[Obsługa administracyjna i dzierżawy katalogu](../../sql-database/saas-dbpertenant-provision-and-catalog.md)| Dowiedz się, jak aplikacja nawiązuje połączenie z dzierżawcami przy użyciu bazy danych wykazu i jak katalog mapuje dzierżawy na swoje dane. |
|[Monitorowanie wydajności i zarządzanie nią](../../sql-database/saas-dbpertenant-performance-monitoring.md)| Dowiedz się, jak używać funkcji monitorowania SQL Database i ustawiać alerty w przypadku przekroczenia progów wydajności. |
|[Monitorowanie za pomocą dzienników usługi Azure Monitor](../../sql-database/saas-dbpertenant-log-analytics.md) | Informacje dotyczące monitorowania dużych ilości zasobów w wielu pulach przy użyciu [dzienników Azure monitor](../../azure-monitor/log-query/log-query-overview.md) . |
|[Przywracanie pojedynczej dzierżawy](../../sql-database/saas-dbpertenant-restore-single-tenant.md)| Dowiedz się, jak przywrócić bazę danych dzierżawy do wcześniejszego punktu w czasie. Dowiedz się również, jak przywrócić bazę danych równoległych, która pozostawia istniejącą dzierżawę w trybie online. |
|[Zarządzanie schematem bazy danych dzierżawy](saas-tenancy-schema-management.md)| Dowiedz się, jak aktualizować schemat i aktualizować dane referencyjne we wszystkich bazach danych dzierżaw. |
|[Uruchamianie zapytań rozproszonych między dzierżawcami](saas-tenancy-cross-tenant-reporting.md) | Utwórz bazę danych analizy ad hoc i uruchom rozproszone zapytania w czasie rzeczywistym dla wszystkich dzierżawców.  |
|[Uruchamianie analizy dla wyodrębnionych danych dzierżawy](saas-tenancy-tenant-analytics.md) | Wyodrębnij dane dzierżawy do bazy danych analizy lub magazynu danych na potrzeby zapytań analitycznych w trybie offline. |


## <a name="next-steps"></a>Następne kroki

- [Ogólne wskazówki i porady dotyczące wdrażania i używania Wingtip biletów SaaS aplikacji](saas-tenancy-wingtip-app-guidance-tips.md)
- [Wdrażanie aplikacji Wingtip SaaS](../../sql-database/saas-dbpertenant-get-started-deploy.md)
