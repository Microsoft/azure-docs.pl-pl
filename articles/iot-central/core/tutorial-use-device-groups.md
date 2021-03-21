---
title: Samouczek — używanie grup urządzeń w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Samouczek — jako operator, Dowiedz się, jak używać grup urządzeń do analizowania danych telemetrycznych z urządzeń w aplikacji IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a7d26eebb24662a448d8ccb44d037e7706fe776b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832848"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Samouczek: używanie grup urządzeń do analizowania danych telemetrycznych urządzenia

W tym artykule opisano, jak operator, aby użyć grup urządzeń do analizowania danych telemetrycznych urządzenia w aplikacji IoT Central platformy Azure.

Grupa urządzeń to lista urządzeń zgrupowanych ze sobą, ponieważ są one zgodne z określonymi kryteriami. Grupy urządzeń ułatwiają zarządzanie, wizualizowanie i analizowanie urządzeń w dużej skali przez grupowanie urządzeń w mniejsze, logiczne grupy. Na przykład możesz utworzyć grupę urządzeń, aby wyświetlić listę wszystkich urządzeń z warunkiem klimatyzacyjnym w Seattle, aby umożliwić pracownikom znalezienie urządzeń, dla których są odpowiedzialni.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie grupy urządzeń
> * Przeanalizuj dane telemetryczne urządzenia za pomocą grupy urządzeń

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy ukończyć [Tworzenie aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md) i [dodać symulowane urządzenie do aplikacji IoT Central](./quick-create-simulated-device.md) przewodników Szybki Start, aby utworzyć szablon urządzenia **kontrolera czujnika** do pracy z programem.

## <a name="create-simulated-devices"></a>Tworzenie symulowanych urządzeń

Przed utworzeniem grupy urządzeń należy dodać co najmniej pięć symulowanych urządzeń opartych na szablonie urządzenia **kontrolera czujnika** do użycia w tym samouczku:


:::image type="content" source="media/tutorial-use-device-groups/simulated-devices.png" alt-text="Zrzut ekranu przedstawiający pięć urządzeń symulowanego kontrolera czujnika":::

W przypadku czterech symulowanych urządzeń czujników użyj widoku **Zarządzanie urządzeniem** , aby ustawić nazwę klienta na *contoso*:

:::image type="content" source="media/tutorial-use-device-groups/customer-name.png" alt-text="Zrzut ekranu pokazujący sposób ustawienia właściwości chmury Nazwa klienta":::

## <a name="create-a-device-group"></a>Tworzenie grupy urządzeń

Aby utworzyć grupę urządzeń:

1. W okienku po lewej stronie wybierz pozycję **grupy urządzeń** .

1. Wybierz pozycję **+ Nowe**.

1. Nazwij grupę urządzeń *contoso*. Możesz również dodać opis. Grupa urządzeń może zawierać tylko urządzenia z jednego szablonu urządzenia. Wybierz szablon urządzenia **kontrolera czujnika** , który ma być używany dla tej grupy.

1. Aby dostosować grupę urządzeń w celu uwzględnienia tylko urządzeń należących do firmy **contoso**, wybierz pozycję **+ Filtr**. Wybierz właściwość **Nazwa klienta** , operator porównania **równa** się i **contoso** jako wartość. Można dodać wiele filtrów i urządzeń spełniających **wszystkie** kryteria filtrowania są umieszczane w grupie urządzeń. Utworzona grupa urządzeń jest dostępna dla każdego, kto ma dostęp do aplikacji, więc każda osoba może wyświetlać, modyfikować lub usuwać grupę urządzeń.

    > [!TIP]
    > Grupa urządzeń jest kwerendą dynamiczną. Za każdym razem, gdy oglądasz listę urządzeń, na liście mogą znajdować się różne urządzenia. Lista zależy od tego, które urządzenia spełniają kryteria zapytania.

1. Wybierz polecenie **Zapisz**.

:::image type="content" source="media/tutorial-use-device-groups/device-group-query.png" alt-text="Zrzut ekranu pokazujący konfigurację zapytania grupy urządzeń":::

> [!NOTE]
> W przypadku urządzeń Azure IoT Edge wybierz pozycję Szablony Azure IoT Edge, aby utworzyć grupę urządzeń.

## <a name="analytics"></a>Analiza

Możesz użyć **analizy** z grupą urządzeń, aby przeanalizować dane telemetryczne z urządzeń w grupie. Na przykład można wykreślić średnią temperaturę raportowaną przez wszystkie czujniki środowiska firmy Contoso.

Aby przeanalizować dane telemetryczne dla grupy urządzeń:

1. Wybierz pozycję **Analiza** w okienku po lewej stronie.

1. Wybierz utworzoną grupę urządzeń **contoso Devices** . Następnie dodaj typy telemetrii **temperatury** i **wilgotności** :

    :::image type="content" source="media/tutorial-use-device-groups/create-analysis.png" alt-text="Zrzut ekranu pokazujący typy telemetrii wybrane do analizy":::

    Użyj ikon koła zębatego obok typów telemetrii, aby wybrać typ agregacji. Wartość domyślna to **Average**. Użyj **Group by** , aby zmienić sposób wyświetlania zagregowanych danych. Jeśli na przykład podzieli według identyfikatora urządzenia, zobaczysz wykres dla każdego urządzenia po wybraniu opcji **Analizuj**.

1. Wybierz pozycję **Analizuj** , aby wyświetlić średnie wartości telemetrii:

    :::image type="content" source="media/tutorial-use-device-groups/view-analysis.png" alt-text="Zrzut ekranu pokazujący średnie wartości dla wszystkich urządzeń contoso":::

    Możesz dostosować widok, zmienić wyświetlany okres i wyeksportować dane.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać grup urządzeń w aplikacji IoT Central platformy Azure, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Jak utworzyć reguły telemetrii](tutorial-create-telemetry-rules.md)
