---
title: Wyodrębnianie tekstu z obrazów
titleSuffix: Azure Cognitive Search
description: Przetwarzaj i Wyodrębniaj tekst oraz inne informacje z obrazów w potokach Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e77bbd6e82d0d4a48b72e13e60b60608f2d7674
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419595"
---
# <a name="how-to-process-and-extract-information-from-images-in-ai-enrichment-scenarios"></a>Jak przetwarzać i wyodrębniać informacje z obrazów w scenariuszach wzbogacania AI

Usługa Azure Wyszukiwanie poznawcze oferuje kilka możliwości pracy z obrazami i plikami obrazów. Podczas łamania dokumentu można użyć parametru *imageAction* , aby wyodrębnić tekst ze zdjęć lub zdjęć zawierających tekst alfanumeryczny, na przykład słowo "Stop" w znaku stopu. Inne scenariusze obejmują generowanie tekstowej reprezentacji obrazu, na przykład "Dandelion" dla Zdjęcia Dandelion lub koloru "żółty". Możesz również wyodrębnić metadane dotyczące obrazu, takie jak jego rozmiar.

W tym artykule omówiono przetwarzanie obrazów bardziej szczegółowo i przedstawiono wskazówki dotyczące pracy z obrazami w potoku wzbogacenia AI.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Pobierz znormalizowane obrazy

W ramach łamania dokumentów istnieje nowy zestaw parametrów konfiguracji indeksatora do obsługi plików obrazów lub obrazów osadzonych w plikach. Te parametry służą do normalizacji obrazów do dalszej obróbki podrzędnej. Normalizacja obrazów sprawia, że są one bardziej jednorodne. Rozmiar dużych obrazów jest zmieniany na maksymalną wysokość i szerokość, aby można było ich używać. W przypadku obrazów zapewniających zadawanie metadanych w orientacji obrót obrazu jest dostosowywany do ładowania pionowego. Korekty metadanych są przechwytywane w typie złożonym utworzonym dla każdego obrazu. 

Nie można wyłączyć normalizacji obrazu. Umiejętności, które iterą nad obrazami, oczekują znormalizowane obrazy. Włączenie normalizacji obrazu na indeksatorze wymaga dołączenia zestawu umiejętności do tego indeksatora.

| Parametr konfiguracji | Opis |
|--------------------|-------------|
| imageAction   | Ustaw wartość "Brak", jeśli nie ma żadnej akcji, gdy zostaną napotkane obrazy osadzone lub pliki obrazów. <br/>Ustaw wartość "generateNormalizedImages", aby generować tablicę znormalizowanych obrazów w ramach łamania dokumentów.<br/>Ustaw wartość "generateNormalizedImagePerPage", aby wygenerować tablicę znormalizowanych obrazów, gdzie w przypadku plików PDF w źródle danych każda strona jest renderowana w jednym obrazie wyjściowym.  Funkcja jest taka sama jak "generateNormalizedImages" dla typów plików innych niż PDF.<br/>Dla każdej opcji, która nie jest "none", obrazy zostaną uwidocznione w polu *normalized_images* . <br/>Wartość domyślna to "Brak". Ta konfiguracja jest odnosi się tylko do źródeł danych obiektów blob, gdy wartość "dataToExtract" jest ustawiona na "contentAndMetadata". <br/>Co najwyżej 1000 obrazów zostanie wyodrębnionych z danego dokumentu. Jeśli w dokumencie znajduje się więcej niż 1000 obrazów, pierwsze 1000 zostanie wyodrębnione i zostanie wygenerowane ostrzeżenie. |
|  normalizedImageMaxWidth | Maksymalna szerokość (w pikselach) dla wygenerowanych znormalizowanych obrazów. Wartość domyślna to 2000. Maksymalna dozwolona wartość to 10000. | 
|  normalizedImageMaxHeight | Maksymalna wysokość (w pikselach) dla wygenerowanych znormalizowanych obrazów. Wartość domyślna to 2000. Maksymalna dozwolona wartość to 10000.|

> [!NOTE]
> Jeśli właściwość *imageAction* jest ustawiona na wartość inną niż "none", nie będzie można ustawić właściwości *przeanalizmode* na wartość inną niż "default".  W konfiguracji indeksatora można ustawić tylko jedną z tych dwóch właściwości na wartość inną niż domyślna.

Ustaw parametr **analizymode** na `json` (Aby indeksować każdy obiekt BLOB jako pojedynczy dokument) lub `jsonArray` (jeśli obiekty blob zawierają tablice JSON, a każdy element tablicy ma być traktowany jako oddzielny dokument).

