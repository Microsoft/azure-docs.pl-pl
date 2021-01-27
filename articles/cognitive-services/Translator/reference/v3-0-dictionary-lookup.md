---
title: Metoda wyszukiwania słownika translatora
titleSuffix: Azure Cognitive Services
description: Metoda wyszukiwania słownika oferuje alternatywne tłumaczenia wyrazu i niewielką liczbę fraz idiomatyczne.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: lajanuar
ms.openlocfilehash: 88a76a16de43853a001f5db895d6ad418940de0f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895496"
---
# <a name="translator-30-dictionary-lookup"></a>Translator 3,0: wyszukiwanie słownika

Oferuje alternatywne tłumaczenia wyrazu i niewielką liczbę fraz idiomatyczne. Każde tłumaczenie ma część mowy i listę zwrotów. Ponowne tłumaczenia umożliwiają użytkownikowi zrozumienie tłumaczenia w kontekście. [Przykładowa operacja słownika](./v3-0-dictionary-examples.md) umożliwia dalsze przechodzenie do szczegółów, aby zobaczyć przykład użycia poszczególnych par tłumaczeń.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` żądanie do:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przesłane na ciągu zapytania są następujące:

| Parametr zapytania  | Opis |
| ------ | ----------- |
| api-version <img width=200/>   | **Wymagany parametr**.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi być równa `3.0` |
| wniosek | **Wymagany parametr**.<br/>Określa język tekstu wejściowego. Język źródłowy musi być jednym z [obsługiwanych języków](./v3-0-languages.md) zawartych w `dictionary` zakresie. |
| na wartość   | **Wymagany parametr**.<br/>Określa język tekstu wyjściowego. Język docelowy musi być jednym z [obsługiwanych języków](v3-0-languages.md) zawartych w `dictionary` zakresie. |


Nagłówki żądań obejmują:

| Nagłówki  | Opis |
| ------ | ----------- |
| Nagłówki uwierzytelniania <img width=200/>  | **Wymagany nagłówek żądania**.<br/>Zobacz <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostępne opcje uwierzytelniania</a>. |
| Content-Type | **Wymagany nagłówek żądania**.<br/>Określa typ zawartości ładunku. Możliwe wartości to: `application/json` . |
| Długość zawartości   | **Wymagany nagłówek żądania**.<br/>Długość treści żądania. |
| X-ClientTraceId   | **Opcjonalne**.<br/>Wygenerowany przez klienta identyfikator GUID służący do unikatowej identyfikacji żądania. Możesz pominąć ten nagłówek, jeśli dołączysz identyfikator śledzenia w ciągu zapytania przy użyciu parametru zapytania o nazwie `ClientTraceId` . |

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON z właściwością ciągu o nazwie `Text` , która reprezentuje termin do wyszukania.

```json
[
    {"Text":"fly"}
]
```

Obowiązują następujące ograniczenia:

* Tablica może zawierać co najwyżej 10 elementów.
* Wartość tekstowa elementu tablicy nie może być dłuższa niż 100 znaków, w tym spacje.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź to tablica JSON z jednym wynikiem dla każdego ciągu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `normalizedSource`: Ciąg przedstawiający znormalizowaną formę okresu źródłowego. Na przykład, jeśli żądanie nosi wartość "Jan", znormalizowana forma będzie "Jan". Zawartość tego pola zmieni się na dane wejściowe [przykładów wyszukiwania](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Ciąg dający termin źródłowy w formularzu najlepiej dopasowanego do wyświetlania przez użytkownika końcowego. Na przykład, jeśli dane wejściowe to "Jan", formularz wyświetlania będzie odzwierciedlał zwykły pisownię nazwy: "Jan". 

  * `translations`: Lista tłumaczeń dla terminu źródłowego. Każdy element listy jest obiektem o następujących właściwościach:

    * `normalizedTarget`: Ciąg przedstawiający znormalizowaną postać tego terminu w języku docelowym. Ta wartość powinna być używana jako dane wejściowe [przykładów wyszukiwania](./v3-0-dictionary-examples.md).

    * `displayTarget`: Ciąg określający termin w języku docelowym i w postaci najlepiej dopasowanej do wyświetlania przez użytkownika końcowego. Ogólnie rzecz biorąc, będzie ona różnić się tylko od `normalizedTarget` wielkości liter. Na przykład, prawidłowy rzeczownik, taki jak "Juan", będzie miał `normalizedTarget = "juan"` i `displayTarget = "Juan"` .

    * `posTag`: Ciąg kojarzenia tego terminu ze znacznikiem części mowy.

        | Nazwa tagu | Opis  |
        |----------|--------------|
        | KOREKT      | Przymiotniki   |
        | Zaawansowana      | Parametrów      |
        | CONJ     | Spójniki |
        | DET      | Określenia  |
        | MODAL    | zlecenia        |
        | RZECZOWNIK     | Rzeczowniki        |
        | PRODUKCYJN     | Przyimkami |
        | PRON     | Zaimki     |
        | CZASOWNIK     | zlecenia        |
        | RÓŻNYCH    | Inne        |

        Jako uwagi dotyczące implementacji te Tagi zostały określone przez znakowanie części mowy po stronie angielskiej, a następnie pobierając najbardziej częste znaczniki dla każdej pary Source/Target. Dlatego jeśli ludzie często przekładają wyrazu hiszpańskiego na inny tag części mowy w języku angielskim, Tagi mogą kończyć się nieprawidłowym (w odniesieniu do wyrazu hiszpańskiego).

    * `confidence`: Wartość z zakresu od 0,0 do 1,0, która reprezentuje "pewność" (lub prawdopodobnie dokładniej "prawdopodobieństwo w danych szkoleniowych") tej pary tłumaczeń. Suma wyników pewności dla jednego słowa źródłowego może lub nie może być sumą do 1,0. 

    * `prefixWord`: Ciąg nadający wyraz do wyświetlenia jako prefiks tłumaczenia. Obecnie jest to jednopłciowe określenie rzeczowników w językach, które mają jednopłciowe określenia. Na przykład prefiks słowa "Mosca" w języku hiszpańskim to "La", ponieważ "Mosca" jest rzeczownikiem Feminine w języku hiszpańskim. Jest to zależne od tłumaczenia, a nie źródła. Jeśli nie ma prefiksu, będzie to pusty ciąg.
    
    * `backTranslations`: Lista "retranslations" obiektu docelowego. Na przykład słowa źródłowe, do których można przetłumaczyć element docelowy. Lista jest gwarantowana, aby zawierała żądany wyraz źródłowy (na przykład, jeśli szukane słowo źródłowe jest "przylot", wówczas zostanie zagwarantowane, że "przylot" będzie na `backTranslations` liście). Nie ma jednak gwarancji, że jest w pierwszej pozycji i często nie będzie. Każdy element `backTranslations` listy jest obiektem opisanym przez następujące właściwości:

        * `normalizedText`: Ciąg reprezentujący znormalizowaną formę terminu źródłowego, który jest tłumaczeniem zwrotnym dla elementu docelowego. Ta wartość powinna być używana jako dane wejściowe [przykładów wyszukiwania](./v3-0-dictionary-examples.md).        

        * `displayText`: Ciąg dający termin źródłowy, który jest tłumaczeniem zwrotnym elementu docelowego w postaci najlepiej dopasowanej do wyświetlania przez użytkownika końcowego.

        * `numExamples`: Liczba całkowita reprezentująca liczbę przykładów dostępnych dla tej pary tłumaczeń. Rzeczywiste przykłady muszą zostać pobrane z oddzielnym wywołaniem [przykładów wyszukiwania](./v3-0-dictionary-examples.md). Ta liczba jest przeznaczona głównie do ułatwienia wyświetlania w interfejsie użytkownika. Na przykład interfejs użytkownika może dodać hiperlink do tłumaczenia zwrotnego, jeśli liczba przykładów jest większa od zera i w przypadku braku przykładów pokazać tłumaczenie zwrotne jako zwykły tekst. Należy zauważyć, że rzeczywista liczba przykładów zwracanych przez wywołanie do [przykładów odnośnika](./v3-0-dictionary-examples.md) może być mniejsza niż `numExamples` , ponieważ na bieżąco można zastosować dodatkowe filtrowanie, aby usunąć przykłady "złe".
        
        * `frequencyCount`: Liczba całkowita reprezentująca częstotliwość tej pary tłumaczeń w danych. Głównym celem tego pola jest zapewnienie interfejsu użytkownika przy użyciu metody do sortowania wstecz-tłumaczenia, tak aby najczęstsze terminy były pierwsze.

    > [!NOTE]
    > Jeśli wyszukiwany okres nie istnieje w słowniku, odpowiedź jest 200 (OK), ale `translations` Lista jest pusta.

## <a name="examples"></a>Przykłady

Ten przykład pokazuje, jak wyszukiwać alternatywne tłumaczenia w hiszpańskim okresie w języku angielskim `fly` .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

Treść odpowiedzi (skróci do przejrzystości) to:

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

Ten przykład pokazuje, co się stanie, gdy wyszukiwany termin nie istnieje dla poprawnej pary słownika.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Ponieważ termin nie został znaleziony w słowniku, treść odpowiedzi zawiera pustą `translations` listę.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```