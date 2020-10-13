---
title: Azure Media Services często zadawane pytania
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ff15206e2373ce6481cb65cbbeeb3c88cd2b7154
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269424"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Media Services V2 — często zadawane pytania

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ten artykuł dotyczy często zadawanych pytań zgłoszonych przez społeczność użytkowników z Azure Media Services (AMS).

## <a name="general-ams-faqs"></a>Ogólne często zadawane pytania

P: jak można przesyłać strumieniowo do urządzeń z systemem Apple iOS?

Odp.: Dodaj ścieżkę "(format = M3U8-AAPL)" do części "/manifest" adresu URL, aby poinformować serwer źródłowy przesyłania strumieniowego, aby zwracał zawartość HLS do użycia na urządzeniach natywnych firmy Apple iOS (Aby uzyskać szczegółowe informacje, zobacz [dostarczanie zawartości](media-services-deliver-content-overview.md)).

P: Jak skalować indeksowanie?

Odp.: zarezerwowane jednostki są takie same na potrzeby kodowania i indeksowania zadań. Postępuj zgodnie z instrukcjami dotyczącymi [skalowania jednostek zarezerwowanych w kodowaniu](media-services-scale-media-processing-overview.md). **Należy zauważyć** , że nie ma to wpływ na wydajność indeksatora.

P: plik wideo został przekazany, zakodowany i opublikowany. Co to jest powód, w którym wideo nie będzie odtwarzane, gdy próbuję przesłać strumieniowo?

Odp.: jedną z najczęstszych przyczyn nie jest punkt końcowy przesyłania strumieniowego, z którego próbujesz odtworzyć stan **uruchomiony** .  

P: Czy można wykonać kompozycję w strumieniu na żywo?

Odp.: złożenie strumieni na żywo nie jest obecnie oferowane w Azure Media Services, więc konieczne jest wstępne utworzenie na komputerze.

P: Czy można użyć Azure CDN z przesyłaniem strumieniowym na żywo?

Odp.: Media Services obsługuje integrację z Azure CDN (Aby uzyskać więcej informacji, zobacz [jak zarządzać punktami końcowymi przesyłania strumieniowego w ramach konta Media Services](media-services-portal-manage-streaming-endpoints.md)).  Możesz użyć przesyłania strumieniowego na żywo w usłudze CDN. Azure Media Services zapewnia Smooth Streaming, HLS i wyjście w formacie MPEG-KRESKowego. Wszystkie te formaty używają protokołu HTTP do przesyłania danych i uzyskiwania korzyści z buforowania HTTP. W przypadku przesyłania strumieniowego na żywo rzeczywiste dane wideo/audio są podzielone na fragmenty, a te pojedyncze fragmenty są przechowywane w pamięci podręcznej w usłudze CDN. Tylko dane muszą być odświeżane. Usługa CDN okresowo odświeża dane manifestu.

P: czy usługa Azure Media Services obsługuje przechowywanie obrazów?

Odp.: Jeśli chcesz przechowywać obrazy JPEG lub PNG, zachowaj je na platformie Azure Blob Storage. Nie ma korzyści, aby umieszczać je na koncie Media Services, chyba że chcesz zachować je skojarzonych z zasobami wideo lub audio. Lub jeśli może być konieczne użycie obrazów jako nakładki w koderze wideo. Media Encoder Standard obsługuje nakładanie się obrazów na wierzchu wideo oraz to, co jest wyświetlane w formacie JPEG i PNG jako obsługiwane formaty danych wejściowych. Aby uzyskać więcej informacji, zobacz [Tworzenie nakładek](media-services-advanced-encoding-with-mes.md#overlay).

P: jak można kopiować zasoby z jednego konta Media Services do innego?

Odp.: Aby skopiować zasoby z jednego konta Media Services do innego przy użyciu platformy .NET, należy użyć metody rozszerzenia [IAsset. Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) dostępnej w repozytorium [rozszerzeń Azure Media Services .NET SDK](https://github.com/Azure/azure-sdk-for-media-services-extensions/) . Aby uzyskać więcej informacji, zobacz [ten](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) wątek forum.

P: Jakie są obsługiwane znaki nazewnictwa plików podczas pracy z usługą AMS?

Odp.: Media Services używa wartości właściwości IAssetFile.Name podczas kompilowania adresów URL dla zawartości przesyłania strumieniowego (na przykład http://{AMSAccount}. Origin. MediaServices. Windows. NET/{GUID}/{IAssetFile. Name}/streamingParameters). Z tego powodu nie jest dozwolone kodowanie procentowo. Wartość właściwości **name** nie może zawierać żadnego z następujących [znaków:%-Encoding](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters), które są zastrzeżone.! * ' ();: @ &= + $,/?% # [] ". Ponadto dla rozszerzenia nazwy pliku może istnieć tylko jeden ".".

P: jak nawiązać połączenie przy użyciu REST?

Odp.: Aby uzyskać informacje na temat nawiązywania połączenia z interfejsem API usługi AMS, zobacz [dostęp do interfejsu api Azure Media Services przy użyciu uwierzytelniania w usłudze Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

P: jak obrócić wideo w trakcie procesu kodowania?

Odp.: [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) obsługuje rotację według kątów 90/180/270. Domyślnym zachowaniem jest "automatycznie", gdzie próbuje wykryć metadane rotacji w pliku MP4/MOV przychodzących i wyrównać je. Dołącz następujący element **Source** do jednego z ustawień predefiniowanych JSON zdefiniowanych w [tym miejscu](media-services-mes-presets-overview.md):

```json
"Version": 1.0,
"Sources": [
{
  "Streams": [],
  "Filters": {
    "Rotation": "90"
  }
}
],
"Codecs": [

...
```


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
