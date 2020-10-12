---
title: Witamy w aplikacji Wingtips
description: Dowiedz się więcej na temat modeli dzierżawy bazy danych oraz przykładowej aplikacji Wingtips SaaS dla Azure SQL Database w środowisku chmury.
keywords: samouczek usługi sql database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 2c8c094efdfa9f46c6e6c42e34fd4010e43fa972
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84051199"
---
# <a name="the-wingtip-tickets-saas-application"></a>Aplikacja SaaS bilety Wingtip
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Te same *bilety Wingtip* SaaS aplikację są implementowane w każdym z trzech przykładów. Aplikacja jest prostą listą zdarzeń i biletów aplikacji SaaS dla małych miejsc — kina, klubów itp. Każdy z miejsc jest dzierżawcą aplikacji i ma swoje własne dane: szczegóły dotyczące miejsca, listy wydarzeń, klientów, zamówień biletów itp.  Aplikacja wraz ze skryptami i samouczkami zarządzania przedstawia kompleksowy scenariusz SaaS. Obejmuje to dzierżawy, monitorowanie i zarządzanie wydajnością, zarządzanie schematami oraz raportowanie i analiza między dzierżawcami.

## <a name="three-saas-application-and-tenancy-patterns"></a>Trzy SaaS aplikacji i dzierżawców

Dostępne są trzy wersje aplikacji; Każdy z nich eksploruje różne wzorce dzierżawy bazy danych na Azure SQL Database.  Pierwszy używa autonomicznej aplikacji na dzierżawcę z własną bazą danych. Druga aplikacja korzysta z aplikacji wielodostępnej z bazą danych na dzierżawcę. Trzeci przykład korzysta z aplikacji wielodostępnej z bazami danych z wieloma dzierżawcami podzielonej na fragmenty.

![Trzy wzorce dzierżawy][image-three-tenancy-patterns]

 Każdy przykład obejmuje kod aplikacji oraz skrypty zarządzania i samouczki, które eksplorują różne wzorce projektowania i zarządzania.  Każdy przykład jest wdrażany w mniej niż pięć minut.  Wszystkie trzy można wdrożyć obok siebie, aby można było porównać różnice w projekcie i zarządzaniu.

## <a name="standalone-application-per-tenant-pattern"></a>Aplikacja autonomiczna na wzorzec dzierżawy

Aplikacja autonomiczna na wzorzec dzierżawców korzysta z pojedynczej aplikacji dzierżawców z bazą danych dla każdej dzierżawy. Aplikacja każdej dzierżawy, w tym jej baza danych, jest wdrażana w osobnej grupie zasobów platformy Azure. Grupę zasobów można wdrożyć w ramach subskrypcji dostawcy usług lub subskrypcji dzierżawcy i zarządzanej przez dostawcę w imieniu dzierżawcy. Aplikacja autonomiczna na wzorzec dzierżawy zapewnia największą izolację dzierżawy, ale zazwyczaj jest najtańsze, ponieważ nie ma możliwości udostępniania zasobów między wieloma dzierżawcami.  Ten wzorzec jest dobrze dostosowany do aplikacji, które mogą być bardziej skomplikowane i które są wdrażane w mniejszych liczbach dzierżawców.  W przypadku wdrożeń autonomicznych można dostosować aplikację dla każdej dzierżawy łatwiej niż w innych wzorcach.  

Zapoznaj się z [samouczkami][docs-tutorials-for-wingtip-sa] i kodem w serwisie GitHub  [. ../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Wzorzec bazy danych na dzierżawcę

Wzorzec bazy danych na dzierżawcę jest efektywny dla dostawców usług, którzy są zainteresowani izolacją dzierżawy i chcą uruchamiać scentralizowaną usługę, która umożliwia oszczędne wykorzystanie udostępnionych zasobów. Baza danych jest tworzona dla każdego miejsca lub dzierżawy, a wszystkie bazy danych są zarządzane centralnie. Bazy danych mogą być hostowane w pulach elastycznych, aby zapewnić wydajne i łatwe zarządzanie wydajnością, które wykorzystuje nieprzewidywalne wzorce obciążeń dzierżawców. Baza danych wykazu przechowuje mapowanie między dzierżawcami i ich bazami danych. To mapowanie jest zarządzane przy użyciu funkcji zarządzania mapami fragmentu w [bibliotece klienta Elastic Database](elastic-database-client-library.md), co zapewnia wydajne zarządzanie połączeniami z aplikacją.

Zapoznaj się z [samouczkami][docs-tutorials-for-wingtip-dpt] i kodem w serwisie GitHub  [. ../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Wzorzec bazy danych podzielonej na fragmenty z wieloma dzierżawcami

Bazy danych z wieloma dzierżawcami są efektywne dla dostawców usług poszukujących niższych kosztów na dzierżawcę i są w niej zmniejszone z ograniczoną izolacją dzierżawy. Ten wzorzec umożliwia pakowanie dużej liczby dzierżawców w pojedynczą bazę danych, co zapewnia koszt dla dzierżawy w dół. Najbliżej nieskończonej skali można fragmentowania dzierżawców w wielu bazach danych. Baza danych wykazu mapuje dzierżawy do baz danych.  

Ten wzorzec umożliwia również model *hybrydowy* , w którym można zoptymalizować koszt z wieloma dzierżawcami w bazie danych, lub zoptymalizować pod kątem izolacji z jedną dzierżawą w swojej bazie danych. Wybór można przeprowadzić w przypadku dzierżawcy przez dzierżawcę, gdy dzierżawa jest inicjowana lub później, bez wpływu na aplikację.  Ten model może być używany efektywnie, gdy grupy dzierżawców muszą być traktowane inaczej. Na przykład dzierżawy o niskich kosztach mogą być przypisywane do udostępnionych baz danych, a dzierżawy w warstwie Premium można przypisywać do własnych baz danych. 

Zapoznaj się z [samouczkami][docs-tutorials-for-wingtip-mt] i kodem w serwisie GitHub  [. ../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Następne kroki

#### <a name="conceptual-descriptions"></a>Opisy pojęć

- Bardziej szczegółowy opis wzorców dzierżawy aplikacji jest dostępny w wzorcach dzierżawy wielodostępnej [bazy danych SaaS][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Samouczki i kod

- Aplikacja autonomiczna na dzierżawcę:
    - [Samouczki dotyczące aplikacji autonomicznej][docs-tutorials-for-wingtip-sa].
    - [Kod aplikacji autonomicznej w serwisie GitHub][github-code-for-wingtip-sa].

- Baza danych na dzierżawcę:
    - [Samouczki dla bazy danych na dzierżawcę][docs-tutorials-for-wingtip-dpt].
    - [Kod dla bazy danych na dzierżawcę w witrynie GitHub][github-code-for-wingtip-dpt].

- Podzielonej na fragmenty wiele dzierżawców:
    - [Samouczki dotyczące podzielonej na fragmenty wielu dzierżawców][docs-tutorials-for-wingtip-mt].
    - [Kod dla wielu dzierżawców podzielonej na fragmenty w serwisie GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Trzy wzorce dzierżawy."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

