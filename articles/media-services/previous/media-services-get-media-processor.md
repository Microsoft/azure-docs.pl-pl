---
title: Jak utworzyć procesor multimediów przy użyciu zestawu SDK Azure Media Services dla platformy .NET | Microsoft Docs
description: Dowiedz się, jak utworzyć składnik procesora multimediów służący do kodowania, konwertowania formatu, szyfrowania lub odszyfrowywania zawartości multimedialnej dla Azure Media Services. Przykłady kodu są zapisywane w języku C# i używają zestawu Media Services SDK dla platformy .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: af6badda426f1bb81d8528cfda9b8c02d55712b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "61463850"
---
# <a name="how-to-get-a-media-processor-instance"></a>Instrukcje: Pobieranie wystąpienia procesora multimediów
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Omówienie
W Media Services procesor multimediów to składnik obsługujący konkretne zadanie przetwarzania, takie jak kodowanie, Konwersja formatu, szyfrowanie lub odszyfrowywanie zawartości multimedialnej. Zazwyczaj tworzysz procesor multimediów podczas tworzenia zadania do kodowania, szyfrowania lub konwertowania formatu zawartości multimedialnej.

## <a name="azure-media-processors"></a>Procesory multimediów platformy Azure 

Poniższy temat zawiera listę procesorów multimediów:

* [Kodowanie procesorów multimediów](scenarios-and-availability.md#encoding-media-processors)
* [Procesory multimediów usługi analizy](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Pobierz procesor multimediów

Poniższa metoda pokazuje, jak uzyskać wystąpienie procesora multimediów. W przykładzie kodu założono użycie zmiennej na poziomie modułu o nazwie **_CONTEXT** , aby odwołać się do kontekstu serwera, zgodnie z opisem w sekcji [How to: Connect to Media Services programowo](media-services-use-aad-auth-to-access-ams-api.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz już, jak uzyskać wystąpienie procesora multimediów, przejdź do tematu [jak kodować element zawartości](media-services-dotnet-encode-with-media-encoder-standard.md) , który pokazuje, jak używać Media Encoder Standard do kodowania elementu zawartości.

