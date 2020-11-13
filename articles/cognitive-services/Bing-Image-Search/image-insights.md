---
title: Uzyskaj szczegółowe informacje o obrazach — interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak za pomocą interfejs API wyszukiwania obrazów Bing uzyskać więcej informacji na temat obrazu.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: a4c8aff8694c9d1dde0ab8d34e65f89a7ab4efbd
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592111"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Uzyskaj szczegółowe informacje o obrazach przy użyciu interfejs API wyszukiwania obrazów Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

> [!IMPORTANT]
> Zamiast korzystać z punktu końcowego/images/Details w celu uzyskania szczegółowych informacji o obrazach, należy użyć [Wyszukiwanie wizualne](../bing-visual-search/overview.md) , ponieważ oferuje bardziej kompleksowy wgląd w szczegółowe dane.


Każdy obraz zawiera token Insights, którego można użyć do uzyskania informacji o obrazie. Można na przykład uzyskać kolekcję powiązanych obrazów, stron sieci Web, które zawierają obraz, lub listę handlowców, w których można kupić produkt widoczny na obrazie.  

Aby uzyskać szczegółowe informacje o obrazie, Przechwyć token [imageInsightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken) obrazu w odpowiedzi.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

Następnie Wywołaj punkt końcowy szczegółów obrazu i ustaw parametr zapytania [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) na token w `imageInsightsToken` .  

Aby określić szczegółowe informacje, które chcesz pobrać, ustaw `modules` parametr zapytania. Aby uzyskać szczegółowe informacje, ustaw wartość `modules` `All` . Aby uzyskać tylko informacje o podpisie i kolekcji, ustaw `modules` jako `Caption%2CCollection` . Aby uzyskać pełną listę możliwych szczegółowych informacji, zobacz [moduły](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested). Nie wszystkie szczegółowe informacje są dostępne dla wszystkich obrazów. Odpowiedź zawiera wszystkie żądania szczegółowych informacji, o ile są dostępne.

Poniższy przykład żąda wszystkich dostępnych szczegółowych informacji o powyższym obrazie.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Uzyskiwanie szczegółowych informacji o znanym obrazie

Jeśli masz adres URL obrazu, który chcesz uzyskać szczegółowe informacje, użyj parametru zapytania [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) zamiast parametru [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) w celu określenia obrazu. Lub, jeśli dysponujesz plikiem obrazu, możesz wysłać plik binarny obrazu w treści żądania POST. Jeśli używasz żądania POST, `Content-Type` nagłówek musi być ustawiony na `multipart/data-form` . Przy użyciu jednej z opcji rozmiar obrazu nie może przekraczać 1 MB.  

Jeśli masz adres URL obrazu, Poniższy przykład pokazuje, jak zażądać szczegółowych informacji o obrazie.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Pobieranie wszystkich informacji o obrazie  

