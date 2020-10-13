---
title: Migrowanie z Azure Media Encoder do Media Encoder Standard | Microsoft Docs
description: W tym temacie omówiono sposób migrowania z Azure Media Encoder do Media Encoder Standard procesora multimediów.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 32eab0f729280ad25f45853e05ea982b60a6d9f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89259340"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Migrowanie z Azure Media Encoder do Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

W tym artykule omówiono procedurę migrowania ze starszej wersji procesora nośnika z Azure Media Encoder (AZWA) (która jest wycofywana) do Media Encoder Standard procesora multimediów. Aby uzyskać daty wycofania, zobacz temat ten [starszy składnik](legacy-components.md) .

Podczas kodowania plików o nazwie AZWA klienci zazwyczaj używają nazwanego ciągu wstępnie zdefiniowanego, takiego jak `H264 Adaptive Bitrate MP4 Set 1080p` . Aby przeprowadzić migrację, należy zaktualizować kod, aby używał **Media Encoder Standard** procesora multimediów zamiast azwa i jednego z odpowiedników [ustawień systemowych](media-services-mes-presets-overview.md) `H264 Multiple Bitrate 1080p` . 

## <a name="migrating-to-media-encoder-standard"></a>Migrowanie do Media Encoder Standard

Oto typowy przykład kodu w języku C#, który używa starszego procesora nośnika. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Poniżej znajduje się zaktualizowana wersja, która używa Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Scenariusze zaawansowane 

Jeśli utworzono własne ustawienie wstępne kodowania dla opcji AZWA przy użyciu schematu, istnieje [odpowiedni schemat dla Media Encoder Standard](media-services-mes-schema.md). Jeśli masz pytania dotyczące sposobu mapowania starszych ustawień do nowego kodera, skontaktuj się z nami za pośrednictwem mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Znane różnice 

Media Encoder Standard jest bardziej niezawodna, niezawodna, ma lepszą wydajność i produkuje lepsze dane wyjściowe niż w przypadku starszego kodera AZWA. Ponadto: 

* Media Encoder Standard tworzy pliki wyjściowe z inną konwencją nazewnictwa niż AZWA.
* Media Encoder Standard tworzy artefakty, takie jak pliki zawierające [metadane plików wejściowych](media-services-input-metadata-schema.md) i [metadane plików wyjściowych](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Następne kroki

* [Starsze składniki](legacy-components.md)
* [Strona cennika](https://azure.microsoft.com/pricing/details/media-services/#encoding)
