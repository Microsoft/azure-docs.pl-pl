---
title: Terminologia Azure Data Catalog
description: Ten artykuł zawiera wprowadzenie do pojęć i terminów używanych w dokumentacji Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 008d0f106b07d61f0989a479457b12398000b5d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674579"
---
# <a name="azure-data-catalog-terminology"></a>Terminologia Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Ten artykuł zawiera wprowadzenie do pojęć i terminów używanych w dokumentacji Azure Data Catalog.

## <a name="catalog"></a>Wykaz

Azure Data Catalog to oparte na chmurze repozytorium metadanych, w którym można zarejestrować źródła danych i zasoby danych. Katalog służy jako Centralna lokalizacja magazynu dla metadanych strukturalnych wyodrębnionych ze źródeł danych i opisowych metadanych dodawanych przez użytkowników.

## <a name="data-source"></a>Źródło danych

Źródło danych to system lub kontener, który zarządza zasobami danych. Przykładami mogą być SQL Server bazy danych, bazy danych Oracle, bazy danych SQL Server Analysis Services (tabelaryczne lub wielowymiarowe) oraz serwery SQL Server Reporting Services.

## <a name="data-asset"></a>Zasób danych

Zasoby danych to obiekty znajdujące się w źródłach danych, które mogą być zarejestrowane w wykazie. Przykłady obejmują tabele i widoki SQL Server, tabele i widoki programu Oracle, miary SQL Server Analysis Services, wymiary i wskaźniki KPI oraz raporty SQL Server Reporting Services.

## <a name="data-asset-location"></a>Lokalizacja zasobu danych

Katalog przechowuje lokalizację źródła danych lub zasobu danych, którego można użyć do nawiązania połączenia ze źródłem przy użyciu aplikacji klienckiej. Format i szczegóły lokalizacji różnią się w zależności od typu źródła danych. Na przykład tabela SQL Server może być identyfikowana na podstawie jej czterech nazw części: Nazwa serwera, nazwa bazy danych, nazwa schematu, nazwa obiektu, podczas gdy raport SQL Server Reporting Services może być identyfikowany przez jego adres URL.

## <a name="structural-metadata"></a>Metadane strukturalne

Metadane strukturalne to metadane wyodrębnione ze źródła danych, które opisują strukturę zasobu danych. Obejmuje to lokalizację zasobów, jej nazwę i typ obiektu oraz dodatkowe cechy charakterystyczne dla typu. Na przykład metadane strukturalne dla tabel i widoków zawierają nazwy i typy danych kolumn obiektu.

## <a name="descriptive-metadata"></a>Metadane opisowe

Metadane opisowe to metadane opisujące cel lub intencję zasobu danych. Zazwyczaj metadane opisowe są dodawane przez użytkowników wykazu przy użyciu portalu Azure Data Catalog, ale można je również wyodrębnić ze źródła danych podczas rejestracji. Na przykład Narzędzie rejestracji Azure Data Catalog będzie wyodrębniać opisy z właściwości Description w SQL Server Analysis Services i SQL Server Reporting Services oraz z [właściwości rozszerzonej MS_Description](/previous-versions/sql/sql-server-2008-r2/ms190243(v=sql.105)) w SQL Server bazach danych, jeśli te właściwości zostały wypełnione wartościami.

## <a name="request-access"></a>Żądanie dostępu

Metadane opisowe zasobów danych mogą zawierać informacje na temat żądania dostępu do zasobu danych lub źródła danych. Te informacje są prezentowane z lokalizacją zasobu danych i mogą zawierać co najmniej jedną z następujących opcji:

* Adres e-mail użytkownika lub zespołu odpowiedzialnego za udzielanie dostępu do źródła danych.
* Adres URL udokumentowanego procesu, który użytkownicy muszą wykonać, aby uzyskać dostęp do źródła danych.
* Adres URL narzędzia do zarządzania tożsamościami i dostępem (na przykład Microsoft Identity Manager), którego można użyć w celu uzyskania dostępu do źródła danych.
* Wpis swobodny, który opisuje sposób, w jaki użytkownicy mogą uzyskać dostęp do źródła danych.

