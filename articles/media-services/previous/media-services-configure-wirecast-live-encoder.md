---
title: Skonfiguruj koder Wirecast usługi webstream, aby wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów | Microsoft Docs
description: 'W tym temacie pokazano, jak skonfigurować koder Wirecast na żywo, aby wysyłał strumień o pojedynczej szybkości transmisji bitów do kanałów AMS, dla których włączono obsługę kodowania na żywo. '
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: a736270919f36cb7d4bbd4e686a98ddc57f402fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016835"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Użyj kodera Wirecast, aby wysłać strumień na żywo o pojedynczej szybkości transmisji bitów

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Narzędzie Wirecast](media-services-configure-wirecast-live-encoder.md)
>

W tym artykule opisano sposób konfigurowania kodera Wirecast na żywo usługi [Telestream](https://www.telestream.net/wirecast/overview.htm) w celu wysyłania strumienia pojedynczego szybkości transmisji bitów do kanałów usługi AMS obsługujących kodowanie na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku pokazano, jak zarządzać usługą Azure Media Services (AMS) za pomocą narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze z systemem Windows. Jeśli jesteś w systemie Mac lub Linux, użyj Azure Portal, aby utworzyć [kanały](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Kodery muszą obsługiwać protokół TLS 1,2 w przypadku używania protokołów RTMP. Użyj Wirecast w wersji 13.0.2 lub nowszej z powodu wymagania TLS 1,2.

## <a name="prerequisites"></a>Wymagania wstępne
* [Tworzenie konta usługi Azure Media Services](media-services-portal-create-account.md)
* Upewnij się, że punkt końcowy przesyłania strumieniowego jest uruchomiony. Aby uzyskać więcej informacji, zobacz [Zarządzanie punktami końcowymi przesyłania strumieniowego na koncie Media Services](media-services-portal-manage-streaming-endpoints.md)
* Zainstaluj najnowszą wersję narzędzia [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Uruchom narzędzie i Połącz się z kontem AMS.

## <a name="tips"></a>Porady
* Gdy to możliwe, używaj przewodowego połączenia internetowego.
* Dobrą praktyką jest podwajanie szybkości transmisji bitów podczas określania wymagań dotyczących przepustowości. Chociaż nie jest to wymaganie obowiązkowe, pomaga ograniczyć wpływ przeciążenia sieci.
* W przypadku korzystania z koderów opartych na oprogramowaniu Zamknij wszystkie zbędne programy.

## <a name="create-a-channel"></a>Tworzenie kanału
1. W narzędziu AMSE przejdź do karty **Live** , a następnie kliknij prawym przyciskiem myszy w obszarze kanału. Wybierz pozycję **Utwórz kanał...** z menu.

    ![Zrzut ekranu przedstawia wybrany kanał z menu.](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Określ nazwę kanału pole Opis jest opcjonalne. W obszarze Ustawienia kanału wybierz pozycję **standardowa** dla opcji Live Encoding, z protokołem wejściowym ustawionym na wartość **RTMP**. Wszystkie inne ustawienia można pozostawić zgodnie z oczekiwaniami.

    Upewnij się, że wybrano wartość **Uruchom nowy kanał teraz** .

3. Kliknij pozycję **Utwórz kanał**.

   ![Zrzut ekranu przedstawia okno dialogowe Tworzenie kanału na żywo.](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Kanał może trwać przez 20 minut.
>
>

Podczas uruchamiania kanału można [skonfigurować koder](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Rozliczanie zaczyna się zaraz po przeniesieniu kanału w stan gotowości. Aby uzyskać więcej informacji, zobacz [Stany kanału](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Konfigurowanie kodera Wirecast Telestream
W tym samouczku używane są następujące ustawienia wyjściowe. W dalszej części tej sekcji opisano kroki konfiguracji bardziej szczegółowo.

**Wideo**:

* Koder-dekoder: H.264
* Profil: wysoki (poziom 4.0)
* Szybkość transmisji bitów: 5000 KB/s
* Klatka kluczowa: 2 sekundy (60 s)
* Szybkość klatek: 30

**Dźwięk**:

* Koder-dekoder: AAC (LC)
* Szybkość transmisji bitów: 192 kb/s
* Częstotliwość próbkowania: 44,1 kHz

### <a name="configuration-steps"></a>Kroki konfiguracji
1. Otwórz aplikację Wirecast usługi webstream na używanej maszynie i skonfiguruj ją na potrzeby przesyłania strumieniowego RTMP.
2. Skonfiguruj dane wyjściowe, przechodząc do karty **dane wyjściowe** i wybierając pozycję **Ustawienia wyjściowe...**.

    Upewnij się, że **miejsce docelowe danych wyjściowych** jest ustawione na **serwer RTMP**.
3. Kliknij przycisk **OK**.
4. Na stronie Ustawienia Ustaw pole **docelowe** , które ma być **Azure Media Services**.

    Profil kodowania jest wstępnie wybrany dla **platformy Azure H. 264 720 16:9 (1280x720)**. Aby dostosować te ustawienia, wybierz ikonę koła zębatego z prawej strony listy rozwijanej, a następnie wybierz pozycję **nowe ustawienie wstępne**.

    ![Zrzut ekranu przedstawia okno dialogowe Wybieranie szablonu z wybraną pozycją BlobTrigger.](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Skonfiguruj ustawienia wstępne kodera.

    Nazwij ustawienie wstępne i sprawdź następujące zalecane ustawienia:

    **Wideo**

   * Koder: MainConcept H. 264
   * Liczba klatek na sekundę: 30
   * Średnia szybkość transmisji bitów: 5000 kbit/s (można ją dostosować w zależności od ograniczeń sieci)
   * Profil: główny
   * Klatka kluczowa co: 60 klatek

     **Dźwięk**

   * Docelowa szybkość transmisji bitów: 192 kbit/s
   * Częstotliwość próbkowania: 44,100 kHz

     ![Zrzut ekranu przedstawia ustawienia wstępne kodera dla AzureTest1.](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Naciśnij pozycję **Zapisz**.

    Pole kodowania ma teraz nowo utworzony profil dostępny do wyboru.

    Upewnij się, że wybrano nowy profil.
7. Pobierz adres URL wejścia kanału, aby przypisać go do **punktu końcowego Wirecast RTMP**.

    Wróć do narzędzia AMSE i sprawdź stan ukończenia kanału. Po zmianie stanu od **rozpoczęcia** na **uruchomiony** możesz uzyskać wejściowy adres URL.

    Gdy kanał jest uruchomiony, kliknij prawym przyciskiem myszy nazwę kanału, przejdź w dół, aby umieścić wskaźnik myszy nad **Kopiuj wejściowy adres URL do schowka** , a następnie wybierz **podstawowy adres URL wejścia**.  

    ![Zrzut ekranu przedstawia opcję Kopiuj dane wejściowe U R L do schowka dla podstawowego wejścia U R L.](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. W oknie **Ustawienia wyjściowe** Wirecast wklej te informacje w polu **adres** sekcji danych wyjściowych i przypisz nazwę strumienia.

    ![Zrzut ekranu przedstawia ustawienia danych wyjściowych.](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Wybierz przycisk **OK**.
2. Na głównym ekranie **Wirecast** upewnij się, że źródła danych wejściowych dla wideo i audio są gotowe, a następnie kliknij pozycję **strumień** w lewym górnym rogu.

    ![Zrzut ekranu przedstawia przycisk strumienia Wirecast.](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Zanim klikniesz pozycję **Stream**, **musisz** upewnić się, że kanał jest gotowy.
> Upewnij się również, że kanał nie zostanie pozostawiony w stanie gotowym bez źródła danych wejściowych przez dłużej niż > 15 minut.
>
>

## <a name="test-playback"></a>Odtwarzanie testów

Przejdź do narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanał, który ma zostać przetestowany. W menu Umieść wskaźnik myszy nad **odtwarzaniem wersji zapoznawczej** i wybierz pozycję **z Azure Media Player**.  

![Zrzut ekranu pokazuje, że wybrano opcję odtwarzania Podgląd przy użyciu Azure Media Player.](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Jeśli w odtwarzaczu pojawi się strumień, koder został prawidłowo skonfigurowany do łączenia z usługą AMS.

Jeśli wystąpi błąd, należy zresetować kanał i dopasować ustawienia kodera. Aby uzyskać wskazówki, zobacz artykuł dotyczący [rozwiązywania problemów](media-services-troubleshooting-live-streaming.md) .  

## <a name="create-a-program"></a>Tworzenie programu
1. Po potwierdzeniu odtwarzania kanału Utwórz program. Na karcie na **żywo** w narzędziu AMSE kliknij prawym przyciskiem myszy w obszarze programu i wybierz pozycję **Utwórz nowy program**.  

    ![Zrzut ekranu przedstawia wybraną opcję Utwórz program.](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Nazwij program i, w razie potrzeby, Dostosuj **Długość okna archiwum** (domyślnie cztery godziny). Możesz również określić lokalizację magazynu lub pozostawić jako domyślną.  
3. Zaznacz pole **Uruchom program teraz** .
4. Kliknij pozycję **Utwórz program**.  

   >[!NOTE]
   >Tworzenie programu trwa krócej niż tworzenie kanału.
       
5. Po uruchomieniu programu Potwierdź odtwarzanie, klikając prawym przyciskiem myszy program i przechodząc do **odtwarzania programów** , a następnie wybierając opcję **z Azure Media Player**.  
6. Po potwierdzeniu ponownie kliknij prawym przyciskiem myszy program i wybierz polecenie **Kopiuj wyjściowy adres URL do schowka** (lub Pobierz te informacje z opcji **Informacje o programie i ustawienia** z menu).

Strumień jest teraz gotowy do osadzenia w odtwarzaczu lub jest dystrybuowany do odbiorców na potrzeby wyświetlania na żywo.  

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Aby uzyskać wskazówki, zobacz artykuł dotyczący [rozwiązywania problemów](media-services-troubleshooting-live-streaming.md) .

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
