---
title: Konfigurowanie embarga Haivision KB do wysyłania pojedynczego strumienia na żywo o szybkości transmisji bitów na platformę Azure | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak skonfigurować koder na żywo Haivision KB do wysyłania pojedynczego strumienia szybkości transmisji bitów do kanałów usługi AMS, które są włączone do kodowania na żywo.
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
ms.openlocfilehash: babb5a10818c8108bae34402962fd2503dbecbdc
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641681"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Wysyłanie pojedynczej transmisji bitów strumieniowej na żywo za pomocą embarga na żywo Haivision KB  
> [!div class="op_single_selector"]
> * [Okręg wyborczy Haivision](media-services-configure-kb-live-encoder.md)
> * [Narzędzie Wirecast](media-services-configure-wirecast-live-encoder.md)

W tym temacie pokazano, jak skonfigurować [koder kodera na żywo Havision KB](https://www.haivision.com/products/kb-series/) do wysyłania pojedynczego strumienia szybkości transmisji bitów do kanałów usługi AMS, które są włączone do kodowania na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku pokazano, jak zarządzać usługą Azure Media Services (AMS) za pomocą narzędzia Usługi Azure Media Services Explorer (AMSE). To narzędzie działa tylko na komputerze z systemem Windows. Jeśli korzystasz z komputera Mac lub Linux, użyj witryny Azure portal do tworzenia [kanałów](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programów](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Wymagania wstępne
*   Dostęp do emadera Haivision KB z systemem SW v5.01 lub większym.
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
[Okręg wyborczy Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Określ nazwę kanału, pole opisu jest opcjonalne. W obszarze Ustawienia kanału wybierz opcję **Standardowy** dla opcji Kodowanie na żywo z protokołem wejściowym ustawionym na **RTMP**. Możesz pozostawić wszystkie inne ustawienia, tak jak jest. Upewnij się, że zostanie wybrana **opcja Rozpocznij nowy kanał.**
3. Kliknij **pozycję Utwórz kanał**.
[Okręg wyborczy Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Uruchomienie kanału może potrwać nawet 20 minut.

## <a name="configure-the-haivision-kb-encoder"></a>Konfigurowanie kodera Haivision KB
W tym samouczku używane są następujące ustawienia wyjściowe. W dalszej części tej sekcji opisano kroki konfiguracji bardziej szczegółowo.

Wideo:
-   Koder-dekoder: H.264
-   Profil: wysoki (poziom 4.0)
-   Szybkość transmisji bitów: 5000 kb/s
-   Klatka kluczowa: 2 sekundy (60 klatek)
-   Liczba klatek na sekundę: 30

Audio:
-   Koder-dekoder: AAC (LC)
-   Szybkość transmisji bitów: 192 kb/s
-   Częstotliwość próbkowania: 44,1 kHz

## <a name="configuration-steps"></a>Kroki konfiguracji
1.  Zaloguj się do interfejsu użytkownika Haivision KB.
2.  Kliknij **przycisk Menu** w centrum sterowania kanałem i wybierz pozycję **Dodaj kanał**  
    ![Zrzut ekranu 2017-08-14 o 9.15.09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Wpisz **nazwę kanału** w polu Nazwa i kliknij przycisk Dalej.  
    ![Zrzut ekranu 2017-08-14 o 9.19.07](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Wybierz **źródło wejściowe kanału** z listy rozwijanej **Źródło wejściowe** i kliknij przycisk Dalej.
    ![Zrzut ekranu 2017-08-14 o 9.20.44](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Z listy rozwijanej **Szablon kodera** wybierz **H264-720-AAC-192** i kliknij dalej.
    ![Zrzut ekranu 2017-08-14 o 9.23.15](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Z listy rozwijanej **Wybierz nowe dane wyjściowe** wybierz **RTMP** i kliknij dalej.  
    ![Zrzut ekranu 2017-08-14 o 9.27.51 AM](./media/media-services-configure-kb-live-encoder/step6.png)
7.  W oknie **Dane wyjściowe kanału** wypełnij informacje o strumieniu platformy Azure. Wklej łącze **RTMP** z początkowej konfiguracji kanału w obszarze **Serwer.** W typie obszaru **Nazwa wyjściowa** w nazwie kanału. W obszarze Szablon nazwy strumienia użyj szablonu RTMPStreamName_%video_bitrate%, aby nadać nazwę strumieniu.
    ![Zrzut ekranu 2017-08-14 o 9.33.17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Kliknij przycisk Dalej, a następnie kliknij pozycję Gotowe.
9.  Kliknij **przycisk Odtwórz,** aby uruchomić kanał kodera.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Testowanie odtwarzania
Przejdź do narzędzia AMSE i kliknij prawym przyciskiem myszy testowany kanał. Z menu umieść wskaźnik myszy na playbacku w wersji zapoznawczej i wybierz za pomocą programu Azure Media Player.

Jeśli strumień pojawi się w odtwarzaczu, koder został poprawnie skonfigurowany do łączenia się z programem AMS.

Jeśli zostanie wyświetlony błąd, kanał musi zostać zresetowany i dostosować ustawienia kodera. Zapoznaj się z artykułem dotyczącym rozwiązywania problemów, aby uzyskać wskazówki.

## <a name="create-a-program"></a>Tworzenie programu
1.  Po potwierdzeniu odtwarzania kanału utwórz program. W obszarze karty Aktywne w narzędziu AMSE kliknij prawym przyciskiem myszy w obszarze programu i wybierz polecenie Utwórz nowy program.
[Okręg wyborczy Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Nazwij program i, w razie potrzeby, dostosuj długość okna archiwum (domyślnie do czterech godzin). Można również określić lokalizację magazynu lub pozostawić jako domyślną.
2.  Zaznacz pole Start programu teraz.
3.  Kliknij pozycję Utwórz program.
4.  Po uruchomieniu programu potwierdź odtwarzanie, klikając prawym przyciskiem myszy program i przechodząc do programu Odtwórz programy, a następnie wybierając za pomocą programu Azure Media Player.
5.  Po potwierdzeniu kliknij prawym przyciskiem myszy program i wybierz pozycję Skopiuj wyjściowy adres URL do Schowka (lub pobierz te informacje z opcji Informacje i ustawienia programu z menu).

Strumień jest teraz gotowy do umieszczenia w odtwarzaczu lub rozdania go odbiorcom w celu oglądania na żywo.

> [!NOTE]
> Tworzenie programu zajmuje mniej czasu niż tworzenie kanału.
