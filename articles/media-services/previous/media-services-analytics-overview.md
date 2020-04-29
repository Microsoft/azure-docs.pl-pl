---
title: Media Analytics na platformie Media Services | Microsoft Docs
description: Omówienie publicznej wersji zapoznawczej Media Analytics, kolekcji usług przetwarzania mowy i komputerowych na skalę przedsiębiorstwa, zgodności, zabezpieczeń i globalnego zasięgu
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.reviewer: milanga; johndeu
ms.openlocfilehash: ab1eba3de474d9ff985e62f491c24fa63be0fa63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77069629"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics na platformie Media Services 

## <a name="retirement-plans"></a>Plany wycofania

> [!IMPORTANT]
> Niektóre procesory multimediów są wycofywane. Aby uzyskać daty wycofania i więcej informacji, zobacz temat [składniki starszej wersji](legacy-components.md) . 

## <a name="overview"></a>Omówienie

Więcej organizacji używa wideo jako preferowanego nośnika do uczenia pracowników, zaangażowania ich klientów i dokumentowania funkcji firmy. Chmura obliczeniowa zapewnia sposób przechowywania, przesyłania strumieniowego i uzyskiwania dostępu do tych dużych plików multimedialnych. Jednak w miarę wzrostu rozmiaru zawartości wideo firma potrzebuje równie efektywnego sposobu wyodrębniania szczegółowych informacji z zawartości. 

Aby rozwiązać ten wzrost zapotrzebowania, Azure Media Services oferuje Azure Media Analytics. Analiza multimediów to kolekcja składników mowy i obrazu, które ułatwiają organizacjom i przedsiębiorstwom pozyskiwanie przydatnych informacji z posiadanych plików wideo. Utworzone przy użyciu podstawowych składników platformy Media Services, Media Analytics może obsłużyć przetwarzanie multimediów w dowolnej skali.

Dzięki Media Analytics deweloperzy mogą szybko dogrzać zaawansowane funkcje wideo do aplikacji. Zapewnia to środowiska korporacyjne z pełną skalowalnością, zgodnością, zabezpieczeniami i globalnym zasięgiem wymaganym przez duże organizacje.

Na poniższym diagramie przedstawiono Media Analytics i inne główne części platformy Media Services. 

