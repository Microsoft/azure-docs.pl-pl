---
title: Dowiedz się więcej o koderach zalecanych przez usługę Azure Media Services | Dokumenty firmy Microsoft
description: W tym artykule wymieniono kodery lokalne zalecane przez usługę Azure Media Services.
services: media-services
keywords: kodowanie;kodery;nośniki
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 3c42070525fc60e45b976620513929c3d5a32341
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535098"
---
# <a name="recommended-on-premises-encoders"></a>Zalecane kodery lokalne

Podczas przesyłania strumieniowego na żywo za pomocą usługi Azure Media Services można określić sposób, w jaki kanał ma odbierać strumień wejściowy. Jeśli zdecydujesz się użyć kodera lokalnego z kanałem kodowania na żywo, koder powinien wypchnąć wysokiej jakości strumień jednoserwowej szybkości transmisji jako dane wyjściowe. Jeśli zdecydujesz się użyć cekodera lokalnego z kanałem przechodzącym, koder powinien wypchnąć strumień o wielu szybkościach transmisji bitów jako dane wyjściowe ze wszystkimi żądanymi jakościami wyjściowymi. Aby uzyskać więcej informacji, zobacz [Przesyłanie strumieniowe na żywo za pomocą koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md).

## <a name="encoder-requirements"></a>Wymagania dotyczące kodera

Kodery muszą obsługiwać protokół TLS 1.2 podczas korzystania z protokołów HTTPS lub RTMPS.

## <a name="live-encoders-that-output-rtmp"></a>Kodery na żywo, które wyprowadzają RTMP 

Usługa Azure Media Services zaleca użycie jednego z następujących koderów na żywo, które mają RTMP jako dane wyjściowe:

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast (wersja 13.0.2 lub nowsza ze względu na wymaganie TLS 1.2)

  Enkodery muszą obsługiwać protokół TLS 1.2 podczas korzystania z protokołów RTMPS.
- Teradek Slice 756
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Kodery na żywo, które wyprowadzają pofragmentowane MP4 

Usługa Azure Media Services zaleca użycie jednego z następujących koderów na żywo, które mają wielosadłową pofragmentowaną mp4 (płynne przesyłanie strumieniowe) jako dane wyjściowe:

- Media Excel Hero Live i Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (wersja 2.14.15 i nowsza ze względu na wymaganie TLS 1.2)

  Kodery muszą obsługiwać protokół TLS 1.2 podczas korzystania z protokołów HTTPS.
- Envivio 4Caster C4 Gen III
- Wyobraź sobie komunikację Selenio MCP3

> [!NOTE]
> Koder na żywo można wysłać strumień o pojedynczej szybkości transmisji bitów do kanału przekazywane, ale ta konfiguracja nie jest zalecane, ponieważ nie zezwala na adaptacyjne przesyłanie strumieniowe szybkości transmisji bitów do klienta.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Jak zostać partnerem emkodera lokalnego

Jako partner usługi Azure Media Services na lokalnym koderze usługa Media Services promuje produkt, polecając koder klientom korporacyjnym. Aby stać się partnerem kodera lokalnego, należy sprawdzić zgodność kodera lokalnego z usługą Media Services. Aby to zrobić, wykonaj następujące weryfikacje:

Przechodzenie przez weryfikację kanału
1. Tworzenie lub odwiedzanie konta usługi Azure Media Services
2. Tworzenie i uruchamianie kanału **przekazu**
3. Skonfiguruj koder do wypychania strumienia na żywo o wielu szybkościach transmisji bitów.
4. Tworzenie opublikowanego wydarzenia na żywo
5. Uruchom koder na żywo przez około 10 minut
6. Zatrzymywać wydarzenie na żywo
7. Tworzenie, uruchamianie punktu końcowego przesyłania strumieniowego, używanie odtwarzacza, takiego jak [Azure Media Player,](https://aka.ms/azuremediaplayer) aby obejrzeć zarchiwizowany zasób, aby upewnić się, że odtwarzanie nie ma widocznych usterek dla wszystkich poziomów jakości (lub możesz też oglądać i sprawdzać poprawność za pośrednictwem adresu URL wersji zapoznawczej podczas sesji na żywo przed krokiem 6)
8. Zarejestruj identyfikator zasobu, opublikowany adres URL przesyłania strumieniowego dla archiwum na żywo oraz ustawienia i wersję używaną z kodera na żywo
9. Resetowanie stanu kanału po utworzeniu każdej próbki
10. Powtórz kroki od 3 do 9 dla wszystkich konfiguracji obsługiwanych przez koder (z sygnalizacją/podpisami/podpisami/różnymi szybkościami kodowania i bez niej)

Weryfikacja kanału kodowania na żywo
1. Tworzenie lub odwiedzanie konta usługi Azure Media Services
2. Tworzenie i uruchamianie kanału **kodowania na żywo**
3. Skonfiguruj koder do wypychania transmisji na żywo o pojedynczej szybkości transmisji bitów.
4. Tworzenie opublikowanego wydarzenia na żywo
5. Uruchom koder na żywo przez około 10 minut
6. Zatrzymywać wydarzenie na żywo
7. Tworzenie, uruchamianie punktu końcowego przesyłania strumieniowego, używanie odtwarzacza, takiego jak [Azure Media Player,](https://aka.ms/azuremediaplayer) aby obejrzeć zarchiwizowany zasób, aby upewnić się, że odtwarzanie nie ma widocznych usterek dla wszystkich poziomów jakości (lub możesz też oglądać i sprawdzać poprawność za pośrednictwem adresu URL wersji zapoznawczej podczas sesji na żywo przed krokiem 6)
8. Zarejestruj identyfikator zasobu, opublikowany adres URL przesyłania strumieniowego dla archiwum na żywo oraz ustawienia i wersję używaną z kodera na żywo
9. Resetowanie stanu kanału po utworzeniu każdej próbki
10. Powtórz kroki od 3 do 9 dla wszystkich konfiguracji obsługiwanych przez koder (z i bez sygnalizacji reklamowej / podpisów / różnych prędkości kodowania)

Weryfikacja długowieczności
1. Tworzenie lub odwiedzanie konta usługi Azure Media Services
2. Tworzenie i uruchamianie kanału **przekazu**
3. Skonfiguruj koder do wypychania strumienia na żywo o wielu szybkościach transmisji bitów.
4. Tworzenie opublikowanego wydarzenia na żywo
5. Uruchom koder na żywo przez tydzień lub dłużej
6. Użyj odtwarzacza, takiego jak [Azure Media Player,](https://aka.ms/azuremediaplayer) aby od czasu do czasu oglądać transmisje na żywo (lub zarchiwizowane zasoby), aby upewnić się, że odtwarzanie nie ma widocznych usterek
7. Zatrzymywać wydarzenie na żywo
8. Zarejestruj identyfikator zasobu, opublikowany adres URL przesyłania strumieniowego dla archiwum na żywo oraz ustawienia i wersję używaną z kodera na żywo

Nareszcie, wyślij zarejestrowane ustawienia i parametry archiwum amsstreaming@microsoft.comna żywo do usługi Media Services, wysyłając wiadomość e-mail. Po otrzymaniu usługi Media Services przeprowadza testy weryfikacyjne na przykładach z kodera na żywo. Możesz skontaktować się z usługą Media Services w przypadku jakichkolwiek pytań dotyczących tego procesu.