Domyślnie 2000 pikseli dla znormalizowanych obrazów Maksymalna szerokość i wysokość opiera się na maksymalnym rozmiarze obsługiwanym przez [umiejętność OCR](cognitive-search-skill-ocr.md) i [umiejętności analizy obrazu](cognitive-search-skill-image-analysis.md). [Umiejętność OCR](cognitive-search-skill-ocr.md) obsługuje maksymalną szerokość i wysokość 4200 dla języków innych niż angielski i 10000 w języku angielskim.  W przypadku zwiększenia maksymalnych limitów przetwarzanie może zakończyć się niepowodzeniem w przypadku większych obrazów, w zależności od definicji zestawu umiejętności i języka dokumentów. 

ImageAction można określić w [definicji indeksatora](/rest/api/searchservice/create-indexer) w następujący sposób:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

Gdy *imageAction* jest ustawiona na wartość inną niż "none", nowe pole *normalized_images* będzie zawierać tablicę obrazów. Każdy obraz jest typu złożonego, który ma następujące elementy członkowskie:

| Element członkowski obrazu       | Opis                             |
|--------------------|-----------------------------------------|
| dane               | Zakodowany ciąg BASE64 standardowego obrazu w formacie JPEG.   |
| szerokość              | Szerokość znormalizowanego obrazu w pikselach. |
| wysokość             | Wysokość znormalizowanego obrazu (w pikselach). |
| originalWidth      | Oryginalna Szerokość obrazu przed normalizacją. |
| originalHeight      | Oryginalna wysokość obrazu przed normalizacją. |
| rotationFromOriginal |  Obrót w prawo w stopniach, które wystąpiły w celu utworzenia znormalizowanego obrazu. Wartość z przedziału od 0 stopni do 360 stopni. Ten krok odczytuje metadane z obrazu wygenerowanego przez aparat lub skaner. Zwykle jest to wielokrotność 90 stopni. |
| contentOffset | Przesunięcie znaku w polu zawartości, z którego został wyodrębniony obraz. To pole jest stosowane tylko w przypadku plików z osadzonymi obrazami. |
| pageNumber | Jeśli obraz został wyodrębniony lub renderowany z pliku PDF, to pole zawiera numer strony w pliku PDF, który został wyodrębniony lub wyrenderowany, rozpoczynając od 1.  Jeśli obraz nie został utworzony z pliku PDF, to pole będzie miało wartość 0.  |

 Przykładowa wartość *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>Umiejętność związana z obrazami

Istnieją dwie wbudowane umiejętności poznawcze, które pobierają obrazy jako dane wejściowe: [OCR](cognitive-search-skill-ocr.md) i [Analiza obrazu](cognitive-search-skill-image-analysis.md). 

Obecnie te umiejętności działają tylko z obrazami wygenerowanymi przez krok krakinging dokumentu. W związku z tym jedynymi obsługiwanymi danymi wejściowymi są `"/document/normalized_images"` .

### <a name="image-analysis-skill"></a>Umiejętność analizy obrazów

[Umiejętność analizy obrazów](cognitive-search-skill-image-analysis.md) wyodrębnia bogaty zestaw funkcji wizualnych opartych na zawartości obrazu. Na przykład można wygenerować podpis na podstawie obrazu, generować Tagi lub identyfikować osobistości i punkty orientacyjne.

### <a name="ocr-skill"></a>Umiejętność OCR

[Umiejętność OCR](cognitive-search-skill-ocr.md) wyodrębnia tekst z plików obrazów, takich jak JPGs, PNGs i mapy bitowe. Można wyodrębnić tekst oraz informacje o układzie. Informacje o układzie zawierają granice pól dla każdego identyfikowanego ciągu.

## <a name="embedded-image-scenario"></a>Scenariusz obrazu osadzonego

Typowy scenariusz polega na utworzeniu pojedynczego ciągu zawierającego całą zawartość pliku, zarówno tekstowej, jak i tekstu pierwotnego, wykonując następujące czynności:  

