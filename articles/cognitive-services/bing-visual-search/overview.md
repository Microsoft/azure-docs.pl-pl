---
title: Co to jest interfejs API wyszukiwania wizualnego Bing?
titleSuffix: Azure Cognitive Services
description: Wyszukiwanie wizualne Bing dostarcza informacje szczegółowe dotyczące obrazu, takie jak podobne obrazy lub źródła zakupów.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 12/19/2019
ms.author: scottwhi
ms.openlocfilehash: 2eab79d79a287bc8a92133c6901c420dfaee2fd5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082049"
---
# <a name="what-is-the-bing-visual-search-api"></a>Co to jest interfejs API wyszukiwania wizualnego Bing?

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Interfejs API wyszukiwania wizualnego Bing zwraca szczegółowe informacje o obrazie. Możesz przekazać obraz lub podać adres URL. Szczegółowe dane są wizualnie podobnymi obrazami, źródłami zakupów, stronami sieci Web, które zawierają obraz i nie tylko. Szczegółowe informacje zwracane przez interfejs API wyszukiwania wizualnego Bing są podobne do pokazanych w Bing.com/images. 

Jeśli używasz [interfejs API wyszukiwania obrazów Bing](../bing-image-search/overview.md), możesz użyć tokenów usługi Insights z wyników wyszukiwania tego interfejsu API dla Wyszukiwanie wizualne Bing zamiast przekazywania obrazu.

> [!IMPORTANT]
> Jeśli uzyskujesz szczegółowe informacje o obrazach za pomocą interfejsu API wyszukiwania obrazów Bing, rozważ przejście do interfejsu API wyszukiwania wizualnego Bing, który zapewnia bardziej kompleksowe szczegółowe informacje.

## <a name="insights"></a>Insights

Poniższe informacje można odnaleźć przy użyciu wyszukiwanie wizualne Bing:

| Szczegółowe informacje                              | Opis |
|--------------------------------------|-------------|
| Obrazy podobne wizualnie              | Lista obrazów, które wizualnie przypominają obraz wejściowy. |
| Produkty podobne wizualnie            | Produkty, które są wizualnie podobne do pokazanego produktu.            |
| Źródła zakupów                     | Miejsca, w których można kupić rzecz pokazaną na obrazie wejściowym.            |
| Powiązane wyszukiwania                     | Powiązane wyszukiwania wykonane przez innych użytkowników lub oparte na zawartości obrazu.            |
| Strony sieci Web, które zawierają obraz     | Strony internetowe, które zawierają obraz wejściowy.            |
| Przepisy                              | Strony sieci Web, które zawierają przepisy dotyczące tworzenia naczyń z obrazu wejściowego.            |
| Jednostki                             | Dobrze znane osoby, miejsca i rzeczy. |

Oprócz szczegółowych informacji wyszukiwanie wizualne Bing zwraca różne warunki (czyli Tagi) pochodzące z obrazu wejściowego. Tagi umożliwiają użytkownikom Eksplorowanie pojęć znalezionych w obrazie. Na przykład jeśli obraz wejściowy jest sławę Athlete, jeden z tagów może być nazwą Athlete, inny tag może być sportem. Lub, jeśli obraz wejściowy jest kołowy firmy Apple, Tagi mogą być kołowe firmy Apple, ciasta i Desserts.

Wyniki wyszukiwanie wizualne Bing zawierają również pola ograniczenia dotyczące regionów interesujących obraz. Na przykład jeśli obraz zawiera kilka osobistości, wyniki mogą zawierać pola granic dla każdego rozpoznanego osobistości. Lub, jeśli Bing rozpoznaje produkt lub odzież w obrazie, wynik może zawierać obwiednię dla rozpoznanego elementu.

## <a name="workflow"></a>Przepływ pracy

Interfejs API wyszukiwania wizualnego Bing jest usługą internetową zgodną z wzorcem REST, łatwą do wywołania z dowolnego języka programowania, który może wysyłać żądania HTTP i analizować format JSON. Dla usługi można użyć interfejsu API REST lub zestawu SDK.

1. Utwórz [konto Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , aby uzyskać dostęp do interfejsy API wyszukiwania Bing. Jeśli nie masz subskrypcji platformy Azure, możesz [bezpłatnie utworzyć konto](https://azure.microsoft.com/free/cognitive-services/).
2. Wyślij żądanie do interfejsu API z prawidłowym zapytaniem wyszukiwania.
3. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="next-steps"></a>Następne kroki

Najpierw wypróbuj interfejs API wyszukiwania wizualnego Bing [Interactive](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/).
Demonstracja pokazuje, jak można szybko dostosować zapytanie wyszukiwania i wyszukuj sieć Web dla obrazów.

Aby szybko rozpocząć pracę z pierwszym żądaniem, zobacz przewodniki Szybki start:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)

## <a name="see-also"></a>Zobacz też

* Informacje o [obrazach Wyszukiwanie wizualne](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) zawierają opis definicji i informacji na temat punktów końcowych, nagłówków żądań, odpowiedzi i parametrów zapytania, których można użyć do żądania wyników wyszukiwania na podstawie obrazu.

* [Użycie interfejsu api wyszukiwanie Bing i wymagania dotyczące wyświetlania](../bing-web-search/use-display-requirements.md) określają akceptowalne zastosowania zawartości i informacji uzyskanych za pośrednictwem interfejsów API wyszukiwania Bing.

* Odwiedź [stronę centrum interfejsu API wyszukiwanie Bing](../bing-web-search/search-the-web.md) , aby poznać inne dostępne interfejsy API.