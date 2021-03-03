---
title: Warunkowa umiejętność
titleSuffix: Azure Cognitive Search
description: Umiejętności warunkowe w usłudze Azure Wyszukiwanie poznawcze umożliwiają filtrowanie, tworzenie ustawień domyślnych i scalanie wartości w definicji zestawu umiejętności.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f47ca56fa1b40422edeb0d4e11c24be6f60e49e5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666358"
---
# <a name="conditional-cognitive-skill"></a>Warunkowa umiejętność

Umiejętność **warunkowa** umożliwia korzystanie z scenariuszy wyszukiwanie poznawcze platformy Azure, które wymagają operacji logicznej do określenia danych, które mają zostać przypisane do danych wyjściowych. Te scenariusze obejmują filtrowanie, przypisywanie wartości domyślnej i scalanie danych na podstawie warunku.

Poniższa pseudokodzie demonstruje, jakie są działania warunkowe:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> To umiejętność nie jest powiązana z interfejsem API usługi Azure Cognitive Services i nie jest naliczana opłata za korzystanie z niego. Nadal jednak należy [dołączyć zasób Cognitive Services](cognitive-search-attach-cognitive-services.md) , aby zastąpić opcję zasobu "Free", która ogranicza użytkownika do niewielkiej liczby wzbogacań dziennie.

## <a name="odatatype"></a>@odata.type  
Microsoft. umiejętności. util. ConditionalSkill


## <a name="evaluated-fields"></a>Ocenione pola

Ta umiejętność jest specjalna, ponieważ jej dane wejściowe są oceniane.

Następujące elementy są prawidłowymi wartościami wyrażenia:

-   Ścieżki adnotacji (ścieżki w wyrażeniach muszą być rozdzielone znakami "$ (" i ")")
 <br/>
    Przykłady:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literały (ciągi, cyfry, prawda, FAŁSZ, null) <br/>
    Przykłady:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Wyrażenia wykorzystujące operatory porównania (= =,! =, >=, >, <=, <) <br/>
    Przykłady:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Wyrażenia wykorzystujące operatory logiczne (&&, | |,!, ^) <br/>
    Przykłady:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Wyrażenia wykorzystujące Operatory liczbowe (+,-, \* ,/,%) <br/>
    Przykłady: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Ponieważ umiejętność warunkowa obsługuje szacowanie, można jej używać w scenariuszach mniejszych transformacji. Na przykład zobacz [Definicja umiejętności 4](#transformation-example).

## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji
W danych wejściowych jest rozróżniana wielkość liter.

| Dane wejściowe   | Opis |
|-------------|-------------|
| rozgrzewa   | To dane wejściowe to [oceniane pole](#evaluated-fields) , które reprezentuje warunek do oceny. Ten warunek powinien być wartością logiczną (*true* lub *false*).   <br/>  Przykłady: <br/> "= true" <br/> "= $ (/Document/Language) = =" fr "" <br/> "= $ (/Document/Pages/ \* /Language) = = $ (/Document/expectedLanguage)" <br/> |
| whenTrue    | To dane wejściowe to [oceniane pole](#evaluated-fields) , które reprezentuje wartość zwracaną, jeśli warunek ma *wartość true*. Stałe ciągi powinny być zwracane w pojedynczym cudzysłowie ("i"). <br/>Przykładowe wartości: <br/> "=" kontrakt ""<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/Entities/ \* )" <br/> |
| whenFalse   | To dane wejściowe to [oceniane pole](#evaluated-fields) , które reprezentuje wartość zwracaną, jeśli warunek zostanie obliczony na *wartość false*. <br/>Przykładowe wartości: <br/> "=" kontrakt ""<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/Entities/ \* )" <br/>

## <a name="skill-outputs"></a>Wyniki umiejętności
Istnieje jedno wyjście o nazwie "output". Zwraca wartość *whenFalse* , jeśli warunek ma wartość false lub *whenTrue* , jeśli warunek ma wartość true.

## <a name="examples"></a>Przykłady

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Przykładowa definicja kwalifikacji 1: Filtruj dokumenty, aby zwracały tylko dokumenty francuski

Następujące dane wyjściowe zwracają tablicę zdań ("/document/frenchSentences"), jeśli językiem dokumentu jest francuski. Jeśli język nie jest w języku francuskim, wartość jest równa *null*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Jeśli wartość "/document/frenchSentences" jest używana jako *kontekst* innej umiejętności, ta umiejętność jest uruchamiana tylko wtedy, gdy opcja "/Document/frenchSentences" nie ma *wartości null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Przykładowa definicja umiejętności 2: Ustaw wartość domyślną dla wartości, która nie istnieje

Poniższe dane wyjściowe tworzą adnotację ("/document/languageWithDefault") ustawioną na język dokumentu lub do "es", jeśli język nie jest ustawiony.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Przykładowa definicja umiejętności 3: scalanie wartości z dwóch pól w jeden

W tym przykładzie niektóre zdania mają właściwość *frenchSentiment* . Zawsze, gdy właściwość *frenchSentiment* ma wartość null, chcemy użyć wartości *englishSentiment* . Dane wyjściowe są przypisywane do elementu członkowskiego o nazwie *tonacji* ("/Document/sentences/*/Sentiment").

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Przykład transformacji
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Przykładowa definicja umiejętności 4: transformacja danych dla pojedynczego pola

W tym przykładzie otrzymamy *tonacji* z zakresu od 0 do 1. Chcemy przekształcić ten element, aby znajdował się w zakresie od-1 do 1. W celu przeprowadzenia tej drobnej transformacji możemy użyć umiejętności warunkowej.

W tym przykładzie nie używamy warunkowego aspektu umiejętności, ponieważ warunek ma zawsze *wartość true*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Uwagi specjalne
Niektóre parametry są oceniane, dlatego należy zachować szczególną ostrożność, aby postępować zgodnie z udokumentowanym wzorcem. Wyrażenia muszą zaczynać się od znaku równości. Ścieżka musi być rozdzielonych znakami "$ (" i ")". Upewnij się, że umieszczasz ciągi w pojedynczym cudzysłowie. Pomaga to w ewaluatora rozróżnić ciągi i rzeczywiste ścieżki i operatory. Upewnij się również, że umieszczono odstęp wokół operatorów (na przykład "*" w ścieżce oznacza coś innego niż mnożenie).


## <a name="next-steps"></a>Następne kroki

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
