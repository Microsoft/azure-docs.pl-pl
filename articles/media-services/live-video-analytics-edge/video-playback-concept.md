---
title: Odtwarzanie wideo — Azure
description: Symbol zastępczy
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 17b1f93c18dfb013916c4c0d3756fb97a73e2675
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87042973"
---
# <a name="video-playback"></a>Odtwarzanie wideo 

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem 

* [Analiza filmów wideo na żywo na IoT Edge przegląd](overview.md)
* [Analiza filmów wideo na żywo na IoT Edge terminologii](terminology.md)
* [Koncepcje grafu multimediów](media-graph-concept.md)

## <a name="overview"></a>Omówienie  

[Wykresy multimediów](media-graph-concept.md) umożliwiają nagranie wideo w Azure Media Services [elementu zawartości](terminology.md#asset). Ten dokument zawiera informacje o krokach, które należy wykonać w celu odtworzenia zasobów przy użyciu istniejących możliwości przesyłania strumieniowego Azure Media Services.

## <a name="streaming-endpoint"></a>Punkt końcowy przesyłania strumieniowego 

Azure Media Services do [przesyłania strumieniowego](terminology.md#streaming) zasobów do odtwarzaczy wideo przy użyciu standardowych protokołów przesyłania strumieniowego opartych na protokole HTTP, takich jak http Live Streaming (HLS) i MPEG-kreska. Ta konwersja z zarejestrowanej zawartości do formatów przesyłania strumieniowego jest obsługiwana przez [punkt końcowy przesyłania strumieniowego](../latest/streaming-endpoint-concept.md), który jest zasobem, który należy udostępnić na koncie usługi Azure Media.

## <a name="streaming-policy"></a>Zasady przesyłania strumieniowego 

Azure Media Services oferuje różne metody zabezpieczania strumieni wideo, zgodnie z opisem w artykule [Ochrona zawartości za pomocą Media Services dynamicznym szyfrowaniem](../latest/content-protection-overview.md) . Na wysokim poziomie dostępne są opcje ochrony zawartości:

* **W obszarze-Wyczyść przesyłanie strumieniowe — w przypadku,** gdy podczas przesyłania strumieniowego nie jest stosowane szyfrowanie.
* **Użyj Advanced Encryption Standard (AES-128)** — i Zaimplementuj metodę, aby dostarczyć klucze do odszyfrowywania wideo tylko do uwierzytelnionych osób przeglądających.
* **Używaj systemów Digital Rights Management (DRM)** — do kontrolowania użycia, modyfikacji i dostarczania wideo do urządzeń, które wymuszają te zasady.

Aby zapewnić ochronę zawartości, możesz zdefiniować i utworzyć [zasady przesyłania strumieniowego](../latest/streaming-policy-concept.md) na koncie usługi multimediów i użyć ich do przesyłania strumieniowego wszystkich zasobów (przy założeniu, że wszystkie strumienie mają te same wymagania dotyczące zabezpieczeń). Można również użyć dowolnej ze wstępnie zdefiniowanych zasad (na przykład Predefined_ClearStreamingOnly).

## <a name="streaming-locator"></a>Lokalizator przesyłania strumieniowego  

Po uruchomieniu punktu końcowego przesyłania strumieniowego na koncie usługi multimediów i zdefiniowaniu zasad przesyłania strumieniowego można przystępować strumieniowo nagrane multimedia z zasobu za pośrednictwem protokołów HLS lub PAUZ. Odtwarzacze sieci Web i aplikacje mobilne wymagają adresu URL wskazującego strumień HLS lub PAUZy. Ten adres URL można skompilować przy użyciu [lokalizatora przesyłania strumieniowego](../latest/streaming-locators-concept.md). Jak opisano w tym artykule, i przedstawiono w przykładzie [Tworzenie lokalizatora przesyłania strumieniowego i adresów URL kompilacji](../latest/create-streaming-locator-build-url.md) , adres URL przesyłania strumieniowego składa się z punktu końcowego przesyłania strumieniowego, zasad przesyłania strumieniowego i lokalizatora przesyłania strumieniowego.

## <a name="content-recorded-using-file-sink"></a>Zawartość zarejestrowana przy użyciu ujścia plików  

Zgodnie z opisem w [zlewce plików programu Media Graph](media-graph-concept.md#file-sink)można użyć wykresów multimediów do nagrywania filmów wideo do lokalnego systemu plików urządzenia brzegowego przy użyciu ujścia pliku na grafie multimediów. Ujścia pliku generuje pliki [MP4](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4) i można użyć elementu [ &lt; wideo &gt; ](https://developer.mozilla.org/docs/Web/HTML/Element/video) HTML5 do odtwarzania takiej zawartości. 

## <a name="next-steps"></a>Następne kroki

[Azure IoT Edge](../../iot-edge/index.yml)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->
