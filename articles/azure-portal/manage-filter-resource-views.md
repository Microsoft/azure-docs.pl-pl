---
title: Wyświetlanie i filtrowanie informacji o zasobach platformy Azure
description: Filtruj informacje i korzystaj z różnych widoków, aby lepiej zrozumieć zasoby platformy Azure.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: bb48d0b0a7bf6017fbf407a95c33ef17729e34e3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771638"
---
# <a name="view-and-filter-azure-resource-information"></a>Wyświetlanie i filtrowanie informacji o zasobach platformy Azure

Azure Portal pozwala przeglądać szczegółowe informacje o zasobach w ramach subskrypcji platformy Azure. W tym artykule opisano sposób filtrowania informacji i używania różnych widoków w celu lepszego zrozumienia zasobów.

Artykuł koncentruje się na ekranie **wszystkie zasoby** , który został przedstawiony na poniższym zrzucie ekranu. Ekrany poszczególnych typów zasobów, takich jak maszyny wirtualne, mają różne opcje, takie jak uruchamianie i zatrzymywanie maszyny wirtualnej.

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="Widok Azure Portal wszystkich zasobów":::

## <a name="filter-resources"></a>Filtruj zasoby

Rozpocznij Eksplorowanie **wszystkich zasobów** przy użyciu filtrów, aby skoncentrować się na podzbiorze zasobów. Poniższy zrzut ekranu przedstawia filtrowanie grup zasobów, wybierając dwie z sześciu grup zasobów w ramach subskrypcji.

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="Widok filtru oparty na grupach zasobów":::

Można łączyć filtry, w tym na podstawie wyszukiwania tekstu, jak pokazano na poniższym zrzucie ekranu. W takim przypadku wyniki są objęte zakresem zasobów, które zawierają "SimpleWinVM" w jednej z dwóch już wybranych grup zasobów.

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="Widok filtru na podstawie wpisu tekstu":::

Aby zmienić kolumny, które znajdują się w widoku, wybierz pozycję **Zarządzaj widokiem** , a następnie **Edytuj kolumny**.

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="Edytuj kolumny wyświetlane w widoku":::

## <a name="save-use-and-delete-views"></a>Zapisywanie, używanie i usuwanie widoków

Możesz zapisać widoki, które zawierają wybrane filtry i kolumny. Aby zapisać widok i korzystać z niego:

1. Wybierz pozycję **Zarządzaj widokiem** i **Zapisz widok**.

1. Wprowadź nazwę widoku, a następnie wybierz przycisk **OK**. Zapisany widok jest teraz wyświetlany w menu **Zarządzaj widokiem** .

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="Zapisany widok":::

1. Aby użyć widoku, przełącz się między **domyślny** i jednym z własnych widoków, aby zobaczyć, jak ma to wpływ na listę wyświetlanych zasobów.

Aby usunąć widok:

1. Wybierz pozycję **Zarządzaj widok** , a następnie **Przeglądaj wszystkie widoki**.

1. W okienku **zapisane widoki** wybierz widok, a następnie wybierz ikonę **Usuń** Wyświetl ikonę ![ ](media/manage-filter-resource-views/icon-delete.png) .

## <a name="export-information-from-a-view"></a>Eksportuj informacje z widoku

Informacje o zasobach można wyeksportować z widoku. Aby wyeksportować informacje w formacie CSV:

1. Wybierz pozycję **Eksportuj do pliku CSV**.

    :::image type="content" source="media/manage-filter-resource-views/export-csv.png" alt-text="Zrzut ekranu przedstawiający Eksportowanie do formatu CSV":::

1. Zapisz plik lokalnie, a następnie otwórz go w programie Excel lub innej aplikacji obsługującej format CSV. 

Podczas poruszania się po portalu zobaczysz inne obszary, w których można eksportować informacje, takie jak pojedyncze grupy zasobów.

## <a name="summarize-resources-with-visuals"></a>Podsumowywanie zasobów za pomocą wizualizacji

Oglądane przez nas widoki zostały uwzględnione w _widokach listy_, ale istnieją również _widoki podsumowania_ zawierające wizualizacje. Możesz zapisywać te widoki i używać ich w taki sam sposób, jak w przypadku widoków listy. Filtry są utrwalane między dwoma typami widoków. Istnieją Standardowe widoki, takie jak widok **lokalizacji** przedstawiony poniżej, a także widoki, które są istotne dla określonych usług, takich jak widok **stanu** usługi Azure Storage.

:::image type="content" source="media/manage-filter-resource-views/summary-map.png" alt-text="Podsumowanie zasobów w widoku mapy":::

Aby zapisać widok podsumowania i korzystać z niego:

1. Z menu Widok wybierz pozycję **Widok podsumowania**.

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="Menu Widok podsumowania":::

1. Widok podsumowania umożliwia Podsumowanie według różnych atrybutów, w tym **lokalizacji** i **typu**. Wybierz opcję **Podsumowanie według** i odpowiednią wizualizację. Poniższy zrzut ekranu przedstawia **Podsumowanie typu** za pomocą wizualizacji **wykresu słupkowego** .

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="Podsumowanie typu przedstawiające wykres słupkowy":::

1. Wybierz pozycję **Zarządzaj widok** i **Zapisz** , aby zapisać ten widok tak jak w widoku listy.

1. W widoku Podsumowanie, w obszarze **Podsumowanie typu**, wybierz pasek na wykresie. Wybranie paska zawiera listę przefiltrowanych w dół do jednego typu zasobu.

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="Wszystkie zasoby filtrowane według typu":::

## <a name="run-queries-in-azure-resource-graph"></a>Uruchamianie zapytań na wykresie zasobów platformy Azure

Wykres zasobów platformy Azure umożliwia wydajne i wydajną eksplorację zasobów dzięki możliwości wykonywania zapytań na dużą skalę między zestawem subskrypcji. Ekran **wszystkie zasoby** w Azure Portal zawiera link umożliwiający otwarcie kwerendy wykresu zasobów należącej do zakresu bieżącego widoku filtrowanego.

Aby uruchomić zapytanie grafu zasobów:

1. Wybierz pozycję **Otwórz zapytanie**.

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="Otwórz zapytanie grafu zasobów platformy Azure":::

1. W **Eksploratorze Azure Resource Graph** wybierz pozycję **Uruchom zapytanie** , aby zobaczyć wyniki.

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="Uruchom zapytanie grafu zasobów platformy Azure":::

    Aby uzyskać więcej informacji, zobacz [Uruchamianie pierwszego zapytania grafu zasobów przy użyciu Eksploratora Azure Resource Graph](../governance/resource-graph/first-query-portal.md).

## <a name="next-steps"></a>Następne kroki

[Azure Portal — omówienie](azure-portal-overview.md)

[Tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure Portal](azure-portal-dashboards.md)
