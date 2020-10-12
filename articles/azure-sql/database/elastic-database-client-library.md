---
title: Tworzenie skalowalnych baz danych w chmurze
description: Twórz skalowalne aplikacje baz danych platformy .NET przy użyciu biblioteki klienta Elastic Database.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: bfe5dc00ba0255520c04ea85157f0b8bdc71b590
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84050226"
---
# <a name="building-scalable-cloud-databases"></a>Tworzenie skalowalnych baz danych w chmurze
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Skalowanie baz danych można łatwo wykonywać przy użyciu skalowalnych narzędzi i funkcji dla Azure SQL Database. W szczególności można użyć **biblioteki klienta Elastic Database** do tworzenia skalowanych baz danych i zarządzania nimi. Ta funkcja pozwala łatwo opracowywać aplikacje podzielonej na fragmenty przy użyciu setek (nawet tysięcy) baz danych w Azure SQL Database.

Do pobrania:

* Wersja języka Java biblioteki, zobacz [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* Wersja programu .NET biblioteki, zobacz [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentacja

1. [Wprowadzenie do narzędzi elastycznej bazy danych](elastic-scale-get-started.md)
2. [Funkcje Elastic Database](elastic-scale-introduction.md)
3. [Zarządzanie mapami fragmentów](elastic-scale-shard-map-management.md)
4. [Migrowanie istniejących baz danych w celu skalowania w poziomie](elastic-convert-to-use-elastic-tools.md)
5. [Routing zależny od danych](elastic-scale-data-dependent-routing.md)
6. [Zapytania z wieloma fragmentami](elastic-scale-multishard-querying.md)
7. [Dodawanie fragmentu przy użyciu narzędzi Elastic Database](elastic-scale-add-a-shard.md)
8. [Aplikacje z wieloma dzierżawcami z narzędziami Elastic Database i zabezpieczeniami na poziomie wiersza](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)
9. [Uaktualnianie aplikacji biblioteki klienta](elastic-scale-upgrade-client-library.md) 
10. [Omówienie zapytań elastycznych](elastic-query-overview.md)
11. [Słownik narzędzi Elastic Database](elastic-scale-glossary.md)
12. [Elastic Database bibliotekę kliencką z Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Elastic Database bibliotekę kliencką z Dapper](elastic-scale-working-with-dapper.md)
14. [Narzędzie do dzielenia i scalania](elastic-scale-overview-split-and-merge.md)
15. [Liczniki wydajności dla menedżera map fragmentów](elastic-database-client-library.md) 
16. [Często zadawane pytania dotyczące narzędzi Elastic Database](elastic-scale-faq.md)

## <a name="client-capabilities"></a>Możliwości klienta

Skalowanie aplikacji przy użyciu usługi *fragmentowania* przedstawia wyzwania zarówno dla deweloperów, jak i administratora. Biblioteka klienta upraszcza zadania zarządzania przez udostępnienie narzędzi, które umożliwiają deweloperom i administratorom zarządzanie skalowanymi bazami danych. W typowym przykładzie istnieje wiele baz danych (nazywanych "fragmentów") w celu zarządzania nimi. Klienci znajdują się w tej samej bazie danych i jedna baza danych na klienta (schemat o pojedynczej dzierżawie). Biblioteka klienta obejmuje następujące funkcje:

- **Zarządzanie mapami fragmentu**: zostanie utworzona specjalna baza danych o nazwie "Menedżer mapy fragmentu". Zarządzanie mapami fragmentu umożliwia aplikacji Zarządzanie metadanymi o jej fragmentów. Deweloperzy mogą korzystać z tej funkcji, aby zarejestrować bazy danych jako fragmentów, opisać mapowania pojedynczych kluczy fragmentowania lub zakresów kluczy do tych baz danych i zachować te metadane jako liczbę i kompozycję baz danych w celu odzwierciedlenia zmian pojemności. Bez Elastic Databasej biblioteki klienta należy poświęcać dużo czasu na pisanie kodu zarządzania podczas wdrażania fragmentowania. Aby uzyskać szczegółowe informacje, zobacz [fragmentu Map Management](elastic-scale-shard-map-management.md).

- **Routing zależny od danych**: Wyobraź sobie żądanie do aplikacji. Na podstawie wartości klucza fragmentowania żądania aplikacja musi ustalić poprawną bazę danych na podstawie wartości klucza. Następnie zostanie otwarte połączenie z bazą danych w celu przetworzenia żądania. Routing zależny od danych umożliwia otwieranie połączeń przy użyciu jednego prostego wywołania w mapie fragmentu aplikacji. Routing zależny od danych to inny obszar kodu infrastruktury, który jest teraz objęty funkcjonalnością w Elastic Databaseej bibliotece klienta. Aby uzyskać szczegółowe informacje, zobacz [Routing zależny od danych](elastic-scale-data-dependent-routing.md).
- **Zapytania fragmentu (MSQ)**: zapytania z wieloma fragmentuami działają, gdy żądanie obejmuje kilka (lub wszystkie) fragmentów. Zapytanie fragmentu wykonuje ten sam kod T-SQL na wszystkich fragmentów lub zestaw fragmentów. Wyniki z uczestniczących fragmentów są scalone z ogólnym zestawem wyników przy użyciu semantyki ALL. Funkcje udostępniane za pośrednictwem biblioteki klienta programu obsługują wiele zadań, w tym: Zarządzanie połączeniami, zarządzanie wątkami, obsługa błędów i przetwarzanie pośrednich wyników. MSQ może wysyłać zapytania do setek fragmentów. Aby uzyskać szczegółowe informacje, zobacz [wykonywanie zapytań o wiele fragmentu](elastic-scale-multishard-querying.md).

Ogólnie rzecz biorąc klienci korzystający z narzędzi Elastic Database mogą uzyskać pełną funkcjonalność języka T-SQL podczas przesyłania operacji fragmentu-Local w przeciwieństwie do operacji międzyfragmentuowych mających własne semantyke.



## <a name="next-steps"></a>Następne kroki

- Elastic Database biblioteki klienta ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) — w celu **pobrania** biblioteki.

- [Rozpocznij pracę z narzędziami Elastic Database](elastic-scale-get-started.md) — aby wypróbować **przykładową aplikację** , która pokazuje funkcje klienta programu.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) — aby wprowadzać wkłady do kodu.
- [Azure SQL Database Elastic Query — Omówienie](elastic-query-overview.md) — do korzystania z zapytań elastycznych.

- [Przeniesienie danych między skalowanymi bazami danych w chmurze](elastic-scale-overview-split-and-merge.md) — Aby uzyskać instrukcje dotyczące korzystania z **Narzędzia Split-Merge**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

