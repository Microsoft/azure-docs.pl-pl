---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 28f676892967abbd0da63d7a75ea3d164b87ce97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017518"
---
Jako operator w aplikacji IoT Central platformy Azure możesz:

* Wyświetlanie telemetrii wysyłanej przez urządzenie na stronie **Przegląd** :

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Wyświetlanie danych telemetrycznych z urządzenia":::

* Wyświetl właściwości urządzenia na stronie **informacje** :

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Wyświetl właściwości urządzenia":::

## <a name="customize-the-device-template"></a>Dostosowywanie szablonu urządzenia

Jako programista rozwiązań możesz dostosować szablon urządzenia, który IoT Central tworzony automatycznie, gdy urządzenie z termostatem jest połączone.

Aby dodać właściwość chmury do przechowywania nazwy klienta skojarzonej z urządzeniem:

1. W aplikacji IoT Central przejdź do szablonu urządzenia z **termostatem** na stronie **Szablony urządzeń** .

1. W szablonie urządzenia **termostatu** wybierz pozycję **właściwości chmury**.

1. Wybierz pozycję **Dodaj właściwość chmury**. Wprowadź *nazwę klienta* jako **nazwę wyświetlaną** , a następnie wybierz **ciąg** jako **schemat**. Następnie wybierz pozycję **Zapisz**.

Aby dostosować sposób wyświetlania **raportu Get Max-Min** w aplikacji IoT Central, wybierz pozycję **Dostosuj** w szablonie urządzenia. Zamień **raport pobierz Max-Min.** z *raportem pobieranie stanu*. Następnie wybierz pozycję **Zapisz**.

Model **termostatu** obejmuje właściwość do zapisu **temperatury docelowej** , szablon urządzenia zawiera właściwość Cloud **name (Nazwa klienta** ). Utwórz widok, za pomocą którego operator może edytować te właściwości:

1. Wybierz pozycję **widoki** , a następnie wybierz kafelek **Edytowanie urządzenia i danych w chmurze** .

1. Wprowadź _Właściwości_ jako nazwę formularza.

1. Wybierz nazwę **docelowej temperatury** i **nazwy klienta** . Następnie wybierz pozycję **Dodaj sekcję**.

1. Zapisz zmiany.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Widok do aktualizowania wartości właściwości":::

## <a name="publish-the-device-template"></a>Publikowanie szablonu urządzenia

Aby operator widział i korzystał z wprowadzonych dostosowań, należy opublikować szablon urządzenia.

Z poziomu szablonu urządzenia **termostat** wybierz pozycję **Publikuj**. Na panelu **publikowanie tego szablonu urządzenia** wybierz pozycję **Publikuj**.

Operator może teraz używać widoku **Właściwości** do aktualizowania wartości właściwości i wywoływać polecenie o nazwie **Pobierz raport o stanie** na stronie poleceń urządzenia:

* Zaktualizuj wartości zapisywalnych właściwości na stronie **Właściwości** :

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Aktualizowanie właściwości urządzenia":::

* Wywołaj polecenia ze strony **poleceń** :

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Wywoływanie polecenia":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Wyświetlanie odpowiedzi polecenia":::
