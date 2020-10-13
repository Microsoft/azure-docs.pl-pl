---
title: Samouczek — używanie grup urządzeń w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Samouczek — jako operator, Dowiedz się, jak używać grup urządzeń do analizowania danych telemetrycznych z urządzeń w aplikacji IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 3192a9f121d4380a3e681747596fc91997662bf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967935"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Samouczek: używanie grup urządzeń do analizowania danych telemetrycznych urządzenia

W tym artykule opisano, jak operator, aby użyć grup urządzeń do analizowania danych telemetrycznych urządzenia w aplikacji IoT Central platformy Azure.

Grupa urządzeń to lista urządzeń zgrupowanych ze sobą, ponieważ są one zgodne z określonymi kryteriami. Grupy urządzeń ułatwiają zarządzanie, wizualizowanie i analizowanie urządzeń w dużej skali przez grupowanie urządzeń w mniejsze, logiczne grupy. Na przykład możesz utworzyć grupę urządzeń, aby wyświetlić listę wszystkich urządzeń z warunkiem klimatyzacyjnym w Seattle, aby umożliwić pracownikom znalezienie urządzeń, dla których są odpowiedzialni.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie grupy urządzeń
> * Przeanalizuj dane telemetryczne urządzenia za pomocą grupy urządzeń

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy ukończyć [Tworzenie aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md) i [dodać symulowane urządzenie do aplikacji IoT Central](./quick-create-simulated-device.md) przewodników Szybki Start, aby utworzyć szablon urządzenia **zestawu deweloperskiego IoT DevKit** do pracy z usługą.

## <a name="create-simulated-devices"></a>Tworzenie symulowanych urządzeń

Przed utworzeniem grupy urządzeń należy dodać co najmniej pięć symulowanych urządzeń z szablonu urządzenia **zestawu deweloperskiego IoT DevKit** do użycia w tym samouczku:

![Pięć symulowanych urządzeń czujników](./media/tutorial-use-device-groups/simulated-devices.png)

W przypadku czterech symulowanych urządzeń czujników użyj widoku **Zarządzanie urządzeniem** , aby ustawić nazwę klienta na *contoso*:

![Ustaw nazwę klienta na contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Tworzenie grupy urządzeń

Aby utworzyć grupę urządzeń:

1. W okienku po lewej stronie wybierz pozycję **grupy urządzeń** .

1. Wybierz **+** :

    ![Nowa grupa urządzeń](media/tutorial-use-device-groups/image1.png)

1. Nadaj grupie urządzeń nazwę *contoso Devices*. Możesz również dodać opis. Grupa urządzeń może zawierać tylko urządzenia z jednego szablonu urządzenia. Wybierz szablon urządzenia **zestawu deweloperskiego IoT DevKit** , który ma być używany dla tej grupy.

1. Aby dostosować grupę urządzeń w celu uwzględnienia tylko urządzeń należących do firmy **contoso**, wybierz pozycję **+ Filtr**. Wybierz właściwość **Nazwa klienta** , operator porównania **równa** się i **contoso** jako wartość. Można dodać wiele filtrów i urządzeń spełniających **wszystkie** kryteria filtrowania są umieszczane w grupie urządzeń. Utworzona grupa urządzeń jest dostępna dla każdego, kto ma dostęp do aplikacji, więc każda osoba może wyświetlać, modyfikować lub usuwać grupę urządzeń:

    ![Zapytanie grupy urządzeń](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > Grupa urządzeń jest kwerendą dynamiczną. Za każdym razem, gdy oglądasz listę urządzeń, na liście mogą znajdować się różne urządzenia. Lista zależy od tego, które urządzenia spełniają kryteria zapytania.

1. Wybierz polecenie **Zapisz**.

> [!NOTE]
> W przypadku urządzeń Azure IoT Edge wybierz pozycję Szablony Azure IoT Edge, aby utworzyć grupę urządzeń.

## <a name="analytics"></a>Analiza

Możesz użyć **analizy** z grupą urządzeń, aby przeanalizować dane telemetryczne z urządzeń w grupie. Na przykład można wykreślić średnią temperaturę raportowaną przez wszystkie czujniki środowiska firmy Contoso.

Aby przeanalizować dane telemetryczne dla grupy urządzeń:

1. Wybierz pozycję **Analiza** w okienku po lewej stronie.

1. Wybierz utworzoną grupę urządzeń **contoso Devices** . Następnie dodaj typy telemetrii **temperatury** i **wilgotności** :

    ![Tworzenie analizy](./media/tutorial-use-device-groups/create-analysis.png)

    Użyj ikon koła zębatego obok typów telemetrii, aby wybrać typ agregacji. Wartość domyślna to **Average**. Użyj **podziału przez** , aby zmienić sposób wyświetlania zagregowanych danych. Jeśli na przykład podzieli według identyfikatora urządzenia, zobaczysz wykres dla każdego urządzenia po wybraniu opcji **Analizuj**.

1. Wybierz pozycję **Analizuj** , aby wyświetlić średnie wartości telemetrii:

    ![Wyświetlanie analizy](./media/tutorial-use-device-groups/view-analysis.png)

    Możesz dostosować widok, zmienić wyświetlany okres i wyeksportować dane.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać grup urządzeń w aplikacji IoT Central platformy Azure, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Jak utworzyć reguły telemetrii](tutorial-create-telemetry-rules.md)
