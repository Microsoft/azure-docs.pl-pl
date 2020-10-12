---
title: Zarządzanie farmami
description: Opisuje sposób zarządzania farmami
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85847476"
---
# <a name="manage-farms"></a>Zarządzanie farmami

Farmami można zarządzać w usłudze Azure FarmBeats. Ten artykuł zawiera informacje dotyczące sposobu tworzenia Farm, instalowania urządzeń, czujników i dronom, które ułatwiają zarządzanie farmami.

## <a name="create-farms"></a>Tworzenie Farm

Wykonaj następujące kroki:

1. Zaloguj się do akceleratora farmy. zostanie wyświetlona strona **farmy** .
    Na stronie **farmy** zostanie wyświetlona lista Farm na wypadek, gdyby zostały już utworzone w ramach subskrypcji.

    Oto przykład obrazu:

    ![Farmy projektów](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Wybierz pozycję **Utwórz farmę** i podaj **nazwę**, **uprawy** i **adres**.
3. W obszarze **Zdefiniuj granicę farmy**(pole obowiązkowe) wybierz opcję **Oznacz na mapie** lub **Wklej kod GEOJSON**.

Poniżej przedstawiono dwa sposoby definiowania granic farmy:

1. **Oznacz na mapie**: Użyj narzędzia mapowania mapy, aby narysować i oznaczyć granicę farmy. Aby oznaczyć granice,  ![ farmy projektu/ ](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) i oznaczyć dokładne granice.

    ![Farmy projektów](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **Wklej kod GEOJSON**: GEOJSON to format kodowania geograficznych struktur danych przy użyciu JavaScript Object Notation (JSON). Ta opcja powoduje wyświetlenie pola tekstowego, w którym można wprowadzić ciąg GEOJSON w celu oznaczenia granic farmy. Możesz również utworzyć kod GEOJSON z GeoJSON.io.
Użyj etykietek narzędzi, aby ułatwić wprowadzanie informacji.

    ![Farmy projektów](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

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
2.  Wybierz pozycję **Edytuj farmę** , aby edytować informacje o farmie. W oknie **szczegóły farmy** można edytować **nazwy**, **uprawy**, **adresy**i definiować pola **granic farmy** .

    ![Farmy projektów](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. Wybierz pozycję **Prześlij** , aby zapisać edytowane dane.

## <a name="delete-farm"></a>Usuwanie farmy

Na stronie **farmy** zostanie wyświetlona lista utworzonych Farm. Aby usunąć farmę, wykonaj następujące czynności:

1.  Wybierz farmę z listy, aby usunąć szczegóły farmy.
2.  Wybierz pozycję **Usuń farmę** , aby usunąć farmę.

    ![Farmy projektów](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > Po usunięciu farmy urządzenia i mapy skojarzone z farmą nie są usuwane. Wszystkie informacje farmy skojarzone z urządzeniem i mapami nie będą miały znaczenia. Możesz nadal wyświetlać urządzenia, dane telemetryczne i mapy z usługi FarmBeats.


## <a name="next-steps"></a>Następne kroki

Teraz, po utworzeniu farmy, Dowiedz się, jak [uzyskać dane czujników](get-sensor-data-from-sensor-partner.md) przepływające do farmy.