Aby zażądać wszystkich szczegółowych informacji o obrazie, ustaw parametr query [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na `All` . Aby uzyskać wyszukiwanie pokrewne, żądanie musi zawierać ciąg zapytania użytkownika. Ten przykład przedstawia użycie [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) do określenia obrazu.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Obiekt najwyższego poziomu jest obiektem [ImageInsightsResponse](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) zamiast obiektu [images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) .  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>Rozpoznawanie jednostek w obrazie  

Funkcja rozpoznawania jednostek identyfikuje jednostki w obrazie, obecnie tylko osoby. Aby identyfikować jednostki w obrazie, ustaw parametr query [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na `RecognizedEntities` .  

> [!NOTE]
> Nie można określić tego modułu z żadnym innym modułem. W przypadku określenia tego modułu z innymi modułami odpowiedź nie obejmuje rozpoznanych jednostek.  

Poniżej pokazano, jak określić obraz przy użyciu parametru [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) . Pamiętaj, aby w adresie URL zakodować parametry zapytania.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Poniżej przedstawiono odpowiedź na poprzednie żądanie. Ponieważ obraz zawiera dwie osoby, odpowiedź identyfikuje region dla każdej osoby. W takim przypadku osoby zostały rozpoznane w grupach CelebrityAnnotations i CelebRecognitionAnnotations. Bing wyświetla listę osób w każdej grupie, na podstawie prawdopodobieństwa, że są one zgodne z osobą w oryginalnym obrazie. Lista znajduje się w kolejności malejącej. Grupa CelebRecognitionAnnotations zapewnia najwyższy poziom pewności, że dopasowanie jest poprawne.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

`region`Pole określa obszar obrazu, gdzie Bing rozpoznaje jednostkę. W przypadku osób, region reprezentuje głowę osoby.  

Wartości prostokąta są względem szerokości i wysokości oryginalnego obrazu i mieszczą się w przedziale od 0,0 do 1,0. Na przykład jeśli obraz jest 300x200, a górny róg regionu znajduje się w punkcie (10, 20) i u dołu, prawy róg jest w punkcie (290, 150), a następnie znormalizowany prostokąt jest:  

-   Left: 10/300 = 0,03333...  
-   Góra: 20/200 = 0,1  
-   Prawy: 290/300 = 0,9667...  
-   Dół: 150/200 = 0,75  

Możesz użyć regionu, który powraca do usługi Bing w kolejnych wywołaniach szczegółowych informacji. Na przykład, aby uzyskać wizualnie podobne obrazy rozpoznanej jednostki. Aby uzyskać więcej informacji, zobacz przycinanie obrazów do użycia z wizualnie podobnymi i modułami rozpoznawania jednostek. Poniżej przedstawiono mapowanie między polami regionu i parametrami zapytania, których można użyć do przycinania obrazów.  

-   Lewy dostęp do [licencji CAL](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
-   Najważniejsze mapy do [Cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
-   Prawa mapa do [samochodu](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
-   Dolna mapa do [pliku cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Znajdowanie obrazów podobnych wizualnie  

Aby znaleźć obrazy, które są wizualnie podobne do oryginalnego obrazu, ustaw parametr query [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na SimilarImages.  

Poniższe żądanie pokazuje, jak uzyskać wizualnie podobne obrazy. Żądanie używa parametru zapytania [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) do identyfikowania oryginalnego obrazu. Aby poprawić istotność, należy uwzględnić ciąg zapytania użytkownika.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


Poniżej przedstawiono odpowiedź na poprzednie żądanie.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Przycinanie obrazów do użycia z wizualnie podobnymi i modułami rozpoznawania jednostek  

Aby określić region obrazu używany przez usługę Bing do określenia, czy obrazy są podobne do wizualizacji, czy do rozpoznawania jednostek, użyj parametrów zapytania [cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal), [Cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat), [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)i [samochodu](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) . Domyślnie usługi Bing używają całego obrazu.  

Parametry określają górny, lewy róg i dolny róg regionu, który jest wykorzystywany przez Bing do porównania. Określ wartości jako ułamki szerokości i wysokości obrazu oryginalnego. Wartości ułamkowe zaczynają się od (0,0, 0,0) w górnej części, w lewym górnym rogu i na końcu (1,0, 1,0) w prawym dolnym rogu. Na przykład, aby określić, że górny lewy róg zaczyna się od góry i z kwartału w kierunku od lewej strony, ustaw `cal` na 0,25 i `cat` 0,25.  

Poniższa sekwencja wywołań przedstawia efekt określania regionu przycinania. Pierwsze wywołanie nie obejmuje przycinania i usługa Bing rozpoznaje dwie osoby stojące w środku obrazu.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Odpowiedź zawiera dwie rozpoznane jednostki.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

Drugie wywołanie przycina obraz w pionie w dół i Bing rozpoznał pojedynczą osobę po prawej stronie obrazu.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Odpowiedź zawiera jedną rozpoznaną jednostkę.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>Wyszukiwanie podobnych produktów wizualnych  

Aby znaleźć obrazy zawierające produkty, które są wizualnie podobne do produktów znalezionych w oryginalnym obrazie, ustaw parametr query [module](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) na SimilarProducts.  

Poniższe żądanie pokazuje, jak uzyskać obrazy podobnych produktów. Żądanie używa parametru zapytania [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) do zidentyfikowania oryginalnego obrazu, który został zwrócony w poprzednim żądaniu. Aby poprawić istotność, należy uwzględnić ciąg zapytania użytkownika.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Poniżej przedstawiono odpowiedź na poprzednie żądanie. Odpowiedź zawiera obraz podobnego produktu i wskazuje, ilu handlowców oferuje produkt w trybie online, czy istnieją klasyfikacje produktów i najniższą cenę (patrz `aggregateOffer` pole).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

Aby uzyskać listę handlowców, które oferują produkt online (patrz pole [offerCount](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) ), ponownie wywołaj interfejs API i ustaw wartość `modules` ShoppingSources. Następnie ustaw `insightsToken` parametr zapytania na token znaleziony w obrazie podsumowania produktu.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Poniżej znajduje się odpowiedź na poprzednie żądanie.  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```