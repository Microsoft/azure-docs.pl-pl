---
title: Tworzenie strumienia na żywo za pomocą programu OBS Studio
description: Dowiedz się, jak utworzyć Azure Media Services strumień na żywo przy użyciu portalu i programu OBS Studio
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 83f072b67f5aa162137a55d2b311dccf0daf7f53
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98956076"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Tworzenie strumienia Azure Media Services na żywo za pomocą OBS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ten przewodnik Szybki Start pomoże Ci utworzyć Azure Media Services strumień na żywo przy użyciu Azure Portal i otwartego programu Broadcast Studio (OBS). Przyjęto założenie, że masz subskrypcję platformy Azure i utworzono konto Media Services.

W tym przewodniku szybki start omówiono następujące zagadnienia:

- Konfigurowanie kodera lokalnego przy użyciu OBS.
- Konfigurowanie strumienia na żywo.
- Konfigurowanie danych wyjściowych strumienia na żywo.
- Uruchamianie domyślnego punktu końcowego przesyłania strumieniowego.
- Używanie Azure Media Player do wyświetlania strumienia na żywo i danych wyjściowych na żądanie.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Otwórz przeglądarkę internetową i przejdź do [Microsoft Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Konfigurowanie kodera lokalnego przy użyciu OBS

1. Pobierz i zainstaluj program OBS dla systemu operacyjnego w [witrynie sieci Web Open Broadcast Software](https://obsproject.com/).
1. Uruchom aplikację i pozostaw ją otwartą.

## <a name="run-the-default-streaming-endpoint"></a>Uruchom domyślny punkt końcowy przesyłania strumieniowego

1. Wybierz **punkty końcowe przesyłania strumieniowego** na liście Media Services.

   ![Element menu punktów końcowych przesyłania strumieniowego](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Jeśli domyślny stan punktu końcowego przesyłania strumieniowego jest zatrzymany, wybierz go. Ten krok spowoduje przejście do strony dla tego punktu końcowego.
1. Wybierz pozycję **Uruchom**.

   ![Przycisk Start dla punktu końcowego przesyłania strumieniowego](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Konfigurowanie Azure Media Services strumienia na żywo

1. Przejdź do konta Azure Media Services w portalu, a następnie wybierz pozycję **przesyłanie strumieniowe na żywo** z listy **Media Services** .

   ![Link do przesyłania strumieniowego na żywo](media/live-events-obs-quickstart/select-live-streaming.png)
1. Wybierz pozycję **Dodaj wydarzenie na żywo** , aby utworzyć nowe zdarzenie przesyłania strumieniowego na żywo.

   ![Ikona dodawania zdarzenia na żywo](media/live-events-obs-quickstart/add-live-event.png)
1. Wprowadź nazwę nowego zdarzenia, na przykład *TestLiveEvent*, w polu **Nazwa zdarzenia na żywo** .

   ![Pole nazwy zdarzenia na żywo](media/live-events-obs-quickstart/live-event-name.png)
1. Wprowadź opcjonalny opis zdarzenia w polu **Opis** .
1. Wybierz opcję **przekazywanie bez kodowania w chmurze** .

   ![Opcja kodowania w chmurze](media/live-events-obs-quickstart/cloud-encoding.png)
1. Wybierz opcję **RTMP** .
1. Upewnij się, że **nie** wybrano opcji **Uruchom wydarzenie na żywo**, aby uniknąć naliczania opłat za wydarzenie na żywo przed jego gotowością. (Rozliczenie rozpocznie się po rozpoczęciu zdarzenia na żywo).

   ![Opcja uruchamiania zdarzenia na żywo](media/live-events-obs-quickstart/start-live-event-no.png)
1. Wybierz przycisk **Recenzja + Utwórz** , aby przejrzeć ustawienia.
1. Wybierz przycisk **Utwórz** , aby utworzyć wydarzenie na żywo. Następnie nastąpi powrót do listy wydarzeń na żywo.
1. Wybierz łącze do utworzonego zdarzenia na żywo. Zwróć uwagę, że zdarzenie zostało zatrzymane.
1. Pozostaw Tę stronę otwartą w przeglądarce. Powrócimy do niego później.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Konfigurowanie strumienia na żywo za pomocą programu OBS Studio

OBS rozpoczyna się od domyślnej sceny, ale bez wybranych danych wejściowych.

   ![Ekran domyślny OBS](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Dodaj źródło wideo

1. W panelu **źródła** kliknij ikonę **Dodaj** , aby wybrać nowe urządzenie źródłowe. Zostanie otwarte menu **źródła** .

1. Wybierz pozycję **urządzenie przechwytywania wideo** z menu urządzenie źródłowe. Zostanie otwarte menu **Utwórz/wybierz źródło** .

   ![Menu źródeł OBS z wybranym urządzeniem wideo](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Wybierz pozycję **Dodaj istniejący** przycisk radiowy, a następnie kliknij przycisk **OK**. Zostanie otwarte menu **Właściwości urządzenia wideo** .

   ![OBS nowe menu Źródło wideo z Dodaj istniejącymi](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. Z listy rozwijanej **urządzenie** wybierz dane wejściowe wideo, które mają być używane na potrzeby emisji. Pozostaw pozostałe ustawienia dla tej pory, a następnie kliknij przycisk **OK**. Źródło danych wejściowych zostanie dodane do panelu **źródła** , a w obszarze **podglądu** zostanie wyświetlony widok dane wejściowe wideo.

   ![Ustawienia aparatu OBS](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Dodawanie źródła audio

1. W panelu **źródła** kliknij ikonę **Dodaj** , aby wybrać nowe urządzenie źródłowe. Zostanie otwarte menu urządzenie źródłowe.

1. Wybierz pozycję **przechwytywanie danych wejściowych audio** z menu urządzenie źródłowe. Zostanie otwarte menu **Utwórz/wybierz źródło** .

   ![Menu źródeł OBS z wybranym urządzeniem audio](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Wybierz pozycję **Dodaj istniejący** przycisk radiowy, a następnie kliknij przycisk **OK**. Zostanie otwarte okno dialogowe **Właściwości przechwytywania danych wejściowych audio** .

   ![Źródło Audio OBS z dodaniem istniejącego wybranego ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. Z listy rozwijanej **urządzenie** wybierz urządzenie przechwytywania audio, które ma być używane dla emisji. Pozostaw pozostałe ustawienia dla tej pory, a następnie kliknij przycisk OK. Urządzenie przechwytywania audio zostanie dodane do panelu miksera audio.

   ![Lista rozwijana wyboru urządzenia audio OBS](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Konfigurowanie przesyłania strumieniowego w programie OBS

W następnej procedurze powrócisz do Azure Media Services w przeglądarce, aby skopiować wejściowy adres URL w celu wprowadzenia do ustawień wyjściowych:

1. Na stronie Azure Media Services portalu wybierz pozycję **Rozpocznij** , aby rozpocząć wydarzenie na żywo strumienia. (Rozliczenia zaczynają się teraz).

   ![Ikona Start](media/live-events-obs-quickstart/start.png)
1. Ustaw przełącznik **RTMP** na **RTMP**.
1. W polu **wejściowy adres URL** Skopiuj adres URL do Schowka.

   ![Wejściowy adres URL](media/live-events-obs-quickstart/input-url.png)

1. Przejdź do aplikacji OBS.

1. Kliknij przycisk **Ustawienia** w panelu **sterowania** . Zostaną otwarte opcje ustawień.

   ![Panel sterowania OBS z wybranymi ustawieniami](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Wybierz pozycję **strumień** z menu **Ustawienia** .

1. Z listy rozwijanej **Usługa** wybierz pozycję Pokaż wszystko, a następnie wybierz pozycję **niestandardowe...**.

1. W polu **serwer** Wklej adres URL RTMP skopiowany do Schowka.

1. Wprowadź coś w polu **klucz strumienia** .  Nie ma znaczenia, ale musi mieć wartość.

    ![Ustawienia strumienia OBS](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Wybierz pozycję **dane wyjściowe** z menu **Ustawienia** .

1. Wprowadź wartość *2* w polu **Interwał klatek kluczowych** . Ustawia długość fragmentu na 2 sekundy. W celu uzyskania małych opóźnień na żywo Użyj wartości 1 sekundy.

1. Opcjonalnie: dla **ustawienia wstępnego użycia procesora CPU** ustaw wartość *veryfast* , jeśli używasz komputera z niską mocą obliczeniową. Opcjonalnie można ustawić mniejszą wartość w KB/s, jeśli istnieją niepożądane warunki sieciowe.

   ![Ustawienia wyjściowe OBS](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Pozostaw pozostałe ustawienia bez zmian i kliknij przycisk **OK**.

### <a name="start-streaming"></a>Rozpocznij przesyłanie strumieniowe

1. W panelu **sterowania** kliknij pozycję **Rozpocznij przesyłanie strumieniowe**.

    ![Przycisk Rozpocznij przesyłanie strumieniowe OBS](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Przejdź do ekranu Azure Media Services zdarzenia na żywo w przeglądarce i kliknij link **Załaduj ponownie odtwarzacz** . Twój strumień powinien być teraz widoczny w odtwarzaczu w wersji zapoznawczej.

## <a name="set-up-outputs"></a>Konfigurowanie danych wyjściowych

Ta część skonfiguruje dane wyjściowe i umożliwi zapisanie nagrania strumienia na żywo.  

> [!NOTE]
> Aby przesłać strumieniowo te dane wyjściowe, musi być uruchomiony punkt końcowy przesyłania strumieniowego. Zobacz sekcję [Uruchom ponownie domyślny punkt końcowy przesyłania strumieniowego](#run-the-default-streaming-endpoint) .

1. Wybierz link **Utwórz dane wyjściowe** poniżej widocznej **przeglądarki wideo.**
1. Jeśli chcesz, możesz zmienić nazwę danych wyjściowych w polu **Nazwa** na bardziej zrozumiały dla użytkownika, aby można było ją łatwo znaleźć w przyszłości.

   ![Pole nazwy wyjściowej](media/live-events-wirecast-quickstart/output-name.png)
1. Pozostaw wszystkie pozostałe pola teraz.
1. Wybierz pozycję **dalej** , aby dodać lokalizator przesyłania strumieniowego.
1. Zmień nazwę lokalizatora na bardziej przyjazny dla użytkownika, jeśli chcesz.

   ![Pole nazwy lokalizatora](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Pozostaw teraz wszystkie inne elementy na tym ekranie.
1. Wybierz przycisk **Utwórz**.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Odtwórz emisję wyjściową przy użyciu Azure Media Player

1. Skopiuj adres URL przesyłania strumieniowego z **wyjściowego** odtwarzacza wideo.
1. W przeglądarce sieci Web Otwórz [demonstrację Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Wklej adres URL przesyłania strumieniowego do pola **adres url** Azure Media Player.
1. Wybierz przycisk **Aktualizuj odtwarzacz** .
1. Wybierz ikonę **odtwarzania** na filmie wideo, aby zobaczyć swój strumień na żywo.

## <a name="stop-the-broadcast"></a>Zatrzymaj emisję

Jeśli uważasz, że masz zbyt dużą zawartość, Zatrzymaj emisję.

1. W portalu wybierz pozycję **Zatrzymaj**.

1. W OBS wybierz przycisk **Zatrzymaj przesyłanie strumieniowe** w panelu **sterowania** . Ten krok powoduje zatrzymanie emisji z OBS.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Odtwórz dane wyjściowe na żądanie przy użyciu Azure Media Player

Utworzone dane wyjściowe są teraz dostępne do przesyłania strumieniowego na żądanie, o ile jest uruchomiony punkt końcowy przesyłania strumieniowego.

1. Przejdź do listy Media Services i wybierz pozycję **zasoby**.
1. Znajdź utworzone wcześniej dane wyjściowe zdarzenia i wybierz łącze do elementu zawartości. Zostanie otwarta strona dane wyjściowe zasobu.
1. Skopiuj adres URL przesyłania strumieniowego w odtwarzaczu wideo dla elementu zawartości.
1. Wróć do Azure Media Player w przeglądarce i wklej adres URL przesyłania strumieniowego do pola adresu URL.
1. Wybierz pozycję **Aktualizuj odtwarzacz**.
1. Wybierz ikonę **odtwarzania** na filmie wideo, aby wyświetlić zasób na żądanie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

> [!IMPORTANT]
> Zatrzymaj usługi! Po wykonaniu kroków opisanych w tym przewodniku szybki start Pamiętaj, aby zatrzymać wydarzenie na żywo i punkt końcowy przesyłania strumieniowego, lub naliczanie opłat za czas, w którym pozostaną one uruchomione. Aby zatrzymać wydarzenie na żywo, zobacz procedurę [zatrzymania emisji](#stop-the-broadcast) , kroki 2 i 3.

Aby zatrzymać punkt końcowy przesyłania strumieniowego:

1. Z listy Media Services wybierz pozycję **punkty końcowe przesyłania strumieniowego**.
2. Wybierz domyślny punkt końcowy przesyłania strumieniowego, który został wcześniej uruchomiony. Ten krok powoduje otwarcie strony punktu końcowego.
3. Wybierz pozycję **Zatrzymaj**.

> [!TIP]
> Jeśli nie chcesz zachować zasobów z tego zdarzenia, pamiętaj o ich usunięciu, aby nie naliczać opłat za magazyn.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zdarzenia na żywo i wyjście na żywo w Media Services](./live-events-outputs-concept.md)
