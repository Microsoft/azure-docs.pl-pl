---
title: Przeszukiwanie widoku niestandardowego — wyszukiwanie niestandardowe Bing
titleSuffix: Azure Cognitive Services
description: Po skonfigurowaniu niestandardowego środowiska wyszukiwania można je przetestować z poziomu portalu wyszukiwanie niestandardowe Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: a27f252f401d0fcf863391a0d45538b01b1141e9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367288"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Wywoływanie wystąpienia wyszukiwanie niestandardowe Bing z portalu

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Po skonfigurowaniu niestandardowego środowiska wyszukiwania można je przetestować z poziomu [portalu](https://customsearch.ai)wyszukiwanie niestandardowe Bing. 

![zrzut ekranu portalu wyszukiwania niestandardowego Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Utwórz zapytanie wyszukiwania 

Po zalogowaniu się do [portalu](https://customsearch.ai)wyszukiwanie niestandardowe Bing wybierz wystąpienie wyszukiwania i kliknij kartę **produkcja** . W obszarze **punkty końcowe** wybierz punkt końcowy interfejsu API (na przykład internetowy interfejs API). Twoja subskrypcja określa, jakie punkty końcowe są wyświetlane.

Aby utworzyć zapytanie wyszukiwania, wprowadź wartości parametrów dla punktu końcowego. Należy pamiętać, że parametry wyświetlane w portalu mogą ulec zmianie w zależności od wybranego punktu końcowego. Zobacz [odwołanie interfejs API wyszukiwania niestandardowego](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) , aby uzyskać więcej informacji. Aby zmienić subskrypcję używaną przez wystąpienie wyszukiwania, Dodaj odpowiedni klucz subskrypcji i zaktualizuj odpowiednie parametry rynku i/lub języka.

Poniżej przedstawiono kilka ważnych parametrów:


|Parametr  |Opis  |
|---------|---------|
|Zapytanie     | Termin wyszukiwania, który ma zostać wyszukany. Dostępne tylko dla punktów końcowych sieci Web, obrazów, wideo i automatycznego sugerowania |
|Identyfikator konfiguracji niestandardowej | Identyfikator konfiguracji wybranego wystąpienia wyszukiwania niestandardowego. To pole jest tylko do odczytu. |
|Do     | Rynek, z którego pochodzą wyniki. Dostępne tylko dla punktów końcowych sieci Web, obrazów, wideo i hostowanego interfejsu użytkownika.        |
|Klucz subskrypcji | Klucz subskrypcji do przetestowania. Możesz wybrać klucz z listy rozwijanej lub wprowadzić go ręcznie.          |

Kliknięcie **dodatkowych parametrów** ujawnia następujące parametry:  

|Parametr  |Opis  |
|---------|---------|
|Bezpieczne wyszukiwanie     | Filtr używany do filtrowania stron sieci Web na potrzeby zawartości dla dorosłych. Dostępne tylko dla punktów końcowych sieci Web, obrazów, wideo i hostowanego interfejsu użytkownika. Należy pamiętać, że niestandardowe wyszukiwanie wideo Bing obsługuje tylko dwie wartości: `moderate` i `strict` .        |
|Język interfejsu użytkownika    | Język używany dla ciągów interfejsu użytkownika. Na przykład jeśli włączysz obrazy i wideo w hostowanym interfejsie użytkownika, karty **obrazu** i **wideo** używają określonego języka.        |
|Liczba     | Liczba wyników wyszukiwania do zwrócenia w odpowiedzi. Dostępne tylko dla punktów końcowych sieci Web, obrazów i wideo.         |
|Przesunięcie    | Liczba wyników wyszukiwania do pominięcia przed zwróceniem wyników. Dostępne tylko dla punktów końcowych sieci Web, obrazów i wideo.        |
    
Po określeniu wszystkich wymaganych opcji kliknij pozycję **Połącz** , aby wyświetlić odpowiedź JSON w okienku po prawej stronie. W przypadku wybrania punktu końcowego hostowanego interfejsu użytkownika można testować środowisko wyszukiwania w dolnym okienku.

## <a name="change-your-bing-custom-search-subscription"></a>Zmień subskrypcję usługi wyszukiwanie niestandardowe Bing

Subskrypcję skojarzoną z wystąpieniem wyszukiwanie niestandardowe Bing można zmienić bez tworzenia nowego wystąpienia. Aby wywołania interfejsu API były wysyłane i obciążane do nowej subskrypcji, Utwórz nowy zasób wyszukiwanie niestandardowe Bing w Azure Portal. Użyj nowego klucza subskrypcji w żądaniach interfejsu API wraz z niestandardowym IDENTYFIKATORem konfiguracji wystąpienia.

## <a name="next-steps"></a>Następne kroki

- [Wywoływanie widoku niestandardowego przy użyciu języka C #](./call-endpoint-csharp.md)
- [Wywoływanie niestandardowego widoku przy użyciu języka Java](./call-endpoint-java.md)
- [Wywoływanie niestandardowego widoku przy użyciu NodeJs](./call-endpoint-nodejs.md)
- [Wywoływanie niestandardowego widoku przy użyciu języka Python](./call-endpoint-python.md)

- [Wywoływanie niestandardowego widoku przy użyciu zestawu C# SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)