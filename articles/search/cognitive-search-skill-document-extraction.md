---
title: Umiejętność wyodrębniania dokumentu (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Wyodrębnia zawartość z pliku w ramach potoku wzbogacania. Ta umiejętność jest obecnie dostępna w publicznej wersji zapoznawczej.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: chalton
ms.openlocfilehash: 3c77da8252fb3dcb2672a50ec75f676e18dd31da
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652334"
---
# <a name="document-extraction-cognitive-skill"></a>Umiejętność wyodrębniania dokumentów

> [!IMPORTANT] 
> Ta umiejętność jest obecnie dostępna w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Obecnie nie ma obsługi portalu lub zestawu SDK platformy .NET.

Umiejętność **wyodrębniania dokumentu** wyodrębnia zawartość z pliku w ramach potoku wzbogacania. Dzięki temu można wykorzystać krok wyodrębniania dokumentu, który zwykle występuje przed wykonaniem zestawu umiejętności z plikami, które mogą być generowane przez inne umiejętności.

> [!NOTE]
> Podczas rozszerzania zakresu przez zwiększenie częstotliwości przetwarzania, Dodawanie większej liczby dokumentów lub Dodawanie algorytmów AI, należy [dołączyć Cognitive Services rozliczanego zasobu](cognitive-search-attach-cognitive-services.md). Opłaty naliczane podczas wywoływania interfejsów API w Cognitive Services oraz do wyodrębniania obrazów w ramach etapu krakingania dokumentu w indeksie. Nie są naliczane opłaty za Wyodrębnianie tekstu z dokumentów.
>
> Do wykonania wbudowanych umiejętności są naliczane opłaty za istniejące [Cognitive Services cena płatność zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem. Cennik wyodrębniania obrazów został opisany na [stronie cennika](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Parametry umiejętności

W nazwach parametrów jest rozróżniana wielkość liter.

| Dane wejściowe | Dozwolone wartości | Opis |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Ustaw na `default` na potrzeby wyodrębniania dokumentów z plików, które nie są czystym tekstem ani JSON. Ustaw, aby `text` zwiększyć wydajność na zwykłych plikach tekstowych. Ustaw, aby `json` wyodrębnić zawartość strukturalną z plików JSON. Jeśli `parsingMode` wartość nie jest jawnie zdefiniowana, zostanie ustawiona na wartość `default` . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Ustaw, aby `contentAndMetadata` wyodrębnić wszystkie metadane i zawartość tekstową z każdego pliku. Ustaw, aby `allMetadata` wyodrębnić tylko [metadane specyficzne dla typu zawartości](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (na przykład metadane unikatowe dla plików PNG). Jeśli `dataToExtract` wartość nie jest jawnie zdefiniowana, zostanie ustawiona na wartość `contentAndMetadata` . |
| `configuration` | Sprawdź poniżej. | Słownik parametrów opcjonalnych, który dostosowuje sposób wykonywania wyodrębniania dokumentu. Zapoznaj się z poniższą tabelą opisów obsługiwanych właściwości konfiguracji. |

| Parametr konfiguracji   | Dozwolone wartości | Opis |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Ustaw na `none` Ignorowanie osadzonych obrazów lub plików obrazów w zestawie danych. Jest to opcja domyślna. <br/>Aby przeprowadzić [analizę obrazów przy użyciu umiejętności poznawczych](cognitive-search-concept-image-scenarios.md), ustaw opcję na wartość `generateNormalizedImages` tak, aby umiejętność utworzyła tablicę znormalizowanych obrazów jako część pęknięcia dokumentu. Ta akcja wymaga, aby ustawiono wartość `parsingMode` `default` i `dataToExtract` został ustawiony na `contentAndMetadata` . Znormalizowany obraz odnosi się do dodatkowego przetwarzania, w wyniku którego dane wyjściowe są jednorodne, skalowane i obracane, aby podwyższyć poziom renderowania, gdy dołączysz obrazy do wyników wyszukiwania wizualnego (na przykład zdjęcia o tym samym rozmiarze w kontrolce wykresu, jak widać w [demonstracji JFK](https://github.com/Microsoft/AzureSearch_JFK_Files)). Te informacje są generowane dla każdego obrazu przy użyciu tej opcji.  <br/>Jeśli ustawisz `generateNormalizedImagePerPage` opcję, plik PDF będzie traktowany inaczej, w przeciwieństwie do wyodrębniania obrazów osadzonych, każda strona będzie renderowana jako obraz i znormalizowana.  Typy plików inne niż PDF będą traktowane tak samo, jak w przypadku `generateNormalizedImages` Ustawienia.
| `normalizedImageMaxWidth` | Dowolna liczba całkowita z zakresu od 50-10000 | Maksymalna szerokość (w pikselach) dla wygenerowanych znormalizowanych obrazów. Wartość domyślna to 2000. | 
| `normalizedImageMaxHeight` | Dowolna liczba całkowita z zakresu od 50-10000 | Maksymalna wysokość (w pikselach) dla wygenerowanych znormalizowanych obrazów. Wartość domyślna to 2000. |

> [!NOTE]
> Domyślnie 2000 pikseli dla znormalizowanych obrazów Maksymalna szerokość i wysokość opiera się na maksymalnym rozmiarze obsługiwanym przez [umiejętność OCR](cognitive-search-skill-ocr.md) i [umiejętności analizy obrazu](cognitive-search-skill-image-analysis.md). [Umiejętność OCR](cognitive-search-skill-ocr.md) obsługuje maksymalną szerokość i wysokość 4200 dla języków innych niż angielski i 10000 w języku angielskim.  W przypadku zwiększenia maksymalnych limitów przetwarzanie może zakończyć się niepowodzeniem w przypadku większych obrazów, w zależności od definicji zestawu umiejętności i języka dokumentów. 
## <a name="skill-inputs"></a>Dane wejściowe kwalifikacji

| Nazwa wejściowa     | Opis |
|--------------------|-------------|
| `file_data` | Plik, z którego ma zostać wyodrębniona zawartość. |

Dane wejściowe "file_data" muszą być obiektami zdefiniowanymi w następujący sposób:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Ten obiekt odwołania do pliku może być wygenerowany jeden z trzech sposobów:

 - Ustawianie `allowSkillsetToReadFileData` parametru w definicji indeksatora na wartość "true".  Spowoduje to utworzenie ścieżki `/document/file_data` , która jest obiektem reprezentującym oryginalne dane pliku pobrane ze źródła danych obiektu BLOB. Ten parametr ma zastosowanie tylko do danych w magazynie obiektów BLOB.

 - Ustawienie `imageAction` parametru w definicji indeksatora na wartość inną niż `none` .  Powoduje to utworzenie tablicy obrazów, które są zgodne z wymaganą Konwencją dla danych wejściowych do tej umiejętności, jeśli są one przenoszone indywidualnie (tj. `/document/normalized_images/*` ).

 - Posiadanie niestandardowej kwalifikacji zwraca obiekt JSON zdefiniowany dokładnie tak jak powyżej.  `$type`Parametr musi być ustawiony na wartość dokładnie `file` , a `data` parametr musi być podstawowym 64 zakodowanym bajtem danych tablicy zawartości pliku.

## <a name="skill-outputs"></a>Wyniki umiejętności

| Nazwa wyjściowa    | Opis |
|--------------|-------------|
| `content` | Zawartość tekstowa dokumentu. |
| `normalized_images`   | Gdy `imageAction` zostanie ustawiona wartość inne `none` , nowe pole *normalized_images* będzie zawierać tablicę obrazów. Zapoznaj [się z dokumentacją dotyczącą wyodrębniania obrazów](cognitive-search-concept-image-scenarios.md) , aby uzyskać szczegółowe informacje na temat formatu danych wyjściowych każdego obrazu. |

##  <a name="sample-definition"></a>Definicja Przykładowa

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>Przykładowe dane wejściowe

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Zobacz także

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Jak przetwarzać i wyodrębniać informacje z obrazów w scenariuszach wyszukiwania poznawczego](cognitive-search-concept-image-scenarios.md)