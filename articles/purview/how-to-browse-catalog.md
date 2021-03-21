---
title: 'Instrukcje: przeglądanie Data Catalog'
description: Ten artykuł zawiera omówienie sposobu przeglądania Data Catalog Azure kontrolą na podstawie typu zasobu.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: dd2fd4fbc9d8aecd9821d668dd02bd4b68659cf6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97695062"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Przeglądanie wykazu danych usługi Azure kontrolą

W tym artykule opisano sposób odnajdywania danych w usłudze Azure kontrolą Data Catalog przy użyciu hierarchicznej przestrzeni nazw źródła danych.

## <a name="browse-experience"></a>Przeglądanie środowiska

Odbiorca danych może odnajdywać dane przy użyciu znanej hierarchicznej przestrzeni nazw dla każdego źródła danych przy użyciu widoku Eksploratora. Po zarejestrowaniu i przeskanowaniu źródła danych Mapa danych wyodrębnia informacje o strukturze (hierarchicznej przestrzeni nazw) źródła danych. Te informacje służą do tworzenia środowiska przeglądania w celu odnajdywania danych.

Na przykład można łatwo znaleźć zestaw danych o nazwie *DateDimension* w folderze o nazwie *Dimensions* w usłudze Azure Data Lake Storage Gen 2. Możesz użyć środowiska "Przeglądaj według typu zasobu", aby przejść do konta magazynu ADLS generacji 2, a następnie przejść do folderu usługi > kontener >, aby uzyskać dostęp do folderu o określonych *wymiarach* , a następnie wyświetlić tabelę *DateDimension* .

Natywne środowisko przeglądania z hierarchiczną przestrzenią nazw jest dostarczane dla każdego z odpowiednich źródeł danych.

## <a name="browse-the-data-catalog-by-asset-type"></a>Przeglądaj Data Catalog według typu zasobu

1. Możesz przeglądać zasoby danych, wybierając **Typ Przeglądaj według zasobu** na stronie głównej.

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Strona główna kontrolą" border="true":::

1. Na stronie **przeglądanie typów zasobów** kafelki są podzielone na kategorie według źródła danych. Aby dowiedzieć się więcej na temat zasobów w każdym źródle danych, wybierz odpowiedni kafelek.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="Strona przeglądania typów zasobów" border="true":::

1. Na następnej stronie zostaną wyświetlone zasoby najwyższego poziomu w ramach wybranego typu danych. Wybierz jeden z zasobów, aby jeszcze bardziej eksplorować jego zawartość.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="Strona przeglądania specyficzna dla typu elementu zawartości. Pokazany przykład to konto usługi Azure Storage" border="true":::

1. Zostanie otwarty widok Eksploratora. Rozpocznij przeglądanie, wybierając element zawartości na panelu po lewej stronie. Elementy podrzędne są wyświetlane w prawym panelu strony.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="Widok Eksploratora" border="true":::

1. Aby wyświetlić szczegóły elementu zawartości, wybierz pozycję Nazwa lub przycisk wielokropka po prawej stronie.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="Wybierz przycisk wielokropka, aby wyświetlić stronę szczegółów zasobu" border="true":::

## <a name="view-related-data-assets"></a>Wyświetlanie powiązanych zasobów danych

Po umieszczeniu na stronie szczegółów zasobu można także wyświetlić inne powiązane zasoby danych. Na przykład możesz przejść do folderu nadrzędnego elementu *DateDimension* , aby wyświetlić folder *Dimensions* , a nawet przejść do kontenera, aby wyświetlić zasoby w ramach określonej hierarchii.

1. Na stronie Szczegóły zasobu wybierz kartę **pokrewne** , aby wyświetlić inne powiązane zasoby.

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="Uruchom powiązaną kartę" border="true":::

1. Pełna hierarchia bieżącego elementu zawartości zostanie wyświetlona po lewej stronie.

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="Struktura hierarchiczna" border="true":::

1. Wybierz dowolny poziom w hierarchii, aby wyświetlić listę bezpośrednich zasobów na tym poziomie.

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="Wybierz inną hierarchię" border="true":::

## <a name="next-steps"></a>Następne kroki

- [Tworzenie, importowanie i eksportowanie terminów słownika](how-to-create-import-export-glossary.md)
- [Zarządzanie szablonami terminów dla słownika biznesowego](how-to-manage-term-templates.md)
