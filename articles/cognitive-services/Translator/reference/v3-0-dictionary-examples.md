---
title: Przykładowa Metoda słownika translatora
titleSuffix: Azure Cognitive Services
description: Przykładowa Metoda słownika translatora zawiera przykłady pokazujące, jak terminy w słowniku są używane w kontekście.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: lajanuar
ms.openlocfilehash: e7f0e106c1ca154dcd54990395430b3e0f6c536f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895513"
---
# <a name="translator-30-dictionary-examples"></a>Translator 3,0: przykłady słownika

Zawiera przykłady pokazujące, jak terminy w słowniku są używane w kontekście. Ta operacja jest używana wspólnie z funkcją [Lookup dictionary](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` żądanie do:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametry żądania

Parametry żądania przesłane na ciągu zapytania są następujące:

| Parametr zapytania | Opis |
| --------- | ----------- |
| api-version <img width=200/> | **Wymagany parametr**.<br/>Wersja interfejsu API żądana przez klienta. Wartość musi być `3.0` . |
| wniosek | **Wymagany parametr**.<br/>Określa język tekstu wejściowego. Język źródłowy musi być jednym z [obsługiwanych języków](./v3-0-languages.md) zawartych w `dictionary` zakresie. |
| na wartość | **Wymagany parametr**.<br/>Określa język tekstu wyjściowego. Język docelowy musi być jednym z [obsługiwanych języków](./v3-0-languages.md) zawartych w `dictionary` zakresie.  | 

Nagłówki żądań obejmują:

| Nagłówki  | Opis |
| ------ | ----------- |
| Nagłówki uwierzytelniania <img width=200/>  | **Wymagany nagłówek żądania**.<br/>Zobacz <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">dostępne opcje uwierzytelniania</a>. |
| Content-Type | **Wymagany nagłówek żądania**.<br/>Określa typ zawartości ładunku. Możliwe wartości to: `application/json` . |
| Długość zawartości   | **Wymagany nagłówek żądania**.<br/>Długość treści żądania. |
| X-ClientTraceId   | **Opcjonalne**.<br/>Wygenerowany przez klienta identyfikator GUID służący do unikatowej identyfikacji żądania. Możesz pominąć ten nagłówek, jeśli dołączysz identyfikator śledzenia w ciągu zapytania przy użyciu parametru zapytania o nazwie `ClientTraceId` . |

## <a name="request-body"></a>Treść żądania

Treść żądania jest tablicą JSON. Każdy element tablicy jest obiektem JSON o następujących właściwościach:

  * `Text`: Ciąg określający termin do wyszukania. Powinna to być wartość `normalizedText` pola z retłumaczenia poprzedniego żądania [wyszukiwania słownika](./v3-0-dictionary-lookup.md) . Może być również wartością `normalizedSource` pola.

  * `Translation`: Ciąg określający przetłumaczony tekst, który został wcześniej zwrócony przez operację [wyszukiwania słownika](./v3-0-dictionary-lookup.md) . Powinna to być wartość z `normalizedTarget` pola na `translations` liście odpowiedzi na [Wyszukiwanie słownika](./v3-0-dictionary-lookup.md) . Usługa zwróci przykłady dla określonej pary wyrazów dla docelowej lokalizacji źródłowej.

Przykład:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Obowiązują następujące ograniczenia:

* Tablica może zawierać co najwyżej 10 elementów.
* Wartość tekstowa elementu tablicy nie może być dłuższa niż 100 znaków, w tym spacje.

## <a name="response-body"></a>Treść odpowiedzi

Pomyślna odpowiedź to tablica JSON z jednym wynikiem dla każdego ciągu w tablicy wejściowej. Obiekt wynikowy zawiera następujące właściwości:

  * `normalizedSource`: Ciąg przedstawiający znormalizowaną formę okresu źródłowego. Ogólnie rzecz biorąc powinna być taka sama jak wartość `Text` pola w indeksie zgodnej listy w treści żądania.
    
  * `normalizedTarget`: Ciąg dający znormalizowaną postać terminu docelowego. Ogólnie rzecz biorąc powinna być taka sama jak wartość `Translation` pola w indeksie zgodnej listy w treści żądania.
  
  * `examples`: Lista przykładów dla pary (termin źródłowy, termin docelowy). Każdy element listy jest obiektem o następujących właściwościach:

    * `sourcePrefix`: Ciąg, który ma zostać połączony _przed_ wartością `sourceTerm` w celu utworzenia kompletnego przykładu. Nie należy dodawać znaku spacji, ponieważ jest już tam, gdy powinien być. Ta wartość może być pustym ciągiem.

    * `sourceTerm`: Ciąg równy rzeczywistemu wyszukiwanemu okresowi. Ciąg zostanie dodany z `sourcePrefix` i `sourceSuffix` w celu utworzenia kompletnego przykładu. Jego wartość jest oddzielona, aby można było ją oznaczyć w interfejsie użytkownika, np. przez pogrubienie.

    * `sourceSuffix`: Ciąg, który ma zostać połączony _po_ wartości `sourceTerm` w celu utworzenia kompletnego przykładu. Nie należy dodawać znaku spacji, ponieważ jest już tam, gdy powinien być. Ta wartość może być pustym ciągiem.

    * `targetPrefix`: Ciąg podobny do `sourcePrefix` dla elementu docelowego.

    * `targetTerm`: Ciąg podobny do `sourceTerm` dla elementu docelowego.

    * `targetSuffix`: Ciąg podobny do `sourceSuffix` dla elementu docelowego.

    > [!NOTE]
    > Jeśli w słowniku nie ma przykładów, odpowiedź jest 200 (OK), ale `examples` Lista jest pusta.

## <a name="examples"></a>Przykłady

Ten przykład pokazuje, jak wyszukiwać przykłady dla pary składające się z okresu angielskiego `fly` i jego tłumaczenia hiszpańskiego `volar` .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

Treść odpowiedzi (skróci do przejrzystości) to:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```