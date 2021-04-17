---
title: 'How to: search the Data Catalog'
description: Ten artykuł zawiera omówienie sposobu przeszukiwania wykazu danych.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 178604335968c3664bde51c144759c1c040c359d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564917"
---
# <a name="search-the-azure-purview-data-catalog"></a>Wyszukiwanie w usłudze Azure Purview Data Catalog

Odnajdywanie danych to pierwszy krok dla obciążenia analizy danych lub zarządzania danymi dla użytkowników danych. Odnajdywanie danych może być czasochłonne, ponieważ możesz nie wiedzieć, gdzie znaleźć dane. Nawet po znalezieniu danych możesz mieć wątpliwości co do tego, czy możesz ufać tym danych i korzystać z nich jako zależności.

Celem wyszukiwania w usłudze Azure Purview jest przyspieszenie procesu odnajdywania danych w celu szybkiego znalezienia ważnych danych. W tym artykule opisano sposób przeszukiwania wykazu danych usługi Azure Purview w celu szybkiego znalezienia szukanych danych.

## <a name="search-the-catalog-for-assets"></a>Wyszukiwanie zasobów w wykazie

W usłudze Azure Purview pasek wyszukiwania znajduje się w górnej części środowiska użytkownika programu Purview Studio.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Zrzut ekranu przedstawiający lokalizację paska wyszukiwania usługi Azure Purview" border="true":::

Po kliknięciu paska wyszukiwania możesz wyświetlić historię ostatnich wyszukiwań i ostatnio używanych zasobów. Wybierz pozycję "Wyświetl wszystko", aby wyświetlić wszystkie ostatnio przeglądane zasoby.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Zrzut ekranu przedstawiający pasek wyszukiwania przed wprowadzeniu jakichkolwiek słów kluczowych" border="true":::

Wprowadź słowa kluczowe, które ułatwiają identyfikację zasobu, takie jak jego nazwa, typ danych, klasyfikacje i terminy w słowniku. Po wprowadzeniu słów kluczowych odnoszących się do żądanego zasobu usługa Azure Purview wyświetla sugestie dotyczące tego, co należy wyszukać i jakie są potencjalne dopasowania zasobów. Aby ukończyć wyszukiwanie, kliknij pozycję "Wyświetl wyniki wyszukiwania" lub naciśnij klawisz Enter.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Zrzut ekranu przedstawiający pasek wyszukiwania wprowadzany przez użytkownika w słowach kluczowych" border="true":::

Na stronie wyników wyszukiwania zostanie wyświetlona lista zasobów, które pasują do podanych słów kluczowych w kolejności według istotności. Istnieją różne czynniki, które mogą mieć wpływ na ocenę istotności zasobu. Możesz bardziej odfiltrować listę, wybierając określone magazyny danych, klasyfikacje, kontakty, etykiety i terminy słownika dotyczące szukanych zasobów.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Zrzut ekranu przedstawiający wyniki wyszukiwania" border="true":::

 Kliknij żądany zasób, aby wyświetlić stronę szczegółów zasobu, na której można wyświetlić właściwości, w tym schemat, dane dotyczące danych trwałych i właścicieli zasobów.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Zrzut ekranu przedstawiający stronę szczegółów zasobu" border="true":::

## <a name="search-query-syntax"></a>Składnia zapytania wyszukiwania

Wszystkie zapytania wyszukiwania składają się ze słów kluczowych i operatorów. Słowo kluczowe to coś, co byłoby częścią właściwości zasobu. Potencjalne słowa kluczowe mogą być klasyfikacją, terminem słownika, opisem zasobu lub nazwą zasobu. Słowo kluczowe może być tylko częścią właściwości, do których chcesz się dopasować. Użyj słów kluczowych i operatorów wymienionych poniżej, aby upewnić się, że usługa Azure Purview zwraca szukane zasoby. 

Poniżej przedstawiono operatory, których można użyć do redagowania zapytania wyszukiwania. Operatory można łączyć tyle razy, ile potrzeba w jednym zapytaniu.

| Operator | Definicja | Przykład |
| -------- | ---------- | ------- |
| LUB | Określa, że element zawartości musi mieć co najmniej jedno z dwóch słów kluczowych. Musi być w wszystkich limitach. Biały obszar jest również operatorem OR.  | Zapytanie zwraca `hive OR database` zasoby, które zawierają elementy "hive", "database" lub oba te elementy. |
| AND | Określa, że element zawartości musi zawierać oba słowa kluczowe. Musi być w wszystkich limitach | Zapytanie zwraca `hive AND database` zasoby, które zawierają zarówno "gałąź", jak i "bazę danych". |
| NOT | Określa, że element zawartości nie może zawierać słowa kluczowego po prawej stronie klauzuli NOT | Zapytanie zwraca `hive NOT database` zasoby zawierające gałąź "hive", ale nie "database". |
| () | Grupuje zestaw słów kluczowych i operatorów. Podczas łączenia wielu operatorów nawiasy określają kolejność operacji. | Zapytanie zwraca `hive AND (database OR warehouse)` zasoby zawierające elementy "hive" i "database" lub "warehouse" albo oba te elementy. |
| "" | Określa dokładną zawartość frazy, do których zapytanie musi być zgodne. | Zapytanie zwraca `"hive database"` zasoby zawierające frazę "baza danych hive" we właściwościach |
| * | Symbol wieloznaczny, który dopasowuje jeden do wielu znaków. Nie może być pierwszym znakiem w s słowach kluczowych. | Zapytanie zwraca `dat*` zasoby, które mają właściwości, które zaczynają się od "dat", takie jak "dane" lub "baza danych". |
| ? | Symbol wieloznaczny, który pasuje do pojedynczego znaku. Nie może być pierwszym znakiem w s słowach kluczowych | Zapytanie zwraca zasoby, które mają właściwości, które zaczynają się od "dat" i są czterema literami, takimi jak `dat?` "date" lub "data". |

> [!Note]
> Zawsze określaj operatory logiczne (**AND**, **OR**, **NOT**) we wszystkich limitach. W przeciwnym razie przypadek nie ma znaczenia ani nie zawiera dodatkowych spacji.

## <a name="next-steps"></a>Następne kroki

- [Jak tworzyć, importować i eksportować terminy w słowniku](how-to-create-import-export-glossary.md)
- [Jak zarządzać szablonami terminów dla słownika biznesowego](how-to-manage-term-templates.md)
