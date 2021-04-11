---
title: Jak używać źródeł danych profilowania danych w Azure Data Catalog
description: Artykuł zawiera instrukcje dotyczące sposobu uwzględniania profilów danych na poziomie tabeli i kolumny podczas rejestrowania źródeł danych w Azure Data Catalog oraz jak używać profilów danych do zrozumienia źródeł danych.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 473ba69fa30542dfa41dfc5f19002ac36b56b482
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674958"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Jak w Azure Data Catalog źródła danych profilu danych

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Wprowadzenie

**Microsoft Azure Data Catalog** to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i odnajdywania źródeł danych w przedsiębiorstwie. Innymi słowy, **Azure Data Catalog** to pomoc dla osób, które odnajdują i korzystają ze źródeł danych oraz pomagają organizacjom w uzyskaniu większej korzyści z istniejących danych. Gdy źródło danych jest zarejestrowane w **Azure Data Catalog**, jego metadane są kopiowane i indeksowane przez usługę, ale wątek nie kończy w tym miejscu.

Funkcja **profilowania danych** **Azure Data Catalog** analizuje dane z obsługiwanych źródeł danych w katalogu i zbiera statystyki i informacje o tych danych. Można łatwo dołączyć profil zasobów danych. Po zarejestrowaniu zasobu danych wybierz pozycję **Dołącz profil danych** w narzędziu rejestracji źródła danych.

## <a name="what-is-data-profiling"></a>Co to jest Profilowanie danych

Profilowanie danych bada dane w zarejestrowanym źródle danych i zbiera statystyki oraz informacje o tych danych. Podczas odnajdywania źródła danych te statystyki mogą pomóc w ustaleniu przydatności danych w celu rozwiązania problemu biznesowego.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Następujące źródła danych obsługują profilowanie danych:

* SQL Server (w tym tabele i widoki usługi Azure SQL DB i usługi Azure Synapse Analytics)
* Tabele i widoki programu Oracle
* Tabele i widoki programu Teradata
* Tabele Hive

Uwzględnienie profilów danych podczas rejestrowania zasobów danych pomaga użytkownikom odpowiedzieć na pytania dotyczące źródeł danych, w tym:

* Czy może służyć do rozwiązywania problemu biznesowego?
* Czy dane są zgodne z określonymi standardami czy wzorcami?
* Jakie są anomalie źródła danych?
* Jakie są problemy z integracją tych danych z moją aplikacją?

> [!NOTE]
> Możesz również dodać dokumentację do elementu zawartości, aby opisać, w jaki sposób można zintegrować dane z aplikacją. Zobacz [jak dokumentować źródła danych](data-catalog-how-to-documentation.md).
>

<a name="howto"></a>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Jak dołączyć profil danych podczas rejestrowania źródła danych

Można łatwo dołączyć profil do źródła danych. Po zarejestrowaniu źródła danych w panelu **obiekty do zarejestrowania** narzędzia rejestracji źródła danych wybierz pozycję **Dołącz profil danych**.

![Pole wyboru Dołącz profil danych](media/data-catalog-data-profile/data-catalog-register-profile.png)

Aby dowiedzieć się więcej na temat rejestrowania źródeł danych, zobacz [jak rejestrować źródła danych](data-catalog-how-to-register.md) i rozpoczynać [pracę z Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrowanie zasobów danych zawierających profile danych

Aby odnajdywać zasoby danych, które zawierają profil danych, można uwzględnić `has:tableDataProfiles` lub `has:columnsDataProfiles` jeden z wyszukiwanych terminów.

> [!NOTE]
> Wybranie opcji **Dołącz profil danych** w narzędziu rejestracji źródła danych obejmuje zarówno informacje o profilu tabeli, jak i na poziomie kolumny. Jednak interfejs API Data Catalog zezwala na zarejestrowanie zasobów danych tylko z jednym zestawem informacji o profilu.
>

## <a name="viewing-data-profile-information"></a>Wyświetlanie informacji o profilu danych

Po znalezieniu odpowiedniego źródła danych z profilem możesz wyświetlić szczegóły profilu danych. Aby wyświetlić profil danych, wybierz zasób danych i wybierz pozycję **profil danych** w oknie Portal Data Catalog.

![Karta profil danych](media/data-catalog-data-profile/data-catalog-view.png)

Profil danych w **Azure Data Catalog** zawiera informacje o profilu tabeli i kolumny, w tym:

### <a name="object-data-profile"></a>Profil danych obiektu

* Liczba wierszy
* Rozmiar tabeli
* Kiedy obiekt został ostatnio zaktualizowany

### <a name="column-data-profile"></a>Profil danych kolumny

* Column — typ danych
* Liczba unikatowych wartości
* Liczba wierszy z wartościami NULL
* Minimalna, maksymalna, średnia i odchylenie standardowe dla wartości kolumn

## <a name="summary"></a>Podsumowanie

Profilowanie danych zawiera dane statystyczne i informacje o zarejestrowanych zasobach danych, które ułatwiają ustalenie przydatności danych w celu rozwiązania problemów z działalnością biznesową. Podobnie jak w przypadku dodawania adnotacji i dokumentowania źródeł danych, profile danych mogą zapewnić użytkownikom dokładniejsze zrozumienie Twoich danych.

## <a name="see-also"></a>Zobacz też

* [Jak rejestrować źródła danych](data-catalog-how-to-register.md)
* [Rozpoczynanie pracy z usługą Azure Data Catalog](data-catalog-get-started.md)
