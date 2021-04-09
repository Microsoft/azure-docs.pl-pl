---
title: Identyfikatory — aparat rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Poznaj koncepcje dotyczące wyodrębniania danych z dokumentów tożsamości za pomocą interfejsu API wstępnie skompilowanych identyfikatorów aparatu rozpoznawania formularzy.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 01a73e7940f88a3eb6e040f26d255448294cab18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467842"
---
# <a name="form-recognizer-prebuilt-identification-card-id-model"></a>Model wbudowanej karty identyfikacyjnej aparatu rozpoznawania formularzy (identyfikator)

Aparat rozpoznawania formularzy platformy Azure umożliwia analizowanie i wyodrębnianie informacji z kart identyfikacyjnych instytucji rządowych (identyfikatorów) przy użyciu wbudowanego modelu identyfikatorów. Łączymy nasze zaawansowane możliwości [rozpoznawania znaków optycznych (OCR)](../computer-vision/concept-recognizing-text.md) z funkcjami rozpoznawania identyfikatorów, aby wyodrębnić najważniejsze informacje z naszych paszportów ogólnoświatowych i licencji na sterownik w Stanach Zjednoczonych (wszystkie 50 stanów i Waszyngtonie). Interfejs API identyfikatorów wyodrębnia najważniejsze informacje z tych dokumentów tożsamości, takie jak imię, nazwisko, Data urodzenia, numer dokumentu i inne. Ten interfejs API jest dostępny w formie aparatu rozpoznawania w wersji testowej 2.0 2.1 jako usługi w chmurze i jako kontener lokalny.

## <a name="what-does-the-id-service-do"></a>Do czego służy usługa identyfikatorów? 

Usługa prekompilowanych identyfikatorów wyodrębnia wartości kluczy z paszportów na całym świecie i z licencji w Stanach Zjednoczonych i zwraca je w zorganizowanej strukturalnej odpowiedzi JSON. 

![Licencja przykładowego sterownika](./media/id-example-drivers-license.JPG)

