---
title: Identyfikatory — Rozpoznawanie formularzy
titleSuffix: Azure Cognitive Services
description: Poznaj pojęcia związane z wyodrębnianiem danych z dokumentów tożsamości za Rozpoznawanie formularzy wstępnie sbudowaną interfejsem API identyfikatorów.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: 00e51d2c9515191b6d127355f49eeed3000a46ed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514717"
---
# <a name="form-recognizer-prebuilt-identification-id-document-model"></a>Rozpoznawanie formularzy wstępnie utworzonego modelu dokumentu identyfikacji (ID)

Usługa Azure Rozpoznawanie formularzy może analizować i wyodrębniać informacje z dokumentów identyfikacyjnych wystawionych przez rząd przy użyciu wstępnie utworzonego modelu identyfikatorów. Łączy ona nasze zaawansowane funkcje optycznego rozpoznawania znaków [(OCR, Optical Character Recognition)](../computer-vision/overview-ocr.md) z możliwościami rozpoznawania identyfikatorów w celu wyodrębniania kluczowych informacji z paszportów na całym świecie i licencji sterowników USA (wszystkie 50 stanów i D.C.). Interfejs API identyfikatorów wyodrębnia kluczowe informacje z tych dokumentów tożsamości, takie jak imię, nazwisko, data urodzenia, numer dokumentu i inne. Ten interfejs API jest dostępny w Rozpoznawanie formularzy wersji zapoznawczej 2.1 jako usługa w chmurze i jako kontener lokalnie.

## <a name="what-does-the-id-service-do"></a>Co robi usługa identyfikatorów?

Wstępnie utworzone identyfikatory usługi wyodrębnia wartości kluczy z paszportów na całym świecie i licencji sterowników USA i zwraca je w zorganizowanej odpowiedzi JSON ze strukturą.

### <a name="drivers-license-example"></a>**Przykład prawa jazdy**

![Przykładowe prawo jazdy](./media/id-example-drivers-license.JPG)

### <a name="passport-example"></a>**Przykładowy paszport**

