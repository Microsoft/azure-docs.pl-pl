---
title: Analizowanie plików wideo i audio
titleSuffix: Azure Media Services
description: Dowiedz się, jak analizować zawartość audio i wideo przy użyciu AudioAnalyzerPreset i VideoAnalyzerPreset w Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: c00af3a128685dfbd2435b65fe4d00107ca22ba4
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94744361"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Analizowanie plików wideo i audio przy użyciu Azure Media Services

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services v3 umożliwia wyodrębnienie szczegółowych informacji z plików wideo i audio przy użyciu Video Indexer. W tym artykule opisano ustawienia wstępne programu Media Services v3 Analyzer używane do wyodrębniania szczegółowych informacji. Aby uzyskać bardziej szczegółowe informacje, można korzystać bezpośrednio z usługi Video Indexer. Aby zrozumieć, kiedy należy użyć Video Indexer a Media Services ustawień wstępnych analizatora, zapoznaj się z [dokumentem porównania](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Istnieją dwa tryby ustawień predefiniowanych analizatora dźwięku, podstawowa i standardowa. Zobacz Opis różnic w poniższej tabeli.

Aby analizować zawartość przy użyciu ustawień wstępnych Media Services v3, należy utworzyć **transformację** i przesłać **zadanie** , które używa jednego z następujących ustawień wstępnych: [VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset) lub **AudioAnalyzerPreset**. Samouczek przedstawiający sposób używania **VideoAnalyzerPreset** można znaleźć w temacie [Analizowanie wideo za pomocą Azure Media Services](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> W przypadku użycia ustawień wstępnych analizatora wideo lub audio Użyj Azure Portal, aby ustawić, że konto ma 10 jednostek zarezerwowanych multimediów, chociaż nie jest to wymagane. Do ustawień wstępnych audio można użyć opcji S1 lub S2. Aby uzyskać więcej informacji, zobacz temat [Scale media processing (Skalowanie przetwarzania multimediów)](media-reserved-units-cli-how-to.md).

## <a name="compliance-privacy-and-security"></a>Zgodność, ochrona prywatności i zabezpieczenia

Ważną kwestią jest przestrzeganie wszystkich obowiązujących przepisów dotyczących używania Video Indexer i nie można używać Video Indexer lub żadnej innej usługi platformy Azure w sposób naruszający prawa innych lub mogą być szkodliwe dla innych osób. Przed przekazaniem jakichkolwiek filmów wideo, w tym wszelkich danych biometrycznych, do usługi Video Indexer na potrzeby przetwarzania i przechowywania, należy dysponować wszystkimi właściwymi prawami, w tym wszystkimi odpowiednimi komunikatami o zgodzie, z poszczególnych osób w filmie wideo. Aby dowiedzieć się o zgodności, ochronie prywatności i bezpieczeństwie w Video Indexer, [warunki Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)firmy Microsoft. W celu zachowania poufności informacji firmy Microsoft i obsługi danych należy zapoznać się z zasadami [zachowania poufności informacji](https://privacy.microsoft.com/PrivacyStatement)firmy Microsoft, tematami dotyczącymi [usług online](https://www.microsoft.com/licensing/product-licensing/products) ("Ost") i [uzupełnieniem przetwarzania danych](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Dodatkowe informacje o ochronie prywatności, takie jak przechowywanie danych, usuwanie/niszczenie, są dostępne w pliku OST i [tutaj](../video-indexer/faq.md). Korzystając z Video Indexer, wyrażasz zgodę na związanie Cognitive Services warunkami, OST, DPA i zasad zachowania poufności informacji.

## <a name="built-in-presets"></a>Wbudowane ustawienia wstępne

Media Services obecnie obsługuje następujące wbudowane ustawienia wstępne analizatora:  

|**Nazwa ustawienia wstępnego**|**Scenariusz**|**Szczegóły**|
|---|---|---|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analizowanie standardu audio|Ustawienie wstępne stosuje wstępnie zdefiniowany zestaw operacji analitycznych opartych na formacie AI, w tym transkrypcję mowy. Obecnie ustawienie wstępne obsługuje przetwarzanie zawartości przy użyciu jednej ścieżki audio zawierającej mowę w jednym języku. Możesz określić język dla ładunku audio w danych wejściowych przy użyciu formatu BCP-47 "Language tag-region". Obsługiwane języki to angielski ("en-US" i "pl-GB"), hiszpański ("es-ES" i "es-MX"), francuski ("fr-FR"), włoski ("it-IT"), japoński ("ja-JP"), portugalski ("pt-BR"), chiński ("zh-CN"), niemiecki ("de-DE"), arabski ("AR-EG" i "AR-SY"), rosyjski ("ru-RU"), hindi ("Hi-IN") i koreański ("ko-KR").<br/><br/> Jeśli język nie jest określony lub ma wartość null, funkcja automatycznego wykrywania języka wybiera pierwszy wykryty język i kontynuuje w wybranym języku dla czasu trwania pliku. Funkcja automatycznego wykrywania języka jest obecnie obsługiwana w języku angielskim, chińskim, francuskim, niemieckim, włoskim, japońskim, hiszpańskim, rosyjskim i portugalskim. Nie obsługuje on dynamicznego przełączania między językami po wykryciu pierwszego języka. Funkcja automatycznego wykrywania języka działa najlepiej z nagraniami dźwiękowymi z wyraźnie dostrzegalną mową. Jeśli automatyczne wykrywanie języka nie odnajdzie języka, transkrypcja powróci do języka angielskiego.|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analizowanie języka audio Basic|Ten tryb wykonuje transkrypcję zamiany mowy na tekst i generowanie pliku napisów VTT. Dane wyjściowe tego trybu obejmują plik JSON usługi Insights, w tym informacje o słowach kluczowych, transkrypcji i chronometrażu. Funkcja automatycznego wykrywania języka i diarization prelegenta nie są uwzględnione w tym trybie. Lista obsługiwanych języków jest dostępna [tutaj](https://go.microsoft.com/fwlink/?linkid=2109463)|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analizowanie audio i wideo|Wyodrębnia szczegółowe informacje (bogate metadane) zarówno z audio, jak i wideo, a następnie wyprowadza plik w formacie JSON. Możesz określić, czy podczas przetwarzania pliku wideo chcesz wyodrębnić szczegółowe informacje o usłudze audio. Aby uzyskać więcej informacji, zobacz [Analizowanie wideo](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Wykrywanie twarzy znajdujących się w wideo|Opisuje ustawienia, które mają być używane podczas analizowania wideo w celu wykrycia wszystkich obecnych twarzy.|

### <a name="audioanalyzerpreset-standard-mode"></a>AudioAnalyzerPreset tryb standardowy

Ustawienie wstępne umożliwia wyodrębnienie wielu szczegółowych informacji audio z pliku audio lub wideo.

Dane wyjściowe obejmują plik JSON (ze wszystkimi szczegółowymi informacjami) i plik VTT dla transkrypcji audio. To ustawienie wstępne akceptuje właściwość, która określa język pliku wejściowego w postaci ciągu [BCP47](https://tools.ietf.org/html/bcp47) . Szczegółowe informacje o dźwięku obejmują:

* **Transkrypcja audio**: transkrypcja wyrazów mówionych z sygnaturami czasowymi. Obsługiwane są wiele języków.
* **Indeksowanie prezentera**: mapowanie głośników i odpowiednich słów wymawiających.
* **Analiza tonacjii mowy**: dane wyjściowe analizy tonacji wykonane na transkrypcji audio.
* **Słowa kluczowe**: słowa kluczowe, które są wyodrębniane z transkrypcji audio.

### <a name="audioanalyzerpreset-basic-mode"></a>Tryb podstawowy AudioAnalyzerPreset

Ustawienie wstępne umożliwia wyodrębnienie wielu szczegółowych informacji audio z pliku audio lub wideo.

Dane wyjściowe obejmują plik JSON i plik VTT dla transkrypcji audio. To ustawienie wstępne akceptuje właściwość, która określa język pliku wejściowego w postaci ciągu [BCP47](https://tools.ietf.org/html/bcp47) . Dane wyjściowe obejmują:

* **Transkrypcja audio**: transkrypcja wyrazów mówionych z sygnaturami czasowymi. Obsługiwane są wiele języków, ale automatyczne wykrywanie języka i diarization głośników nie są uwzględniane.
* **Słowa kluczowe**: słowa kluczowe, które są wyodrębniane z transkrypcji audio.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

Ustawienie wstępne pozwala wyodrębnić wiele szczegółowych informacji audio i wideo z pliku wideo. Dane wyjściowe obejmują plik JSON (ze wszystkimi szczegółowymi informacjami), plik VTT dla transkrypcji wideo oraz kolekcję miniatur. To ustawienie wstępne akceptuje również ciąg [BCP47](https://tools.ietf.org/html/bcp47) (reprezentujący język filmu wideo) jako właściwość. Szczegółowe informacje wideo obejmują wszystkie szczegółowe informacje o danych audio wymienione powyżej oraz następujące dodatkowe elementy:

* **Śledzenie twarzy**: czas, w którym twarze są obecne w filmie wideo. Każda z nich ma identyfikator kroju i odpowiednią kolekcję miniatur.
* **Tekst wizualny**: tekst wykrywany za pomocą optycznego rozpoznawania znaków. Tekst jest sygnaturą czasową, a także używany do wyodrębniania słów kluczowych (oprócz transkrypcji audio).
* **Klatki kluczowe**: Kolekcja klatek kluczowych wyodrębnionych z filmu wideo.
* **Moderowanie zawartości wizualnej**: część filmów wideo oflagowanych jako osoba dorosła lub erotycznej.
* **Adnotacja**: Wynik dodawania adnotacji do filmów wideo opartych na wstępnie zdefiniowanym modelu obiektów

## <a name="insightsjson-elements"></a>insights.jselementów

Dane wyjściowe obejmują plik JSON (insights.json) ze wszystkimi szczegółowymi informacjami znalezionymi w pliku wideo lub dźwiękiem. KOD JSON może zawierać następujące elementy:

### <a name="transcript"></a>zapisy

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator wiersza.|
|tekst|Samego transkrypcji.|
|language|Język transkrypcji. Przeznaczone do obsługi transkrypcji, w których każdy wiersz może mieć inny język.|
|Liczba|Lista przedziałów czasu, w których pojawił się ten wiersz. Jeśli wystąpienie jest transkrypcją, będzie miało tylko 1 wystąpienie.|

Przykład:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>aparat

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator wiersza OCR.|
|tekst|Tekst OCR.|
|ufność|Wiarygodność rozpoznawania.|
|language|Język OCR.|
|Liczba|Lista przedziałów czasu, w których wystąpiło to OCR (ten sam OCR może występować wiele razy).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>ściank

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator kroju.|
|name|Nazwa kroju. Może to być "nieznany #0", zidentyfikowanego osobistości lub osoby przeszkolonej przez klienta.|
|ufność|Niepewność identyfikacji kroju.|
|description (opis)|Opis osobistości. |
|thumbnailId|Identyfikator miniatury tej czołowej.|
|knownPersonId|Wewnętrzny identyfikator (jeśli jest znaną osobą).|
|referenceId|Identyfikator Bing (jeśli jest to osobistości Bing).|
|referenceType|Obecnie tylko Bing.|
|tytuł|Tytuł (jeśli jest to osobistości — na przykład "dyrektor naczelny firmy Microsoft").|
|imageUrl|Adres URL obrazu, jeśli jest to osobistości.|
|Liczba|Wystąpienia, w których pojawiła się Strona w danym przedziale czasu. Każde wystąpienie ma również thumbnailsId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>zrzutów

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator zrzutu.|
|Ramki kluczowe|Lista kluczowych klatek w ramach zrzutu (każdy ma identyfikator i listę zakresów czasu wystąpienia). Wystąpienia klatek kluczowych mają pole thumbnailId z IDENTYFIKATORem miniatury klatki kluczowej.|
|Liczba|Lista zakresów czasu tego zrzutu (zrzuty mają tylko 1 wystąpienie).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>statystyki

|Nazwa|Opis|
|---|---|
|CorrespondenceCount|Liczba zbieżności wideo.|
|WordCount|Liczba wyrazów na prelegenta.|
|SpeakerNumberOfFragments|Ilość fragmentów, które są w filmie wideo.|
|SpeakerLongestMonolog|Najdłuższa monologa. Jeśli prelegent znajduje się w monolog, jest on uwzględniony. Wyciszenie na początku i na końcu monolog zostaje usunięte.|
|SpeakerTalkToListenRatio|Obliczenia opierają się na czasie poświęcanym na monolog osoby mówiącej (bez wyciszenia między nimi) podzieloną przez łączny czas wideo. Czas jest zaokrąglany do trzeciego punktu dziesiętnego.|


### <a name="sentiments"></a>mową

Mową są agregowane według pola sentimentType (pozytywna/neutralna/ujemna). Na przykład 0-0,1, 0,1-0,2.

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator tonacji.|
|averageScore |Średnia wszystkich ocen wszystkich wystąpień tego typu tonacji — wartość dodatnia/neutralna/ujemna|
|Liczba|Lista przedziałów czasu, w których wystąpiła ta tonacji.|
|sentimentType |Typem może być "dodatnia", "neutralna" lub "ujemna".|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>Etykieta

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator etykiety.|
|name|Nazwa etykiety (na przykład "Computer", "TV").|
|language|Nazwa etykiety językowej (po translacji). BCP-47|
|Liczba|Lista przedziałów czasu, w których pojawiła się Ta etykieta (etykieta może być wyświetlana wiele razy). Każde wystąpienie ma pole zaufania. |

```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>keywords (słowa kluczowe)

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator słowa kluczowego.|
|tekst|Tekst słowa kluczowego.|
|ufność|Wiarygodność rozpoznawania słowa kluczowego.|
|language|Język słowa kluczowego (w przypadku tłumaczenia).|
|Liczba|Lista przedziałów czasu, w których pojawiło się to słowo kluczowe (słowo kluczowe może występować wiele razy).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

Blok visualContentModeration zawiera zakresy czasu, które Video Indexer mogły mieć zawartość dla dorosłych. Jeśli visualContentModeration jest pusty, nie ma zidentyfikowanej treści dla dorosłych.

Filmy wideo, które mają zawierać zawartość dla dorosłych lub erotycznej, mogą być dostępne tylko w widoku prywatnym. Użytkownicy mogą przesłać żądanie dotyczące przeglądu zawartości przez człowieka, w takim przypadku `IsAdult` atrybut będzie zawierał wynik przeglądu przez człowieka.

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator moderowania zawartości wizualnej.|
|adultScore|Wynik dla dorosłych (z usługi Content moderator).|
|racyScore|Wynik erotycznej (z moderowaniem zawartości).|
|Liczba|Lista przedziałów czasu, w których pojawiło się to moderowanie zawartości wizualnej.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>Następne kroki

[Samouczek: Analizowanie wideo za pomocą usługi Azure Media Services](analyze-videos-tutorial-with-api.md)