## <a name="preview"></a>Wersja zapoznawcza

Wersja zapoznawcza w Azure Data Catalog to migawka składająca się z maksymalnie 20 rekordów, które można wyodrębnić ze źródła danych podczas rejestracji, i przechowywać w katalogu przy użyciu metadanych zasobów danych. Wersja zapoznawcza może pomóc użytkownikom, którzy odnajdują zasób danych, lepiej zrozumieć swoją funkcję i cel. Innymi słowy, oglądanie przykładowych danych może być bardziej cenne niż wyświetlanie tylko nazw kolumn i typów danych.
Wersje zapoznawcze są obsługiwane tylko w przypadku tabel i widoków i muszą zostać jawnie wybrane przez użytkownika podczas rejestracji.

## <a name="data-profile"></a>Profil danych

Profil danych w Azure Data Catalog to migawka metadanych na poziomie tabeli i na poziomie kolumny dotyczących zarejestrowanego zasobu danych, które mogą zostać wyodrębnione ze źródła danych podczas rejestracji, i przechowywane w katalogu z metadanymi zasobów danych. Profil danych może pomóc użytkownikom, którzy odnajdują zasób danych lepiej zrozumieć swoją funkcję i cel. Podobnie jak w przypadku wersji zapoznawczych, profile danych muszą zostać jawnie wybrane przez użytkownika podczas rejestracji.

> [!NOTE]
> Wyodrębnienie profilu danych może być kosztowną operacją dla dużych tabel i widoków i może znacząco zwiększyć czas wymagany do zarejestrowania źródła danych.


## <a name="user-perspective"></a>Perspektywa użytkownika

W Azure Data Catalog każdy użytkownik może podać metadane opisowe dla zarejestrowanego zasobu danych. Każdy użytkownik ma odrębną perspektywę dotyczącą danych i ich użycia. Na przykład administrator odpowiedzialny za serwer może podawać szczegóły dotyczące umowy dotyczącej poziomu usług (SLA) lub systemu Windows. Steward danych może dostarczyć linki do dokumentacji dotyczącej procesów firmy obsługiwanych przez dane; analityk może podać opis warunków, które są najbardziej odpowiednie dla innych analityków, a które mogą być najbardziej cenne dla tych użytkowników, którzy muszą odkrywać i zrozumieć dane.

Każda z tych perspektyw jest z natury cenna, a w Azure Data Catalog każdy użytkownik może podać informacje, które są dla nich zrozumiałe, natomiast wszyscy użytkownicy mogą używać tych informacji do zrozumienia danych i ich przeznaczenia.

## <a name="expert"></a>Ekspert

Ekspert jest użytkownikiem, który został zidentyfikowany jako mający poinformowaną perspektywę "eksperta" dla zasobu danych. Każdy użytkownik może dodać siebie lub innego użytkownika jako ekspert dla elementu zawartości. Wymieniony jako ekspert nie przekazuje żadnych dodatkowych uprawnień w Azure Data Catalog; Dzięki temu użytkownicy mogą łatwo lokalizować te perspektywy, które najprawdopodobniej będą przydatne podczas przeglądania opisowych metadanych elementu zawartości.

## <a name="owner"></a>Właściciel

Właściciel to użytkownik, który ma dodatkowe uprawnienia do zarządzania zasobem danych w Azure Data Catalog. Użytkownicy mogą przejąć własność zarejestrowanych zasobów danych, a właściciele mogą dodawać innych użytkowników jako współwłaścicieli. Aby uzyskać więcej informacji, zobacz [jak zarządzać zasobami danych](data-catalog-how-to-manage.md)  

> [!NOTE]
> Własność i zarządzanie są dostępne tylko w wersji Standard programu Azure Data Catalog.

## <a name="registration"></a>Rejestracja

Rejestracja to czynność wyodrębniania metadanych zasobów danych ze źródła danych i kopiowania jej do usługi Azure Data Catalog. Zarejestrowane zasoby danych można następnie dodać do nich adnotacje i odnaleźć.

## <a name="next-steps"></a>Następne kroki

[Szybki Start: Tworzenie Azure Data Catalog](data-catalog-get-started.md)