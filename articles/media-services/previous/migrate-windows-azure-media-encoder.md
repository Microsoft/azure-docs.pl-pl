---
title: Migrowanie z systemu Windows Azure Media Encoder do Media Encoder Standard | Microsoft Docs
description: W tym temacie omówiono sposób migrowania z programu Windows Azure Media Encoder do Media Encoder Standard procesora multimediów.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 0a80d25145162c81ef999f8af1015cd590d5a090
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011123"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migrowanie z systemu Windows Azure Media Encoder do Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

W tym artykule omówiono procedurę migrowania ze starszego procesora nośnika systemu Windows Azure Media Encoder (WAME) (który jest wycofywany) do Media Encoder Standard procesora multimediów. Aby uzyskać daty wycofania, zobacz temat ten [starszy składnik](legacy-components.md) .

Podczas kodowania plików przy użyciu WAME klienci zazwyczaj używają nazwanego ciągu wstępnie zdefiniowanego, takiego jak `H264 Adaptive Bitrate MP4 Set 1080p` . Aby przeprowadzić migrację, należy zaktualizować kod, aby używał **Media Encoder Standard** procesora multimediów zamiast WAME oraz jednego z odpowiedników [ustawień systemowych](media-services-mes-presets-overview.md) , takich jak `H264 Multiple Bitrate 1080p` . 

## <a name="migrating-to-media-encoder-standard"></a>Migrowanie do Media Encoder Standard

Oto typowy przykład kodu w języku C#, który używa starszego składnika. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
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

Jeśli utworzono własne ustawienie wstępne kodowania dla WAME przy użyciu jego schematu, istnieje [odpowiedni schemat dla Media Encoder Standard](media-services-mes-schema.md).

## <a name="known-differences"></a>Znane różnice 

Media Encoder Standard jest bardziej niezawodna, niezawodna, ma lepszą wydajność i produkuje lepsze dane wyjściowe niż starszy koder WAME. W dodatku: 

* Media Encoder Standard tworzy pliki wyjściowe z inną konwencją nazewnictwa niż WAME.
* Media Encoder Standard tworzy artefakty, takie jak pliki zawierające [metadane plików wejściowych](media-services-input-metadata-schema.md) i [metadane plików wyjściowych](media-services-output-metadata-schema.md).
* Zgodnie z opisem na [stronie cennika](https://azure.microsoft.com/pricing/details/media-services/#encoding) (szczególnie w sekcji często zadawane pytania) podczas kodowania filmów wideo przy użyciu Media Encoder Standard, opłaty są naliczane na podstawie czasu trwania plików utworzonych jako dane wyjściowe. W przypadku WAME opłaty są naliczane na podstawie rozmiarów wejściowych plików wideo i wyjściowych plików wideo.

## <a name="need-help"></a>Potrzebujesz pomocy?

Możesz otworzyć bilet pomocy technicznej, przechodząc do [nowego żądania obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Następne kroki

* [Starsze składniki](legacy-components.md)
* [Strona cennika](https://azure.microsoft.com/pricing/details/media-services/#encoding)
