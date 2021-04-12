---
title: Sprawdzanie Video Indexer danych wyjściowych produkowanych przez interfejs API V2 na platformie Azure
titleSuffix: Azure Media Services
description: W tym temacie przedstawiono Azure Media Services Video Indexer dane wyjściowe generowane przez interfejs API v2.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/16/2020
ms.author: juliako
ms.openlocfilehash: 2ac7c3c2149ce43c860c7726381733ef377de8d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100530743"
---
# <a name="examine-the-video-indexer-output"></a>Sprawdzanie danych wyjściowych Video Indexer

Gdy plik wideo jest indeksowany, Video Indexer generuje zawartość JSON, która zawiera szczegółowe informacje o określonych danych wideo. Szczegółowe informacje obejmują: transkrypcje, OCRs, twarze, tematy, bloki itp. Każdy typ usługi Insights obejmuje wystąpienia zakresów czasu, które są wyświetlane, gdy szczegółowe informacje pojawiają się w filmie wideo. 

Możesz wizualnie zapoznać się ze szczegółowymi informacjami na temat wideo, naciskając przycisk **odtwarzania** na filmie wideo w witrynie [Video Indexer](https://www.videoindexer.ai/) . 

Możesz również użyć interfejsu API, wywołując interfejs API **pobierania indeksu wideo** , a stan odpowiedzi to OK, uzyskujesz szczegółowe dane wyjściowe JSON jako zawartość odpowiedzi.

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Ten artykuł bada dane wyjściowe Video Indexer (zawartość JSON). <br/>Aby uzyskać informacje o dostępnych funkcjach i szczegółowych danych, zobacz [Video Indexer Insights](video-indexer-overview.md#video-insights).

> [!NOTE]
> Wygaśnięcie wszystkich tokenów dostępu w Video Indexer wynosi godzinę.

## <a name="get-the-insights"></a>Uzyskaj wgląd w szczegółowe dane

### <a name="insightsoutput-produced-in-the-websiteportal"></a>Szczegółowe informacje/dane wyjściowe utworzone w witrynie sieci Web/portalu

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
1. Znajdź wideo, którego dane wyjściowe chcesz sprawdzić.
1. Naciśnij pozycję **Play** (Odtwórz).
1. Wybierz kartę **szczegółowe** dane (podsumowanie szczegółowych informacji) lub kartę **oś czasu** (umożliwia filtrowanie odpowiednich szczegółowych informacji).
1. Pobierz artefakty i zapoznaj się z nimi.

Aby uzyskać więcej informacji, zobacz [Wyświetlanie i edytowanie wglądu w dane wideo](video-indexer-view-edit.md).

## <a name="insightsoutput-produced-by-api"></a>Szczegółowe informacje/dane wyjściowe generowane przez interfejs API

1. Aby pobrać plik JSON, wywołaj [interfejs API indeksu pobierania wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)
1. Jeśli interesuje Cię również określone artefakty, wywołaj [interfejs API pobierania artefaktów wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?)

    W wywołaniu interfejsu API Określ żądany typ artefaktu (OCR, twarzy, ramki kluczowe itp.)

## <a name="root-elements-of-the-insights"></a>Główne elementy szczegółowych informacji

|Nazwa|Opis|
|---|---|
|accountId|Identyfikator konta w VI listy odtwarzania.|
|identyfikator|Identyfikator listy odtwarzania.|
|name|Nazwa listy odtwarzania.|
|description (opis)|Opis listy odtwarzania.|
|userName|Nazwa użytkownika, który utworzył listę odtwarzania.|
|utworzony|Godzina utworzenia listy odtwarzania.|
|Ustawienia prywatności|Tryb prywatności listy odtwarzania (prywatny/publiczny).|
|stan|Lista odtwarzania (przekazana, przetwarzanie, przetwarzanie, Niepowodzenie, poddane kwarantannie).|
|Jestem właścicielem|Wskazuje, czy lista odtwarzania została utworzona przez bieżącego użytkownika.|
|isedytowalne|Wskazuje, czy bieżący użytkownik ma uprawnienia do edytowania listy odtwarzania.|
|IsBase|Wskazuje, czy lista odtwarzania jest podstawową listą odtwarzania (wideo) czy listą odtwarzania z innych filmów wideo (pochodnych).|
|durationInSeconds|Łączny czas trwania listy odtwarzania.|
|summarizedInsights|Zawiera jeden [summarizedInsights](#summarizedinsights).
|wideo|Lista [filmów wideo](#videos) tworzących listę odtwarzania.<br/>Jeśli ta lista odtwarzania skonstruowanych zakresów czasu innych filmów wideo (pochodnych), wideo na tej liście będzie zawierać tylko dane z dołączonych zakresów czasu.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

W tej sekcji przedstawiono podsumowanie szczegółowych informacji.

|Atrybut | Opis|
|---|---|
|name|Nazwa filmu wideo. Na przykład Azure Monitor.|
|identyfikator|Identyfikator filmu wideo. Na przykład 63c6d532ff.|
|Ustawienia prywatności|Podział może mieć jeden z następujących trybów: **prywatny**, **publiczny**. **Public** — wideo jest widoczne dla wszystkich użytkowników na Twoim koncie i dla wszystkich użytkowników, którzy mają link do filmu wideo. **Prywatny** — wideo jest widoczne dla wszystkich użytkowników Twojego konta.|
|czas trwania|Zawiera jeden okres czasu, który opisuje czas wystąpienia szczegółowych informacji. Czas trwania jest w sekundach.|
|thumbnailVideoId|Identyfikator filmu wideo, z którego zrobiono miniaturę.
|thumbnailId|Identyfikator miniatury wideo. Aby uzyskać rzeczywistą miniaturę, wywołaj polecenie [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) i przekaż je ThumbnailVideoId i thumbnailId.|
|twarze/animatedCharacters|Może zawierać zero lub więcej twarzy. Aby uzyskać szczegółowe informacje, zobacz twarzy [/animatedCharacters](#facesanimatedcharacters).|
|keywords (słowa kluczowe)|Może zawierać zero lub więcej słów kluczowych. Aby uzyskać szczegółowe informacje, zobacz [słowa kluczowe](#keywords).|
|mową|Może zawierać zero lub więcej mową. Aby uzyskać szczegółowe informacje, zobacz [mową](#sentiments).|
|audioEffects| Może zawierać zero lub więcej audioEffects. Aby uzyskać szczegółowe informacje, zobacz [audioEffects](#audioeffects-public-preview).|
|Etykieta| Może zawierać zero lub więcej etykiet. Aby uzyskać szczegółowe informacje, zobacz [etykiety](#labels).|
|Marek| Może zawierać zero lub więcej marek. Aby uzyskać szczegółowe informacje, zobacz [marki](#brands).|
|statystyki | Aby uzyskać szczegółowe informacje, zobacz [Statystyka](#statistics).|
|emocji| Może zawierać zero lub więcej emocji. Aby uzyskać szczegółowe informacje, zobacz [emocji](#emotions).|
|opisano|Może zawierać zero lub więcej tematów. Szczegółowe informacje dotyczące [tematów](#topics) .|

## <a name="videos"></a>wideo

|Nazwa|Opis|
|---|---|
|accountId|Identyfikator konta wideo VI.|
|identyfikator|Identyfikator wideo.|
|name|Nazwa filmu wideo.
|stan|Stan filmu wideo (przekazany, przetwarzany, przetworzony, Niepowodzenie, poddane kwarantannie).|
|processingProgress|Postęp przetwarzania podczas przetwarzania (na przykład 20%).|
|failureCode|Kod błędu w przypadku niepowodzenia przetwarzania (na przykład "UnsupportedFileType").|
|failureMessage|Komunikat o niepowodzeniu, jeśli przetworzenie nie powiodło się.|
|externalId|Zewnętrzny identyfikator wideo (jeśli jest określony przez użytkownika).|
|externalUrl|Zewnętrzny adres URL wideo (jeśli jest określony przez użytkownika).|
|metadane|Zewnętrzne metadane wideo (Jeśli określone przez użytkownika).|
|isdorosły|Wskazuje, czy film wideo został ręcznie sprawdzony i zidentyfikowany jako wiadomość wideo dla dorosłych.|
|Insights|Obiekt Insights. Aby uzyskać więcej informacji, zobacz [szczegółowe](#insights)informacje.|
|thumbnailId|Identyfikator miniatury wideo. Aby uzyskać rzeczywiste wywołanie miniatury i przekazać je do identyfikatora [wideo i thumbnailId](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) .|
|publishedUrl|Adres URL do przesyłania strumieniowego wideo.|
|publishedUrlProxy|Adres URL do przesyłania strumieniowego wideo z programu (dla urządzeń firmy Apple).|
|viewToken|Krótkoterminowy token widoku do przesyłania strumieniowego wideo.|
|sourceLanguage|Język źródłowy wideo.|
|language|Rzeczywisty język wideo (tłumaczenia).|
|indexingPreset|Ustawienie wstępne używane do indeksowania wideo.|
|streamingPreset|Ustawienie wstępne używane do publikowania filmu wideo.|
|linguisticModelId|Model CRI używany do transkrypcja wideo.|
|statystyki | Aby uzyskać więcej informacji, zobacz [Statystyka](#statistics).|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>Insights

Każdy wgląd (na przykład wiersze transkrypcji, twarzy, marki itp.) zawiera listę unikatowych elementów (na przykład face1, face2, face3), a każdy element ma własne metadane i listę jego wystąpień (czyli zakresy czasowe z dodatkowymi opcjonalnymi metadanymi).

Obiekt może mieć identyfikator, nazwę, miniaturę, inne metadane i listę wystąpień czasowych (na przykład: 00:00:05 – 00:00:10, 00:01:00-00:02:30 i 00:41:21 – 00:41:49). Każde wystąpienie czasowe może mieć dodatkowe metadane. Na przykład współrzędne prostokąta powierzchni (20230, 60, 60).

|Wersja|Wersja kodu|
|---|---|
|sourceLanguage|Język źródłowy wideo (przy założeniu, że jeden język główny). W postaci ciągu [BCP-47](https://tools.ietf.org/html/bcp47) .|
|language|Język informacji szczegółowych (przetłumaczony od języka źródłowego). W postaci ciągu [BCP-47](https://tools.ietf.org/html/bcp47) .|
|zapisy|Wgląd w dane [transkrypcji](#transcript) .|
|aparat|Szczegółowe informacje [OCR](#ocr) .|
|keywords (słowa kluczowe)|Informacje o [słowach kluczowych](#keywords) .|
|bloki|Może zawierać co najmniej jeden [blok](#blocks)|
|twarze/animatedCharacters|Informacje o twarzy [/animatedCharacters](#facesanimatedcharacters) .|
|Etykieta|Szczegółowe informacje o [etykietach](#labels) .|
|zrzutów|Informacje na temat [zrzutów](#shots) .|
|Marek|Informacje o [marek](#brands) .|
|audioEffects|[AudioEffects](#audioeffects-public-preview) wgląd w szczegółowe dane.|
|mową|[Mową](#sentiments) wgląd w szczegółowe dane.|
|visualContentModeration|[VisualContentModeration](#visualcontentmoderation) wgląd w szczegółowe dane.|
|textualContentModeration|[TextualContentModeration](#textualcontentmoderation) wgląd w szczegółowe dane.|
|emocji| [Emocji](#emotions) wgląd w szczegółowe dane.|
|opisano|Szczegółowe informacje dotyczące [tematów](#topics) .|
|głośniki|Informacje na temat [głośników](#speakers) .|

Przykład:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>bloki

Atrybut | Opis
---|---
identyfikator|Identyfikator bloku.|
Liczba|Lista zakresów czasu tego bloku.|

#### <a name="transcript"></a>zapisy

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator wiersza.|
|tekst|Samego transkrypcji.|
|ufność|Niezgodność z dokładnością transkrypcji.|
|speakerId|Identyfikator osoby mówiącej.|
|language|Język transkrypcji. Przeznaczone do obsługi transkrypcji, w których każdy wiersz może mieć inny język.|
|Liczba|Lista przedziałów czasu, w których pojawił się ten wiersz. Jeśli wystąpienie jest transkrypcją, będzie miało tylko 1 wystąpienie.|

Przykład:

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "text":"ignite 2016. Your mission at Microsoft is to empower every",
  "confidence":0.8944,
  "speakerId":2,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
```

#### <a name="ocr"></a>aparat

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator wiersza OCR.|
|tekst|Tekst OCR.|
|ufność|Wiarygodność rozpoznawania.|
|language|Język OCR.|
|Liczba|Lista przedziałów czasu, w których wystąpiło to OCR (ten sam OCR może występować wiele razy).|
|wysokość|Wysokość prostokąta OCR.|
|top (pierwsze)|Górna lokalizacja w px|
|left| Lewa lokalizacja w px|
|szerokość|Szerokość prostokąta OCR.|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>keywords (słowa kluczowe)

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator słowa kluczowego.|
|tekst|Tekst słowa kluczowego.|
|ufność|Wiarygodność rozpoznawania słowa kluczowego.|
|language|Język słowa kluczowego (w przypadku tłumaczenia).|
|Liczba|Lista przedziałów czasu, w których pojawiło się to słowo kluczowe (słowo kluczowe może występować wiele razy).|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="facesanimatedcharacters"></a>twarze/animatedCharacters

`animatedCharacters` element zastępuje `faces` w przypadku, gdy wideo zostało zindeksowane przy użyciu modelu znaków animowanych. Odbywa się to przy użyciu modelu niestandardowego w Custom Vision, Video Indexer uruchamia go w klatkach kluczowych.

Jeśli istnieją twarzy (Nieanimowane znaki), Video Indexer używa Face API we wszystkich ramkach wideo, aby wykrywać twarze i osobistości.

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator kroju.|
|name|Nazwa kroju. Może to być "nieznany #0, zidentyfikowanej osobistości lub osoby przeszkolonej przez klienta.|
|ufność|Niepewność identyfikacji kroju.|
|description (opis)|Opis osobistości. |
|thumbnailId|Identyfikator miniatury tej czołowej.|
|knownPersonId|Jeśli jest to znana osoba, jej identyfikator wewnętrzny.|
|referenceId|Jeśli jest to osobistości Bing, jego identyfikator Bing.|
|referenceType|Obecnie tylko Bing.|
|tytuł|Jeśli jest to osobistości, jego tytuł (na przykład "dyrektor naczelny firmy Microsoft").|
|imageUrl|Jeśli jest to osobistości, jego adres URL obrazu.|
|Liczba|Są to wystąpienia, w których pojawiły się powierzchnie w danym przedziale czasu. Każde wystąpienie ma również thumbnailsId. |

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

#### <a name="labels"></a>Etykieta

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

#### <a name="scenes"></a>ze

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator sceny.|
|Liczba|Lista zakresów czasu tej sceny (scena może mieć tylko 1 wystąpienie).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>zrzutów

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator zrzutu.|
|Ramki kluczowe|Lista klatek kluczowych w zrzucie (każdy ma identyfikator i listę zakresów czasu wystąpienia). Każde wystąpienie z klatką kluczową ma pole thumbnailId, które zawiera identyfikator miniatury klatki kluczowej.|
|Liczba|Lista zakresów czasu tego zrzutu (zrzut może mieć tylko 1 wystąpienie).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>Marek

Nazwy firmowe i towarowe wykryte w zamiany mowy na tekst transkrypcji i/lub OCR wideo. Nie obejmuje to wizualizacji rozpoznawania marki ani logo.

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator marki.|
|name|Nazwa marki.|
|referenceId | Sufiks adresu URL witryny Wikipedia. Na przykład "Target_Corporation" jest sufiksem [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) .
|referenceUrl | Adres URL witryny Wikipedia marki, jeśli istnieje. Przykładowy adres URL to [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|description (opis)|Opis marek.|
|tags|Lista wstępnie zdefiniowanych tagów, które zostały skojarzone z tą marką.|
|ufność|Wartość ufności detektora Video Indexer marką (0-1).|
|Liczba|Lista zakresów czasu tej marki. Każde wystąpienie ma element marktype, który wskazuje, czy ta marka pojawiła się w transkrypcji, czy w OCR.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>statystyki

|Nazwa|Opis|
|---|---|
|CorrespondenceCount|Liczba zbieżności wideo.|
|SpeakerWordCount|Liczba wyrazów na prelegenta.|
|SpeakerNumberOfFragments|Ilość fragmentów, które są w filmie wideo.|
|SpeakerLongestMonolog|Najdłuższa monologa. Jeśli prelegent znajduje się w monolog, jest on uwzględniony. Wyciszenie na początku i na końcu monolog zostaje usunięte.| 
|SpeakerTalkToListenRatio|Obliczenia opierają się na czasie poświęcanym na monolog osoby mówiącej (bez wyciszenia między nimi) podzieloną przez łączny czas wideo. Czas jest zaokrąglany do trzeciego punktu dziesiętnego.|

#### <a name="audioeffects-public-preview"></a>audioEffects (publiczna wersja zapoznawcza)

|Nazwa|Opis
|---|---|
|identyfikator|Identyfikator efektu audio|
|typ|Typ efektu audio|
|Liczba|Lista przedziałów czasu, w których pojawił się ten efekt audio. Każde wystąpienie ma pole zaufania.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Siren",
    "instances": [
    {
       "confidence": 0.87,
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
       "confidence": 0.87,
       "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>mową

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

#### <a name="visualcontentmoderation"></a>visualContentModeration

Blok visualContentModeration zawiera zakresy czasu, które Video Indexer mogły mieć zawartość dla dorosłych. Jeśli visualContentModeration jest pusta, nie istnieje żadna zawartość dla dorosłych.

Filmy wideo, które mają zawierać zawartość dla dorosłych lub erotycznej, mogą być dostępne tylko w widoku prywatnym. Użytkownicy mogą przesłać żądanie dotyczące przeglądu zawartości przez człowieka, w takim przypadku atrybut isdorosły będzie zawierał wynik przeglądu przez człowieka.

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

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator moderowania zawartości tekstowej.|
|bannedWordsCount |Liczba zabronionych wyrazów.|
|bannedWordsRatio |Stosunek całkowitej liczby wyrazów.|

#### <a name="emotions"></a>emocji

Video Indexer identyfikuje emocji na podstawie podpowiedzi i sygnałów dźwiękowych. Zidentyfikowanych rozpoznawania emocji może być: radości, smutek, gniew lub obaw.

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator rozpoznawania emocji.|
|typ|Rozpoznawania emocji, która została zidentyfikowana na podstawie wskazówek dotyczących mowy i audio. Rozpoznawania emocji może być: radości, smutek, gniew lub obaw.|
|Liczba|Lista przedziałów czasu, w których wystąpiła ta rozpoznawania emocji.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>opisano

Video Indexer wykonuje wnioskowanie głównych tematów z transkrypcji. Gdy jest to możliwe, dołączana jest Taksonomia [IPTC](https://iptc.org/standards/media-topics/) dla drugiego poziomu. 

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator tematu.|
|name|Nazwa tematu, na przykład: "środki farmaceutyczne".|
|referenceId|Struktura nawigacyjna odzwierciedla hierarchię tematów. Na przykład: "kondycja i samopoczucie/medycyna, opieka medyczna/środki farmaceutyczne".|
|ufność|Wynik pewności z zakresu [0, 1]. Wyższy poziom.|
|language|Język używany w temacie.|
|iptcname|Nazwa kodu nośnika IPTC, jeśli wykryto.|
|Liczba |Obecnie Video Indexer nie indeksuje tematu w przedziałach czasowych, dlatego cały klip wideo jest używany jako interwał.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

#### <a name="speakers"></a>głośniki

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator osoby mówiącej.|
|name|Nazwa głośnika w postaci "głośnik # *<number>* " na przykład: "prelegent #1".|
|Liczba |Lista przedziałów czasu, w których pojawił się ten głośnik.|

```json
"speakers":[
{
  "id":1,
  "name":"Speaker #1",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "name":"Speaker #2",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
` ` `
```
## <a name="next-steps"></a>Następne kroki

[Portal dla deweloperów Video Indexer](https://api-portal.videoindexer.ai)

Aby uzyskać informacje na temat sposobu osadzania widżetów w aplikacji, zobacz [osadzanie Video Indexer elementów widget w aplikacjach](video-indexer-embed-widgets.md). 

