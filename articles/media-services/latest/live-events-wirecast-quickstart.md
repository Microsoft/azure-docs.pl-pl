---
title: Tworzenie Azure Media Services strumienia na żywo
description: Dowiedz się, jak utworzyć Azure Media Services strumień na żywo przy użyciu portalu i Wirecast
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 76bbb980b6430f7cffc23ec078e2c932128dc637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89265273"
---
# <a name="create-an-azure-media-services-live-stream"></a>Tworzenie Azure Media Services strumienia na żywo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ten przewodnik Szybki Start pomoże Ci utworzyć Azure Media Services strumień na żywo przy użyciu Azure Portal i usługi Wirecast. Przyjęto założenie, że masz subskrypcję platformy Azure i utworzono konto Media Services.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Otwórz przeglądarkę internetową i przejdź do [Microsoft Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

W tym przewodniku szybki start omówiono następujące zagadnienia:

- Skonfigurowanie kodera lokalnego przy użyciu bezpłatnej wersji próbnej usługi Telestream Wirecast.
- Konfigurowanie strumienia na żywo.
- Konfigurowanie danych wyjściowych strumienia na żywo.
- Uruchamianie domyślnego punktu końcowego przesyłania strumieniowego.
- Używanie Azure Media Player do wyświetlania strumienia na żywo i danych wyjściowych na żądanie.

Aby zachować prostotę, użyjemy ustawienia wstępnego kodowania dla Azure Media Services w Wirecast, kodowaniu w chmurze i RTMP.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Konfigurowanie kodera lokalnego przy użyciu Wirecast