![Wideo na żądanie — przepływ pracy](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Procesory multimediów usługi Analiza multimediów tworzą pliki MP4 lub JSON. Jeśli procesor multimediów tworzy plik MP4, można stopniowo pobrać plik. Jeśli procesor multimediów produkuje plik JSON, można pobrać plik z usługi Azure Blob Storage. 

## <a name="media-analytics-services"></a>Usługi Media Analytics

### <a name="indexer"></a>Indeksator
Za pomocą Azure Media Indexer można przeszukiwać zawartość i generować ścieżki zamkniętego podpisu. Aby uzyskać szczegółowe informacje i przykłady, zobacz [indeksowanie plików multimedialnych za pomocą Azure Media Indexer](media-services-index-content.md).

### <a name="motion-detector"></a>Wykrywanie ruchu
Możesz użyć detektora ruchu, aby wykryć ruch wideo przy użyciu tła stacjonarnego. Dzięki temu można sprawdzić, czy w przypadku zdarzeń ruchu wykrytych przez aparaty nadzoru wykryto fałszywe pozytywne wartości. Aby uzyskać szczegółowe informacje i przykłady, zobacz [wykrywanie ruchu dla Azure Media Analytics](media-services-motion-detection.md).

### <a name="face-detector"></a>Wykrywanie twarzy
Za pomocą narzędzia do wykrywania twarzy można wykrywać twarze osób i ich emocji, w tym szczęście, smutek i niespodziewane. Jest to kilka przydatnych aplikacji branżowych opisanych później, w tym agregowanie i analizowanie reakcji osób biorących udział w wydarzeniu. Aby uzyskać szczegółowe informacje i przykłady, zobacz [wykrywanie czołowe i rozpoznawania emocji dla Azure Media Analytics](media-services-face-and-emotion-detection.md).

### <a name="video-summarization"></a>Podsumowanie wideo
Podsumowanie wideo może pomóc w tworzeniu podsumowań długich filmów wideo przez automatyczne wybieranie interesujących fragmentów kodu z obrazu źródłowego. Ta możliwość jest przydatna, gdy chcesz szybko zapoznać się z oczekiwaniami w długim filmie wideo. Aby uzyskać szczegółowe informacje i przykłady, zobacz [używanie Azure Media Video thumbnails do tworzenia podsumowania filmów wideo](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optyczne rozpoznawanie znaków
Za pomocą usługi Azure Media OCR (optyczne rozpoznawanie znaków) można konwertować zawartość tekstową w plikach wideo na edytowalny, przeszukiwany tekst cyfrowy. Następnie można zautomatyzować wyodrębnianie znaczących metadanych z sygnału wideo na nośniku.
### <a name="scalable-face-redaction"></a>Skalowalne Redakcja kroju
Azure Media Redactor to Media Analyticsy procesor multimediów, który oferuje skalowalne możliwości redakcyjne w chmurze. Za pomocą redakcji twarzy można modyfikować wideo, aby rozmycie twarze wybranych osób. Warto użyć usługi redakcyjnej ze stroną na nośniku wiadomości lub w przypadku bezpieczeństwa publicznego. Kilka minut filmu, które zawiera wiele twarzy, może zająć kilka godzin, ale w przypadku tej usługi, Redakcja twarzy zajmuje zaledwie wiele prostych kroków. Aby uzyskać więcej informacji, zobacz artykuł [redagowanie twarzy z Azure Media Analytics](media-services-face-redaction.md) .

### <a name="content-moderation"></a>Moderowanie zawartości
Usługa Azure Content Moderator umożliwia korzystanie z moderowania maszynowego dla filmów wideo. Na przykład chcesz wykrywać potencjalną zawartość dla dorosłych i nieodpowiednią w filmach wideo oraz przesyłać zawartość oznaczoną flagą do przejrzenia przez zespoły ds. moderowania obsługiwanego przez ludzi. Ręczne moderowanie wideo dla niepożądanej zawartości to czasochłonne i kosztowne zadanie. Korzystając z tej usługi i skojarzonych narzędzi do przeglądu, można połączyć sprzętowo moderowane funkcje, aby zapewnić wydajne i ekonomiczne wyniki. Aby dowiedzieć się więcej, zobacz artykuł [Przetwarzanie filmów wideo przy użyciu usługi Azure Content moderator](media-services-content-moderation.md) .

## <a name="common-scenarios"></a>Typowe scenariusze
Media Analytics może pomóc organizacjom i przedsiębiorstwom zgłębiać nowe szczegółowe informacje od wideo i wydajnie zarządzać dużymi ilościami zawartości wideo. Poniżej przedstawiono kilka scenariuszy:

* **Centra wywołań**. Nawet w przypadku pojawieniu mediów społecznościowych centra obsługi klienta nadal ułatwiają znaczną część transakcji klientów. Zakodowane w tych danych audio to duża ilość informacji o klientach, które można analizować, aby osiągnąć wyższy poziom zadowolenia klientów. Korzystając z Media Indexer, organizacje mogą wyodrębnić indeksy wyszukiwania tekstu i tworzenia oraz pulpity nawigacyjne. Następnie mogą wyodrębnić analizę między typowymi skargami, źródłami skarg i innymi istotnymi danymi.
* **Moderowanie zawartości generowane przez użytkownika**. Z Internetu w odniesieniu do departamentów policyjnych w wielu organizacjach dostępne są publiczne portale, które akceptują nośniki generowane przez użytkownika, takie jak wideo i obrazy. Ilość zawartości może nawiązać wynik ze względu na nieoczekiwane zdarzenia. W takich scenariuszach trudno jest skutecznie przeprowadzać ręczne przeglądy zawartości pod kątem odpowiedniej ważności. Klienci mogą polegać na usłudze moderowania zawartości, aby skoncentrować się na odpowiedniej zawartości.

## <a name="media-analytics-media-processors"></a>Media Analytics procesorów multimediów
W tej sekcji przedstawiono Media Analytics procesorów multimediów i przedstawiono sposób korzystania z platformy .NET lub REST w celu uzyskania obiektu Media procesor (MP).

### <a name="mp-names"></a>Nazwy MP

* Azure Media Indexer
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Usługa Azure Media Content Moderator

### <a name="net"></a>.NET
Następująca funkcja przyjmuje jedną z określonych nazw MP i zwraca obiekt MP.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


### <a name="rest"></a>REST
Żądanie:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.19
    Host: media.windows.net

Odpowiedź:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>Pokazy
Zobacz [demonstracje Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
Zobacz [anonsowanie Media Services analizy](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Następne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
