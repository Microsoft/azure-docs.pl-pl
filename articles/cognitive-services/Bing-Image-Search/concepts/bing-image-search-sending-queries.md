---
title: Dostosuj i Sugeruj zapytania wyszukiwania obrazów — interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat dostosowywania zapytań wyszukiwania wysyłanych do interfejs API wyszukiwania obrazów Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: f697449fffe6c93d8e5082b210678d3f51c0c736
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084414"
---
# <a name="customize-and-suggest-image-search-queries"></a>Dostosuj i Sugeruj zapytania wyszukiwania obrazów

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Skorzystaj z tego artykułu, aby dowiedzieć się, jak dostosować zapytania i zasugerować wyszukiwane terminy do wysłania do interfejs API wyszukiwania obrazów Bing.

## <a name="suggest-search-terms"></a>Sugeruj terminy wyszukiwania

Jeśli Twoja aplikacja zawiera pole wyszukiwania, w którym są wprowadzane terminy wyszukiwania, możesz użyć [interfejs API automatycznego sugerowania Bing](../../bing-autosuggest/get-suggested-search-terms.md) , aby ulepszyć środowisko pracy. Interfejs API może wyświetlać sugerowane terminy wyszukiwania w czasie rzeczywistym. Interfejs API zwraca sugerowane ciągi zapytań w oparciu o częściowe terminy wyszukiwania i Cognitive Services.

## <a name="pivot-the-query"></a>Przestawianie zapytania

Jeśli Bing może segmentować oryginalne zapytanie wyszukiwania, obiekt zwracanych [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) zawiera `pivotSuggestions` . Sugestie przestawne mogą być wyświetlane jako opcjonalne terminy wyszukiwania dla użytkownika. Na przykład jeśli oryginalna kwerenda była *powierzchnią firmy Microsoft* , Bing może segmentować zapytanie do *firmy Microsoft* i *powierzchni* i udostępniać sugerowane przestawki dla każdego z nich. Te sugestie mogą być wyświetlane jako opcjonalne warunki kwerendy dla użytkownika.

Poniższy przykład przedstawia sugestie dotyczące obszaru Pivot dla urządzenia *Microsoft Surface* :  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Pole `pivotSuggestions` zawiera listę segmentów (elementów bazowych), na które zostało podzielone oryginalne zapytanie. Dla każdego elementu bazowego odpowiedź zawiera listę obiektów [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) zawierających sugerowane zapytania. `text`Pole zawiera sugerowane zapytanie. `displayText`Pole zawiera termin, który zastępuje przestawkę Pivot w oryginalnym zapytaniu. Przykładem jest Data wydania powierzchni.

Jeśli ciąg zapytania Pivot jest szukanym przez użytkownika, użyj `text` pól i, `thumbnail` Aby wyświetlić ciągi zapytania Pivot. Udostępnij miniaturę i tekst przy użyciu `webSearchUrl` adresu URL lub `searchLink` adresu URL. Służy `webSearchUrl` do wysyłania użytkownika do wyników wyszukiwania Bing. Jeśli postanowisz własną stronę wyników, użyj `searchLink` .

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Rozwiń zapytanie

Jeśli usługa Bing może rozwinąć zapytanie w celu zawężenia kryteriów oryginalnego wyszukiwania, obiekt [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) zawiera pole `queryExpansions`. Na przykład, jeśli kwerenda była *powierzchnią firmy Microsoft* , rozszerzone zapytania mogą być następujące:
- Microsoft Surface **Pro 3** .
- Microsoft Surface **RT** .
- **Telefon** naziemny firmy Microsoft.
- Microsoft Surface **Hub** .

W poniższym przykładzie pokazano rozwinięte zapytania dla frazy *Microsoft Surface* .

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

Pole `queryExpansions` zawiera listę obiektów [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj). `text`Pole zawiera rozwinięte zapytanie. `displayText`Pole zawiera termin rozwinięcia. Jeśli rozszerzony ciąg zapytania jest wyszukiwany przez użytkownika, użyj `text` pól i, `thumbnail` Aby wyświetlić rozwinięte ciągi zapytań. Udostępnij miniaturę i tekst przy użyciu `webSearchUrl` adresu URL lub `searchLink` adresu URL. Służy `webSearchUrl` do wysyłania użytkownika do wyników wyszukiwania Bing. Jeśli postanowisz własną stronę wyników, użyj `searchLink` .

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze nie podjęto próby interfejs API wyszukiwania obrazów Bing, wypróbuj [Przewodnik Szybki Start](../quickstarts/csharp.md). Jeśli szukasz czegoś bardziej złożonego, Wypróbuj ten samouczek, aby utworzyć [jednostronicową aplikację sieci Web](../tutorial-bing-image-search-single-page-app.md).