1. Pobierz i zainstaluj program Wirecast dla systemu operacyjnego w [witrynie sieci Web usługi Telestream](https://www.telestream.net).
1. Uruchom aplikację i Użyj swojego ulubionego adresu e-mail do zarejestrowania produktu. Pozostaw otwartą aplikację.
1. W otrzymanej wiadomości e-mail zweryfikuj swój adres e-mail. Następnie aplikacja zacznie korzystać z bezpłatnej wersji próbnej.
1. Zalecane: Obejrzyj samouczek wideo na ekranie Otwieranie aplikacji.

## <a name="set-up-an-azure-media-services-live-stream"></a>Konfigurowanie Azure Media Services strumienia na żywo

1. Przejdź do konta Azure Media Services w portalu, a następnie wybierz pozycję **przesyłanie strumieniowe na żywo** z listy **Media Services** .

   ![Link do przesyłania strumieniowego na żywo](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Wybierz pozycję **Dodaj wydarzenie na żywo** , aby utworzyć nowe zdarzenie przesyłania strumieniowego na żywo.

   ![Ikona dodawania zdarzenia na żywo](media/live-events-wirecast-quickstart/add-live-event.png)
1. Wprowadź nazwę nowego zdarzenia, na przykład *TestLiveEvent*, w polu **Nazwa zdarzenia na żywo** .

   ![Pole nazwy zdarzenia na żywo](media/live-events-wirecast-quickstart/live-event-name.png)
1. Wprowadź opcjonalny opis zdarzenia w polu **Opis** .
1. Wybierz opcję **przekazywanie bez kodowania w chmurze** .

   ![Opcja kodowania w chmurze](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Wybierz opcję **RTMP** .
1. Upewnij się, że **nie** wybrano opcji **Uruchom wydarzenie na żywo**, aby uniknąć naliczania opłat za wydarzenie na żywo przed jego gotowością. (Rozliczenie rozpocznie się po rozpoczęciu zdarzenia na żywo).

   ![Opcja uruchamiania zdarzenia na żywo](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. Wybierz przycisk **Recenzja + Utwórz** , aby przejrzeć ustawienia.
1. Wybierz przycisk **Utwórz** , aby utworzyć wydarzenie na żywo. Następnie nastąpi powrót do listy wydarzeń na żywo.
1. Wybierz łącze do utworzonego zdarzenia na żywo. Zwróć uwagę, że zdarzenie zostało zatrzymane.
1. Pozostaw Tę stronę otwartą w przeglądarce. Powrócimy do niego później.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Konfigurowanie strumienia na żywo za pomocą programu Wirecast Studio

1. W aplikacji Wirecast wybierz pozycję **Utwórz pusty dokument** z menu głównego, a następnie wybierz pozycję **Kontynuuj**.

   ![Wirecast ekranu startowego](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Umieść kursor nad pierwszą warstwą w obszarze **warstwy Wirecast** .  Wybierz ikonę **Dodaj** , która zostanie wyświetlona, a następnie wybierz dane wejściowe wideo, które chcesz przesłać strumieniowo.

   ![Ikona dodawania Wirecast](media/live-events-wirecast-quickstart/add-icon.png)

   Zostanie otwarte okno dialogowe **warstwa główna 1** .
1. Wybierz opcję **Przechwytywanie wideo** z menu, a następnie wybierz kamerę, której chcesz użyć.

   ![Obszar podglądu przechwytywania wideo](media/live-events-wirecast-quickstart/video-shot-selection.png)

   Widok z aparatu zostanie wyświetlony w obszarze podglądu.
1. Umieść kursor nad drugą warstwą w obszarze **warstwy Wirecast** . Wybierz ikonę **Dodaj** , która zostanie wyświetlona, a następnie wybierz wejście audio, które chcesz przesłać strumieniowo. Zostanie otwarte okno dialogowe **warstwa główna 2** .
1. Wybierz z menu pozycję **przechwytywanie audio** , a następnie wybierz dane wejściowe audio, których chcesz użyć.

   ![Dane wejściowe przechwytywania audio](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. Z menu głównego wybierz pozycję **Ustawienia wyjściowe**. Zostanie wyświetlone okno dialogowe **Wybierz miejsce docelowe danych wyjściowych** .
1. Wybierz pozycję **Azure Media Services** z listy rozwijanej **Lokalizacja docelowa** . Ustawienie danych wyjściowych Azure Media Services automatycznie wypełnia *większość* ustawień danych wyjściowych.

   ![Ekran ustawień danych wyjściowych Wirecast](media/live-events-wirecast-quickstart/azure-media-services.png)


W następnej procedurze powrócisz do Azure Media Services w przeglądarce, aby skopiować wejściowy adres URL w celu wprowadzenia do ustawień wyjściowych:

1. Na stronie Azure Media Services portalu wybierz pozycję **Rozpocznij** , aby rozpocząć wydarzenie na żywo strumienia. (Rozliczenia zaczynają się teraz).

   ![Ikona Start](media/live-events-wirecast-quickstart/start.png)
2. Ustaw przełącznik **bezpieczny/niebezpieczny** na **niezabezpieczony**. Ten krok ustawia protokół na RTMP zamiast RTMP.
3. W polu **wejściowy adres URL** Skopiuj adres URL do Schowka.

   ![Wejściowy adres URL](media/live-events-wirecast-quickstart/input-url.png)
4. Przejdź do aplikacji Wirecast i wklej **wejściowy adres URL** do pola **adres** w ustawieniach danych wyjściowych.

   ![Wejściowy adres URL Wirecast](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Wybierz przycisk **OK**.

## <a name="set-up-outputs"></a>Konfigurowanie danych wyjściowych

Ta część skonfiguruje dane wyjściowe i umożliwi zapisanie nagrania strumienia na żywo.  

> [!NOTE]
> Aby przesłać strumieniowo te dane wyjściowe, musi być uruchomiony punkt końcowy przesyłania strumieniowego. Zobacz sekcję [Uruchom ponownie domyślny punkt końcowy przesyłania strumieniowego](#run-the-default-streaming-endpoint) .

1. Wybierz link **Utwórz dane wyjściowe** poniżej widocznej **przeglądarki wideo.**
1. Jeśli chcesz, możesz zmienić nazwę danych wyjściowych w polu **Nazwa** na bardziej przyjazny dla użytkownika, aby ułatwić znalezienie go w przyszłości.
   
   ![Pole nazwy wyjściowej](media/live-events-wirecast-quickstart/output-name.png)
1. Pozostaw wszystkie pozostałe pola teraz.
1. Wybierz pozycję **dalej** , aby dodać lokalizator przesyłania strumieniowego.
1. Zmień nazwę lokalizatora na bardziej przyjazny dla użytkownika, jeśli chcesz.
   
   ![Pole nazwy lokalizatora](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Pozostaw teraz wszystkie inne elementy na tym ekranie.
1. Wybierz przycisk **Utwórz**.

## <a name="start-the-broadcast"></a>Rozpocznij emitowanie

1. W Wirecast wybierz pozycję **wyjściowe**  >  **Rozpocznij/Zatrzymaj rozgłaszanie**  >  **Azure Media Services Start: Azure Media Services** z menu głównego.

   ![Elementy menu uruchamiania emisji](media/live-events-wirecast-quickstart/start-broadcast.png)

   Gdy strumień został wysłany do zdarzenia na żywo, **aktywne** okno w Wirecast pojawia się w odtwarzaczu wideo na stronie zdarzenia na żywo w Azure Media Services.

1. Wybierz przycisk **Przejdź** w oknie Podgląd, aby rozpocząć emitowanie wideo i dźwięku wybranego dla warstw Wirecast.

   ![Przycisk Wirecast](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Jeśli wystąpi błąd, spróbuj ponownie załadować odtwarzacz, wybierając link **Załaduj ponownie odtwarzacz** powyżej odtwarzacza.

## <a name="run-the-default-streaming-endpoint"></a>Uruchom domyślny punkt końcowy przesyłania strumieniowego

1. Wybierz **punkty końcowe przesyłania strumieniowego** na liście Media Services.

   ![Element menu punktów końcowych przesyłania strumieniowego](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Jeśli domyślny stan punktu końcowego przesyłania strumieniowego jest zatrzymany, wybierz go. Ten krok spowoduje przejście do strony dla tego punktu końcowego.
1. Wybierz pozycję **Uruchom**.
   
   ![Przycisk Start dla punktu końcowego przesyłania strumieniowego](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Odtwórz emisję wyjściową przy użyciu Azure Media Player

1. Skopiuj adres URL przesyłania strumieniowego z **wyjściowego** odtwarzacza wideo.
1. W przeglądarce sieci Web Otwórz [demonstrację Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Wklej adres URL przesyłania strumieniowego do pola **adres url** Azure Media Player.
1. Wybierz przycisk **Aktualizuj odtwarzacz** .
1. Wybierz ikonę **odtwarzania** na filmie wideo, aby zobaczyć swój strumień na żywo.

## <a name="stop-the-broadcast"></a>Zatrzymaj emisję

Jeśli uważasz, że masz zbyt dużą zawartość, Zatrzymaj emisję.

1. W Wirecast wybierz przycisk **Broadcast (emisja** ). Ten krok powoduje zatrzymanie emisji z Wirecast.
1. W portalu wybierz pozycję **Zatrzymaj**. Następnie zostanie wyświetlony komunikat ostrzegawczy, że strumień na żywo zostanie zatrzymany, ale dane wyjściowe staną się teraz zasobem na żądanie.
1. Wybierz pozycję **Zatrzymaj** w komunikacie ostrzegawczym. Azure Media Player teraz zawiera błąd, ponieważ strumień na żywo nie jest już dostępny.

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
