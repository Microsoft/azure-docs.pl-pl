---
title: Odtwarzanie wielodniowych zapisów — Azure
description: W ramach tego samouczka nauczysz się używać interfejsów API usługi Azure Media Service do odtwarzania wielodniowego ciągłego nagrywania wideo.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 1ec9260be7241057478b06446ac2aa53c14bcb47
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803436"
---
# <a name="tutorial-playback-of-multi-day-recordings"></a>Samouczek: Odtwarzanie wielodniowych zapisów  

Ten samouczek kompiluje się w samouczku dotyczącym [ciągłego nagrywania wideo](continuous-video-recording-concept.md) (CVR). W ramach tego samouczka nauczysz się używać interfejsów API usługi Azure Media Service do odtwarzania wielodniowego ciągłego nagrywania wideo z chmury. 

Jest to przydatne w scenariuszach takich jak bezpieczeństwo publiczne, gdzie istnieje potrzeba utrzymania nagrania materiału z aparatu przez wiele dni (lub tygodni) i istnieje sporadyczna potrzeba oglądania określonych części tego materiału.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Uruchom przykładową aplikację, która pokazuje, jak przeglądać zawartość rejestrowania wielodniowego
> * Wyświetl wybrane fragmenty tego nagrania

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem  

Zaleca się zapoznanie się z następującymi stronami dokumentacji:

