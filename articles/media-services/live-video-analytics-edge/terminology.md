---
title: Analiza filmów wideo na żywo na temat terminologii usługi IoT EDG — Azure
description: Ten artykuł zawiera omówienie analizy filmów wideo na żywo na IoT Edge terminologii.
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: e3a77b69adf2241a4af2652db4edb6673a63b4f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88690616"
---
# <a name="terminology"></a>Terminologia

Ten artykuł zawiera omówienie terminologii związanej z [analizą filmów wideo na żywo na IoT Edge](overview.md).

## <a name="azure-media-services"></a>Azure Media Services

Azure Media Services to platforma multimediów w chmurze, która umożliwia tworzenie rozwiązań multimedialnych. Więcej informacji na ten temat można znaleźć w dokumentacji [Azure Media Services](../latest/media-services-overview.md) .

## <a name="asset"></a>Zasób

Element [zawartości](../latest/assets-concept.md) to jednostka w Azure Media Services, która mapuje do kontenera obiektów BLOB na koncie usługi Azure Storage, które jest dołączone do konta Media Services. Wszystkie pliki skojarzone z elementem zawartości są przechowywane jako obiekty blob w tym kontenerze, podczas gdy Media Services przechowuje metadane (na przykład nazwę, opis, czas utworzenia) skojarzoną z elementem zawartości.

Analiza filmów wideo na żywo na IoT Edge może tworzyć zasoby i/lub dodawać dane do istniejących zasobów. Dzięki temu scenariusze ciągłego i odtwarzania wideo opartego na zdarzeniach (z funkcją przechwytywania wideo na urządzeniu brzegowym, nagrywania do Azure Media Services i odtwarzania za pośrednictwem istniejących możliwości przesyłania strumieniowego Azure Media Services).

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) to język niezależny od i środowisko zdalnego wywołania procedury (RPC) o wysokiej wydajności. Używa schematów strukturalnych opartych na sesji za pośrednictwem [buforów protokołu 3](https://developers.google.com/protocol-buffers/docs/proto3) jako podstawowego formatu wymiany komunikatów na potrzeby komunikacji.

## <a name="media-graph"></a>Graf multimedialny

Program [Media Graph](media-graph-concept.md) pozwala określić, gdzie mają być przechwytywane nośniki, jak należy je przetworzyć i gdzie powinny zostać dostarczone wyniki. Umożliwia zdefiniowanie grafu składającego się ze źródeł, procesorów i węzłów ujścia, a tym samym zapewnia możliwość tworzenia aplikacji do analizy wideo na żywo. Wykres multimedialny został szczegółowo omówiony na stronie koncepcji grafu multimediów.

## <a name="recording"></a>Nagrywanie

W kontekście systemu zarządzania wideo dla kamer z zabezpieczeniami nagrywanie wideo odnosi się do procesu przechwytywania wideo z kamer i zapisywania go w pliku (lub plików) do późniejszego przeglądania za pośrednictwem aplikacji mobilnych i przeglądarek. Nagrywanie wideo można podzielić na stałe nagrania [wideo](continuous-video-recording-concept.md) i [nagrania wideo oparte na zdarzeniach](event-based-video-recording-concept.md). Wyjaśniono je bardziej szczegółowo na stronie koncepcji [nagrywania wideo](video-recording-concept.md) .

## <a name="rtsp"></a>Protokół

[RTSP](https://tools.ietf.org/html/rfc2326) odnosi się do Real-Time protokołu przesyłania strumieniowego. Jest to protokół poziomu aplikacji służący do kontroli nad dostarczaniem danych z właściwościami w czasie rzeczywistym. Protokół RTSP zapewnia rozszerzalną platformę umożliwiającą kontrolowane dostarczanie na żądanie danych w czasie rzeczywistym, takich jak audio i wideo. 

## <a name="streaming"></a>Przesyłanie strumieniowe

Jeśli na urządzeniu przenośnym znajduje się Obejrzyj film wideo z usług takich jak Netflix, YouTube i inne, masz doświadczenie wideo dotyczące przesyłania strumieniowego. Odtwarzanie rozpoczyna się wkrótce po trafieniu "Play" (Jeśli masz wystarczającą przepustowość) i możesz poszukiwać z powrotem i z tyłu na osi czasu wideo. W przypadku przesyłania strumieniowego pomysłem jest dostarczenie tylko części wideo, która jest oglądana, i umożliwienie przeglądarce rozpoczęcia odtwarzania wideo, gdy dane są nadal przesyłane z serwera do klienta odtwarzania. W kontekście Azure Media Services [przesyłania strumieniowego](https://en.wikipedia.org/wiki/Streaming_media) odnosi się do procesu dostarczania nośnika z [Azure Media Services](../azure-media-player/azure-media-player-overview.md) do klienta przesyłania strumieniowego (na przykład Azure Media Player). Azure Media Services do przesyłania strumieniowego wideo do klientów za pomocą standardowych protokołów przesyłania strumieniowego opartych na protokole HTTP, takich jak [http Live Streaming (HLS)](https://developer.apple.com/streaming/) i [MPEG-kreska](https://dashif.org/about/). HLS jest obsługiwany przez Azure Media Player i odtwarzacze sieci Web, takie jak [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/), [Google Shaka Player](https://github.com/google/shaka-player)lub można je renderować natywnie w aplikacjach mobilnych za pomocą [Exoplayer](https://github.com/google/ExoPlayer) systemu Android i systemu [iOS.](https://developer.apple.com/av-foundation/) Polecenie MPEG-KRESKa jest podobnie obsługiwane przez Azure Media Player, [Znajdź listę klientów na tej stronie](https://dashif.org/clients/). 

Za pomocą [grafu multimediów](#media-graph)s do nagrywania wideo do zasobu w Azure Media Services można korzystać z funkcji przesyłania strumieniowego Media Services w celu dostarczania strumieni wideo w HLS i kreskowaniu. Więcej informacji na ten temat można znaleźć w artykule dotyczącym [odtwarzania wideo](video-playback-concept.md) .

## <a name="vms"></a>MASZYN

[Maszyny wirtualne](https://en.wikipedia.org/wiki/Video_management_system) odwołują się do systemu zarządzania wideo. Systemy te są używane do konfigurowania i kontrolowania kamer CCTV, przechwytywania i rejestrowania filmów wideo. Te systemy zapewniają również aplikacjom klienckim Odtwarzanie zapisanego wideo

## <a name="next-steps"></a>Następne kroki

[Wykresy multimedialne](media-graph-concept.md)
