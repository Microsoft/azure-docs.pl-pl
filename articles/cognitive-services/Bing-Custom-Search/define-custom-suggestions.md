---
title: Definiowanie niestandardowych sugestii automatycznego sugerowania — wyszukiwanie niestandardowe Bing
titleSuffix: Azure Cognitive Services
description: Niestandardowe automatyczne sugerowanie zwraca listę sugerowanych ciągów zapytań wyszukiwania, które są istotne dla Twojego środowiska wyszukiwania.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 986a7c8f1282b76c2ae94eebcc2c31dc3b3e7cad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96352018"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Skonfiguruj niestandardowe środowisko automatycznego sugerowania

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Niestandardowe automatyczne sugerowanie zwraca listę sugerowanych ciągów zapytań wyszukiwania, które są istotne dla Twojego środowiska wyszukiwania. Sugerowane ciągi zapytań są oparte na częściowym ciągu zapytania, który użytkownik udostępnia w polu wyszukiwania. Lista będzie zawierać maksymalnie 10 sugestii. 

Należy określić, czy zwrócić tylko sugestie niestandardowe, czy też dołączyć sugestie Bing. Jeśli dołączysz sugestie Bing, sugestie niestandardowe są wyświetlane przed sugestiami Bing. W przypadku podania wystarczającej sugestii możliwe jest, że zwracana lista sugestii nie będzie zawierać sugestii usługi Bing. Sugestie dotyczące usługi Bing są zawsze w kontekście wystąpienia wyszukiwania niestandardowego. 

Aby skonfigurować sugestie dotyczące zapytania wyszukiwania dla danego wystąpienia, kliknij kartę **Autosugerowanie** .  

> [!NOTE]
> Aby skorzystać z tej funkcji, musisz subskrybować wyszukiwanie niestandardowe na odpowiednim poziomie (zobacz [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

W celu odzwierciedlenia sugestii w obsługiwanym punkcie końcowym (interfejsie API lub hostowany interfejs użytkownika) może upłynąć nawet 24 godziny.

## <a name="enable-bing-suggestions"></a>Włącz sugestie Bing

Aby włączyć sugestie Bing, przełącz suwak **automatycznych sugestii Bing** do pozycji Wł. Suwak zmieni się na niebieski.

## <a name="add-your-own-suggestions"></a>Dodawanie własnych sugestii

Aby dodać własne sugestie ciągu zapytania, Dodaj je do listy w obszarze **sugestie zdefiniowane przez użytkownika**. Po dodaniu sugestii na liście naciśnij klawisz ENTER lub kliknij **+** ikonę. Możesz określić sugestię w dowolnym języku. Można dodać maksymalnie 5 000 sugestii ciągu zapytania.

## <a name="upload-suggestions"></a>Przekaż sugestie

Jako opcję można przekazać listę sugestii z pliku. Plik musi zawierać jeden ciąg zapytania wyszukiwania w każdym wierszu. Aby przekazać plik, kliknij ikonę Przekaż i wybierz plik do przekazania. Usługa wyodrębnia sugestie z pliku i dodaje je do listy.

## <a name="remove-suggestions"></a>Usuń sugestie

Aby usunąć sugestię ciągu zapytania, kliknij ikonę Usuń obok sugestii, którą chcesz usunąć.

## <a name="block-suggestions"></a>Zablokuj sugestie

Jeśli dołączysz sugestie usługi Bing, możesz dodać listę ciągów zapytań wyszukiwania, które nie mają być zwracane przez usługę Bing. Aby dodać zablokowane ciągi zapytań, kliknij przycisk **Pokaż zablokowane sugestie**. Dodaj ciąg zapytania do listy, a następnie naciśnij klawisz ENTER lub kliknij **+** ikonę. Można dodać maksymalnie 50 zablokowanych ciągów zapytań.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Aby niestandardowe zmiany konfiguracji automatycznego sugerowania zaczęły obowiązywać, może minąć do 24 godzin.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Włączanie automatycznego sugerowania w hostowanym interfejsie użytkownika

Aby włączyć sugestie ciągu zapytania dla hostowanego interfejsu użytkownika, kliknij pozycję **hostowany interfejs użytkownika**. Przewiń w dół do sekcji **dodatkowa konfiguracja** . W obszarze **Wyszukiwanie w sieci Web** **Wybierz pozycję włączone,** aby **włączyć automatyczne sugerowanie**. Aby włączyć automatyczne sugerowanie, należy wybrać układ, który zawiera pole wyszukiwania.


## <a name="calling-the-autosuggest-api"></a>Wywoływanie interfejsu API automatycznego sugerowania

Aby uzyskać sugerowane ciągi zapytań przy użyciu interfejs API wyszukiwania niestandardowego Bing, Wyślij `GET` żądanie do poniższego punktu końcowego.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

Odpowiedź zawiera listę `SearchAction` obiektów, które zawierają sugerowane ciągi zapytań.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Każda sugestia zawiera `displayText` `query` pole i. `displayText`Pole zawiera sugerowany ciąg zapytania, który służy do wypełniania listy rozwijanej pola wyszukiwania.

Jeśli użytkownik wybierze sugerowany ciąg zapytania z listy rozwijanej, użyj ciągu zapytania w `query` polu podczas wywoływania [interfejs API wyszukiwania niestandardowego Bing](overview.md).


## <a name="next-steps"></a>Następne kroki

- [Pobieranie niestandardowych sugestii]()
- [Przeszukiwanie wystąpienia niestandardowego](./search-your-custom-view.md)
- [Konfigurowanie i korzystanie z niestandardowego interfejsu hosta hostowanego](./hosted-ui.md)