* [Analiza filmów wideo na żywo na IoT Edge przegląd](overview.md)
* [Analiza filmów wideo na żywo na IoT Edge terminologii](terminology.md)
* [Koncepcje grafu multimediów](media-graph-concept.md)
* [Ciągłe nagrywanie wideo](continuous-video-recording-concept.md) 
* [Przewodnik: odtwarzanie nagrań](playback-recordings-how-to.md)
* [Samouczek: ciągłe nagrywanie filmów wideo](continuous-video-recording-tutorial.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [samouczek CVR](continuous-video-recording-tutorial.md). Ten samouczek i odpowiednie interfejsy API omówione w [samouczku: ciągłe nagrywanie wideo](continuous-video-recording-tutorial.md) stosuje się do nagrań, które nie są dłuższe niż 5 minut. zaleca się, aby nagrać 5 godzin wideo, jeśli nie więcej. Interfejsy API używane do przeglądania nagrań są najlepszym rozwiązaniem w przypadku długich nagrań.
* Zalecamy uruchomienie tego samouczka w trakcie pracy z [samouczkiem: ciągłe nagrywanie wideo](continuous-video-recording-tutorial.md) jest nadal wykonywane — to oznacza, że nadal nagrywasz wideo w chmurze.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej 

W ramach [samouczka CVR](continuous-video-recording-tutorial.md)utworzono konto usługi Media. W tym samouczku należy mieć pełny dostęp API do tego konta. Aby utworzyć nazwę główną usługi, można użyć kroków z sekcji [Uzyskiwanie poświadczeń w celu uzyskania dostępu do Media Services interfejsu API](../latest/access-api-howto.md?tabs=portal) . Powinno być możliwe uzyskanie bloku JSON z Azure Portal, który wygląda następująco:

```
{
    "AadClientId": "<<INSERT_AZURE_AD_APP_ID_HERE>>",
    "AadSecret": "<<INSERT_AZURE_AD_APP_SECRET_HERE>>",
    "AadTenantDomain": "<<YOUR_TENANT_DOMAIN>>",
    "AadTenantId": "<<YOUR_TENANT_ID>>",
    "AccountName": "<<YOUR_ACCOUNT_NAME>>",
    "Location": "<<YOUR_REGION_NAME>>",
    "ResourceGroup": "<<YOUR_RESOURCE_GROUP>>",
    "SubscriptionId": "<<YOUR_SUBSCRIPTION_ID>>",
    "ArmAadAudience": "https://management.core.windows.net",
    "ArmEndpoint": "https://management.azure.com"
}
```

Następnie w programie Visual Studio Code Otwórz pozycję src/AMS-Asset-Player. Ten folder zawiera pliki niezbędne dla tego samouczka. Otwórz appsettings.jspliku i skopiuj jego zawartość do nowego pliku, appsettings.development.js. Wprowadź następujące zmiany do ostatniego pliku:

```
  "AMS" : {
    "subscriptionId" : "Use value of SubscriptionId above",
    "resourceGroup" : "Use value of ResourceGroup above",
    "accountId" : "Use value of AccountName above",
    "aadTenantId" : "Use value of AadTenantId above",
    "aadClientId" : "Use value of AadClientId above",
    "aadSecret" : "Use value of AadSecret above"
} 
```

1. W Visual Studio Code Otwórz kartę **rozszerzenia** (lub naciśnij klawisze CTRL + SHIFT + X) i Wyszukaj pozycję Azure IoT Hub.
1. Kliknij prawym przyciskiem myszy i wybierz pozycję **Ustawienia rozszerzenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Ustawienia rozszerzenia":::
1. Wyszukaj i Włącz opcję "Pokaż pełny komunikat".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Ustawienia rozszerzenia" jest wybrany.

![Strumień](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
Po prawej stronie odtwarzacza można zobaczyć kontrolki, aby przeglądać archiwum. Lata, miesiące i daty w tym formancie są wypełniane przy użyciu interfejsu API availableMedia udokumentowanego w [podręczniku: odtwarzanie nagrań](playback-recordings-how-to.md).
Po rozszerzeniu dnia, jeśli CVR samouczek jest uruchamiany przez kilka godzin, zobaczysz następujący wynik:

![Przeglądaj archiwum](./media/playback-multi-day-recordings-tutorial/results.png)

Źródłem kanału informacyjnego wideo w samouczku jest plik MKV. Gdy symulator RSTP (zobacz [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) osiągnie koniec pliku, kończy strumień. Węzeł źródłowy RTSP w grafie multimediów wykrywa to i ponownie nawiązuje połączenie, a odtwarzanie wideo zostanie wznowione. Między każdym takim końcem pliku a ponownym nawiązaniem połączenia występuje przerwy w zarejestrowanym archiwum, które jest wyświetlane jako nowy wpis w availableMedia wyniki.

![Wyniki](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
Po kliknięciu jednego wpisu na liście aplikacja utworzy adres URL przesyłania strumieniowego z odpowiednim filtrem, na przykład https://{hostname}/{locatorId}/Content. ISM/manifest (format = MPD-Time-CSF, startTime = RRRR-MM-DDTgg: MM: SS). Odtwarzacz załaduje ten adres URL, a Odtwarzanie rozpocznie się w żądanym czasie rozpoczęcia.

Alternatywnie można użyć określonych godzin początkowych i końcowych za pośrednictwem kontrolek w dolnej części strony. Możesz użyć wyników wywołania availableMedia, jak wymieniono po prawej stronie, jako prowadnicy do dozwolonych wartości czasu rozpoczęcia i zakończenia. Szczegółowe ograniczenia dotyczące filtrów startTime i endTime są udokumentowane w [przewodniku: odtwarzanie nagrań](playback-recordings-how-to.md). Po wybraniu wartości czasu, po kliknięciu przycisku Prześlij aplikacja załaduje odtwarzacz przy użyciu adresu URL przesyłania strumieniowego, takiego jak: https://{hostname}/{locatorId}/Content. ISM/manifest (format = MPD-Time-CSF, startTime = RRRR-MM-DDTgg: MM: SS, endTime = RRRR-MM-DDTgg: MM: SS) odtwarzanie zostanie rozpoczęte o żądanym czasie rozpoczęcia.

## <a name="next-steps"></a>Następne kroki

[Rejestrowanie wideo oparte na zdarzeniach w chmurze i odtwarzanie z chmury](event-based-video-recording-tutorial.md)
