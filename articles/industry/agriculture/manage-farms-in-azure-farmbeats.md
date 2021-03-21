---
title: Zarządzanie farmami
description: Opisuje sposób zarządzania farmami
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 050b3b4d67eda9b6c9b4621c014e3e6baad34053
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173804"
---
# <a name="manage-farms"></a>Zarządzanie farmami

Farmami można zarządzać w usłudze Azure FarmBeats. Ten artykuł zawiera informacje dotyczące sposobu tworzenia Farm, instalowania urządzeń, czujników i dronom, które ułatwiają zarządzanie farmami.

## <a name="create-farms"></a>Tworzenie Farm

Wykonaj następujące kroki:

1. Zaloguj się do akceleratora farmy. zostanie wyświetlona strona **farmy** .
    Na stronie **farmy** zostanie wyświetlona lista Farm na wypadek, gdyby zostały już utworzone w ramach subskrypcji.

    Oto przykład obrazu:

    ![Zrzut ekranu przedstawiający stronę farmy.](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Wybierz pozycję **Utwórz farmę** i podaj **nazwę**, **uprawy** i **adres**.
3. W obszarze **Zdefiniuj granicę farmy**(pole obowiązkowe) wybierz opcję **Oznacz na mapie** lub **Wklej kod GEOJSON**.

Poniżej przedstawiono dwa sposoby definiowania granic farmy:

1. **Oznacz na mapie**: Użyj narzędzia mapowania mapy, aby narysować i oznaczyć granicę farmy. Aby oznaczyć granice,  ![ zrzut ekranu pokazujący ikonę ołówka na potrzeby rysowania granic na mapie ](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) i Oznacz dokładne granice.

    ![Zrzut ekranu pokazujący rysowane granice na mapie.](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **Wklej kod GEOJSON**: GEOJSON to format kodowania geograficznych struktur danych przy użyciu JavaScript Object Notation (JSON). Ta opcja powoduje wyświetlenie pola tekstowego, w którym można wprowadzić ciąg GEOJSON w celu oznaczenia granic farmy. Możesz również utworzyć kod GEOJSON z GeoJSON.io.
Użyj etykietek narzędzi, aby ułatwić wprowadzanie informacji.

    ![Zrzut ekranu, który podświetla opcję wklejania kodu GEOJSON na ekranie tworzenie farmy.](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  Wybierz pozycję **Prześlij** , aby utworzyć farmę. Nowa Farma zostanie utworzona i wyświetlona na stronie **farmy** .

## <a name="view-farm"></a>Wyświetl farmę

Na stronie Lista farmy zostanie wyświetlona lista utworzonych Farm. Wybierz farmę, aby wyświetlić listę:

 - **Liczba urządzeń** — wyświetla liczbę i stan urządzeń wdrożonych w farmie.
 - **Map** — Mapa farmy z urządzeniami wdrożonymi w farmie.
 - **Telemetrię** — wyświetla dane telemetryczne z czujników wdrożonych w farmie.
 - **Najnowsze mapy dokładności** — wyświetla najnowszą mapę indeksów satelitarnych (Evi, NDWI), mapę cieplną wilgotności gleby i mapę położenia czujnika.

## <a name="edit-farm"></a>Edytowanie farmy

Na stronie **farmy** zostanie wyświetlona lista utworzonych Farm.

1.  Wybierz farmę, aby wyświetlić i edytować farmę.
2.  Wybierz pozycję **Edytuj farmę** , aby edytować informacje o farmie. W oknie **szczegóły farmy** można edytować **nazwy**, **uprawy**, **adresy** i definiować pola **granic farmy** .

    ![Farmy projektów](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. Wybierz pozycję **Prześlij** , aby zapisać edytowane dane.

## <a name="delete-farm"></a>Usuwanie farmy

Na stronie **farmy** zostanie wyświetlona lista utworzonych Farm. Aby usunąć farmę, wykonaj następujące czynności:

1.  Wybierz farmę z listy, aby usunąć szczegóły farmy.
2.  Wybierz pozycję **Usuń farmę** , aby usunąć farmę.

    ![Zrzut ekranu pokazujący ekran usuwanie farmy i wyróżniony przycisk Usuń.](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > Po usunięciu farmy urządzenia i mapy skojarzone z farmą nie są usuwane. Wszystkie informacje farmy skojarzone z urządzeniem i mapami nie będą miały znaczenia. Możesz nadal wyświetlać urządzenia, dane telemetryczne i mapy z usługi FarmBeats.


## <a name="next-steps"></a>Następne kroki

Teraz, po utworzeniu farmy, Dowiedz się, jak [uzyskać dane czujników](get-sensor-data-from-sensor-partner.md) przepływające do farmy.
