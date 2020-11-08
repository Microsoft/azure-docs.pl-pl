---
title: Znajdź podobne obrazy z poprzednich wyszukiwań przy użyciu tokenów usługi Image Insights i interfejs API wyszukiwania wizualnego Bing
titleSuffix: Azure Cognitive Services
description: Użyj biblioteki klienta wyszukiwanie wizualne Bing, aby uzyskać adresy URL obrazów z poprzednich wyszukiwań.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: f68618c4387bc5105914eccc4e6149650119fd0f
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366081"
---
# <a name="tutorial-find-similar-images-from-previous-searches-using-an-image-insights-token"></a>Samouczek: Znajdowanie podobnych obrazów z poprzednich wyszukiwań przy użyciu tokenu usługi Image Insights

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Biblioteka klienta wyszukiwanie wizualne umożliwia znalezienie obrazów w trybie online z poprzednich wyszukiwań, które zwracają `ImageInsightsToken` . Ta aplikacja pobiera `ImageInsightsToken` i używa tokenu podczas kolejnego wyszukiwania. Następnie wysyła do usługi `ImageInsightsToken` Bing i zwraca wyniki, które zawierają adresy url wyszukiwanie Bing i adresy URL podobnych obrazów, które znajdują się w trybie online.

Pełny kod źródłowy dla tego samouczka można znaleźć w dodatkowej obsłudze błędów i adnotacjach w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs).

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* Jeśli używasz systemu Linux/MacOS, możesz uruchomić tę aplikację przy użyciu narzędzia [mono](https://www.mono-project.com/).
* Pakiety wyszukiwanie wizualne i wyszukiwanie obrazów NuGet.
    - Na Eksplorator rozwiązań w programie Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet** z menu. Zainstaluj `Microsoft.Azure.CognitiveServices.Search.CustomSearch` pakiet i `Microsoft.Azure.CognitiveServices.Search.ImageSearch` pakiet. Zainstalowanie pakietu NuGet powoduje także zainstalowanie następujących elementów:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-client-library"></a>Pobierz ImageInsightsToken z biblioteki klienta wyszukiwanie obrazów Bing

Ta aplikacja korzysta z `ImageInsightsToken` uzyskanych za pośrednictwem [biblioteki klienta wyszukiwanie obrazów Bing](../bing-image-search/quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp). W nowej aplikacji konsolowej C# Utwórz klienta, aby wywołać interfejs API za pomocą polecenia `ImageSearchClient()` . Następnie użyj `SearchAsync()` zapytania:

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Zapisz pierwszy wynik wyszukiwania przy użyciu `imageResults.Value.First()` , a następnie Zapisz obraz `ImageInsightsToken` .

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Ta `ImageInsightsToken` wartość jest wysyłana do Wyszukiwanie wizualne Bing w żądaniu.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Dodawanie ImageInsightsToken do żądania wyszukiwanie wizualne

Określ `ImageInsightsToken` żądanie Wyszukiwanie wizualne przez utworzenie `ImageInfo` obiektu na podstawie `ImageInsightsToken` odpowiedzi z Wyszukiwanie wizualne Bing.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Użyj wyszukiwanie wizualne Bing, aby znaleźć obrazy z ImageInsightsToken

`VisualSearchRequest`Obiekt zawiera informacje o obrazie, `ImageInfo` który ma zostać przeszukany. Metoda `VisualSearchMethodAsync()` pobiera wyniki. Nie musisz podawać danych binarnych obrazu, ponieważ obraz jest reprezentowany przez token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Wykonaj iterację w wyniku wyszukiwanie wizualne

Wyniki wyszukiwania wizualnego to obiekty `ImageTag`. Każdy tag zawiera listę obiektów `ImageAction`. Każdy `ImageAction` z nich zawiera `Data` pole, które jest listą wartości, które są zależne od typu akcji. Można wykonać iterację `ImageTag` obiektów w `visualSearchResults.Tags` , na przykład, i pobrać `ImageAction` tag w nim. Poniższy przykład drukuje szczegóły `PagesIncluding` akcji:

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

Pobieranie rzeczywistych adresów URL obrazów z typów akcji wymaga rzutowania, który odczytuje `ActionType` jako `ImageModuleAction` , który zawiera `Data` element z listą wartości. Każda wartość to adres URL obrazu.  Poniższy rzutuje `PagesIncluding` Typ akcji na `ImageModuleAction` i odczytuje wartości:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Aby uzyskać więcej informacji o tych typach danych, zobacz [Images - Visual Search (Obrazy — wyszukiwanie wizualne)](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

## <a name="returned-urls"></a>Zwrócone adresy URL

Kompletna aplikacja zwraca następujące adresy URL:

|ActionType  |Adres URL  |
|---------|---------|
|MoreSizes — > WebSearchUrl     |         |
|VisualSearch — > WebSearchUrl     |         |
|ImageById — > WebSearchUrl    |         |
|RelatedSearches > WebSearchUrl:    |         |
|DocumentLevelSuggestions > WebSearchUrl:     |         |
|TopicResults — > WebSearchUrl    | https: \/ /www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID = 1BA795A21EAF6A63175699B71FC36B7C&RD = 1&h = BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fdiscover% 2fcanadian% 2brocky&p = DevEx, 5823.1       |
|ImageResults — > WebSearchUrl    |  https: \/ /www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID = 1BA795A21EAF6A63175699B71FC36B7C&RD = 1&h = PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v = 1&r = https %3 a %2 f %2 f www. Bing. com% 2fimages% 2fsearch% 3fq% 3doutdoor&p = DevEx, 5831.1       |

Jak pokazano powyżej, `TopicResults` typy i `ImageResults` zawierają zapytania dotyczące pokrewnych obrazów. Adresy URL łączą się z wynikami wyszukiwania Bing.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie wyszukiwanie wizualne jednostronicowej aplikacji sieci Web](tutorial-bing-visual-search-single-page-app.md)