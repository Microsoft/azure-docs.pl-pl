---
title: Jak odnajdywać źródła danych w Azure Data Catalog
description: W tym artykule opisano sposób odnajdywania zarejestrowanych zasobów danych za pomocą Azure Data Catalog, w tym wyszukiwanie i filtrowanie oraz korzystanie z funkcji wyróżniania trafień w portalu Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 4eb689b17df8236a00b5914912d1927804944f25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081206"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Jak odnajdywać źródła danych w Azure Data Catalog

## <a name="introduction"></a>Wprowadzenie

Azure Data Catalog to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i odnajdywania źródeł danych przedsiębiorstwa. Innymi słowy, Data Catalog ułatwia osobom odnajdywanie, poznawanie i używanie źródeł danych. Ułatwia ona organizacjom uzyskanie większej wartości z istniejących danych. Po zarejestrowaniu źródła danych w Data Catalog metadane są indeksowane przez usługę, dzięki czemu można łatwo wyszukiwać dane, które są potrzebne.

## <a name="searching-and-filtering"></a>Wyszukiwanie i filtrowanie danych

Odnajdywanie w Data Catalog używa dwóch podstawowych mechanizmów: wyszukiwania i filtrowania.

Wyszukiwanie zaprojektowano pod kątem intuicyjnej obsługi i zaawansowanych możliwości. Domyślnie wyszukiwane terminy są dopasowywane do wszystkich właściwości w wykazie, w tym adnotacji wprowadzonych przez użytkownika.

Filtrowanie służy jako uzupełnienie wyszukiwania. Można wybrać konkretne cechy, takie jak eksperci, typ źródła danych, typ obiektu i Tagi. Można wyświetlić tylko pasujące zasoby danych i ograniczyć wyniki wyszukiwania do pasujących elementów zawartości.

Korzystając z kombinacji wyszukiwania i filtrowania, można szybko poruszać się w źródłach danych, które zostały zarejestrowane za pomocą Data Catalog w celu odnajdywania potrzebnych źródeł danych.

## <a name="search-syntax"></a>Składnia wyszukiwania

Chociaż domyślne wyszukiwanie tekstu swobodnego jest proste i intuicyjne, można również użyć składni wyszukiwania Data Catalog, aby uzyskać większą kontrolę nad wynikami wyszukiwania. Data Catalog Search obsługuje następujące techniki:

| Technika | Zastosowanie | Przykład |
| --- | --- | --- |
| Wyszukiwanie podstawowe |Podstawowe wyszukiwanie używające co najmniej jednego wyszukiwanego terminu. Wyniki są dowolnymi zasobami, które pasują do dowolnej właściwości z co najmniej jednym określonym warunkiem. |`sales data` |
| Określanie zakresu właściwości |Zwracaj tylko źródła danych, w których termin wyszukiwania jest dopasowywany do określonej właściwości. |`name:finance` |
| Operatory logiczne |Poszerzyć lub Zawęź wyszukiwanie przy użyciu operacji logicznych. |`finance NOT corporate` |
| Grupowanie za pomocą nawiasów |Użyj nawiasów, aby zgrupować części zapytania w celu uzyskania izolacji logicznej, szczególnie w połączeniu z operatorami logicznymi. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Operatory porównania |W przypadku właściwości, które mają typy danych liczbowych i dat, należy stosować porównania inne niż równość. |`modifiedTime > "11/05/2014"` |

Aby uzyskać więcej informacji na temat wyszukiwania Data Catalog, zobacz artykuł [Azure Data Catalog](/rest/api/datacatalog/#search-syntax-reference) .

## <a name="hit-highlighting"></a>Wyróżnianie trafień

Gdy przeglądasz wyniki wyszukiwania, wszystkie wyświetlane właściwości, które pasują do określonych warunków wyszukiwania (takich jak nazwa zasobu danych, opis i Tagi), zostaną wyróżnione w celu ułatwienia zidentyfikowania, dlaczego dany zasób danych został zwrócony przez dane wyszukiwanie.

> [!NOTE]
> Aby wyłączyć podświetlanie trafień, użyj przełącznika **Wyróżnij** w portalu Data Catalog.

Wyświetlanie wyników wyszukiwania może nie zawsze być oczywiste, dlaczego zasób danych jest uwzględniony, nawet z włączonym zaznaczaniem trafień. Ponieważ wszystkie właściwości są domyślnie przeszukiwane, zasób danych może zostać zwrócony z powodu dopasowania właściwości na poziomie kolumny. Ponieważ wielu użytkowników może dodawać adnotacje do zarejestrowanych zasobów danych przy użyciu ich własnych tagów i opisów, nie wszystkie metadane są wyświetlane na liście wyników wyszukiwania.

W domyślnym widoku kafelków każdy kafelek wyświetlany w wynikach wyszukiwania zawiera ikonę **Wyświetl termin wyszukania** , dzięki czemu możesz szybko wyświetlić liczbę dopasowań i ich lokalizację, a następnie przeskoczyć do nich, jeśli chcesz.

 ![Wyróżnianie trafień i wyszukiwanie w portalu Azure Data Catalog](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Podsumowanie

Ponieważ rejestracja źródła danych za pomocą Data Catalog kopiuje dane strukturalne i opisowe metadanych ze źródła danych do usługi katalogowej, źródło danych jest łatwiejsze do odnalezienia i zrozumienia. Po zarejestrowaniu źródła danych można je wykryć przy użyciu funkcji filtrowania i wyszukiwania w portalu Data Catalog.

## <a name="next-steps"></a>Następne kroki

* Szczegółowe informacje na temat odnajdywania źródeł danych znajdują się w temacie Wprowadzenie [do Azure Data Catalog](data-catalog-get-started.md).
