---
title: Konfigurowanie kodera Telestream Wirecast do wysyłania pojedynczego strumienia bitów na żywo | Dokumenty firmy Microsoft
description: 'W tym temacie pokazano, jak skonfigurować koder wirecast na żywo do wysyłania pojedynczego strumienia szybkości transmisji bitów do kanałów usługi AMS, które są włączone do kodowania na żywo. '
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 8e3705aaecb0760513f0605aece89b7ffc0044a8
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641645"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Użyj kodera Wirecast, aby wysłać pojedynczy strumień transmisji bitów na żywo 
> [!div class="op_single_selector"]
> * [Narzędzie Wirecast](media-services-configure-wirecast-live-encoder.md)
>

W tym artykule pokazano, jak skonfigurować koder na żywo [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) do wysyłania pojedynczego strumienia szybkości transmisji bitów do kanałów usługi AMS, które są włączone do kodowania na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku pokazano, jak zarządzać usługą Azure Media Services (AMS) za pomocą narzędzia Usługi Azure Media Services Explorer (AMSE). To narzędzie działa tylko na komputerze z systemem Windows. Jeśli korzystasz z komputera Mac lub Linux, użyj witryny Azure portal do tworzenia [kanałów](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programów](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Enkodery muszą obsługiwać protokół TLS 1.2 podczas korzystania z protokołów RTMPS. Ze względu na wymagania dotyczące protokołu TLS 1.2 należy używać funkcji Wirecast w wersji 13.0.2 lub nowszej.

## <a name="prerequisites"></a>Wymagania wstępne
* [Tworzenie konta usługi Azure Media Services](media-services-portal-create-account.md)
* Upewnij się, że jest uruchomiony punkt końcowy przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [Zarządzanie punktami końcowymi przesyłania strumieniowego na koncie usługi Media Services](media-services-portal-manage-streaming-endpoints.md)
* Zainstaluj najnowszą wersję narzędzia [AMSE.](https://github.com/Azure/Azure-Media-Services-Explorer)
* Uruchom narzędzie i połącz się ze swoim kontem AMS.

## <a name="tips"></a>Porady
* Gdy to możliwe, używaj przewodowego połączenia internetowego.
* Dobrą praktyką jest podwajanie szybkości transmisji bitów podczas określania wymagań dotyczących przepustowości. Chociaż nie jest to wymóg obowiązkowy, pomaga złagodzić wpływ przeciążenia sieci.
* W przypadku korzystania z koderów programowych zamknij wszelkie niepotrzebne programy.

## <a name="create-a-channel"></a>Tworzenie kanału
1. W narzędziu AMSE przejdź do karty **Aktywne** i kliknij prawym przyciskiem myszy obszar kanału. Wybierz **pozycję Utwórz kanał...** z menu.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Określ nazwę kanału, pole opisu jest opcjonalne. W obszarze Ustawienia kanału wybierz opcję **Standardowy** dla opcji Kodowanie na żywo z protokołem wejściowym ustawionym na **RTMP**. Możesz pozostawić wszystkie inne ustawienia, tak jak jest.

    Upewnij się, że zostanie wybrana **opcja Rozpocznij nowy kanał.**

3. Kliknij **pozycję Utwórz kanał**.

   ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Uruchomienie kanału może potrwać nawet 20 minut.
>
>

Podczas uruchamiania kanału można [skonfigurować koder](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Rozliczenia rozpoczyna się, jak tylko kanał przechodzi w stan gotowości. Aby uzyskać więcej informacji, zobacz [stany kanału](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Konfigurowanie kodera Telestream Wirecast
W tym samouczku używane są następujące ustawienia wyjściowe. W dalszej części tej sekcji opisano kroki konfiguracji bardziej szczegółowo.

**Film:**

* Koder-dekoder: H.264
* Profil: wysoki (poziom 4.0)
* Szybkość transmisji bitów: 5000 kb/s
* Klatka kluczowa: 2 sekundy (60 sekund)
* Liczba klatek na sekundę: 30

**Dźwięk**:

* Koder-dekoder: AAC (LC)
* Szybkość transmisji bitów: 192 kb/s
* Częstotliwość próbkowania: 44,1 kHz

### <a name="configuration-steps"></a>Kroki konfiguracji
1. Otwórz aplikację Telestream Wirecast na używanym komputerze i skonfiguruj do przesyłania strumieniowego RTMP.
2. Skonfiguruj dane wyjściowe, przechodząc do karty **Wyjście** i wybierając pozycję **Ustawienia wyjściowe...**.

    Upewnij się, że **miejsce docelowe wyjścia** jest ustawione na **RTMP Server**.
3. Kliknij przycisk **OK**.
4. Na stronie ustawień ustaw pole **Miejsce docelowe** jako **Usługa Azure Media Services**.

    Profil kodowania jest wstępnie wybrany do **usługi Azure H.264 720p 16:9 (1280x720)**. Aby dostosować te ustawienia, wybierz ikonę koła zębatego po prawej stronie listy rozwijanej, a następnie wybierz pozycję **Nowy zestaw predefiniowanych**.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Skonfiguruj ustawienia wstępne kodera.

    Nazwij ustawienia wstępne i sprawdź następujące zalecane ustawienia:

    **Wideo**

   * Emoder: MainConcept H.264
   * Liczba klatek na sekundę: 30
   * Średnia szybkość transmisji bitów: 5000 kbitów/s (można regulować na podstawie ograniczeń sieciowych)
   * Profil: Główny
   * Klatka kluczowa co: 60 klatek

     **Audio**

   * Docelowa szybkość transmisji bitów: 192 kbits/s
   * Częstotliwość próbkowania: 44.100 kHz

     ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Naciśnij pozycję **Zapisz**.

    Pole Kodowanie ma teraz nowo utworzony profil dostępny do wyboru.

    Upewnij się, że nowy profil jest zaznaczony.
7. Pobierz wejściowy adres URL kanału, aby przypisać go do **punktu końcowego**Wirecast RTMP .

    Przejdź z powrotem do narzędzia AMSE i sprawdź stan ukończenia kanału. Po zmianie stanu z **Początkowe** na **Uruchomione**można uzyskać wejściowy adres URL.

    Gdy kanał jest uruchomiony, kliknij prawym przyciskiem myszy nazwę kanału, przejdź w dół, aby najechać kursorem na **adres URL kopiuj wejście do schowka,** a następnie wybierz pozycję **Podstawowy adres URL danych wejściowych**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. W oknie **Ustawienia wyjścia** wirecast wklej te informacje w polu **Adres** sekcji dane wyjściowe i przypisz nazwę strumienia.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Kliknij przycisk **OK**.
2. Na głównym ekranie **Wirecast,** potwierdzić źródła wejściowe dla wideo i audio są gotowe, a następnie naciśnij **Strumień** w lewym górnym rogu.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Przed kliknięciem **przycisku Strumień** **należy upewnić** się, że kanał jest gotowy.
> Upewnij się również, że kanał nie jest gotowy bez źródła danych wejściowych przez okres dłuższy niż > 15 minut.
>
>

## <a name="test-playback"></a>Testowanie odtwarzania

Przejdź do narzędzia AMSE i kliknij prawym przyciskiem myszy testowany kanał. Z menu umieść wskaźnik myszy na **playbacku w wersji zapoznawczej** i wybierz **pozycję za pomocą programu Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Jeśli strumień pojawi się w odtwarzaczu, koder został poprawnie skonfigurowany do łączenia się z programem AMS.

Jeśli zostanie wyświetlony błąd, kanał musi zostać zresetowany i dostosować ustawienia kodera. Zapoznaj się z [artykułem dotyczącym rozwiązywania problemów,](media-services-troubleshooting-live-streaming.md) aby uzyskać wskazówki.  

## <a name="create-a-program"></a>Tworzenie programu
1. Po potwierdzeniu odtwarzania kanału utwórz program. W obszarze karty **Aktywne** w narzędziu AMSE kliknij prawym przyciskiem myszy w obszarze programu i wybierz polecenie **Utwórz nowy program**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Nazwij program i, w razie potrzeby, dostosuj **długość okna archiwum** (domyślnie do czterech godzin). Można również określić lokalizację magazynu lub pozostawić jako domyślną.  
3. Zaznacz pole **Start programu teraz.**
4. Kliknij **pozycję Utwórz program**.  

   >[!NOTE]
   >Tworzenie programu zajmuje mniej czasu niż tworzenie kanału.
       
5. Po uruchomieniu programu potwierdź odtwarzanie, klikając prawym przyciskiem myszy program i przechodząc do **odtwarzania programu,** a następnie wybierając za **pomocą programu Azure Media Player**.  
6. Po potwierdzeniu kliknij prawym przyciskiem myszy program i wybierz pozycję **Skopiuj wyjściowy adres URL do Schowka** (lub pobierz te informacje z opcji **Informacje i ustawienia programu** z menu).

Strumień jest teraz gotowy do umieszczenia w odtwarzaczu lub rozdania go odbiorcom w celu oglądania na żywo.  

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Zapoznaj się z [artykułem dotyczącym rozwiązywania problemów,](media-services-troubleshooting-live-streaming.md) aby uzyskać wskazówki.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