![Przykład paszportu](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Wyodrębnione pola

|Nazwa| Typ | Opis | Wartość | 
|:-----|:----|:----|:----|
|  Kraj | country | Kod kraju zgodny ze standardem ISO 3166 | PONIŻEJ | 
|  DateOfBirth | data | DOB w formacie RRRR-MM-DD | "1980-01-01" | 
|  DateOfExpiration | data | Data wygaśnięcia w formacie RRRR-MM-DD | "2019-05-05" |  
|  DocumentNumber | ciąg | Odpowiedni numer paszportu, numer licencji sterownika itp. | "340020013" |  
|  FirstName (Imię) | ciąg | Wyodrębniono podaną nazwę i drugie inicjały, jeśli ma zastosowanie | Jennifer | 
|  LastName (Nazwisko) | ciąg | Wyodrębnione nazwisko | "BROOKS" |   
|  Obywatelstwo | country | Kod kraju zgodny ze standardem ISO 3166 | PONIŻEJ |
|  Biciu | płeć | Możliwe wyodrębnione wartości to "M", "F" i "X" | „F” | 
|  MachineReadableZone | object | Wyodrębniono MRZ paszportu, w tym dwa wiersze z 44 znaków | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  DocumentType | ciąg | Typ dokumentu, na przykład paszport, licencja sterownika | usłudze |  
|  Adres | ciąg | Wyodrębniony adres (tylko licencja sterownika) | "123 ULICA ADRESU MIASTA 99999-1234"|
|  Region (Region) | ciąg | Wyodrębniony region, stan, Prowincja itp. (tylko licencja sterownika) | Węgier | 

### <a name="additional-features"></a>Dodatkowe funkcje

Interfejs API identyfikatorów zwraca również następujące informacje:

* Poziom ufności pól (każde pole zwraca skojarzoną wartość zaufania)
* Tekst nieprzetworzony OCR (wyodrębniany tekst wyjściowy tekstu dla całego potwierdzenia)
* Pole ograniczenia każdego wyodrębnionego pola w licencjach na sterownik w Stanach Zjednoczonych
* Pole ograniczenia dla strefy do odczytu maszynowego (MRZ) w paszportach

  > [!NOTE]
  > Wstępnie skompilowane identyfikatory nie wykrywają autentyczności identyfikatorów
  >
  > Wstępnie skompilowane identyfikatory aparatu rozpoznawania formularzy wyodrębniają dane kluczowe z identyfikatora danych. Nie wykrywa jednak ważności ani autentyczności oryginalnego dokumentu tożsamości. 

## <a name="try-it-out"></a>Czas to wypróbować

Aby wypróbować usługę identyfikatorów aparatów rozpoznawania, przejdź do narzędzia przykładowego interfejsu użytkownika w trybie online:

> [!div class="nextstepaction"]
> [Wypróbuj wbudowane modele](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Wymagania wejściowe

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>Obsługiwane typy identyfikatorów  

* **Wstępnie skompilowane identyfikatory v 2.1-Preview. 3** Wyodrębnia wartości kluczy z paszportów ogólnoświatowych i licencji sterownika w Stanach Zjednoczonych. 

  > [!NOTE]
  > Obsługa typu identyfikatora 
  >
  > Obecnie obsługiwane typy identyfikatorów obejmują licencje na całym świecie i w Stanach Zjednoczonych. Aktywnie dążą do rozwinięcia obsługi identyfikatorów na inne dokumenty tożsamości na całym świecie.

## <a name="post-analyze-id-document"></a>Dokument identyfikatora analizy

Operacja [analizowania identyfikatora](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) przyjmuje obraz lub plik PDF identyfikatora jako dane wejściowe i wyodrębnia wartości zainteresowania. Wywołanie zwraca pole nagłówka odpowiedzi o nazwie `Operation-Location` . `Operation-Location`Wartość jest adresem URL, który zawiera identyfikator wynik do użycia w następnym kroku.

|Nagłówek odpowiedzi| Adres URL wyniku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>Pobierz wynik dokumentu z identyfikatorem analizy

<!---
Need to update this with updated APIM links when available
-->

Drugim krokiem jest wywołanie operacji [**Get Analizuj wyniki idDocument**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) . Ta operacja przyjmuje jako dane wejściowe Identyfikator wyniku, który został utworzony przez operację analizowania identyfikatora. Zwraca odpowiedź JSON, która zawiera pole **stanu** z następującymi możliwymi wartościami. Tę operację można wywołać iteracyjnie, dopóki nie zwróci wartości z wartością **sukces** . Użyj interwału od 3 do 5 sekund, aby uniknąć przekroczenia liczby żądań na sekundę (RPS pliku).

|Pole| Typ | Możliwe wartości |
|:-----|:----:|:----|
|status | ciąg | notStarted: operacja analizy nie została rozpoczęta. |
| |  | Uruchamianie: operacja analizy jest w toku. |
| |  | Niepowodzenie: operacja analizy zakończyła się niepowodzeniem. |
| |  | powodzenie: operacja analizy zakończyła się pomyślnie. |

Gdy wartość w polu **stan** zostanie **zakończona pomyślnie** , odpowiedź JSON będzie zawierać opis potwierdzenia i wyniki rozpoznawania tekstu. Wyniki identyfikatorów są zorganizowane jako słownik nazwanych wartości pól, gdzie każda wartość zawiera wyodrębniony tekst, znormalizowaną wartość, pole ograniczenia, pewność i odpowiednie elementy programu Word. Wynik rozpoznawania tekstu jest zorganizowany jako hierarchia wierszy i słów, z tekstem, obwiednią i informacjami o ufności.

![Przykładowe wyniki odbioru](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Przykładowe dane wyjściowe JSON

Zobacz następujący przykład pomyślnej odpowiedzi JSON: `readResults` węzeł zawiera wszystkie rozpoznane teksty. Tekst jest zorganizowany według strony, następnie według wiersza, a następnie poszczególnych słów. `documentResults`Węzeł zawiera wartości identyfikatora wykryte przez model. Ten węzeł również zawiera użyteczne pary klucz/wartość, takie jak imię, nazwisko, numer dokumentu i inne.

```json
{ 
   "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
      }
    ],
    
     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Country": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "gender",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```


## <a name="next-steps"></a>Następne kroki

- Wypróbuj własne identyfikatory i przykłady w [przykładowym interfejsie użytkownika aparatu rozpoznawania formularzy](https://fott-preview.azurewebsites.net/).
- Ukończ [Przewodnik Szybki Start dla aparatu rozpoznawania](quickstarts/client-library.md) , aby rozpocząć pisanie aplikacji do przetwarzania identyfikatorów z aparatem rozpoznawania formularzy w wybranym języku programistycznym.

## <a name="see-also"></a>Zobacz też

* [**Co to jest rozpoznawanie formularzy?**](./overview.md)
* [**Dokumentacja interfejsu API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
