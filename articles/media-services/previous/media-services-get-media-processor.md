---
title: Jak utworzyć procesor multimediów przy użyciu zestawu SDK Azure Media Services dla platformy .NET | Microsoft Docs
description: Dowiedz się, jak utworzyć składnik procesora multimediów służący do kodowania, konwertowania formatu, szyfrowania lub odszyfrowywania zawartości multimedialnej dla Azure Media Services. Przykłady kodu są zapisywane w języku C# i używają zestawu Media Services SDK dla platformy .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 6ce2a28ff9b34373cc716ea397927ef160bd1097
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013350"
---
# <a name="how-to-get-a-media-processor-instance"></a>Instrukcje: Pobieranie wystąpienia procesora multimediów

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)

## <a name="overview"></a>Omówienie

W Media Services procesor multimediów to składnik obsługujący konkretne zadanie przetwarzania, takie jak kodowanie, Konwersja formatu, szyfrowanie lub odszyfrowywanie zawartości multimedialnej. Zazwyczaj tworzysz procesor multimediów podczas tworzenia zadania do kodowania, szyfrowania lub konwertowania formatu zawartości multimedialnej.

## <a name="azure-media-processors"></a>Procesory multimediów platformy Azure

Poniższy temat zawiera listę procesorów multimediów:

* [Kodowanie procesorów multimediów](scenarios-and-availability.md)
* [Procesory multimediów usługi analizy](scenarios-and-availability.md)

## <a name="get-media-processor"></a>Pobierz procesor multimediów

Poniższa metoda pokazuje, jak uzyskać wystąpienie procesora multimediów. W przykładzie kodu założono użycie zmiennej na poziomie modułu o nazwie **_CONTEXT** , aby odwołać się do kontekstu serwera, zgodnie z opisem w sekcji [How to: Connect to Media Services programowo](media-services-use-aad-auth-to-access-ams-api.md).

```csharp
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak uzyskać wystąpienie procesora multimediów, przejdź do tematu [jak kodować element zawartości](media-services-dotnet-encode-with-media-encoder-standard.md) , który pokazuje, jak używać Media Encoder Standard do kodowania elementu zawartości.