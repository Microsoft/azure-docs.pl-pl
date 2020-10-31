---
title: Przykłady dla zestawu SDK wyszukiwania w sieci Web Bing
titleSuffix: Azure Cognitive Services
description: Zestaw SDK wyszukiwania w sieci Web Bing umożliwia dodawanie możliwości wyszukiwania do aplikacji w języku Python, Node.js, C# lub Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: sample
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: b31335df7c1dc48f82699aa3676561721474071d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076218"
---
# <a name="bing-web-search-sdk-samples"></a>Przykłady dla zestawu SDK wyszukiwania w sieci Web Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Zestaw SDK wyszukiwania w sieci Web Bing jest dostępny w języku Python, Node.js, C# i Java. Przykłady kodu, warunki wstępne i instrukcje kompilacji znajdują się w witrynie GitHub. Następujące scenariusze są obsługiwane:

* Wykonywanie zapytania o pojedynczy wyraz oraz wyświetlanie nazwy i adresu URL pierwszego wyniku dla stron internetowych, obrazów, artykułów z wiadomościami i filmów wideo.
* Wykonywanie zapytania o frazę, weryfikowanie liczby wyników oraz wyświetlanie nazwy i adresu URL pierwszego wyniku.
* Wykonywanie zapytania o termin wyszukiwania z filtrami odpowiedzi ustawionymi na `news` oraz wyświetlanie szczegółów wyników dotyczących wiadomości.
* Wykonywanie zapytania o termin wyszukiwania z parametrami `answerCount` i `promote` oraz wyświetlanie szczegółów wyników.

## <a name="sdks-and-libraries"></a>Zestawy SDK i biblioteki

Poniższe linki umożliwiają uzyskiwanie dostępu do zestawu SDK dla preferowanego języka.

* Rozpocznij pracę, korzystając z [przykładów dla języka Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)
  * Zobacz też [biblioteki języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-websearch) zawierające definicje i zależności.
* Wprowadzenie do [ przykładówNode.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
  * Zobacz też [informacje o wyszukiwaniu w sieci Web platformy Node.js](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesWebSearch).
* Rozpocznij pracę, korzystając z [przykładów dla platformy .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
  * [Pakiet NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
  * Zobacz też [biblioteki platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingWebSearch) zawierające definicje i zależności.
* Rozpocznij pracę, korzystając z [przykładów dla języka Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
  * Zobacz też [biblioteki języka Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch) zawierające definicje i zależności.
