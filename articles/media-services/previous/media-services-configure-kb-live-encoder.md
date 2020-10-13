---
title: Skonfiguruj koder Haivision KB do wysyłania strumienia na żywo o pojedynczej szybkości transmisji bitów do platformy Azure | Microsoft Docs
description: W tym temacie opisano sposób konfigurowania kodera Haivision KB na żywo w celu wysłania strumienia pojedynczego szybkości transmisji bitów do kanałów usługi AMS obsługujących kodowanie na żywo.
services: media-services
documentationcenter: ''
author: dbgeorge
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: ab73b22e927ad1bbcc8a07173e1548157de03cdd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89258099"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Użyj kodera Haivision KB Live, aby wysłać strumień na żywo o pojedynczej szybkości transmisji bitów

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Narzędzie Wirecast](media-services-configure-wirecast-live-encoder.md)

W tym temacie pokazano, jak skonfigurować koder [kodera HAVISION KB na żywo](https://www.haivision.com/products/kb-series/) w celu wysłania strumienia o pojedynczej szybkości transmisji bitów do kanałów usługi AMS obsługujących kodowanie na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku pokazano, jak zarządzać usługą Azure Media Services (AMS) za pomocą narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze z systemem Windows. Jeśli jesteś w systemie Mac lub Linux, użyj Azure Portal, aby utworzyć [kanały](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Wymagania wstępne
*   Dostęp do kodera Haivision KB z uruchomionym programem SW v 5.01 lub nowszym.
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
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Określ nazwę kanału pole Opis jest opcjonalne. W obszarze Ustawienia kanału wybierz pozycję **standardowa** dla opcji Live Encoding, z protokołem wejściowym ustawionym na wartość **RTMP**. Wszystkie inne ustawienia można pozostawić zgodnie z oczekiwaniami. Upewnij się, że wybrano wartość **Uruchom nowy kanał teraz** .
3. Kliknij pozycję **Utwórz kanał**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Kanał może trwać przez 20 minut.

## <a name="configure-the-haivision-kb-encoder"></a>Konfigurowanie kodera Haivision KB
W tym samouczku używane są następujące ustawienia wyjściowe. W dalszej części tej sekcji opisano kroki konfiguracji bardziej szczegółowo.

Wideo:
-   Koder-dekoder: H.264
-   Profil: wysoki (poziom 4.0)
-   Szybkość transmisji bitów: 5000 KB/s
-   Klatka kluczowa: 2 sekundy (60 klatek)
-   Szybkość klatek: 30

Dźwięku
-   Koder-dekoder: AAC (LC)
-   Szybkość transmisji bitów: 192 kb/s
-   Częstotliwość próbkowania: 44,1 kHz

## <a name="configuration-steps"></a>Kroki konfiguracji
1.  Zaloguj się do interfejsu użytkownika Haivision KB.
2.  Kliknij **przycisk menu** w centrum sterowania kanału i wybierz polecenie **Dodaj kanał**  
    ![Zrzut ekranu 2017-08-14 o godzinie 9.15.09 AM](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Wpisz **nazwę kanału** w polu Nazwa, a następnie kliknij przycisk Dalej.  
    ![Zrzut ekranu 2017-08-14 o godzinie 9.19.07 AM](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Wybierz **Źródło danych wejściowych kanału** z listy rozwijanej **Źródło danych wejściowych** i kliknij przycisk Dalej.
    ![Zrzut ekranu 2017-08-14 o godzinie 9.20.44 AM](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Z listy rozwijanej **szablon kodera** wybierz pozycję **wielokrotna H264-720-AAC-192** , a następnie kliknij przycisk Dalej.
    ![Zrzut ekranu 2017-08-14 o godzinie 9.23.15 AM](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Z listy rozwijanej **Wybierz nowe dane wyjściowe** wybierz opcję **RTMP** i kliknij przycisk Dalej.  
    ![Zrzut ekranu 2017-08-14 o godzinie 9.27.51 AM](./media/media-services-configure-kb-live-encoder/step6.png)
7.  W oknie **dane wyjściowe kanału** Wypełnij informacje o usłudze Azure Stream. Wklej link **RTMP** z konfiguracji wstępnego kanału w obszarze **serwer** . W obszarze **Nazwa wyjściowa** wpisz nazwę kanału. W obszarze szablon nazwy strumienia Użyj szablonu RTMPStreamName_% video_bitrate%, aby nawiązać nazwę strumienia.
    ![Zrzut ekranu 2017-08-14 o godzinie 9.33.17 AM](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Kliknij przycisk Dalej, a następnie kliknij przycisk Gotowe.
9.  Kliknij **przycisk Odtwórz** , aby uruchomić kanał kodera.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Odtwarzanie testów
Przejdź do narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanał, który ma zostać przetestowany. W menu Umieść wskaźnik myszy nad odtwarzaniem wersji zapoznawczej i wybierz pozycję z Azure Media Player.

Jeśli w odtwarzaczu pojawi się strumień, koder został prawidłowo skonfigurowany do łączenia z usługą AMS.

Jeśli wystąpi błąd, należy zresetować kanał i dopasować ustawienia kodera. Aby uzyskać wskazówki, zobacz artykuł dotyczący rozwiązywania problemów.

## <a name="create-a-program"></a>Tworzenie programu
1.  Po potwierdzeniu odtwarzania kanału Utwórz program. Na karcie na żywo w narzędziu AMSE kliknij prawym przyciskiem myszy w obszarze programu i wybierz pozycję Utwórz nowy program.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Nazwij program i, w razie potrzeby, Dostosuj długość okna archiwum (domyślnie cztery godziny). Możesz również określić lokalizację magazynu lub pozostawić jako domyślną.
2.  Zaznacz pole Uruchom program teraz.
3.  Kliknij pozycję Utwórz program.
4.  Po uruchomieniu programu Potwierdź odtwarzanie, klikając prawym przyciskiem myszy program i przechodząc do odtworzenia programów, a następnie wybierając pozycję z Azure Media Player.
5.  Po potwierdzeniu ponownie kliknij prawym przyciskiem myszy program i wybierz polecenie Kopiuj wyjściowy adres URL do schowka (lub Pobierz te informacje z opcji Informacje o programie i ustawienia z menu).

Strumień jest teraz gotowy do osadzenia w odtwarzaczu lub jest dystrybuowany do odbiorców na potrzeby wyświetlania na żywo.

> [!NOTE]
> Tworzenie programu trwa krócej niż tworzenie kanału.
