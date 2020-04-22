---
title: Tworzenie transmisji na żywo usługi Azure Media Services za pomocą usługi OBS Studio
description: Dowiedz się, jak utworzyć transmisję na żywo usługi Azure Media Services przy użyciu portalu i usługi OBS Studio
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 04/16/2020
ms.openlocfilehash: 74aa17fb17d682449bd817945c3b8bbf3f95363e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726613"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Tworzenie strumienia na żywo usługi Azure Media Services za pomocą usługi OBS

Ten przewodnik Szybki start pomoże Ci utworzyć transmisję na żywo usługi Azure Media Services przy użyciu portalu Azure i open broadcasting studio (OBS). Przyjęto założenie, że masz subskrypcję platformy Azure i utworzono konto usługi Media Services.

W tym przewodniku Szybki start omówimy:

- Konfigurowanie kodera lokalnego z usługą OBS.
- Konfigurowanie transmisji na żywo.
- Konfigurowanie wyjść strumienia na żywo.
- Uruchamianie domyślnego punktu końcowego przesyłania strumieniowego.
- Za pomocą programu Azure Media Player, aby wyświetlić strumień na żywo i dane wyjściowe na żądanie.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Otwórz przeglądarkę internetową i przejdź do [portalu Microsoft Azure](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Konfigurowanie kodera lokalnego przy użyciu usługi OBS

1. Pobierz i zainstaluj OBS dla swojego systemu operacyjnego na [stronie internetowej Open Broadcaster Software](https://obsproject.com/).
1. Uruchom aplikację i zachowaj ją otwartą.

## <a name="run-the-default-streaming-endpoint"></a>Uruchamianie domyślnego punktu końcowego przesyłania strumieniowego

1. Wybierz **punkty końcowe przesyłania strumieniowego** na liście usługi Media Services.

   ![Element menu punktów końcowych przesyłania strumieniowego](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Jeśli domyślny stan punktu końcowego przesyłania strumieniowego zostanie zatrzymany, wybierz go. Ten krok prowadzi do strony dla tego punktu końcowego.
1. Wybierz pozycję **Uruchom**.

   ![Przycisk Start dla punktu końcowego przesyłania strumieniowego](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Konfigurowanie strumienia na żywo usługi Azure Media Services

1. Przejdź do konta usługi Azure Media Services w portalu, a następnie wybierz pozycję **Przesyłanie strumieniowe na żywo** z listy **usługi Media Services.**

   ![Link do transmisji na żywo](media/live-events-obs-quickstart/select-live-streaming.png)
1. Wybierz **pozycję Dodaj wydarzenie na żywo,** aby utworzyć nowe wydarzenie transmisji strumieniowej na żywo.

   ![Ikona Dodaj wydarzenie na żywo](media/live-events-obs-quickstart/add-live-event.png)
1. Wprowadź nazwę nowego wydarzenia, taką jak *TestLiveEvent,* w polu **Nazwa wydarzenia na żywo.**

   ![Pole nazwa wydarzenia na żywo](media/live-events-obs-quickstart/live-event-name.png)
1. Wprowadź opcjonalny opis zdarzenia w polu **Opis.**
1. Wybierz opcję Przekazywanie — brak opcji **kodowania w chmurze.**

   ![Opcja kodowania w chmurze](media/live-events-obs-quickstart/cloud-encoding.png)
1. Wybierz opcję **RTMP.**
1. Upewnij się, że opcja **Brak** jest zaznaczona dla **startu na żywo zdarzenia**, aby uniknąć naliczania opłat za wydarzenie na żywo, zanim będzie gotowe. (Rozliczenia rozpoczną się po rozpoczęciu wydarzenia na żywo).

   ![Rozpocznij opcję wydarzenia na żywo](media/live-events-obs-quickstart/start-live-event-no.png)
1. Wybierz przycisk **Przejrzyj + utwórz,** aby przejrzeć ustawienia.
1. Wybierz przycisk **Utwórz,** aby utworzyć wydarzenie na żywo. Następnie wrócisz do aukcji wydarzeń na żywo.
1. Wybierz łącze do właśnie utworzonego wydarzenia na żywo. Należy zauważyć, że zdarzenie zostało zatrzymane.
1. Zachowaj tę stronę otwartą w przeglądarce. Wrócimy do niego później.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Konfigurowanie transmisji na żywo przy użyciu usługi OBS Studio

OBS rozpoczyna się od sceny domyślnej, ale bez wybranych wejść.

   ![Domyślny ekran OBS](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Dodawanie źródła wideo

1. W panelu **Źródła** kliknij ikonę **dodaj,** aby wybrać nowe urządzenie źródłowe. Otworzy się menu **Źródła.**

1. Z menu urządzenia **źródłowego wybierz pozycję Video Capture Device.** Zostanie otwarte menu **Utwórz/Wybierz źródło.**

   ![Menu źródeł OBS z wybranym urządzeniem wideo](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Wybierz przycisk opcji **Dodaj istniejącą,** a następnie kliknij przycisk **OK**. Zostanie otwarte menu **Właściwości urządzenia wideo.**

   ![Obs nowe menu źródła wideo z dodatkiem istniejącego wybranego](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. Z listy rozwijanej **Urządzenie** wybierz dane wejściowe wideo, którego chcesz użyć do emisji. Zostaw resztę ustawień w spokoju na razie i kliknij **PRZYCISK OK**. Źródło wejściowe zostanie dodane do panelu **Źródła,** a widok wprowadzania wideo pojawi się w obszarze **Podgląd.**

   ![Ustawienia kamery OBS](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Dodawanie źródła dźwięku

1. W panelu **Źródła** kliknij ikonę **dodaj,** aby wybrać nowe urządzenie źródłowe. Zostanie otwarte menu Urządzenie źródłowe.

1. Z menu urządzenia źródłowego wybierz **polecenie Przechwytywanie sygnału audio.** Zostanie otwarte menu **Utwórz/Wybierz źródło.**

   ![Menu źródeł OBS z wybranym urządzeniem audio](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Wybierz przycisk opcji **Dodaj istniejącą,** a następnie kliknij przycisk **OK**. Otworzy się menu **Właściwości przechwytywania wejścia audio.**

   ![Źródło dźwięku OBS z dodaniem istniejącego wybranego ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. Z listy rozwijanej **Urządzenie** wybierz urządzenie do przechwytywania dźwięku, którego chcesz użyć do emisji. Zostaw na razie pozostałe ustawienia w spokoju i kliknij przycisk OK. Urządzenie do przechwytywania dźwięku zostanie dodane do panelu miksera audio.

   ![Lista rozwijana wyboru urządzenia audio OBS](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Konfigurowanie przesyłania strumieniowego w obs

W następnej procedurze powrócisz do usługi Azure Media Services w przeglądarce, aby skopiować wejściowy adres URL, aby wprowadzić go w ustawieniach danych wyjściowych:

1. Na stronie usługi Azure Media Services w portalu wybierz pozycję **Start,** aby rozpocząć zdarzenie transmisji na żywo. (Rozliczenia rozpoczynają się teraz).

   ![Ikona Start](media/live-events-obs-quickstart/start.png)
1. Ustaw przełącznik **RTMP** na **RTMPS**.
1. W polu **Wejściowy adres URL** skopiuj adres URL do schowka.

   ![Wejściowy adres URL](media/live-events-obs-quickstart/input-url.png)

1. Przełącz się do aplikacji OBS.

1. Kliknij przycisk **Ustawienia** w panelu **Sterowanie.** Zostaną otwarte opcje Ustawień.

   ![Panel Obs Controls z wybranymi ustawieniami](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Z menu **Ustawienia** wybierz **opcję Strumień.**

1. Z listy rozwijanej **Usługa** wybierz pozycję Pokaż wszystko, a następnie wybierz pozycję **Niestandardowe...**.

1. W polu **Serwer** wklej adres URL RTMPS skopiowany do schowka.

1. Wprowadź coś w polu **Klucz strumienia.**  To naprawdę nie ma znaczenia, co to jest, ale musi mieć wartość.

    ![Ustawienia strumienia OBS](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Z menu Ustawienia wybierz pozycję Dane **wyjściowe.** **Output**

1. Wprowadź *2* w polu **Interwał klatki kluczowej.** Spowoduje to ustawienie długości fragmentu na 2 sekundy. Aby uzyskać mniejsze opóźnienie dostarczania na żywo, użyj wartości 1 sekundy.

1. OPCJONALNIE: Ustaw **ustawienie predefiniowane użycia procesora CPU** na *bardzoszybki,* jeśli używasz komputera o niskiej mocy obliczeniowej. Opcjonalnie można ustawić kbps na coś niższego, jeśli istnieją niepożądane warunki sieciowe.

   ![Ustawienia wyjściowe OBS](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Pozostaw pozostałe ustawienia bez pożytku i kliknij **przycisk OK**.

### <a name="start-streaming"></a>Rozpocznij przesyłanie strumieniowe

1. W panelu **Sterowanie** kliknij pozycję **Rozpocznij przesyłanie strumieniowe**.

    ![Przycisk rozpoczęcia przesyłania strumieniowego OBS](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Przełącz się na ekran zdarzenia usługi Azure Media Services Live w przeglądarce i kliknij łącze **Załaduj ponownie odtwarzacz.** Strumień powinien być widoczny w odtwarzaczu podglądu.

## <a name="set-up-outputs"></a>Konfigurowanie wyjść

Ta część skonfiguruje swoje wyjścia i pozwoli Ci zapisać nagranie transmisji na żywo.  

> [!NOTE]
> Aby można było przesyłać strumieniowo to dane wyjściowe, punkt końcowy przesyłania strumieniowego musi być uruchomiony. Zobacz później [Uruchom domyślną sekcję punktu końcowego przesyłania strumieniowego.](#run-the-default-streaming-endpoint)

1. Wybierz **łącze Utwórz wyjścia** poniżej przeglądarki **wideo Wyjścia.**
1. Jeśli chcesz, edytuj nazwę danych wyjściowych w polu **Nazwa** na coś bardziej przyjaznego dla użytkownika, aby można było je łatwo znaleźć później.

   ![Pole Nazwa wyjściowa](media/live-events-wirecast-quickstart/output-name.png)
1. Zostaw na razie całą resztę pudełek w spokoju.
1. Wybierz **przycisk Dalej,** aby dodać lokalizator przesyłania strumieniowego.
1. Zmień nazwę lokalizatora na coś bardziej przyjaznego dla użytkownika, jeśli chcesz.

   ![Pole nazwa lokalizatora](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Zostaw wszystko inne na tym ekranie w spokoju na razie.
1. Wybierz pozycję **Utwórz**.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Odtwarzanie emisji wyjściowej przy użyciu programu Azure Media Player

1. Skopiuj adres URL przesyłania strumieniowego w **odtwarzaczu wideo Wyjściowe.**
1. W przeglądarce sieci Web otwórz [demo programu Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Wklej adres URL przesyłania strumieniowego do pola **adresu URL** programu Azure Media Player.
1. Wybierz przycisk **Aktualizuj odtwarzacz.**
1. Wybierz ikonę **Odtwórz** w filmie, aby zobaczyć transmisję na żywo.

## <a name="stop-the-broadcast"></a>Zatrzymywanie transmisji

Jeśli uważasz, że przesyłałeś wystarczającą ilość zawartości, zatrzymaj emisję.

1. W portalu wybierz pozycję **Zatrzymaj**.

1. W obs wybierz przycisk **Zatrzymaj przesyłanie strumieniowe** w panelu **Sterowanie.** Ten krok zatrzymuje emisję z OBS.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Odtwarzanie danych wyjściowych na żądanie przy użyciu programu Azure Media Player

Dane wyjściowe, które zostały utworzone jest teraz dostępna do przesyłania strumieniowego na żądanie, tak długo, jak punkt końcowy przesyłania strumieniowego jest uruchomiony.

1. Przejdź do listy usługi Media Services i wybierz pozycję **Zasoby**.
1. Znajdź dane wyjściowe zdarzenia utworzone wcześniej i wybierz łącze do zasobu. Zostanie otwarta strona wyjściowa zasobu.
1. Skopiuj adres URL przesyłania strumieniowego pod odtwarzaczem wideo dla zasobu.
1. Wróć do programu Azure Media Player w przeglądarce i wklej adres URL przesyłania strumieniowego do pola adresu URL.
1. Wybierz **pozycję Aktualizuj odtwarzacz**.
1. Wybierz ikonę **Odtwórz** w filmie, aby wyświetlić zasób na żądanie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

> [!IMPORTANT]
> Zatrzymaj usługi! Po wykonaniu kroków w tym przewodniku Szybki start należy zatrzymać zdarzenie na żywo i punkt końcowy przesyłania strumieniowego lub naliczane będą naliczane za czas, w którym pozostają uruchomione. Aby zatrzymać wydarzenie na żywo, zobacz Procedurę [Zatrzymania emisji,](#stop-the-broadcast) kroki 2 i 3.

Aby zatrzymać punkt końcowy przesyłania strumieniowego:

1. Na liście usługi Media Services wybierz pozycję **Punkty końcowe przesyłania strumieniowego**.
2. Wybierz domyślny punkt końcowy przesyłania strumieniowego, który został uruchomiony wcześniej. Ten krok otwiera stronę punktu końcowego.
3. Wybierz **pozycję Zatrzymaj**.

> [!TIP]
> Jeśli nie chcesz, aby zasoby były przechowywane z tego zdarzenia, usuń je, aby nie były naliczane naliczane płatności za przechowywanie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wydarzenia na żywo i wyniki na żywo w umiań Media Services](./live-events-outputs-concept.md)
