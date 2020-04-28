---
title: Wyświetlanie magazynu wiedzy (wersja zapoznawcza) za pomocą Eksplorator usługi Storage
titleSuffix: Azure Cognitive Search
description: Przeglądaj i Analizuj Wyszukiwanie poznawcze sklepu z bazami danych platformy Azure, korzystając z Eksplorator usługi Storage Azure Portal. Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: 167316eca1f85530a040d4543f98ae34a9fb93c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75754064"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Wyświetlanie sklepu z bazami danych za pomocą Eksplorator usługi Storage

> [!IMPORTANT] 
> Magazyn wiedzy jest obecnie w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zapewnia funkcje w wersji zapoznawczej. Dostępna jest obecnie ograniczona obsługa portalu i nie ma obsługi zestawu SDK platformy .NET.

W tym artykule przedstawiono przykład sposobu nawiązywania połączenia z magazynem wiedzy i eksplorowania go przy użyciu Eksplorator usługi Storage w Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

+ Wykonaj kroki opisane w temacie [Tworzenie magazynu wiedzy w Azure Portal](knowledge-store-create-portal.md) , aby utworzyć przykładowy magazyn wiedzy używany w tym instruktażu.

+ Potrzebna będzie również nazwa konta usługi Azure Storage, które zostało użyte do utworzenia magazynu wiedzy, wraz z jego kluczem dostępu z poziomu Azure Portal.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Wyświetlanie, edytowanie i tworzenie zapytań dotyczących sklepu z bazami danych w Eksplorator usługi Storage

1. W Azure Portal [Otwórz konto magazynu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) używane do tworzenia sklepu z bazami danych.

1. W lewym okienku nawigacji konta magazynu kliknij przycisk **Eksplorator usługi Storage**.

1. Rozwiń listę **tabele** , aby wyświetlić listę projekcji tabeli platformy Azure, które zostały utworzone podczas uruchamiania kreatora **importu danych** na przykładowych danych dotyczących usług hotelowych.

Wybierz dowolną tabelę, aby wyświetlić wzbogacone dane, w tym kluczowe frazy i wyniki tonacji.

   ![Wyświetl tabele w Eksplorator usługi Storage](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Wyświetl tabele w Eksplorator usługi Storage")

Aby zmienić typ danych dla dowolnej wartości tabeli lub zmienić poszczególne wartości w tabeli, kliknij przycisk **Edytuj**. Zmiana typu danych dla dowolnej kolumny w jednym wierszu tabeli zostanie zastosowana do wszystkich wierszy.

   ![Edytuj tabelę w Eksplorator usługi Storage](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Edytuj tabelę w Eksplorator usługi Storage")

Aby uruchomić zapytania, kliknij pozycję **zapytanie** na pasku poleceń, a następnie wprowadź warunki.  

   ![Tabela zapytań w Eksplorator usługi Storage](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Tabela zapytań w Eksplorator usługi Storage")

## <a name="clean-up"></a>Czyszczenie

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit.

## <a name="next-steps"></a>Następne kroki

Połącz ten magazyn wiedzy, aby Power BI w celu uzyskania dokładniejszej analizy lub przechodź do przodu przy użyciu kodu, korzystając z interfejsu API REST i programu Poster, aby utworzyć inny magazyn wiedzy.

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia przy użyciu Power BI](knowledge-store-connect-power-bi.md)
> [Tworzenie sklepu z bazami danych w usłudze REST](knowledge-store-create-rest.md)
