---
title: Przykładowe scenariusze użytkowników dla interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono typowe scenariusze integracji interfejs API analizy tekstu z usługami i procesami.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 4f540c7e853efe370c84b7c4d0a7d74911b8cd6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823936"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Przykładowe scenariusze użytkowników dla interfejs API analizy tekstu

Interfejs API analizy tekstu jest usługą opartą na chmurze, która zapewnia zaawansowane przetwarzanie języka naturalnego dla tekstu. W tym artykule opisano przykładowe przypadki użycia związane z integracją interfejsu API z rozwiązaniami i procesami biznesowymi. 

## <a name="analyze-survey-results"></a>Analizowanie wyników ankiety

Uzyskiwanie szczegółowych informacji z wyników ankiety klienta i pracownika przez przetwarzanie nieprzetworzonych odpowiedzi tekstowych przy użyciu analiza tonacji. Agreguj wyniki analizy, zaobserwuj się i Zwiększaj zaangażowanie.

![Obraz opisujący sposób wykonywania analizy tonacji na potrzeby badań klientów i pracowników.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analizowanie zarejestrowanych przychodzących wywołań klienta

Wyodrębnij szczegółowe informacje z wywołań usług Customer Services przy użyciu funkcji zamiany mowy na tekst, analiza tonacji i wyodrębnianie kluczowych fraz. Wyświetl wyniki w Power BI pulpicie nawigacyjnym lub w portalu, aby lepiej zrozumieć klientów, wyróżnić trendy usług klienta i zwiększyć zaangażowanie klientów. Wysyłaj żądania interfejsu API jako partię do raportowania lub w czasie rzeczywistym na potrzeby interwencji. Zapoznaj się z przykładowym kodem [w serwisie GitHub](https://github.com/rlagh2/callcenteranalytics).

![Obraz przedstawiający sposób automatyzowania uzyskiwania szczegółowych informacji z wywołań obsługi klienta przy użyciu analizy tonacji](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Przetwarzaj i Klasyfikuj zdarzenia pomocy technicznej

Użyj wyodrębnianie kluczowych fraz i rozpoznawania jednostek, aby przetwarzać żądania obsługi przesłane w formacie tekstowym bez struktury. Użyj wyodrębnionych fraz i jednostek do kategoryzacji żądań dotyczących planowania zasobów i analizy trendów.

![Obraz opisujący sposób użycia wyodrębniania kluczowych fraz i rozpoznawania jednostek do kategoryzowania raportów i trendów zdarzeń](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Monitoruj źródła danych mediów społecznościowych produktu

Monitoruj Opinie użytkowników o produkcie na stronie serwisu Twitter lub w serwisie Facebook. Za pomocą danych można analizować tonacji klientów w kierunku nowych produktów, wyodrębniać kluczowe frazy dotyczące funkcji i żądań funkcji lub rozwiązywać reklamacje klientów w miarę ich występowania. Zobacz przykład [szablonu Microsoft energooszczędny](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![Obraz opisujący sposób monitorowania produktu i opinii firmy na nośnikach społecznościowych przy użyciu wyodrębniania kluczowych fraz](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Klasyfikowanie i redagowanie dokumentów, które mają informacje poufne

Użyj nazwanego rozpoznawania jednostek do identyfikowania osobistych i poufnych informacji w dokumentach. Dane służą do klasyfikowania dokumentów lub redagowania ich, aby mogły być bezpiecznie udostępniane.

![Obraz opisujący sposób użycia NER do wykrywania informacji osobistych oraz klasyfikowania i redagowania dokumentów](media/use-cases/sensitive-docs.jpg)

## <a name="perform-opinion-mining"></a>Przeprowadź wyszukiwanie opinii

Zapoznaj się z opiniami dotyczącymi konkretnych aspektów produktu lub usługi w ankietach, opinii klientów lub wszędzie tam, gdzie tekst zawiera opinię na temat aspektu. Ułatwiają one tworzenie i ulepszanie produktów, podejmowanie działań marketingowych oraz wyróżnianie sposobu działania produktu lub usługi. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="media/use-cases/aspect-based-sentiment.png" alt-text="Przykładowe opinie dotyczące hotelu.":::

## <a name="next-steps"></a>Następne kroki

* [Czym jest interfejs API analizy tekstu?](overview.md)
* [Wyślij żądanie do interfejs API analizy tekstu przy użyciu biblioteki klienckiej](quickstarts/text-analytics-sdk.md)
