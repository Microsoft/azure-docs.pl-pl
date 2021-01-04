---
title: 'Samouczek: przeglądanie zasobów w usłudze Azure kontrolą i przeglądanie ich elementów ich pochodzenia'
description: W tym samouczku opisano, jak przeglądać zasoby w wykazie i wyświetlać elementy powiązane z danymi.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 7ffbe2ded44ded4f580655f6ae9e98391490f94a
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696100"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>Samouczek: przeglądanie zasobów w usłudze Azure kontrolą (wersja zapoznawcza) i przeglądanie ich elementów ich pochodzenia

> [!IMPORTANT]
> Usługa Azure kontrolą jest obecnie dostępna w wersji zapoznawczej. Dodatkowe [warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych programu Microsoft Azure to m.in. Postanowienia prawne dotyczące funkcji systemu Azure, które są dostępne w wersjach beta, Preview lub w inny sposób nie są jeszcze ogólnie udostępniane.

W tym samouczku pokazano, jak przeglądać zasoby w usłudze Azure kontrolą i wyświetlać ich ważne szczegóły, takie jak elementy zależne.

Ten samouczek jest *częścią 3 serii samouczków z pięcioma częścią* , w której przedstawiono podstawowe informacje dotyczące zarządzania nadzorem danych w ramach obszaru danych przy użyciu usługi Azure kontrolą. W tym samouczku przedstawiono pracę wykonaną w [części 2: nawigowanie po stronie głównej usługi Azure kontrolą (wersja zapoznawcza) i wyszukiwanie elementu zawartości](tutorial-asset-search.md).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>
> * Przeglądaj w poszukiwaniu zasobów w katalogu.
> * Wyświetl elementy zawartości.

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [Samouczek: Przejdź do strony głównej usługi Azure kontrolą (wersja zapoznawcza) i Wyszukaj element zawartości](tutorial-asset-search.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="browse-for-assets-in-the-catalog"></a>Przeglądanie w poszukiwaniu zasobów w katalogu

W poprzednim samouczku przedstawiono sposób, w jaki usługa Search ułatwia odnajdywanie zasobów w katalogu usługi Azure kontrolą. Innym sposobem odnajdywania zasobów w katalogu jest użycie funkcji przeglądania katalogu.

W tej sekcji pokazano, jak przeglądać katalog usługi Azure kontrolą.

1. Przejdź do zasobu usługi Azure kontrolą w Azure Portal i wybierz pozycję **Otwórz kontrolą Studio**. Nastąpi automatyczne przekierowanie do strony głównej programu kontrolą Studio.

1. Na stronie **głównej** wybierz pozycję **Przeglądaj zasoby**.

   Zostanie wyświetlona strona **przeglądanie zasobów** , w której wyświetlane jest podsumowanie wszystkich typów zasobów w katalogu.

1. Aby poznać różne Azure Data Lake zasobów typu Gen2 dostępne w katalogu, wybierz kafelek **Azure Data Lake Gen2** .

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="Zrzut ekranu przedstawiający stronę &quot;przeglądanie według typu zasobów&quot; z wybraną pozycją Azure Data Lake Gen2.":::

1. Jeśli istnieje wiele zasobów, możesz wybrać nagłówek kolumny **Nazwa** , aby posortować elementy zawartości alfabetycznie. W tym samouczku istnieje tylko jeden Azure Data Lake Storage Gen2 element zawartości.

1. Wybierz nazwę elementu zawartości.

1. Wybierz zestaw zasobów **Contoso_GrossProfit_ {N}. SSV** . Jeśli ten zasób nie istnieje w katalogu, wybierz inny.

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Lista zasobów Azure Data Lake Storage Gen2":::

## <a name="view-the-lineage-of-assets"></a>Wyświetlanie elementów zawartości

Na stronie Szczegóły zasobu Przejrzyj źródło danych.

1. Wybierz kartę **elementy** powiązane z zestawem zasobów **Contoso_GrossProfit_ {N}. SSV** .

   Zostanie wyświetlony wybrany element zawartości. Widoczne informacje dotyczące elementów pokazanych pokazują, że ten zestaw zasobów został skopiowany z konta usługi Azure Blob Storage do Azure Data Lake Storage Gen2.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="Zrzut ekranu przedstawiający widok elementu zawartości.":::

1. Wybierz element zawartości obiektu BLOB na tej stronie, a następnie wybierz łącze **Przełącz do elementu zawartości** .

   Zwróć uwagę, że okno zostało przełączone do zestawu zasobów obiektów blob platformy Azure, który był źródłem oryginalnego Azure Data Lake Storage Gen2.

1. Wybierz kartę **Przegląd** , aby zbadać element zawartości i potwierdzić jego szczegóły.

Aby uzyskać informacje na temat sposobu tworzenia Azure Data Factory działania przepływu danych między obiektem blob platformy Azure i zestawem zasobów Azure Data Lake Storage Gen2 i obserwowanie elementu powiązanego, zobacz [Azure Data Factory aktywność przepływu danych](../data-factory/concepts-data-flow-overview.md).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Przeglądaj w poszukiwaniu zasobów w katalogu.
> * Wyświetl elementy zawartości.

Przejdź do następnego samouczka, aby poznać zestawy zasobów, szczegóły zasobów, schematy i klasyfikacje.

> [!div class="nextstepaction"]
> [Zestawy zasobów, szczegóły zasobów, schematy i klasyfikacje](tutorial-schemas-and-classifications.md)
