---
title: Samouczek — łączenie ogólnej aplikacji klienckiej z platformą Azure IoT Central | Microsoft Docs
description: W tym samouczku przedstawiono sposób, w jaki deweloper urządzenia łączy urządzenie z uruchomioną aplikacją kliencką C, C#, Java, JavaScript lub Python do aplikacji IoT Central platformy Azure. Użytkownik modyfikuje automatycznie wygenerowany szablon urządzenia przez dodanie widoków, które umożliwiają operatorowi współpracujące z podłączonym urządzeniem.
author: dominicbetts
ms.author: dobett
ms.date: 11/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 8f1b5eabe235d107b48dc7b2db5b6d4b1188a3fa
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833970"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>Samouczek: Tworzenie i łączenie aplikacji klienckiej z aplikacją usługi Azure IoT Central

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

W tym samouczku pokazano, jak programista urządzeń ma połączyć aplikację kliencką z aplikacją IoT Central platformy Azure. Aplikacja symuluje zachowanie urządzenia z termostatem. Gdy aplikacja nawiązuje połączenie z IoT Central, wysyła identyfikator modelu urządzenia termostatu. IoT Central korzysta z identyfikatora modelu, aby pobrać model urządzenia i utworzyć dla Ciebie szablon urządzenia. Dodano dostosowania i widoki do szablonu urządzenia, aby umożliwić operatorowi współpracujące z urządzeniem.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz i uruchom kod urządzenia i sprawdź, czy jest on połączony z aplikacją IoT Central.
> * Wyświetl symulowane dane telemetryczne wysyłane z urządzenia.
> * Dodawanie niestandardowych widoków do szablonu urządzenia.
> * Opublikuj szablon urządzenia.
> * Użyj widoku, aby zarządzać właściwościami urządzeń.
> * Wywoływanie polecenia w celu sterowania urządzeniem.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>Wyświetlanie danych pierwotnych

Jako deweloper urządzenia możesz użyć widoku **nieprzetworzone dane** , aby sprawdzić pierwotne dane wysyłane przez urządzenie do IoT Central:

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="Widok danych nieprzetworzonych":::

W tym widoku można wybrać kolumny do wyświetlenia i ustawić zakres czasu do wyświetlenia. W kolumnie **dane niemodelowane** są wyświetlane dane z urządzenia, które nie są zgodne z żadną definicją właściwości lub telemetrii w szablonie urządzenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli wolisz korzystać z zestawu samouczków IoT Central i dowiedzieć się więcej na temat tworzenia rozwiązania IoT Central, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie szablonu urządzenia bramy](./tutorial-define-gateway-device-type.md)

Jako deweloper urządzenia teraz znasz podstawowe informacje dotyczące sposobu tworzenia urządzenia przy użyciu języka Java, a niektóre sugerowane następne kroki to:

* Przeczytaj [co to są szablony urządzeń?](./concepts-device-templates.md) aby dowiedzieć się więcej na temat roli szablonów urządzeń podczas implementowania kodu urządzenia.
* Aby dowiedzieć się więcej o sposobach rejestrowania urządzeń w usłudze IoT Central i sposobach IoT Central zabezpieczania połączeń urządzeń, przeczytaj artykuł [wprowadzenie do usługi Azure IoT Central](./concepts-get-connected.md) .
* Odczytaj [ładunki telemetryczne, właściwości i poleceń,](concepts-telemetry-properties-commands.md) aby dowiedzieć się więcej o danych, które są wymieniane przez urządzenia z IoT Central.
* Przeczytaj [Przewodnik dla deweloperów urządzeń IoT Plug and Play](../../iot-pnp/concepts-developer-guide-device.md).
