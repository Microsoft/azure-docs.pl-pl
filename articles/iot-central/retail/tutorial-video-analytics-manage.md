---
title: Samouczek — monitorowanie wideo przy użyciu usługi Azure IoT Central wideo Analytics — szablon aplikacji wykrywania obiektów i ruchu
description: W tym samouczku pokazano, jak korzystać z pulpitów nawigacyjnych w szablonie aplikacji analiza filmów wideo — obiekty i monitorowanie ruchu, aby zarządzać kamerami i monitorować wideo.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: dea009d6d246d68f0686c3dc85f3518ccb13705c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575201"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>Samouczek: monitorowanie i zarządzanie analizą wideo — aplikacja do wykrywania obiektów i ruchu

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Dodaj aparaty wykrywania obiektów i ruchu do aplikacji IoT Central.
> * Zarządzanie strumieniami wideo i odtwarzanie ich w przypadku wykrycia interesujących zdarzeń.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać następujące czynności:

* Samouczek [Tworzenie aplikacji do analizy filmów wideo na żywo w usłudze Azure IoT Central](./tutorial-video-analytics-create-app.md) .
* Jedno z wcześniejszych [IoT Edge utworzyć wystąpienie dla usługi Live Video Analytics (maszyna wirtualna z systemem Linux)](tutorial-video-analytics-iot-edge-vm.md) lub [utworzyć wystąpienie IoT Edge dla samouczków analizy wideo na żywo (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) .

Na komputerze lokalnym musi być zainstalowany program [Docker](https://www.docker.com/products/docker-desktop) , aby można było uruchomić aplikację przeglądarka wideo.

## <a name="add-an-object-detection-camera"></a>Dodawanie aparatu wykrywania obiektów

W aplikacji IoT Central przejdź do utworzonego wcześniej urządzenia **LVA Gateway 001** . Następnie wybierz kartę **polecenia** .

Użyj wartości z poniższej tabeli jako wartości parametrów dla polecenia **Dodaj aparat** . W wartościach podanych w tabeli założono, że używasz symulowanego aparatu na maszynie wirtualnej platformy Azure, odpowiednio Dostosuj wartości, jeśli używasz prawdziwej kamery:

| Pole| Opis| Wartość przykładowa|
|---------|---------|---------|
| Identyfikator aparatu      | Identyfikator urządzenia do aprowizacji | aparat — 003 |
| Nazwa aparatu    | Przyjazna nazwa           | Aparat wykrywania obiektów |
| Adres URL RTSP       | Adres strumienia   | RTSP://10.0.0.4:554/media/camera-300s.mkv|
| Nazwa użytkownika RTSP  |                         | użytkownik    |
| Hasło RTSP  |                         | hasło    |
| Typ wykrywania | Lista rozwijana                | Wykrywanie obiektów       |

Wybierz pozycję **Uruchom** , aby dodać urządzenie aparatu:

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="Dodaj kamerę":::

> [!NOTE]
> Szablon urządzenia **czujnika obiektów brzegowych LVA** już istnieje w aplikacji.

## <a name="add-a-motion-detection-camera-optional"></a>Dodaj aparat wykrywania ruchu (opcjonalnie)

Jeśli masz dwie aparaty podłączone do urządzenia bramy IoT Edge, powtórz poprzednie kroki, aby dodać aparat wykrywania ruchu do aplikacji. Użyj różnych wartości dla **identyfikatora aparatu**, **nazwy aparatu**i parametrów **adresu URL protokołu RTSP** .

## <a name="view-the-downstream-devices"></a>Wyświetlanie urządzeń podrzędnych

Wybierz kartę **urządzenia podrzędne** dla usługi **LVA Gateway 001** urządzenie, aby zobaczyć właśnie dodane urządzenia do aparatu:

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="Dodaj kamerę":::

Urządzenia kamer są również wyświetlane na liście na stronie **urządzenia** w aplikacji.

## <a name="configure-and-manage-the-camera"></a>Konfigurowanie aparatu i zarządzanie nim

Przejdź do urządzenia **Camera-003** i wybierz kartę **Zarządzanie** .

Należy użyć wartości domyślnych lub zmodyfikować, jeśli trzeba dostosować właściwości urządzenia:

**Ustawienia aparatu**

| Właściwość | Opis | Sugerowana wartość |
|-|-|-|
| Host odtwarzania wideo | Host dla przeglądarki Azure Media Player | http://localhost:8094 |

**LVA operacji i diagnostyki**

| Właściwość | Opis | Sugerowana wartość |
|-|-|-|
| Automatyczne uruchamianie | Uruchom wykrywanie obiektów po ponownym uruchomieniu bramy LVA | Zaznaczono |
| Debuguj dane telemetryczne | Ślady zdarzeń | Opcjonalne |
|Limit czasu wnioskowania (s)| Ilość czasu użyta do ustalenia, czy wnioskowanie zostało zatrzymane | 20 |

**Wykrywanie obiektów AI**

| Właściwość | Opis | Sugerowana wartość |
|-|-|-|
| Maksymalny czas przechwytywania wnioskowania (s) | Maksymalny czas przechwytywania | 15 |
| Klasy wykrywania | Ciągi, rozdzielane przecinkami, ze znacznikami wykrywania. Aby uzyskać więcej informacji, zobacz [listę obsługiwanych tagów](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt) | ciężarówka, magistrala |
| Próg ufności | Procent kwalifikacji, aby określić, czy wykrycie obiektu jest prawidłowe | 70 |
| Częstotliwość próbkowania ramki wnioskowania (FPS) | [Opis tutaj] | 2 |

Wybierz pozycję **Zapisz**.

Po kilku sekundach zobaczysz **zaakceptowany** komunikat z potwierdzeniem dla każdego ustawienia:

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="Dodaj kamerę":::

## <a name="start-lva-processing"></a>Rozpocznij przetwarzanie LVA

Przejdź do urządzenia **Camera-003** i wybierz kartę **Commands (polecenia** ).

Uruchom polecenie **uruchamiania przetwarzania LVA** .

Po zakończeniu wykonywania polecenia przejrzyj historię poleceń, aby upewnić się, że nie ma żadnych błędów:

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="Dodaj kamerę":::

## <a name="monitor-the-cameras"></a>Monitoruj aparaty

Przejdź do urządzenia **Camera-003** i wybierz kartę **pulpit nawigacyjny** :

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="Dodaj kamerę":::

Kafelek **Liczba wykryć** pokazuje średnią liczbę wykryć dla każdego z wybranych klas wykrywania w trakcie jednego interwału wykrywania.

Wykres kołowy **klas wykrywania** zawiera wartość procentową wykrycia dla każdego typu klasy.

**Film przedstawiający zdarzenia wnioskowania** to lista linków do zasobów w Azure Media Services, które zawierają wykrycia. Link używa odtwarzacza hosta opisanego w poniższej sekcji.

## <a name="start-the-streaming-endpoint"></a>Uruchamianie punktu końcowego przesyłania strumieniowego

Uruchom punkt końcowy Media Services, aby umożliwić klientowi Media Player aplikacji przesyłanie strumieniowe zapisanego wideo:

* W Azure Portal przejdź do grupy zasobów **LVA-RG** .
* Otwórz zasób **punktu końcowego przesyłania strumieniowego** .
* Na stronie **Szczegóły punktu końcowego przesyłania strumieniowego** wybierz pozycję **Rozpocznij**. Zobaczysz ostrzeżenie, że rozliczenia zaczynają się po uruchomieniu punktu końcowego.

## <a name="view-stored-video"></a>Wyświetl przechowywane wideo

Dni oglądania kamer i reagowania na podejrzane obrazy znajdują się powyżej. Dzięki automatycznemu znakowaniu zdarzeń i bezpośrednim linkom do przechowywanego wideo z wywnioskowanym wykryciem operatory zabezpieczeń mogą znaleźć interesujące Cię zdarzenia na liście, a następnie użyć linku, aby wyświetlić wideo.

Możesz użyć [odtwarzacza wideo amp](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer) , aby wyświetlić wideo przechowywane na koncie Azure Media Services.

Aplikacja IoT Central przechowuje wideo w Azure Media Services, z którego można przesyłać strumieniowo. Potrzebujesz odtwarzacza wideo, aby odtworzyć film wideo zapisany w Azure Media Services.

Upewnij się, że platforma **Docker** jest uruchomiona na komputerze lokalnym.

Otwórz wiersz polecenia i użyj poniższego polecenia, aby uruchomić odtwarzacz wideo w kontenerze platformy Docker na komputerze lokalnym. Zamień symbole zastępcze w poleceniu na wartości zanotowane wcześniej w pliku *scratchpad.txt* . Zawarto zanotować,, `amsAadClientId` , `amsAadSecret` `amsAadTenantId` `amsSubscriptionId` i `amsAccountName` podczas tworzenia jednostki usługi dla konta Media Services:

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Parametr Docker | Dostęp do interfejsu API usługi AMS (JSON)|
|-|-|
|amsAadClientId| AadClientId|
|amsAadSecret| AadSecret |
|amsAadTenantId| AadTenantId |
|amsSubscriptionId| SubscriptionId|
|amsResourceGroup| ResourceGroup |
|amsAccountName| AccountName|

Przejdź do urządzenia **Camera-003** i wybierz kartę **pulpit nawigacyjny** . Następnie kliknij jeden z przechwyconych wykrytych obiektów hiperlinków na kafelku **wideo zdarzenia wnioskowania** . Film wideo pojawia się na stronie wyświetlanej przez lokalny odtwarzacz wideo:

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="Dodaj kamerę":::

## <a name="change-the-simulated-devices-in-application-dashboards"></a>Zmienianie symulowanych urządzeń na pulpitach nawigacyjnych aplikacji

Pulpity nawigacyjne aplikacji są wypełniane jako dane telemetryczne i właściwości wygenerowane z symulowanych urządzeń IoT Central. Aby skonfigurować kafelki do telemetrii z prawdziwych kamer lub symulatora Live555, wykonaj następujące kroki:

1. Przejdź do pulpitu nawigacyjnego **monitorowania aplikacji (przykład)** .
1. Kliknij pozycję **Edytuj**.
1. Wybierz kafelek **notatki** i usuń go.
1. Zmień tytuł pulpitu nawigacyjnego na *monitor Real Camera*.
1. Na kafelku **zliczanie wnioskowania** wybierz ikonę Konfiguruj.
1. W sekcji **Konfigurowanie wykresu** wybierz co najmniej jedną rzeczywistą kamerę w grupie urządzeń **czujnika obiektów LVA Edge** .
1. Wybierz `AI Inference Interface/Inference Count` pole telemetrii.
1. Wybierz pozycję **Aktualizuj**.

1. Powtórz kroki dla następujących kafelków:
    1. Wykres kołowy **wykrywania** używa `AI Inference Interface/Inference/entity/tag/value` typu telemetrii.
    1. **Wnioskowanie** używa `AI Inference Interface/Inference/entity/tag/value` ostatniej znanej wartości.
    1. Stopień **pewności%** używa `AI Inference Interface/Inference/entity/tag/confidence` ostatniej znanej wartości.
    1. **Snapshot** Funkcja Snapshot `AI Inference Interface/Inference Image` jest wyświetlana jako obraz.
    1. Użycie **wideo o zdarzeniu wnioskowania** `AI Inference Interface/Inference Event Video` jest wyświetlane jako link.
1. Wybierz pozycję **Zapisz**.

Pulpit nawigacyjny **monitora rzeczywistej kamery** przedstawia teraz wartości z rzeczywistego urządzenia z kamerą:

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="Dodaj kamerę":::

## <a name="pause-processing"></a>Wstrzymaj przetwarzanie

W aplikacji można wstrzymywać przetwarzanie analizy wideo na żywo:

1. W aplikacji przejdź do strony **polecenia** , aby znaleźć aparat wykrywania obiektów. Następnie uruchom polecenie **stop LVA Processing** .

1. Zatrzymaj punkt końcowy przesyłania strumieniowego dla konta usługi Media Services:
    * W Azure Portal przejdź do grupy zasobów **LVA-RG** .
    * Kliknij zasób **punkt końcowy przesyłania strumieniowego** .
    * Na stronie **Szczegóły punktu końcowego przesyłania strumieniowego** wybierz pozycję **Zatrzymaj**.

## <a name="tidy-up"></a>Uporządkowanego

Jeśli zakończysz pracę z aplikacją, możesz usunąć wszystkie utworzone zasoby w następujący sposób:

1. W aplikacji IoT Central przejdź do strony **aplikacji** w sekcji **Administracja** . Następnie wybierz pozycję **Usuń**.
1. W Azure Portal Usuń grupę zasobów **LVA-RG** .
1. Na maszynie lokalnej Zatrzymaj kontener platformy Docker **przeglądarki amp** .

## <a name="next-steps"></a>Następne kroki

Teraz wiesz już, jak dodać aparaty do aplikacji IoT Central i skonfigurować je pod kątem wykrywania obiektów i ruchu.

Aby dowiedzieć się, jak dostosować kod źródłowy dla modułów IoT Edge:

> [!div class="nextstepaction"]
> [Modyfikowanie i kompilowanie modułów bramy analizy wideo na żywo](./tutorial-video-analytics-build-module.md)
