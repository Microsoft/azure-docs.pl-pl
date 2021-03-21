---
title: Jak dodać zestawy danych referencyjnych do środowiska — Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano sposób dodawania zestawu danych referencyjnych do rozszerzania danych w środowisku Azure Time Series Insightsu.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: d80d97a609aa3a464b9b114439fe7f4058e287c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001834"
---
# <a name="create-a-reference-data-set-for-your-azure-time-series-insights-gen1-environment-using-the-azure-portal"></a>Utwórz zestaw danych referencyjnych dla środowiska Azure Time Series Insights Gen1 przy użyciu Azure Portal

> [!CAUTION]
> To jest artykuł Gen1.

W tym artykule opisano sposób dodawania zestawu danych referencyjnych do środowiska Azure Time Series Insightsowego. Dane referencyjne są przydatne do łączenia się z danymi źródłowymi w celu rozszerzenia wartości.

Zestaw danych referencyjnych to kolekcja elementów, które rozszerzają zdarzenia ze źródła zdarzenia. Azure Time Series Insights aparat transferu danych przychodzących łączy każde zdarzenie ze źródła zdarzeń z odpowiednim wierszem danych w zestawie danych referencyjnych. To rozszerzone zdarzenie jest następnie dostępne dla zapytania. To sprzężenie jest oparte na kolumnach kluczy podstawowych zdefiniowanych w zestawie danych referencyjnych.

Dane referencyjne nie są przyłączone wstecz. W takim przypadku tylko bieżące i przyszłe dane przychodzące są dopasowywane i dołączane do zestawu dat odwołania po jego skonfigurowaniu i przekazaniu.

## <a name="video"></a>Wideo

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Dowiedz się więcej o modelu danych referencyjnych usługi Time Series Insights.</br>

> [!VIDEO <https://www.youtube.com/embed/Z0NuWQUMv1o>]

## <a name="add-a-reference-data-set"></a>Dodawanie zestawu danych referencyjnych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź istniejące środowisko Azure Time Series Insights. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie Azure Portal. Wybierz środowisko Azure Time Series Insights.

1. Wybierz stronę **Overview** (Przegląd). Rozwiń sekcję **podstawowe** informacje w górnej części strony, aby znaleźć **adres URL Eksploratora Time Series Insights** i otworzyć link.  

   [![Rozwiń sekcję podstawowe informacje](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Wyświetl Eksploratora dla środowiska Azure Time Series Insightsowego.

1. Rozwiń selektor środowiska w Eksploratorze Azure Time Series Insights. Wybierz aktywne środowisko. Wybierz ikonę dane referencyjne w prawym górnym rogu strony Eksploratora.

   [![Dodaj dane referencyjne](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Wybierz przycisk **+ Dodaj zestaw danych** , aby rozpocząć dodawanie nowego zestawu danych.

   [![Dodaj zestaw danych](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. Na stronie **Nowy zestaw danych referencyjnych** wybierz format danych:

   - Wybierz opcję **CSV** dla danych rozdzielanych przecinkami. Pierwszy wiersz jest traktowany jako wiersz nagłówka.
   - Wybierz **tablicę JSON** dla danych sformatowanych w formacie JavaScript Object Notation (JSON).

   [![Wybierz format danych.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Podaj dane przy użyciu jednej z dwóch metod:

   - Wklej dane do edytora tekstu. Następnie wybierz przycisk **Analizuj dane referencyjne** .
   - Wybierz przycisk **Wybierz plik** , aby dodać dane z lokalnego pliku tekstowego.

   Na przykład wklej dane CSV: [ ![ wklejone dane CSV](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Na przykład wklej dane tablicy JSON: [ ![ Wklej dane JSON](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Jeśli wystąpi błąd podczas analizowania wartości danych, błąd pojawia się na czerwono u dołu strony, na przykład `CSV parsing error, no rows extracted` .

1. Po pomyślnym przeanalizowaniu danych zostanie wyświetlona siatka danych wyświetlająca kolumny i wiersze reprezentujące dane. Przejrzyj siatkę danych, aby upewnić się, że jest to prawidłowa.

   [![Przejrzyj dane referencyjne](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Przejrzyj każdą kolumnę, aby zrozumieć przyjęty typ danych, a jeśli to konieczne, w razie potrzeby zmień typ danych.  Wybierz symbol typu danych w nagłówku kolumny: **#** dla podwójnej (dane liczbowe), **T | F** dla wartości logicznej lub **ABC** dla ciągu.

   [![Wybierz typy danych w nagłówkach kolumn.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. W razie konieczności zmień nazwy nagłówków kolumn. Nazwa kolumny klucza jest konieczna do przyłączenia do odpowiedniej właściwości w źródle zdarzenia.

   > [!IMPORTANT]
   > Upewnij się, że nazwy kolumn klucza danych referencyjnych są zgodne z nazwą zdarzenia do danych przychodzących, z uwzględnieniem wielkości liter. Nazwy kolumn niebędących kluczami służą do rozszerzania danych przychodzących z odpowiednimi wartościami danych referencyjnych.

1. Wpisz wartość w polu **Filtruj wiersze...** , aby przejrzeć określone wiersze zgodnie z wymaganiami. Filtr jest przydatny do przeglądania danych, ale nie jest stosowany podczas przekazywania danych.

1. Nazwij zestaw danych, wypełniając pole **Nazwa zestawu danych** powyżej siatki danych.

    [![Nadaj nazwę zestawowi danych.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Podaj kolumnę **klucza podstawowego** w zestawie danych, wybierając listę rozwijaną powyżej siatki danych.

    [![Wybierz kolumny klucza.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Opcjonalnie)** Wybierz **+** przycisk, aby dodać kolumnę klucza pomocniczego jako złożony klucz podstawowy. Jeśli musisz cofnąć zaznaczenie, wybierz z listy rozwijanej wartość pustą, aby usunąć klucz pomocniczy.

1. Aby przekazać dane, wybierz przycisk **Przekaż wiersze** .

    [![Przekaż wiersze i Potwierdź dane.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    Strona potwierdza ukończone przekazywanie i wyświetla komunikat **pomyślnie przekazano zestaw danych**.

    > [!WARNING]
    > Kolumny lub właściwości, które są współużytkowane przez zestawy danych referencyjnych, będą wyświetlały **zduplikowany błąd przekazywania nazwy właściwości** . Błąd nie będzie uniemożliwiać pomyślnego przekazania zestawów danych referencyjnych. Można go usunąć przez połączenie wierszy, które udostępniają zduplikowaną nazwę właściwości.

1. Wybierz pozycję **Dodaj wiersz**, **Importuj zbiorczo wiersze** lub **Dodaj kolumnę** , aby dodać więcej wartości danych referencyjnych w razie potrzeby.

    [![Dodaj wiersz, Importuj zbiorczo wiersze lub Dodaj kolumnę.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Wszystkie wiersze, które współużytkują unikatowy klucz z innym wierszem, zostaną przesłonięte przez ostatni dodany wiersz, który udostępni Ten unikatowy klucz.

   > [!NOTE]
   > Dodane wiersze **nie** muszą być *prostokątne* — mogą mieć mniej, większe lub różne kolumny od innych wpisów w zestawie danych referencyjnych.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie danymi referencyjnymi](time-series-insights-manage-reference-data-csharp.md) na drodze programowej.

- Aby uzyskać pełne informacje o interfejsie API, Przeczytaj dokument dotyczący [interfejsu API danych referencyjnych](/rest/api/time-series-insights/gen1-reference-data-api) .