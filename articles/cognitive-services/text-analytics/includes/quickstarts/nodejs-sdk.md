---
title: 'Szybki Start: analiza tekstua Biblioteka kliencka v3 dla Node.js | Microsoft Docs'
description: Rozpocznij pracę z biblioteką kliencką analiza tekstu v3 dla Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/20/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: adfe6282d4a7d2cc35b4f5c71ecb67e6cda641a8
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090737"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/version-3-1)

Dokumentacja referencyjna [v3](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview)  |  kod źródłowy biblioteki [v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  [pakiet v3 (npm)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [przykłady wersji 3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Dokumentacja referencyjna [v3](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-latest)  |  kod źródłowy biblioteki [v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  [pakiet v3 (npm)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [przykłady wersji 3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Dokumentacja referencyjna w [wersji 2](/javascript/api/@azure/cognitiveservices-textanalytics)  |  kod źródłowy biblioteki [v2](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics)  |  [Pakiet v2 (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics)  |  [przykłady w wersji 2](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja [Node.js](https://nodejs.org/).
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" Utwórz zasób analiza tekstu "  target="_blank"> utwórz zasób analiza tekstu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API analizy tekstu. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* Aby można było korzystać z funkcji Analizuj, potrzebny jest zasób analiza tekstu z warstwą cenową standardowa.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp 

cd myapp
```

Uruchom `npm init` polecenie, aby utworzyć aplikację Node z `package.json` plikiem. 

```console
npm init
```
### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

# <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/version-3-1)

Zainstaluj `@azure/ai-text-analytics` pakiety npm:

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.3
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć [w usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), która zawiera przykłady kodu w tym przewodniku Szybki Start. 


# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Zainstaluj `@azure/ai-text-analytics` pakiety npm:

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć [w usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), która zawiera przykłady kodu w tym przewodniku Szybki Start. 

# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Zainstaluj `@azure/cognitiveservices-textanalytics` pakiety npm:

```console
npm install --save @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć [w usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js), która zawiera przykłady kodu w tym przewodniku Szybki Start. 

---

`package.json`Plik aplikacji zostanie zaktualizowany z zależnościami.
Utwórz plik o nazwie `index.js` i Dodaj następujące elementy:

# <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

```javascript
"use strict";

const os = require("os");
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```
---

Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Model obiektów

Klient analiza tekstu jest `TextAnalyticsClient` obiektem, który jest uwierzytelniany na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu, w postaci pojedynczego ciągu lub partii.

Tekst jest wysyłany do interfejsu API jako lista `documents` obiektów, które są `dictionary` obiektami zawierającymi kombinację `id` atrybutów, i, w `text` zależności od `language` używanej metody. Ten `text` atrybut zawiera tekst, który ma być analizowany w pochodzeniu `language` i `id` może być dowolną wartością. 

Obiekt Response jest listą zawierającą informacje o analizie dla każdego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

* [Uwierzytelnianie klienta](#client-authentication)
* [Analiza tonacji](#sentiment-analysis) 
* [Wyszukiwanie opinii](#opinion-mining)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek nazwanych](#named-entity-recognition-ner)
* [Łączenie jednostek](#entity-linking)
* Dane osobowe
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="client-authentication"></a>Uwierzytelnianie klienta

# <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/version-3-1)

Utwórz nowy `TextAnalyticsClient` obiekt z kluczem i punktem końcowym jako parametry.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Utwórz nowy `TextAnalyticsClient` obiekt z kluczem i punktem końcowym jako parametry.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Utwórz nowy obiekt [TextAnalyticsClient](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) z `credentials` i `endpoint` jako parametr.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analiza tonacji

# <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/version-3-1)

Utwórz tablicę ciągów zawierających dokument, który chcesz przeanalizować. Wywołaj metodę klienta `analyzeSentiment()` i Pobierz zwracany `SentimentBatchResult` obiekt. Wykonaj iterację na liście wyników i wydrukuj każdy dokument o IDENTYFIKATORze, tonacji na poziomie dokumentu z wynikami pewności. Dla każdego dokumentu wynik zawiera tonacji na poziomie zdania wraz z przesunięciami, długością i wynikami pewności.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

### <a name="opinion-mining"></a>Wyszukiwanie opinii

Aby przeprowadzić analizę tonacji z opinią wyszukiwania, Utwórz tablicę ciągów zawierających dokument, który chcesz analizować. Wywołaj metodę klienta `analyzeSentiment()` z dodawaniem flagi opcji `includeOpinionMining: true` i Pobierz zwracany `SentimentBatchResult` obiekt. Wykonaj iterację na liście wyników i wydrukuj każdy dokument o IDENTYFIKATORze, tonacji na poziomie dokumentu z wynikami pewności. W przypadku każdego dokumentu wynik zawiera nie tylko tonacji na poziomie zdania, jak powyżej, ale również aspekt i poziom opinii tonacji.

```javascript
async function sentimentAnalysisWithOpinionMining(client){

    const sentimentInput = [
        {
            text: "The food and service were unacceptable, but the concierge were nice",
            id: "0",
            language: "en"
        }
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
            console.log("\tMined opinions");
            for (const { aspect, opinions } of sentence.minedOpinions) {
                console.log(`\t\tAspect text: ${aspect.text}`);
                console.log(`\t\tAspect sentiment: ${aspect.sentiment}`);
                console.log(`\t\tAspect Positive: ${aspect.confidenceScores.positive.toFixed(2)} \tNegative: ${aspect.confidenceScores.negative.toFixed(2)}`);
                console.log("\t\tAspect opinions:");
                for (const { text, sentiment, confidenceScores } of opinions) {
                    console.log(`\t\tOpinion text: ${text}`);
                    console.log(`\t\tOpinion sentiment: ${sentiment}`);
                    console.log(`\t\tOpinion Positive: ${confidenceScores.positive.toFixed(2)} \tNegative: ${confidenceScores.negative.toFixed(2)}`);
                }
            }
        });
    });
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 0.84  Negative: 0.16  Neutral: 0.00
        Sentences Sentiment(1):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 0.84  Negative: 0.16  Neutral: 0.00
        Mined opinions
                Aspect text: food
                Aspect sentiment: negative
                Aspect Positive: 0.01   Negative: 0.99
                Aspect opinions:
                Opinion text: unacceptable
                Opinion sentiment: negative
                Opinion Positive: 0.01  Negative: 0.99
                Aspect text: service
                Aspect sentiment: negative
                Aspect Positive: 0.01   Negative: 0.99
                Aspect opinions:
                Opinion text: unacceptable
                Opinion sentiment: negative
                Opinion Positive: 0.01  Negative: 0.99
                Aspect text: concierge
                Aspect sentiment: positive
                Aspect Positive: 1.00   Negative: 0.00
                Aspect opinions:
                Opinion text: nice
                Opinion sentiment: positive
                Opinion Positive: 1.00  Negative: 0.00
```

# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Utwórz tablicę ciągów zawierających dokument, który chcesz przeanalizować. Wywołaj metodę klienta `analyzeSentiment()` i Pobierz zwracany `SentimentBatchResult` obiekt. Wykonaj iterację na liście wyników i wydrukuj każdy dokument o IDENTYFIKATORze, tonacji na poziomie dokumentu z wynikami pewności. Dla każdego dokumentu wynik zawiera tonacji na poziomie zdania wraz z przesunięciami, długością i wynikami pewności.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Utwórz listę obiektów słownika zawierającego dokumenty, które chcesz przeanalizować. Wywołaj metodę [tonacji ()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) klienta i otrzymaj zwrócony [SentimentBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Wykonaj iterację na liście wyników, a następnie wydrukuj identyfikator każdego dokumentu i ocenę tonacji. Wynik zbliżony do 0 wskazuje negatywną tonacji, natomiast wynik zbliżony do 1 wskazuje pozytywny tonacji.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Wykrywanie języka

# <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/version-3-1)

Utwórz tablicę ciągów zawierających dokument, który chcesz przeanalizować. Wywołaj metodę klienta `detectLanguage()` i otrzymaj zwracaną wartość `DetectLanguageResultCollection` . Następnie można wykonać iterację w wynikach i wydrukować każdy dokument o IDENTYFIKATORze przy użyciu odpowiedniego języka podstawowego.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Utwórz tablicę ciągów zawierających dokument, który chcesz przeanalizować. Wywołaj metodę klienta `detectLanguage()` i otrzymaj zwracaną wartość `DetectLanguageResultCollection` . Następnie można wykonać iterację w wynikach i wydrukować każdy dokument o IDENTYFIKATORze przy użyciu odpowiedniego języka podstawowego.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Primary Language French
```

# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Utwórz listę obiektów słownika zawierających dokumenty. Wywołaj metodę [detectLanguage ()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) klienta i otrzymaj zwrócony [LanguageBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Następnie można wykonać iterację w wynikach i wydrukować identyfikatory każdego dokumentu oraz język.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)

# <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/version-3-1)

> [!NOTE]
> W wersji `3.1` :
> * Łączenie jednostek to oddzielne żądanie niż NER.

Utwórz tablicę ciągów zawierających dokument, który chcesz przeanalizować. Wywołaj metodę klienta `recognizeEntities()` i Pobierz `RecognizeEntitiesResult` obiekt. Wykonaj iterację na liście wyników, a następnie wydrukuj nazwę jednostki, typ, podtyp, przesunięcie, długość i wynik.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Łączenie jednostek

Utwórz tablicę ciągów zawierających dokument, który chcesz przeanalizować. Wywołaj metodę klienta `recognizeLinkedEntities()` i Pobierz `RecognizeLinkedEntitiesResult` obiekt. Wykonaj iterację na liście wyników, a następnie wydrukuj nazwę jednostki, identyfikator, źródło danych, adres URL i dopasowania. Każdy obiekt w `matches` tablicy będzie zawierać przesunięcie, długość i wynik dla tego dopasowania.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

### <a name="personally-identifying-information-pii-recognition"></a>Rozpoznawanie tożsamości użytkownika (dane osobowe)

Utwórz tablicę ciągów zawierających dokument, który chcesz przeanalizować. Wywołaj metodę klienta `recognizePiiEntities()` i Pobierz `RecognizePIIEntitiesResult` obiekt. Wykonaj iterację na liście wyników i wydrukuj nazwę jednostki, typ i wynik.

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

> [!NOTE]
> W wersji `3.0` :
> * Łączenie jednostek to oddzielne żądanie niż NER.

Utwórz tablicę ciągów zawierających dokument, który chcesz przeanalizować. Wywołaj metodę klienta `recognizeEntities()` i Pobierz `RecognizeEntitiesResult` obiekt. Wykonaj iterację na liście wyników, a następnie wydrukuj nazwę jednostki, typ, podtyp, przesunięcie, długość i wynik.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Łączenie jednostek

Utwórz tablicę ciągów zawierających dokument, który chcesz przeanalizować. Wywołaj metodę klienta `recognizeLinkedEntities()` i Pobierz `RecognizeLinkedEntitiesResult` obiekt. Wykonaj iterację na liście wyników, a następnie wydrukuj nazwę jednostki, identyfikator, źródło danych, adres URL i dopasowania. Każdy obiekt w `matches` tablicy będzie zawierać przesunięcie, długość i wynik dla tego dopasowania.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

> [!NOTE]
> W wersji 2,1, konsolidacja jednostki jest uwzględniona w odpowiedzi NER.

Utwórz listę obiektów zawierającą dokumenty. Wywołaj metodę [jednostki klienta ()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) i Pobierz obiekt [EntitiesBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) . Wykonaj iterację na liście wyników i wydrukuj identyfikator każdego dokumentu. Dla każdej wykrytej jednostki Wydrukuj jej nazwę witryny Wikipedia, typ i podtyp (jeśli istnieje), a także lokalizacje w oryginalnym tekście.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

# <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/version-3-1)

Utwórz tablicę ciągów zawierających dokument, który chcesz przeanalizować. Wywołaj metodę klienta `extractKeyPhrases()` i Pobierz zwracany `ExtractKeyPhrasesResult` obiekt. Wykonaj iterację w wynikach i wydrukuj identyfikatory każdego dokumentu oraz wszystkie wykryte frazy klucza.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Utwórz tablicę ciągów zawierających dokument, który chcesz przeanalizować. Wywołaj metodę klienta `extractKeyPhrases()` i Pobierz zwracany `ExtractKeyPhrasesResult` obiekt. Wykonaj iterację w wynikach i wydrukuj identyfikatory każdego dokumentu oraz wszystkie wykryte frazy klucza.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Utwórz listę obiektów zawierającą dokumenty. Wywołaj metodę [frazy kluczowej klienta ()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) i Pobierz zwracany obiekt     [KeyPhraseBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) . Wykonaj iterację w wynikach i wydrukuj identyfikatory każdego dokumentu oraz wszystkie wykryte frazy klucza.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Używanie interfejsu API asynchronicznie z operacją analizy

# <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/version-3-1)

> [!CAUTION]
> Aby użyć operacji analizowania, należy użyć zasobu analiza tekstu z warstwą cenową standardowa (S).  

Utwórz nową funkcję o nazwie `analyze_example()` , która wywołuje `beginAnalyze()` funkcję. Wynik będzie długotrwałą operacją, która będzie sondowana o wyniki.

```javascript
const documents = [
  "Microsoft was founded by Bill Gates and Paul Allen.",
];

async function analyze_example(client) {
  console.log("== Analyze Sample ==");

  const tasks = {
    entityRecognitionTasks: [{ modelVersion: "latest" }]
  };
  const poller = await client.beginAnalyze(documents, tasks);
  const resultPages = await poller.pollUntilDone();

  for await (const page of resultPages) {
    const entitiesResults = page.entitiesRecognitionResults![0];
    for (const doc of entitiesResults) {
      console.log(`- Document ${doc.id}`);
      if (!doc.error) {
        console.log("\tEntities:");
        for (const entity of doc.entities) {
          console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
        }
      } else {
        console.error("  Error:", doc.error);
      }
    }
  }
}

analyze_example(textAnalyticsClient);
```

### <a name="output"></a>Dane wyjściowe

```console
== Analyze Sample ==
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
```

Można również użyć operacji Analizuj, aby wykrywać dane OSOBowe i wyodrębnianie kluczowych fraz. Zobacz Analizuj przykłady dla [języków JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/javascript/beginAnalyze.js) i [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/typescript/src/beginAnalyze.ts) w serwisie GitHub.

# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Ta funkcja jest niedostępna w wersji 3,0.

# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Ta funkcja jest niedostępna w wersji 2,1.

---

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```console
node index.js
```