1. [Wyodrębnij normalized_images](#get-normalized-images)
1. Uruchamiaj umiejętność OCR przy użyciu `"/document/normalized_images"` jako danych wejściowych
1. Scal tekstową reprezentację tych obrazów z nieprzetworzonym tekstem wyodrębnionym z pliku. Możesz użyć umiejętności [scalania tekstu](cognitive-search-skill-textmerger.md) , aby skonsolidować oba fragmenty tekstu w jeden duży ciąg.

Poniższy przykład zestawu umiejętności tworzy pole *merged_text* zawierające tekstową zawartość dokumentu. Zawiera również tekst OCRed z każdego osadzonego obrazu. 

#### <a name="request-body-syntax"></a>Składnia treści żądania
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```

Teraz, gdy masz merged_text pole, możesz je zmapować jako pole z możliwością wyszukiwania w definicji indeksatora. Cała zawartość plików, w tym tekst obrazów, będzie można przeszukiwać.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Wizualizowanie pól powiązanych tekstu

Inny typowy scenariusz przedstawia wizualizację informacji o układzie wyników wyszukiwania. Na przykład możesz chcieć zaznaczyć, gdzie fragment tekstu został znaleziony w obrazie jako część wyników wyszukiwania.

Ponieważ krok OCR jest wykonywany na znormalizowanych obrazach, współrzędne układu znajdują się w znormalizowanym miejscu obrazu. Podczas wyświetlania znormalizowanego obrazu, obecność współrzędnych nie jest ogólnie problemem, ale w niektórych sytuacjach może być konieczne wyświetlenie oryginalnego obrazu. W takim przypadku należy przekonwertować każdy punkt współrzędnej w układzie na oryginalny system współrzędnych obrazu. 

Jako pomocnik, jeśli konieczne jest przekształcenie znormalizowanych współrzędnych do oryginalnej przestrzeni współrzędnych, można użyć następującego algorytmu:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```
## <a name="passing-images-to-custom-skills"></a>Przekazywanie obrazów do umiejętności niestandardowych

W scenariuszach, w których do pracy z obrazami wymagana jest niestandardowa umiejętność, można przekazać obrazy do niestandardowej umiejętności i zwrócić tekst lub obrazy. Przykład przetwarzania obrazu w języku [Python](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing) pokazuje przepływ pracy. Następujący zestawu umiejętności pochodzi z przykładu.

Poniższe zestawu umiejętności pobiera znormalizowany obraz (uzyskany podczas łamania dokumentów) i wyprowadza wycinków obrazu.

#### <a name="sample-skillset"></a>Przykład zestawu umiejętności
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
          "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
          "name": "ImageSkill",
          "description": "Segment Images",
          "context": "/document/normalized_images/*",
          "uri": "https://your.custom.skill.url",
          "httpMethod": "POST",
          "timeout": "PT30S",
          "batchSize": 100,
          "degreeOfParallelism": 1,
          "inputs": [
            {
              "name": "image",
              "source": "/document/normalized_images/*"
            }
          ],
          "outputs": [
            {
              "name": "slices",
              "targetName": "slices"
            }
          ],
          "httpHeaders": {}
        }
  ]
}
```

#### <a name="custom-skill"></a>Niestandardowa umiejętność

Sama niestandardowa umiejętność jest zewnętrzna zestawu umiejętności. W tym przypadku jest to kod w języku Python, który najpierw tworzy pętlę w szczegółowej części rekordu żądania w niestandardowym formacie umiejętności, a następnie konwertuje ciąg szyfrowany algorytmem Base64 na obraz.

```python
# deserialize the request, for each item in the batch
for value in values:
  data = value['data']
  base64String = data["image"]["data"]
  base64Bytes = base64String.encode('utf-8')
  inputBytes = base64.b64decode(base64Bytes)
  # Use numpy to convert the string to an image
  jpg_as_np = np.frombuffer(inputBytes, dtype=np.uint8)
  # you now have an image to work with
```
Podobnie, aby zwrócić obraz, zwróć zakodowany ciąg Base64 w obrębie obiektu JSON z `$type` właściwością `file` .

```python
def base64EncodeImage(image):
    is_success, im_buf_arr = cv2.imencode(".jpg", image)
    byte_im = im_buf_arr.tobytes()
    base64Bytes = base64.b64encode(byte_im)
    base64String = base64Bytes.decode('utf-8')
    return base64String

 base64String = base64EncodeImage(jpg_as_np)
 result = { 
  "$type": "file", 
  "data": base64String 
}
```

## <a name="see-also"></a>Zobacz też
+ [Utwórz indeksator (REST)](/rest/api/searchservice/create-indexer)
+ [Umiejętność analizy obrazów](cognitive-search-skill-image-analysis.md)
+ [Umiejętność OCR](cognitive-search-skill-ocr.md)
+ [Umiejętność scalania tekstu](cognitive-search-skill-textmerger.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Jak zmapować wzbogacone pola](cognitive-search-output-field-mapping.md)
+ [Jak przekazać obrazy do umiejętności niestandardowych](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing)
