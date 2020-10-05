---
title: Wyszukiwanie jednostek Bing przewodniku szybki start dotyczącej biblioteki klienta C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 27951a6da2c5e4a9d17e8b332d8ee9706701649d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88931895"
---
Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć wyszukiwanie jednostek przy użyciu biblioteki klienta wyszukiwanie jednostek Bing dla języka C#. Chociaż wyszukiwanie jednostek Bing ma interfejs API REST zgodny z większością języków programowania, Biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual Studio 2017 lub nowszego](https://www.visualstudio.com/downloads/).
* Struktura [Json.NET](https://www.newtonsoft.com/json) dostępna jako pakiet NuGet.
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu środowiska [Mono](https://www.mono-project.com/).
* [Pakiet zestawu SDK wyszukiwania wiadomości Bing dla narzędzia NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). Zainstalowanie tego pakietu powoduje także zainstalowanie następujących elementów:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Aby dodać wyszukiwanie jednostek Bingą bibliotekę kliencką do projektu programu Visual Studio, użyj opcji **Zarządzaj pakietami NuGet** z **Eksplorator rozwiązań**i Dodaj `Microsoft.Azure.CognitiveServices.Search.EntitySearch` pakiet.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowe rozwiązanie konsolowe dla języka C# w programie Visual Studio. Dodaj następujący kod do głównego pliku kodu.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Tworzenie klienta i wysyłanie żądania wyszukiwania

1. Utwórz nowego klienta wyszukiwania. Dodaj klucz subskrypcji, tworząc nowy obiekt `ApiKeyServiceClientCredentials`.

    ```csharp
    var client = new EntitySearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. Użyj funkcji `Entities.Search()` klienta w celu wyszukania zapytania:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Pobieranie i wyświetlanie opisu jednostki

1. Jeśli interfejs API zwrócił wyniki wyszukiwania, pobierz jednostkę główną z obiektu `entityData`.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. Wyświetlanie opisu jednostki głównej 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../../tutorial-bing-entities-search-single-page-app.md)

* [Czym jest interfejs API wyszukiwania jednostek Bing?](../../overview.md )
