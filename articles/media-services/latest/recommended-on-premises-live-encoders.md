---
title: Kodery przesyłania strumieniowego na żywo zalecane przez Media Services — Azure | Microsoft Docs
description: Dowiedz się więcej o koderach lokalnych dotyczących przesyłania strumieniowego na żywo zalecanych przez Media Services
services: media-services
keywords: kodowanie; kodery; nośnik
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 04/16/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 0676b6b183c64dcd0fb15b87de48a4afed3a0011
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641799"
---
# <a name="tested-on-premises-live-streaming-encoders"></a>Testowanie lokalnych koderów strumieniowych na żywo

W Azure Media Services wydarzenie na [żywo](https://docs.microsoft.com/rest/api/media/liveevents) (kanał) reprezentuje potok do przetwarzania zawartości przesyłania strumieniowego na żywo. Zdarzenie na żywo odbiera strumienie wejściowe na żywo na jeden z dwóch sposobów.

* Lokalny koder na żywo wysyła strumień RTMP o dużej szybkości transmisji bitów lub Smooth Streaming (pofragmentowany plik MP4) do zdarzenia na żywo, które nie ma włączonej obsługi kodowania na żywo z Media Services. Pozyskiwane strumienie przechodzą przez zdarzenia na żywo bez dalszej obróbki. Ta metoda jest nazywana **przekazywaniem**. Zalecamy, aby koder na żywo wysyłał strumienie o szybkości transmisji bitów zamiast strumienia o pojedynczej szybkości transmisji bitów do zdarzenia przekazywania na żywo, aby umożliwić przesyłanie strumieniowe transmisji bitów do klienta. 

    Jeśli używasz strumieni o różnej szybkości transmisji bitów dla zdarzenia przekazywania na żywo, rozmiar grupę GOP wideo i fragmenty wideo na różnych szybkościach transmisji bitów muszą być zsynchronizowane, aby uniknąć nieoczekiwanych zachowań po stronie odtwarzania.

  > [!TIP]
  > Użycie metody przekazującej to najbardziej ekonomiczny sposób na przesyłanie strumieniowe na żywo.
 
* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do zdarzenia na żywo, które umożliwia wykonywanie kodowania na żywo za pomocą Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (fragmentacja MP4). Zdarzenie na żywo następnie wykonuje kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do strumienia wideo o wysokiej szybkości transmisji bitów (adaptacyjnej).

W tym artykule omówiono testowanie lokalnych koderów strumieniowych na żywo. Aby uzyskać instrukcje dotyczące weryfikowania lokalnego kodera na żywo, zobacz [Weryfikowanie lokalnego kodera](become-on-premises-encoder-partner.md)

Aby uzyskać szczegółowe informacje na temat kodowania na żywo z Media Services, zobacz [przesyłanie strumieniowe na żywo z Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Wymagania dotyczące kodera

Kodery muszą obsługiwać protokół TLS 1,2 w przypadku używania protokołów HTTPS lub RTMP.

## <a name="live-encoders-that-output-rtmp"></a>Kodery dynamiczne, które wyprowadzają dane RTMP

Usługa Media Services zaleca używanie jednego z następujących koderów na żywo, które jako dane wyjściowe mają pliki RTMP. Obsługiwane schematy adresów URL to `rtmp://` lub `rtmps://`.

Podczas przesyłania strumieniowego za pośrednictwem protokołu RTMP sprawdź ustawienia zapory i/lub serwera proxy, aby upewnić się, że porty TCP ruchu wychodzącego 1935 i 1936 są otwarte.<br/><br/>
Podczas przesyłania strumieniowego za pośrednictwem protokołu RTMPS sprawdź ustawienia zapory i/lub serwera proxy, aby upewnić się, że porty TCP ruchu wychodzącego 2935 i 2936 są otwarte.

> [!NOTE]
> Kodery muszą obsługiwać protokół TLS 1,2 w przypadku używania protokołów RTMP.

- Adobe Flash Media Live Encoder 3.2
- [Cambria na żywo 4,3](https://www.capellasystems.net/products/cambria-live/)
- Element aktywny (wersja 2.14.15 i nowsze)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Wirecast Telestream (wersja 13.0.2 lub nowsza ze względu na wymaganie TLS 1,2)
- Wirecast telestrumienia S (obsługiwane są tylko RTMP)
- Teradek Slice 756
- VMIX
- xStream
- [Narzędzia FFmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 i Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Kodery dynamiczne, które wyprowadzają pofragmentowany plik MP4

Media Services zaleca użycie jednego z następujących koderów na żywo, które mają Smooth Streaming o wielu szybkościach transmisji bitów (fragmentacja MP4) jako dane wyjściowe. Obsługiwane schematy adresów URL to `http://` lub `https://`.

> [!NOTE]
> Kodery muszą obsługiwać protokół TLS 1,2 w przypadku używania protokołów HTTPS.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Element aktywny (wersja 2.14.15 i nowsza ze względu na wymaganie TLS 1,2)
- Envivio 4Caster C4 Gen III 
- Wyobraź sobie komunikację Selenio MCP3
- Media Excel Hero Live i Hero 4K (UHD/HEVC)
- [Narzędzia FFmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Jeśli przesyłasz strumieniowo wydarzenia na żywo w wielu językach (na przykład jedną angielską ścieżką audio i jedną Hiszpańska ścieżką audio), możesz to zrobić za pomocą kodera programu Excel Live Encoder skonfigurowanego do wysyłania kanału informacyjnego na żywo do zdarzenia przekazywania na żywo.

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurowanie ustawień lokalnego kodera na żywo

Aby uzyskać informacje o ustawieniach, które są prawidłowe dla typu zdarzenia na żywo, zobacz [Porównanie typów zdarzeń na żywo](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Wymagania dotyczące odtwarzania

Aby odtworzyć zawartość, musi być obecny strumień audio i wideo. Odtwarzanie strumienia tylko wideo nie jest obsługiwane.

### <a name="configuration-tips"></a>Porady dotyczące konfiguracji

- Gdy to możliwe, używaj przewodowego połączenia internetowego.
- Podczas określania wymagań dotyczących przepustowości należy zwiększyć szybkość transmisji strumieniowej. Chociaż nie jest to wymagane, prosta reguła pomaga ograniczyć wpływ przeciążenia sieci.
- W przypadku korzystania z koderów opartych na oprogramowaniu Zamknij wszystkie zbędne programy.
- Zmiana konfiguracji kodera po rozpoczęciu wypychania ma negatywny wpływ na zdarzenie. Zmiany konfiguracji mogą spowodować niestabilność zdarzenia. 
- Upewnij się, że podajesz sobie dużo czasu, aby skonfigurować wydarzenie. W przypadku zdarzeń o dużej skali zalecamy uruchomienie Instalatora godzinę przed wydarzeniem.
- Użyj wideo w godzinach H. 264 i danych wyjściowych kodeka audio AAC.
- Upewnij się, że występuje ramka kluczowa lub grupę GOPe czasowe wyrównywanie między jakości wideo.
- Upewnij się, że istnieje unikatowa nazwa strumienia dla każdej jakości wideo.
- Użyj rygorystycznego kodowania CBR zalecanego do optymalnej wydajności z adaptacyjną szybkością transmisji bitów.

> [!IMPORTANT]
> Obejrzyj fizyczny stan maszyny (procesor CPU/pamięć/itp.), ponieważ przekazywanie fragmentów do chmury obejmuje operacje procesora i operacji we/wy. Jeśli zmienisz dowolne ustawienia w koderze, zresetuj zdarzenie/kanał na żywo, aby zmiany zaczęły obowiązywać.

## <a name="see-also"></a>Zobacz także

[Przesyłanie strumieniowe na żywo z Media Services v3](live-streaming-overview.md)

## <a name="next-steps"></a>Następne kroki

[Jak zweryfikować koder](become-on-premises-encoder-partner.md)
