---
title: 'Instrukcje: wyszukiwanie Data Catalog'
description: Ten artykuł zawiera omówienie sposobu przeszukiwania wykazu danych.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 7799266bf9cece1ed789d6fab64ec970a09fbfcb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588477"
---
# <a name="search-the-azure-purview-data-catalog"></a>Przeszukaj Data Catalog Azure kontrolą

Odnajdywanie danych to pierwszy krok dla obciążeń związanych z analizą danych lub zarządzaniem danymi dla odbiorców danych. Odnajdywanie danych może być czasochłonne, ponieważ użytkownik może nie wiedzieć, gdzie znaleźć potrzebne dane. Nawet po znalezieniu danych mogą wystąpić wątpliwości dotyczące tego, czy można zaufać danym i podjąć na nich zależność.

Celem wyszukiwania w usłudze Azure kontrolą jest przyspieszenie procesu odnajdywania danych w celu szybkiego znalezienia danych. W tym artykule opisano sposób przeszukiwania wykazu danych usługi Azure kontrolą w celu szybkiego znalezienia szukanych danych.

## <a name="search-the-catalog-for-assets"></a>Wyszukaj w katalogu zasoby

Na platformie Azure kontrolą pasek wyszukiwania znajduje się u góry okna środowiska kontrolą Studio.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Zrzut ekranu przedstawiający lokalizację paska wyszukiwania Azure kontrolą" border="true":::

Po kliknięciu paska wyszukiwania można zobaczyć ostatnio używane historia wyszukiwania i ostatnio używane zasoby. Wybierz pozycję "Wyświetl wszystko", aby wyświetlić wszystkie ostatnio wyświetlane zasoby.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Zrzut ekranu przedstawiający pasek wyszukiwania przed wprowadzeniem słów kluczowych" border="true":::

Wprowadź słowa kluczowe, które pomagają identyfikować zasoby, takie jak nazwa, typ danych, klasyfikacje i warunki słownika. Podczas wprowadzania słów kluczowych dotyczących żądanego elementu zawartości usługa Azure kontrolą wyświetla sugestie dotyczące zakresu wyszukiwania i potencjalnych dopasowań zasobów. Aby ukończyć wyszukiwanie, kliknij pozycję "Wyświetl wyniki wyszukiwania" lub naciśnij klawisz ENTER.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Zrzut ekranu przedstawiający pasek wyszukiwania w miarę wprowadzania przez użytkownika słów kluczowych" border="true":::

Na stronie Wyniki wyszukiwania zostanie wyświetlona lista zasobów, które pasują do słów kluczowych dostarczonych w kolejności istotności. Istnieją różne czynniki, które mogą wpływać na ocenę przydatności elementu zawartości. Możesz filtrować listę więcej, wybierając określone magazyny danych, klasyfikacje, kontakty, etykiety i warunki słownika, które mają zastosowanie do szukanego elementu zawartości.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Zrzut ekranu przedstawiający wyniki wyszukiwania" border="true":::

 Kliknij żądany zasób, aby wyświetlić stronę szczegóły zasobu, na której można wyświetlić właściwości, takie jak schemat, elementy powiązane i właściciele zasobów.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Zrzut ekranu przedstawiający stronę szczegółów zasobu" border="true":::

## <a name="search-query-syntax"></a>Wyszukaj składnię zapytania

Wszystkie zapytania wyszukiwania składają się z słów kluczowych i operatorów. Słowo kluczowe to element, który będzie częścią właściwości zasobu. Potencjalne słowa kluczowe mogą być klasyfikacją, terminem słownika, opisem zasobu lub nazwą zasobu. Słowo kluczowe może być częścią właściwości, do której chcesz dopasować. Użyj słów kluczowych i operatorów wymienionych poniżej, aby upewnić się, że usługa Azure kontrolą zwraca zasoby, których szukasz. 

Poniżej znajdują się operatory, których można użyć do utworzenia zapytania wyszukiwania. Operatory można łączyć dowolną liczbę razy w pojedynczym zapytaniu.

| Operator | Definicja | Przykład |
| -------- | ---------- | ------- |
| LUB | Określa, że element zawartości musi zawierać co najmniej jeden z tych dwóch słów kluczowych. Musi mieć wszystkie wielkie litery. Biały znak jest również operatorem OR.  | Zapytanie `hive OR database` zwraca zasoby zawierające element "Hive" lub "Database" albo oba te elementy. |
| AND | Określa, że element zawartości musi mieć oba słowa kluczowe. Musi mieć wszystkie wersaliki | Zapytanie `hive AND database` zwraca zasoby, które zawierają zarówno obiekty "Hive", jak i "Database". |
| NOT | Określa, że element zawartości nie może zawierać słowa kluczowego z prawej strony klauzuli NOT | Zapytanie `hive NOT database` zwraca zasoby zawierające element "Hive", ale nie "Database". |
| () | Grupuje zestaw słów kluczowych i operatorów. W przypadku łączenia wielu operatorów, nawiasy określają kolejność operacji. | Zapytanie `hive AND (database OR warehouse)` zwraca zasoby zawierające element "Hive" i "Database" lub "Warehouse" albo oba te elementy. |
| "" | Określa dokładną zawartość w frazie, do której zapytanie musi być zgodne. | Zapytanie `"hive database"` zwraca elementy zawartości zawierające frazę "baza danych Hive" w swoich właściwościach |
| * | Symbol wieloznaczny, który pasuje do jednego do wielu znaków. Nie może być pierwszym znakiem w słowie kluczowym. | Zapytanie `hiv\` * zwraca elementy, które mają właściwości zaczynające się od "HIV", takich jak "Hive" lub "Hive-Table". |
| ? | Symbol wieloznaczny, który jest zgodny z pojedynczym znakiem. Nie może być pierwszym znakiem w słowie kluczowym | Zapytanie `hiv?` zwraca zasoby, których właściwości zaczynają się od "HIV" i są czterema literami, takimi jak "Hive" lub "Hiva". |

> [!Note]
> Zawsze określaj operatory logiczne (**i**, **lub**, **nie**) we wszystkich wersalikach. W przeciwnym razie przypadek nie ma znaczenia lub nie rób dodatkowych spacji.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie, importowanie i eksportowanie terminów słownika](how-to-create-import-export-glossary.md)
- [Zarządzanie szablonami terminów dla słownika biznesowego](how-to-manage-term-templates.md)
