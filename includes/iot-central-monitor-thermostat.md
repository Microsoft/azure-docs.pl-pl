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
ms.openlocfilehash: 77fdaf297fff0e145b1dd53908887bc14f9d3f14
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491136"
---
<!-- All needs updating -->
Jako operator w aplikacji IoT Central platformy Azure możesz:

* Wyświetl dane telemetryczne wysyłane przez dwa składniki termostatu na stronie **Przegląd** :

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Wyświetlanie danych telemetrycznych z urządzenia":::

* Wyświetl właściwości urządzenia na stronie **informacje** . Na tej stronie są wyświetlane właściwości składnika informacje o urządzeniu i dwa składniki termostatu:

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Wyświetl właściwości urządzenia":::

## <a name="customize-the-device-template"></a>Dostosowywanie szablonu urządzenia

Jako programista rozwiązań możesz dostosować szablon urządzenia, który IoT Central tworzony automatycznie, gdy urządzenie kontrolera temperatury jest połączone.

Aby dodać właściwość chmury do przechowywania nazwy klienta skojarzonej z urządzeniem:

1. W aplikacji IoT Central przejdź do szablonu urządzenia **kontrolera temperatury** na stronie **Szablony urządzeń** .

1. W szablonie urządzenia **kontrolera temperatury** wybierz pozycję **właściwości chmury**.

1. Wybierz pozycję **Dodaj właściwość chmury**. Wprowadź *nazwę klienta* jako **nazwę wyświetlaną** , a następnie wybierz **ciąg** jako **schemat**. Następnie wybierz pozycję **Zapisz**.

Aby dostosować sposób wyświetlania poleceń **raportów Get Max-Min** w aplikacji IoT Central:

1. W szablonie urządzenia wybierz pozycję **Dostosuj** .

1. W przypadku usługi **getMaxMinReport (thermostat1)** Zastąp *raport Pobierz Max-Min.* za pomocą *raportu o stanie pobierania thermostat1*.

1. W przypadku usługi **getMaxMinReport (thermostat2)** Zastąp *raport Pobierz Max-Min.* za pomocą *raportu o stanie pobierania thermostat2*.

1. Wybierz pozycję **Zapisz**.

Aby dostosować sposób wyświetlania w aplikacji IoT Central **docelowych właściwości temperatury docelowej** :

1. W szablonie urządzenia wybierz pozycję **Dostosuj** .

1. Dla **targetTemperature (thermostat1)** Zastąp *temperatury docelowej* z *temperaturą docelową (1)*.

1. Dla **targetTemperature (thermostat2)** Zastąp *temperatury docelowej* z *temperaturą docelową (2)*.

1. Wybierz pozycję **Zapisz**.

Składniki termostatu w modelu **kontrolera temperatury** obejmują właściwość do zapisu **temperatury docelowej** , szablon urządzenia zawiera właściwość Cloud Name ( **Nazwa klienta** ). Utwórz widok, za pomocą którego operator może edytować te właściwości:

1. Wybierz pozycję **widoki** , a następnie wybierz kafelek **Edytowanie urządzenia i danych w chmurze** .

1. Wprowadź _Właściwości_ jako nazwę formularza.

1. Wybierz **docelową temperaturę (1)**,  **docelową temperaturę (2)** i **nazwę klienta** . Następnie wybierz pozycję **Dodaj sekcję**.

1. Zapisz zmiany.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Widok do aktualizowania wartości właściwości":::

## <a name="publish-the-device-template"></a>Publikowanie szablonu urządzenia

Aby operator widział i korzystał z wprowadzonych dostosowań, należy opublikować szablon urządzenia.

Z poziomu szablonu urządzenia **termostat** wybierz pozycję **Publikuj**. Na panelu **publikowanie tego szablonu urządzenia** wybierz pozycję **Publikuj**.

Operator może teraz używać widoku **Właściwości** do aktualizowania wartości właściwości i wywoływać polecenia o nazwie **Pobierz thermostat1 stan raport** i Pobierz raport o **stanie thermostat2** na stronie polecenia urządzenia:

* Zaktualizuj wartości zapisywalnych właściwości na stronie **Właściwości** :

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Aktualizowanie właściwości urządzenia":::

* Wywołaj polecenia ze strony **poleceń** :

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Wywoływanie polecenia":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Wyświetlanie odpowiedzi polecenia":::
