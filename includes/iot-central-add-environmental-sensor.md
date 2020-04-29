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
ms.openlocfilehash: 9618dc1cef8d04cc5906579af0ef372694aeaaa9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673985"
---
## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

Utwórz folder o nazwie `environmental-sensor` na komputerze lokalnym.

Pobierz plik JSON [modelu możliwości czujnika środowiska](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) i Zapisz go w `environmental-sensor` folderze.

Użyj edytora tekstów, aby zamienić dwa wystąpienia `{YOUR_COMPANY_NAME_HERE}` z nazwą firmy w pobranym `EnvironmentalSensorInline.capabilitymodel.json` pliku.

W aplikacji IoT Central platformy Azure Utwórz szablon urządzenia o nazwie *czujnik środowiska* , importując plik modelu `EnvironmentalSensorInline.capabilitymodel.json` możliwości urządzenia:

![Szablon urządzenia z zaimportowanym modelem możliwości urządzenia](./media/iot-central-add-environmental-sensor/device-template.png)

Model możliwości urządzenia obejmuje dwa interfejsy: standardowy interfejs **informacji o urządzeniu** i niestandardowy interfejs **czujnika środowiska** . Interfejs **czujnika środowiska** definiuje następujące możliwości:

| Typ | Nazwa wyświetlana | Opis |
| ---- | ------------ | ----------- |
| Właściwość | Stan urządzenia     | Stan urządzenia. Dostępne są dwa stany online/offline. |
| Właściwość (zapisywalny) | Nazwa klienta    | Nazwa klienta, na którym aktualnie działa urządzenie. |
| Właściwość (zapisywalny) | Poziom jasności | Poziom jasności światła na urządzeniu. Może być określona jako 1 (wysoka), 2 (średnia), 3 (niska). |
| Telemetria | Temperatura | Bieżąca temperatura wykryta przez urządzenie. |
| Telemetria | Wilgotność    | Bieżąca wilgotność wykryta przez urządzenie. |
| Polecenie | wskaźnika          | Rozpocznij miganie diody LED na urządzeniu dla danego interwału czasu. |
| Polecenie | wlacza         | Włącz diodę LED na urządzeniu. |
| Polecenie | Wyłączanie        | Wyłącz diodę LED na urządzeniu. |
| Polecenie | rundiagnostics | To polecenie asynchroniczne uruchamia przebieg diagnostyczny na urządzeniu. |

Aby dostosować sposób wyświetlania właściwości **stanu urządzenia** w aplikacji IoT Central, wybierz pozycję **Dostosuj** w szablonie urządzenia. Rozwiń wpis **stanu urządzenia** , wprowadź _online_ jako **prawdziwą nazwę** i w _trybie offline_ jako **wartość false**. Następnie Zapisz zmiany:

![Dostosowywanie szablonu urządzenia](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Tworzenie widoków

Widoki umożliwiają współpracę z urządzeniami połączonymi z aplikacją IoT Central. Na przykład można mieć widoki, które wyświetlają dane telemetryczne, widoki, które wyświetlają właściwości i widoki umożliwiające edytowanie właściwości do zapisu i w chmurze. Widoki są częścią szablonu urządzenia.

Aby dodać widoki domyślne do szablonu urządzenia **czujnika środowiska** , przejdź do szablonu urządzenia, wybierz pozycję **widoki**i wybierz kafelek **Generuj widoki domyślne** . Upewnij się, że **Przegląd** i **informacje** są **włączone**, a następnie wybierz pozycję **Generuj domyślne widoki pulpitu nawigacyjnego**. Masz teraz dwa domyślne widoki zdefiniowane w szablonie.

Interfejs **czujnika środowiska** obejmuje dwie właściwości do zapisu — **nazwę klienta** i **poziom jasności**. Aby utworzyć widok, można użyć programu do edytowania następujących właściwości:

1. Wybierz pozycję **widoki** , a następnie wybierz kafelek **Edytowanie urządzenia i danych w chmurze** .

1. Wprowadź _Właściwości_ jako nazwę formularza.

1. Wybierz **poziom jasności** i właściwości **nazwy klienta** . Następnie wybierz pozycję **Dodaj sekcję**.

1. Zapisz zmiany.

![Dodaj widok, aby włączyć edycję właściwości](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>Publikowanie szablonu

Aby można było dodać urządzenie korzystające z szablonu urządzenia **czujnika środowiska** , należy je opublikować.

W szablonie urządzenia wybierz pozycję **Publikuj**. Na panelu **publikowanie tego szablonu urządzenia** wybierz pozycję **Publikuj**.

Aby sprawdzić, czy szablon jest gotowy do użycia, przejdź do strony **urządzenia** w aplikacji IoT Central. Sekcja **urządzenia** zawiera listę opublikowanych urządzeń w aplikacji:

![Opublikowane szablony na stronie urządzeń](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji IoT Central platformy Azure Dodaj rzeczywiste urządzenie do szablonu urządzenia utworzonego w poprzedniej sekcji:

1. Na stronie **urządzenia** wybierz szablon urządzenia **czujnika środowiska** .

    > [!TIP]
    > Upewnij się, że wybierasz szablon do użycia przed wybraniem opcji **+ New**, w przeciwnym razie utworzysz nieskojarzone urządzenie.

1. Wybierz pozycję **+ Nowy**.

1. Upewnij się, że **symulowane** jest **wyłączone**. Następnie wybierz pozycję **Utwórz**.

Kliknij nazwę urządzenia, a następnie wybierz pozycję **Połącz**. Zanotuj informacje o połączeniu urządzenia w **zakresie identyfikatora**strony **połączenia urządzenia** , **identyfikatora urządzenia**i **klucza podstawowego**. Te wartości są potrzebne podczas tworzenia kodu urządzenia:

![Informacje o połączeniu z urządzeniem](./media/iot-central-add-environmental-sensor/device-connection.png)
