---
title: Badanie danych wyjściowych Video Indexer przez interfejs API w wersji 2 — Azure
titleSuffix: Azure Media Services
description: W tym temacie przeanalizowano Azure Media Services Video Indexer danych wyjściowych wytwarzanych przez interfejs API w wersji 2.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/16/2020
ms.author: juliako
ms.openlocfilehash: 84bb4766b3a896823dd0bef023f8042965a85846
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532869"
---
# <a name="examine-the-video-indexer-output"></a>Badanie Video Indexer danych wyjściowych

Gdy plik wideo jest indeksowany, Video Indexer tworzy zawartość JSON, która zawiera szczegółowe informacje o określonym wideo. Szczegółowe informacje obejmują transkrypcje, OPR, twarze, tematy, bloki itp. Każdy typ szczegółowych informacji zawiera wystąpienia zakresów czasu, które są wyświetlane, gdy szczegółowe informacje pojawiają się w wideo. 

Możesz wizualnie przeanalizować podsumowane szczegółowe informacje o  wideo, naciskając przycisk Odtąd w wideo w [Video Indexer](https://www.videoindexer.ai/) internetowej. 

Możesz również użyć interfejsu API, wywołując interfejs **API** pobierz indeks wideo, a stan odpowiedzi to OK. Jako zawartość odpowiedzi otrzymasz szczegółowe dane wyjściowe JSON.

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

W tym artykule analizujemy Video Indexer danych wyjściowych (zawartość JSON). <br/>Aby uzyskać informacje o dostępnych funkcjach i szczegółowych informacjach, [zobacz Video Indexer szczegółowe informacje.](video-indexer-overview.md#video-insights)

> [!NOTE]
> Wygaśnięcie wszystkich tokenów dostępu w programie Video Indexer wynosi jedną godzinę.

## <a name="get-the-insights"></a>Uzyskiwanie szczegółowych informacji

### <a name="insightsoutput-produced-in-the-websiteportal"></a>Szczegółowe informacje/dane wyjściowe w witrynie internetowej/portalu

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
1. Znajdź wideo, którego dane wyjściowe chcesz zbadać.
1. Naciśnij pozycję **Play** (Odtwórz).
1. Wybierz **kartę Szczegółowe informacje** (podsumowane szczegółowe informacje) lub **kartę Oś** czasu (umożliwia filtrowanie odpowiednich szczegółowych informacji).
1. Pobierz artefakty i ich co się w nich dzieje.

Aby uzyskać więcej informacji, zobacz [Wyświetlanie i edytowanie szczegółowych informacji o wideo.](video-indexer-view-edit.md)

## <a name="insightsoutput-produced-by-api"></a>Szczegółowe informacje/dane wyjściowe produkowane przez interfejs API

1. Aby pobrać plik JSON, wywołaj interfejs [API pobierania indeksu wideo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index)
1. Jeśli interesują Cię również określone artefakty, wywołaj interfejs API pobierania [artefaktów wideo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Artifact-Download-Url)

    W wywołaniu interfejsu API określ żądany typ artefaktu (OCR, twarze, ramki kluczowe itp.)

## <a name="root-elements-of-the-insights"></a>Główne elementy szczegółowych informacji

|Nazwa|Opis|
|---|---|
|accountId|Identyfikator konta VI listy odtwarzania.|
|identyfikator|Identyfikator listy odtwarzania.|
|name|Nazwa listy odtwarzania.|
|description (opis)|Opis listy odtwarzania.|
|userName|Nazwa użytkownika, który utworzył listę odtwarzania.|
|Utworzone|Czas tworzenia listy odtwarzania.|
|tryb privacyMode|Tryb prywatności listy odtwarzania (prywatna/publiczna).|
|stan|Listy odtwarzania (przekazane, przetwarzane, przetworzone, nieudane, poddane kwarantannie).|
|isOwned|Wskazuje, czy lista odtwarzania została utworzona przez bieżącego użytkownika.|
|Iseditable|Wskazuje, czy bieżący użytkownik ma uprawnienia do edytowania listy odtwarzania.|
|isBase|Wskazuje, czy lista odtwarzania to podstawowa lista odtwarzania (wideo), czy lista odtwarzania z innych filmów wideo (pochodna).|
|durationInSeconds|Łączny czas trwania listy odtwarzania.|
|summarizedInsights|Zawiera jeden [summarizedInsights](#summarizedinsights).
|wideo|Lista filmów [wideo konstruowania](#videos) listy odtwarzania.<br/>Jeśli ta lista odtwarzania zawiera zakresy czasu innych filmów wideo (pochodnych), filmy wideo na tej liście będą zawierać tylko dane z uwzględnionych zakresów czasu.|

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
|name|Nazwa wideo. Na przykład Azure Monitor.|
|identyfikator|Identyfikator wideo. Na przykład 63c6d532ff.|
|tryb privacyMode|Podział może mieć jeden z następujących trybów: **Prywatny**, **Publiczny**. **Publiczne** — film wideo jest widoczny dla wszystkich użytkowników na Twoim koncie i dla wszystkich osób, które mają link do tego filmu wideo. **Prywatne** — film wideo jest widoczny dla wszystkich użytkowników na Twoim koncie.|
|czas trwania|Zawiera jeden czas trwania, który opisuje czas, w jaki wystąpił wgląd w szczegółowe dane. Czas trwania jest w sekundach.|
|thumbnailVideoId|Identyfikator wideo, z którego została podjęta miniatura.
|thumbnailId|Identyfikator miniatury filmu wideo. Aby uzyskać rzeczywistą miniaturę, wywołaj [get-thumbnail](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) i przekaż ją thumbnailVideoId i thumbnailId.|
|twarze/animowaneznaki|Może zawierać zero lub więcej twarzy. Aby uzyskać bardziej szczegółowe informacje, zobacz [twarze/animowaneznaki.](#facesanimatedcharacters)|
|keywords (słowa kluczowe)|Może zawierać zero lub więcej słów kluczowych. Aby uzyskać bardziej szczegółowe informacje, zobacz [słowa kluczowe](#keywords).|
|Uczucia|Może zawierać zero lub więcej tonacji. Aby uzyskać bardziej szczegółowe informacje, zobacz [tonacji](#sentiments).|
|audioEffects| Może zawierać zero lub więcej audioEffects. Aby uzyskać bardziej szczegółowe informacje, zobacz [audioEffects](#audioeffects-public-preview).|
|Etykiety| Może zawierać zero lub więcej etykiet. Aby uzyskać szczegółowe informacje, zobacz [etykiety](#labels).|
|Marek| Może zawierać zero lub więcej marek. Aby uzyskać bardziej szczegółowe informacje, zobacz [marki](#brands).|
|statystyki | Aby uzyskać bardziej szczegółowe informacje, zobacz [statystyki](#statistics).|
|Emocje| Może zawierać zero lub więcej emocji. Aby uzyskać bardziej szczegółowe informacje, zobacz [temat Emotions (Emocje).](#emotions)|
|Tematy|Może zawierać zero lub więcej tematów. Szczegółowe [informacje dotyczące](#topics) tematów.|

## <a name="videos"></a>wideo

|Nazwa|Opis|
|---|---|
|accountId|Identyfikator konta VI filmu wideo.|
|identyfikator|Identyfikator filmu wideo.|
|name|Nazwa filmu wideo.
|stan|Stan filmu wideo (przekazany, przetwarzany, przetworzony, nieudany, poddany kwarantannie).|
|processingProgress|Postęp przetwarzania podczas przetwarzania (na przykład 20%).|
|kod błędu|Kod błędu, jeśli przetwarzanie nie powiodło się (na przykład "UnsupportedFileType").|
|failureMessage|Komunikat o błędzie, jeśli przetwarzanie nie powiodło się.|
|externalId|Identyfikator zewnętrzny filmu wideo (jeśli jest określony przez użytkownika).|
|Externalurl|Zewnętrzny adres URL filmu wideo (jeśli jest określony przez użytkownika).|
|metadane|Metadane zewnętrzne filmu wideo (jeśli zostały określone przez użytkownika).|
|isAdult|Wskazuje, czy wideo zostało ręcznie przejmowane i zidentyfikowane jako wideo dla dorosłych.|
|Spostrzeżenia|Obiekt szczegółowych informacji. Aby uzyskać więcej informacji, zobacz [szczegółowe informacje.](#insights)|
|thumbnailId|Identyfikator miniatury filmu wideo. Aby uzyskać rzeczywiste wywołanie [miniatury Get-Thumbnail](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) i przekazać do niego identyfikator wideo i thumbnailId.|
|publishedUrl|Adres URL do strumieniowego przesyłania wideo.|
|publishedUrlProxy|Adres URL do strumieniowego przesyłania wideo (dla urządzeń firmy Apple).|
|viewToken|Token widoku krótkotrwałego do przesyłania strumieniowego wideo.|
|sourceLanguage (język źródłowy)|Język źródłowy filmu wideo.|
|language|Rzeczywisty język (tłumaczenie) filmu wideo.|
|indexingPreset|Ustawienie wstępne używane do indeksowania wideo.|
|streamingPreset|Ustawienie wstępne używane do publikowania wideo.|
|linguisticModelId|Model CRIS używany do transkrypcji wideo.|
|statystyki | Aby uzyskać więcej informacji, zobacz [statystyki](#statistics).|

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
### <a name="insights"></a>Spostrzeżenia

Każdy wgląd (na przykład linie transkrypcji, twarze, marki itp.) zawiera listę unikatowych elementów (na przykład face1, face2, face3), a każdy element ma własne metadane i listę swoich wystąpień (które są zakresami czasu z dodatkowymi opcjonalnymi metadanymi).

Twarz może mieć identyfikator, nazwę, miniaturę, inne metadane i listę jej wystąpień czasowych (na przykład: 00:00:05 – 00:00:10, 00:01:00 - 00:02:30 i 00:41:21 – 00:41:49.) Każde wystąpienie czasowe może mieć dodatkowe metadane. Na przykład współrzędne prostokąta twarzy (20 230 60,60).

|Wersja|Wersja kodu|
|---|---|
|sourceLanguage (język źródłowy)|Język źródłowy wideo (przy założeniu, że jest używany jeden język główny). W postaci ciągu [BCP-47.](https://tools.ietf.org/html/bcp47)|
|language|Język szczegółowych informacji (przetłumaczony na język źródłowy). W postaci ciągu [BCP-47.](https://tools.ietf.org/html/bcp47)|
|Zapis|Szczegółowe [informacje o transkrypcji.](#transcript)|
|Ocr|Szczegółowe [informacje o OCR.](#ocr)|
|keywords (słowa kluczowe)|Szczegółowe [informacje o słowach](#keywords) kluczowych.|
|bloki|Może zawierać jeden lub więcej [bloków](#blocks)|
|twarze/animowaneznaki|Szczegółowe [informacje o twarzach/animowanych obrazach.](#facesanimatedcharacters)|
|Etykiety|Szczegółowe [informacje o etykietach.](#labels)|
|Zdjęć|Szczegółowe [informacje o zrzutach.](#shots)|
|Marek|Szczegółowe [informacje o markach.](#brands)|
|audioEffects|[AudioEffects insights(Wgląd w dane audioEffects).](#audioeffects-public-preview)|
|Uczucia|Szczegółowe [informacje o tonacji.](#sentiments)|
|visualContentModeration|Szczegółowe [informacje o visualContentModeration.](#visualcontentmoderation)|
|textualContentModeration|Szczegółowe [informacje dotyczące textualContentModeration.](#textualcontentmoderation)|
|Emocje| Szczegółowe [informacje o emocjach.](#emotions)|
|Tematy|Szczegółowe [informacje dotyczące](#topics) tematów.|
|głośniki|Szczegółowe [informacje dla osób](#speakers) mówiących.|

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
Wystąpień|Lista zakresów czasu tego bloku.|

#### <a name="transcript"></a>Zapis

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator wiersza.|
|tekst|Sama transkrypcja.|
|ufność|Pewność dokładności transkrypcji.|
|speakerId|Identyfikator prelegenta.|
|language|Język transkrypcji. Przeznaczona do obsługi transkrypcji, w której każdy wiersz może mieć inny język.|
|Wystąpień|Lista zakresów czasu, w których pojawiła się ta linia. Jeśli wystąpienie jest transkrypcją, będzie mieć tylko 1 wystąpienie.|

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

#### <a name="ocr"></a>Ocr

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator wiersza OCR.|
|tekst|Tekst OCR.|
|ufność|Zaufanie do rozpoznawania.|
|language|Język OCR.|
|Wystąpień|Lista zakresów czasu, w których pojawił się ten OCR (ten sam OCR może wystąpić wiele razy).|
|wysokość|Wysokość prostokąta OCR|
|top (pierwsze)|Górna lokalizacja w px|
|left| Lokalizacja po lewej stronie w px|
|szerokość|Szerokość prostokąta OCR|

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
|ufność|Zaufanie do rozpoznawania słowa kluczowego.|
|language|Język słów kluczowych (po przetłumaczeniach).|
|Wystąpień|Lista zakresów czasu, w których pojawiło się to słowo kluczowe (słowo kluczowe może występować wiele razy).|

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

#### <a name="facesanimatedcharacters"></a>twarze/animowaneznaki

`animatedCharacters` Element zastępuje element `faces` w przypadku, gdy wideo zostało zindeksowane za pomocą modelu znaków animowanych. Odbywa się to przy użyciu modelu niestandardowego w Custom Vision, Video Indexer uruchamia go w ramki kluczowe.

Jeśli są obecne twarze (nie animowane), Video Indexer interfejsu API rozpoznawania twarzy na wszystkich ramkach wideo do wykrywania twarzy i osobistości.

|Nazwa|Opis|
|---|---|
|identyfikator|The face ID.|
|name|Nazwa twarzy. Może to być "Nieznany #0, zidentyfikowana osobistość lub wytrenowana osoba klienta.|
|ufność|Pewność identyfikacji twarzy.|
|description (opis)|Opis osobistości. |
|thumbnailId|Identyfikator miniatury tej twarzy.|
|knownPersonId|Jeśli jest to znana osoba, jej wewnętrzny identyfikator.|
|referenceId|Jeśli jest to osobistość Bing, jej identyfikator Bing.|
|typ odwołania|Obecnie wystarczy tylko Bing.|
|tytuł|Jeśli jest osobistą osobistością, jej tytuł (na przykład "Dyrektor naczelny firmy Microsoft").|
|Imageurl|Jeśli jest to osobistość, jej adres URL obrazu.|
|Wystąpień|Są to wystąpienia, w których twarz pojawiła się w danym zakresie czasu. Każde wystąpienie ma również thumbnailsId. |

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

#### <a name="labels"></a>Etykiety

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator etykiety.|
|name|Nazwa etykiety (na przykład "Komputer", "TV").|
|language|Język nazw etykiet (w przypadku tłumaczenia). BCP-47|
|Wystąpień|Lista zakresów czasu, w których pojawiła się ta etykieta (etykieta może pojawić się wiele razy). Każde wystąpienie ma pole ufności. |


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

#### <a name="scenes"></a>Sceny

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator sceny.|
|Wystąpień|Lista zakresów czasu tej sceny (scena może mieć tylko 1 wystąpienie).|

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

#### <a name="shots"></a>Zdjęć

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator zrzutu.|
|Klatek kluczowych|Lista ramek kluczowych w obrębie zrzutu (każda z nich ma identyfikator i listę zakresów czasu wystąpień). Każde wystąpienie ramki kluczowej ma pole thumbnailId, które zawiera identyfikator miniatury ramki klucza.|
|Wystąpień|Lista zakresów czasu tego zrzutu (zrzut może mieć tylko 1 wystąpienie).|

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

Nazwy firm i produktów wykryte w transkrypcji mowy na tekst i/lub wideo OCR. Nie obejmuje to wizualnego rozpoznawania marek ani wykrywania logo.

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator marki.|
|name|Nazwa marek.|
|referenceId | Sufiks adresu URL wikipedii marki. Na przykład sufiks "Target_Corporation" to sufiks [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) .
|referenceUrl | Adres URL Wikipedii marki, jeśli istnieje. Przykładowy adres URL to [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|description (opis)|Opis marek.|
|tags|Lista wstępnie zdefiniowanych tagów, które zostały skojarzone z tą marką.|
|ufność|Wartość ufności detektora Video Indexer marki (0–1).|
|Wystąpień|Lista zakresów czasu tej marki. Każde wystąpienie ma znak brandType, który wskazuje, czy ta marka pojawiła się w transkrypcji, czy w OCR.|

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
|CorrespondenceCount|Liczba esejów w filmie wideo.|
|SpeakerWordCount|Liczba słów na osobę mówiącą.|
|SpeakerNumberOfFragments|Ilość fragmentów, które ma prelegent w filmie wideo.|
|SpeakerLongestMonolog|Najdłuższy monolog prelegenta. Jeśli w monologu osoby mówiącej są wyciszenia, jest ona dołączona. Wyciszenie na początku i na końcu monologu jest usuwane.| 
|SpeakerTalkToListenRatio|Obliczenie jest oparte na czasie spędzonym na monologu prelegenta (bez ciszy między nimi) podzielonym przez łączny czas filmu wideo. Czas jest zaokrąglany do trzeciego separatora dziesiętnego.|

#### <a name="audioeffects-public-preview"></a>audioEffects (publiczna wersja zapoznawcza)

|Nazwa|Opis
|---|---|
|identyfikator|Identyfikator efektu audio|
|typ|Typ efektu audio|
|Wystąpień|Lista zakresów czasu, w których pojawił się ten efekt dźwiękowy. Każde wystąpienie ma pole ufności.|

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

#### <a name="sentiments"></a>Uczucia

Tonacji są agregowane według pola sentimentType (Pozytywna/Neutralna/Negatywna). Na przykład 0–0,1, 0.1–0.2.

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator tonacji.|
|averageScore |Średnia wszystkich wyników wszystkich wystąpień tego typu tonacji — pozytywna/neutralna/negatywna|
|Wystąpień|Lista zakresów czasu, w których pojawiła się ta tonacji.|
|typ tonacji |Typ może mieć wartość "Dodatnia", "Neutralna" lub "Ujemna".|

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

Blok visualContentModeration zawiera zakresy czasu, które Video Indexer potencjalnie zawierają treści dla dorosłych. Jeśli pole visualContentModeration jest puste, nie zostanie zidentyfikowana żadna zawartość dla dorosłych.

Filmy wideo, które zawierają treści dla dorosłych lub treści racją, mogą być dostępne tylko w przypadku wyświetlania prywatnego. Użytkownicy mogą przesłać żądanie przeglądu zawartości przez człowieka. W takim przypadku atrybut IsAdult będzie zawierać wynik przeglądu przez człowieka.

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator moderowania zawartości wizualizacji.|
|adultScore|Wynik dla dorosłych (od moderatora zawartości).|
|racyScore|Wynik emisyjny (z moderowania zawartości).|
|Wystąpień|Lista zakresów czasu, w których pojawiła się ta wizualizacja moderowania zawartości.|

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
|bannedWordsCount |Liczba zakazanych słów.|
|bannedWordsRatio |Stosunek całkowitej liczby wyrazów.|

#### <a name="emotions"></a>Emocje

Video Indexer rozpoznaje emocje na podstawie sygnału mowy i dźwięku. Zidentyfikowaną emocją może być: smutek, smutek, złości lub obawa.

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator emocji.|
|typ|Moment rozpoznawania emocji, który został zidentyfikowany na podstawie sygnału mowy i dźwięku. Emocje mogą być: smutek, smutek, złość lub obawa.|
|Wystąpień|Lista zakresów czasu, w których pojawiła się ta emocja.|

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

#### <a name="topics"></a>Tematy

Video Indexer wnioskuje z głównych tematów z transkrypcji. Jeśli to możliwe, uwzględniona jest taksonomia [IPTC](https://iptc.org/standards/media-topics/) drugiego poziomu. 

|Nazwa|Opis|
|---|---|
|identyfikator|Identyfikator tematu.|
|name|Nazwa tematu, na przykład: "Branża prawna".|
|referenceId|Tematy odzwierciedlają hierarchię tematów. Na przykład: "Opieka zdrowotna i zdrowotna/medycyna i opieka zdrowotna/medycyna".|
|ufność|Ocena ufności w zakresie [0,1]. Wyższy jest bardziej pewna pewności.|
|language|Język używany w temacie.|
|iptcName|Nazwa kodu nośnika IPTC, jeśli zostanie wykryty.|
|Wystąpień |Obecnie Video Indexer nie indeksuje tematu do przedziałów czasu, więc cały film wideo jest używany jako interwał.|

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
|identyfikator|Identyfikator prelegenta.|
|name|Nazwa osoby mówiącej w postaci "Numer osoby *<number>* mówiącej", na przykład: "Nazwa #1".|
|Wystąpień |Lista zakresów czasu, w których pojawiła się ta prelegentka.|

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

[Video Indexer Portal deweloperów](https://api-portal.videoindexer.ai)

Aby uzyskać informacje na temat osadzania widżetów w aplikacji, zobacz [Osadzanie Video Indexer w aplikacjach.](video-indexer-embed-widgets.md) 

