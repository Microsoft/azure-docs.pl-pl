---
title: Kodery do przesyłania strumieniowego na żywo zalecane przez usługę Media Services — Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o lokalnych koderach strumieniowych na żywo zalecanych przez usługę Media Services
services: media-services
keywords: kodowanie;kodery;nośniki
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 04/16/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: bbd2929e245fa5fc01245e7bd02e537db8d5ef36
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536339"
---
# <a name="tested-on-premises-live-streaming-encoders"></a>Przetestowane lokalne kodery strumieniowe na żywo

W usłudze Azure Media Services [zdarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents) (kanał) reprezentuje potok przetwarzania zawartości przesyłania strumieniowego na żywo. Wydarzenie na żywo odbiera transmisje wejściowe na żywo na jeden z dwóch sposobów.

* Lokalny koder na żywo wysyła strumień RTMP o wielu szybkościach transmisji bitów lub płynnego przesyłania strumieniowego (pofragmentowany plik MP4) do zdarzenia na żywo, które nie jest włączone do wykonywania kodowania na żywo za pomocą usługi Media Services. Pozyskane strumienie przechodzą przez wydarzenia na żywo bez dalszego przetwarzania. Ta metoda jest nazywana **pass-through**. Zaleca się, aby koder na żywo wysyłał strumienie o wielu szybkościach transmisji bitów zamiast strumienia o pojedynczej szybkości transmisji bitów do zdarzenia na żywo przekazywanego, aby umożliwić adaptacyjne przesyłanie strumieniowe szybkości transmisji bitów do klienta. 

    Jeśli używasz strumieni o wielu szybkościach transmisji bitów dla zdarzenia na żywo przekazywanego, rozmiar interfejsu GOP wideo i fragmenty wideo na różnych szybkościach transmisji bitów muszą zostać zsynchronizowane, aby uniknąć nieoczekiwanego zachowania po stronie odtwarzania.

  > [!TIP]
  > Korzystanie z metody pass-through jest najbardziej ekonomicznym sposobem przesyłania strumieniowego na żywo.
 
* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do zdarzenia na żywo, który jest włączony do wykonywania kodowania na żywo za pomocą usługi Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (pofragmentowany format MP4). Wydarzenie na żywo następnie wykonuje kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do strumienia wideo o wielu szybkościach transmisji bitów (adaptacyjnej).

W tym artykule omówiono przetestowane lokalne kodery strumieniowe na żywo. Aby uzyskać instrukcje dotyczące weryfikacji lokalnego kodera na żywo, zobacz [sprawdzanie kodera lokalnego](become-on-premises-encoder-partner.md)

Aby uzyskać szczegółowe informacje na temat kodowania na żywo za pomocą usługi Media Services, zobacz [Przesyłanie strumieniowe na żywo z usługą Media Services w wersji 3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Wymagania dotyczące kodera

Kodery muszą obsługiwać protokół TLS 1.2 podczas korzystania z protokołów HTTPS lub RTMPS.

## <a name="live-encoders-that-output-rtmp"></a>Kodery na żywo, które wyprowadzają RTMP

Usługa Media Services zaleca używanie jednego z następujących koderów na żywo, które jako dane wyjściowe mają pliki RTMP. Obsługiwane schematy adresów `rtmp://` `rtmps://`URL są lub .

Podczas przesyłania strumieniowego za pośrednictwem protokołu RTMP sprawdź ustawienia zapory i/lub serwera proxy, aby upewnić się, że porty TCP ruchu wychodzącego 1935 i 1936 są otwarte.<br/><br/>
Podczas przesyłania strumieniowego za pośrednictwem protokołu RTMPS sprawdź ustawienia zapory i/lub serwera proxy, aby upewnić się, że porty TCP ruchu wychodzącego 2935 i 2936 są otwarte.

> [!NOTE]
> Enkodery muszą obsługiwać protokół TLS 1.2 podczas korzystania z protokołów RTMPS.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Na żywo 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (wersja 2.14.15 i nowsza)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast (wersja 13.0.2 lub nowsza ze względu na wymaganie TLS 1.2)
- Telestream Wirecast S (obsługiwany jest tylko RTMP)
- Teradek Slice 756
- TriCaster 8000
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro (właśc.](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Bohater 7 i Bohater 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Kodery na żywo, które wyprowadzają pofragmentowane MP4

Usługa Media Services zaleca użycie jednego z następujących koderów na żywo, które mają wielosytowe płynne przesyłanie strumieniowe (pofragmentowany plik MP4) jako dane wyjściowe. Obsługiwane schematy adresów `http://` `https://`URL są lub .

> [!NOTE]
> Kodery muszą obsługiwać protokół TLS 1.2 podczas korzystania z protokołów HTTPS.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (wersja 2.14.15 i nowsza ze względu na wymaganie TLS 1.2)
- Envivio 4Caster C4 Gen III 
- Wyobraź sobie komunikację Selenio MCP3
- Media Excel Hero Live i Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Jeśli przesyłasz strumieniowo wydarzenia na żywo w wielu językach (na przykład jedną angielską ścieżkę audio i jedną hiszpańską ścieżkę audio), możesz to osiągnąć za pomocą kodera na żywo programu Media Excel skonfigurowanego do wysyłania transmisji na żywo do zdarzenia na żywo.

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurowanie lokalnych ustawień kodera na żywo

Aby uzyskać informacje o tym, jakie ustawienia są prawidłowe dla typu wydarzenia na żywo, zobacz [porównanie typów zdarzeń na żywo](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Wymagania dotyczące odtwarzania

Aby odtwarzać zawartość, musi być obecny strumień audio i wideo. Odtwarzanie strumienia tylko wideo nie jest obsługiwane.

### <a name="configuration-tips"></a>Porady dotyczące konfiguracji

- Gdy to możliwe, używaj przewodowego połączenia internetowego.
- Podczas określania wymagań dotyczących przepustowości, podwoić szybkość transmisji strumieniowej. Chociaż nie jest to obowiązkowe, ta prosta zasada pomaga złagodzić wpływ przeciążenia sieci.
- W przypadku korzystania z koderów programowych zamknij wszelkie niepotrzebne programy.
- Zmiana konfiguracji kodera po rozpoczęciu pchania ma negatywny wpływ na zdarzenie. Zmiany konfiguracji mogą spowodować, że zdarzenie stanie się niestabilne. 
- Upewnij się, że masz wystarczająco dużo czasu na skonfigurowanie wydarzenia. W przypadku zdarzeń na dużą skalę zalecamy rozpoczęcie konfiguracji na godzinę przed wydarzeniem.
- Użyj wyjścia wideo H.264 i kodeka audio AAC.
- Upewnij się, że istnieje klatka kluczowa lub ustawienie czasowe GOP w różnych jakościach wideo.
- Upewnij się, że dla każdej jakości wideo jest unikatowa nazwa strumienia.
- Użyj ścisłego kodowania CBR zalecanego dla optymalnej adaptacyjnej wydajności szybkości transmisji bitów.

> [!IMPORTANT]
> Obserwuj kondycję fizyczną maszyny (CPU / Memory / etc), ponieważ przesyłanie fragmentów do chmury obejmuje operacje procesora i we/wy. Jeśli zmienisz ustawienia w koderze, należy zresetować kanały / wydarzenie na żywo, aby zmiana została uwzględniona.

## <a name="see-also"></a>Zobacz też

[Przesyłanie strumieniowe na żywo za pomocą usługi Media Services w wersji 3](live-streaming-overview.md)

## <a name="next-steps"></a>Następne kroki

[Jak zweryfikować koder](become-on-premises-encoder-partner.md)