![Przykładowy paszport](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Wyodrębnione pola

|Nazwa| Typ | Opis | Wartość |
|:-----|:----|:----|:----|
|  Kraj | country | Kod kraju zgodny ze standardem ISO 3166 | "USA" |
|  DateOfBirth | data | DOB w formacie YYYY-MM-DD | "1980-01-01" |
|  DateOfExpiration | data | Data wygaśnięcia w formacie YYYY-MM-DD | "2019-05-05" |
|  Numer dokumentu | ciąg | Odpowiedni numer paszportu, numer prawa jazdy itp. | "340020013" |
|  FirstName (Imię) | ciąg | Wyodrębnione imię i środkowy początkowy, jeśli ma zastosowanie | "ZAJMIJ SIĘ TYM" |
|  LastName (Nazwisko) | ciąg | Wyodrębnione nazwisko | "BROOKS" |
|  Narodowość | country | Kod kraju zgodny ze standardem ISO 3166 | "USA" |
|  Seks | płeć | Możliwe wyodrębnione wartości to "M", "F" i "X" | „F” |
|  MachineReadableZone | object | Wyodrębniona część mrz usługi Passport, w tym dwa wiersze po 44 znaki | "P<USABROOKS<<OKS<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  Documenttype | ciąg | Typ dokumentu, na przykład Passport, Prawo jazdy | "passport" |
|  Adres | ciąg | Wyodrębniony adres (tylko prawo jazdy) | "123 STREET ADDRESS YOUR CITY WA 99999-1234"|
|  Region (Region) | ciąg | Wyodrębniony region, stan, prowincja itp. (tylko prawo jazdy) | "Waszyngton" |

### <a name="additional-features"></a>Dodatkowe funkcje

Interfejs API identyfikatorów zwraca również następujące informacje:

* Poziom ufności pola (każde pole zwraca skojarzoną wartość ufności)
* Nieprzetworzony tekst OCR (dane wyjściowe tekstu wyodrębnione przez OCR dla całego paragonu)
* Pole granicy każdego wyodrębnione pola w licencjach sterowników w Stanach Zjednoczonych
* Pole granicy dla strefy odczytu maszynowego (MRZ) w paszportach

  > [!NOTE]
  > Wstępnie sbudowaną identyfikatory nie wykrywają autentyczności identyfikatorów
  >
  > Rozpoznawanie formularzy wstępnie sbudowaną identyfikatory wyodrębnia kluczowe dane z danych identyfikatora. Nie wykrywa jednak ważności ani autentyczności oryginalnego dokumentu tożsamości.

## <a name="try-it-out"></a>Czas to wypróbować

Aby wypróbować usługę Rozpoznawanie formularzy identyfikatorów, przejdź do przykładowego narzędzia interfejsu użytkownika online:

> [!div class="nextstepaction"]
> [Wypróbuj wstępnie utworzone modele](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Wymagania dotyczące danych wejściowych

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>Obsługiwane typy identyfikatorów

* **Wstępnie wbudowane identyfikatory w wersji 2.1-preview.3** Wyodrębnia kluczowe wartości z paszportów na całym świecie i amerykańskich licencji sterowników.

  > [!NOTE]
  > Obsługa typów identyfikatorów
  >
  > Obecnie obsługiwane typy identyfikatorów obejmują paszport na całym świecie i licencje na kierowcy w Stanach Zjednoczonych. Aktywnie staramy się rozszerzyć obsługę identyfikatorów o inne dokumenty tożsamości na całym świecie.

## <a name="post-analyze-id-document"></a>Dokument z identyfikatorem analizy POST

Operacja [Analizowanie identyfikatora](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) pobiera obraz lub plik PDF z identyfikatorem jako dane wejściowe i wyodrębnia interesujące cię wartości. Wywołanie zwraca pole nagłówka odpowiedzi o nazwie `Operation-Location` . Wartość `Operation-Location` jest adresem URL, który zawiera identyfikator wyniku, który ma zostać użyty w następnym kroku.

|Nagłówek odpowiedzi| Adres URL wyniku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>GET Analyze Id Document Result

<!---
Need to update this with updated APIM links when available
-->

Drugim krokiem jest wywołanie operacji Get Analyze idDocument Result (Pobierz wynik analizy [**idDocument).**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) Ta operacja przyjmuje jako dane wejściowe identyfikator wyniku, który został utworzony przez operację Analyze ID. Zwraca odpowiedź JSON zawierającą pole **stanu** z następującymi możliwymi wartościami. Tę operację należy wywołać iteracyjnie, dopóki nie zostanie ona zwracana z **wartością succeeded.** Użyj interwału od 3 do 5 sekund, aby uniknąć przekroczenia szybkości żądań na sekundę (RPS).

|Pole| Typ | Możliwe wartości |
|:-----|:----:|:----|
|status | ciąg | notStarted: operacja analizy nie została uruchomiona. |
| |  | running: operacja analizy jest w toku. |
| |  | niepowodzenie: operacja analizy nie powiodła się. |
| |  | succeeded: operacja analizy zakończyła się pomyślnie. |

Jeśli pole **stanu** ma wartość **powodzenie,** odpowiedź JSON będzie zawierać informacje o paragonie i wyniki rozpoznawania tekstu. Wynik identyfikatorów jest zorganizowany jako słownik nazwanych wartości pól, gdzie każda wartość zawiera wyodrębniony tekst, wartość znormalizowaną, pole granicy, pewność i odpowiadające im elementy wyrazu. Wynik rozpoznawania tekstu jest zorganizowany jako hierarchia wierszy i słów z tekstem, polem granicy i informacjami o ufności.

![przykładowe wyniki paragonu](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Przykładowe dane wyjściowe JSON

Zobacz następujący przykład pomyślnej odpowiedzi JSON: Węzeł `readResults` zawiera cały rozpoznany tekst. Tekst jest zorganizowany według strony, następnie według wiersza, a następnie według poszczególnych wyrazów. Węzeł `documentResults` zawiera wartości identyfikatorów odnalezione przez model. W tym węźle znajdziesz również przydatne pary klucz/wartość, takie jak imię, nazwisko, numer dokumentu i inne.

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

* Wypróbuj własne identyfikatory i przykłady w przykładowym [Rozpoznawanie formularzy przykładowym interfejsie użytkownika.](https://fott-preview.azurewebsites.net/)
* Wykonaj Rozpoznawanie formularzy [szybki start,](quickstarts/client-library.md) aby rozpocząć pisanie aplikacji do przetwarzania identyfikatorów za pomocą Rozpoznawanie formularzy w języku programowania.

## <a name="see-also"></a>Zobacz też

* [**Co to jest rozpoznawanie formularzy?**](./overview.md)
* [**Dokumentacja interfejsu API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
