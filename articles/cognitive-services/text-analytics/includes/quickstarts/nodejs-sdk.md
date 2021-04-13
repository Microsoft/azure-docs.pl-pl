---
title: 'Szybki start: analiza tekstu klienta w wersji 3 dla Node.js | Microsoft Docs'
description: Wprowadzenie do biblioteki klienta analiza tekstu w wersji 3 dla Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/09/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: 3640a03f8ac814fec2823a761e651ab386438c5c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327660"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

[Dokumentacja referencyjna w wersji](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview)  |  3 [Kod źródłowy biblioteki w wersji](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  3 [Pakiet w wersji 3 (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [Przykłady w wersji 3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

[Dokumentacja referencyjna w wersji](/javascript/api/overview/azure/ai-text-analytics-readme)  |  3 [Kod źródłowy biblioteki w wersji](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  3 [Pakiet w wersji 3 (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [Przykłady w wersji 3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja programu [Node.js](https://nodejs.org/).
* Po utworzeniu subskrypcji platformy Azure utwórz zasób usługi analiza tekstu zasób analiza tekstu w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
    * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z interfejsem API analiza tekstu API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    * Możesz użyć warstwy cenowej Bezpłatna ( ), aby wypróbować usługę, i przejść później na warstwę płatną `F0` na użytek produkcji.
* Aby korzystać z funkcji Analizuj, musisz mieć zasób analiza tekstu z warstwą cenową Standardowa (S).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp 

cd myapp
```

Uruchom polecenie `npm init` , aby utworzyć aplikację node z `package.json` plikiem . 

```console
npm init
```
### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

Zainstaluj `@azure/ai-text-analytics` pakiety NPM:

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.5
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w [witrynie GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js)która zawiera przykłady kodu w tym przewodniku Szybki start. 


# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Zainstaluj `@azure/ai-text-analytics` pakiety NPM:

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w [witrynie GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js)która zawiera przykłady kodu w tym przewodniku Szybki start. 

---

Plik aplikacji `package.json` zostanie zaktualizowany o zależności.
Utwórz plik o nazwie `index.js` i dodaj następujący kod:

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

---

Utwórz zmienne dla klucza i punktu końcowego platformy Azure zasobu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Model obiektów

Klient analiza tekstu to `TextAnalyticsClient` obiekt, który uwierzytelnia się na platformie Azure przy użyciu twojego klucza. Klient udostępnia kilka metod analizowania tekstu w postaci pojedynczego ciągu lub partii.

Tekst jest wysyłany do interfejsu API jako lista , które są obiektami zawierającymi kombinację `documents` atrybutów , i w zależności od `dictionary` `id` `text` `language` użytej metody. Atrybut `text` przechowuje tekst do przeanalizowania w pochodzeniu , a atrybut może być `language` `id` dowolną wartością. 

Obiekt odpowiedzi to lista zawierająca informacje o analizie dla każdego dokumentu. 

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

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

Utwórz nowy obiekt `TextAnalyticsClient` z kluczem i punktem końcowym jako parametrami.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Utwórz nowy obiekt `TextAnalyticsClient` z kluczem i punktem końcowym jako parametrami.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

---

## <a name="sentiment-analysis"></a>Analiza tonacji

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `analyzeSentiment()` klienta i pobierz zwrócony `SentimentBatchResult` obiekt . Iteruj po liście wyników i wydrukuj identyfikator każdego dokumentu z wynikami ufności na poziomie dokumentu. Dla każdego dokumentu wynik zawiera tonacji na poziomie zdania wraz z przesunięciami, długością i wynikami ufności.

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

Uruchom kod za pomocą `node index.js` polecenia w oknie konsoli.

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

Aby wykonać analizę tonacji przy użyciu wyszukiwania opinii, utwórz tablicę ciągów zawierających dokument, który chcesz przeanalizować. Wywołaj metodę klienta `analyzeSentiment()` z flagą dodawania opcji `includeOpinionMining: true` i pobierz zwrócony `SentimentBatchResult` obiekt. Iteruj po liście wyników i wydrukuj identyfikator każdego dokumentu na poziomie dokumentu z wynikami ufności. Dla każdego dokumentu wynik zawiera nie tylko tonacji na poziomie zdania, jak powyżej, ale także tonacji na poziomie aspektu i opinii.

```javascript
async function sentimentAnalysisWithOpinionMining(client){

  const sentimentInput = [
    {
      text: "The food and service were unacceptable, but the concierge were nice",
      id: "0",
      language: "en"
    }
  ];
  const results = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

  for (let i = 0; i < results.length; i++) {
    const result = results[i];
    console.log(`- Document ${result.id}`);
    if (!result.error) {
      console.log(`\tDocument text: ${sentimentInput[i].text}`);
      console.log(`\tOverall Sentiment: ${result.sentiment}`);
      console.log("\tSentiment confidence scores:", result.confidenceScores);
      console.log("\tSentences");
      for (const { sentiment, confidenceScores, opinions } of result.sentences) {
        console.log(`\t- Sentence sentiment: ${sentiment}`);
        console.log("\t  Confidence scores:", confidenceScores);
        console.log("\t  Mined opinions");
        for (const { target, assessments } of opinions) {
          console.log(`\t\t- Target text: ${target.text}`);
          console.log(`\t\t  Target sentiment: ${target.sentiment}`);
          console.log("\t\t  Target confidence scores:", target.confidenceScores);
          console.log("\t\t  Target assessments");
          for (const { text, sentiment } of assessments) {
            console.log(`\t\t\t- Text: ${text}`);
            console.log(`\t\t\t  Sentiment: ${sentiment}`);
          }
        }
      }
    } else {
      console.error(`\tError: ${result.error}`);
    }
  }
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Uruchom kod za pomocą `node index.js` polecenia w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
- Document 0
        Document text: The food and service were unacceptable, but the concierge were nice
        Overall Sentiment: positive
        Sentiment confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
        Sentences
        - Sentence sentiment: positive
          Confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
          Mined opinions
                - Target text: food
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: service
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: concierge
                  Target sentiment: positive
                  Target confidence scores: { positive: 1, negative: 0 }
                  Target assessments
                        - Text: nice
                          Sentiment: positive
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `analyzeSentiment()` klienta i pobierz zwrócony `SentimentBatchResult` obiekt . Iteruj po liście wyników i wydrukuj identyfikator każdego dokumentu, tonacji na poziomie dokumentu z wynikami ufności. Dla każdego dokumentu wynik zawiera tonacji na poziomie zdania wraz z przesunięciami, długością i wynikami ufności.

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

Uruchom kod za pomocą `node index.js` polecenia w oknie konsoli.

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

---

## <a name="language-detection"></a>Wykrywanie języka

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `detectLanguage()` klienta i pobierz zwrócony . `DetectLanguageResultCollection` Następnie iteruj wyniki i wydrukuj identyfikator każdego dokumentu przy użyciu odpowiedniego języka podstawowego.

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

Uruchom kod za pomocą `node index.js` polecenia w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `detectLanguage()` klienta i pobierz zwrócony . `DetectLanguageResultCollection` Następnie iteruj wyniki i wydrukuj identyfikator każdego dokumentu przy użyciu odpowiedniego języka podstawowego.

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

Uruchom kod za pomocą `node index.js` polecenia w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Primary Language French
```

---

## <a name="named-entity-recognition-ner"></a>rozpoznawanie jednostek nazwanych (NER)

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

> [!NOTE]
> W wersji `3.1` :
> * Łączenie jednostek jest oddzielnym żądaniem niż NER.

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `recognizeEntities()` klienta i pobierz `RecognizeEntitiesResult` obiekt . Iteruj po liście wyników i wydrukuj nazwę jednostki, typ, podtyp, przesunięcie, długość i wynik.

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

Uruchom kod za pomocą `node index.js` polecenia w oknie konsoli.

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

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `recognizeLinkedEntities()` klienta i pobierz `RecognizeLinkedEntitiesResult` obiekt . Iteruj po liście wyników i wydrukuj nazwę jednostki, identyfikator, źródło danych, adres URL i dopasowania. Każdy obiekt w `matches` tablicy będzie zawierać przesunięcie, długość i wynik dla tego dopasowania.

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

Uruchom kod za pomocą `node index.js` polecenia w oknie konsoli.

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

### <a name="personally-identifying-information-pii-recognition"></a>Rozpoznawanie danych osobowych

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `recognizePiiEntities()` klienta i pobierz `RecognizePIIEntitiesResult` obiekt . Iteruj po liście wyników i wydrukuj nazwę jednostki, typ i wynik.

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

Uruchom kod za pomocą `node index.js` polecenia w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

> [!NOTE]
> W wersji `3.0` :
> * Łączenie jednostek jest oddzielnym żądaniem niż NER.

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `recognizeEntities()` klienta i pobierz `RecognizeEntitiesResult` obiekt . Iteruj po liście wyników i wydrukuj nazwę jednostki, typ, podtyp, przesunięcie, długość i wynik.

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

Uruchom kod za pomocą `node index.js` polecenia w oknie konsoli.

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

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `recognizeLinkedEntities()` klienta i pobierz `RecognizeLinkedEntitiesResult` obiekt . Iteruj po liście wyników i wydrukuj nazwę jednostki, identyfikator, źródło danych, adres URL i dopasowania. Każdy obiekt w `matches` tablicy będzie zawierać przesunięcie, długość i wynik dla tego dopasowania.

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

Uruchom kod za pomocą `node index.js` polecenia w oknie konsoli.

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


---

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `extractKeyPhrases()` klienta i pobierz zwrócony `ExtractKeyPhrasesResult` obiekt. Iteruj wyniki i wydrukuj identyfikator każdego dokumentu oraz wszystkie wykryte frazy kluczowe.

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

Uruchom kod za pomocą `node index.js` polecenia w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `extractKeyPhrases()` klienta i pobierz zwrócony `ExtractKeyPhrasesResult` obiekt. Iteruj wyniki i wydrukuj identyfikator każdego dokumentu oraz wszystkie wykryte frazy kluczowe.

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

Uruchom kod za pomocą `node index.js` polecenia w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```


---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Asynchroniczne używanie interfejsu API z operacją Analizowanie

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

> [!CAUTION]
> Aby korzystać z operacji analizy, należy użyć zasobu analiza tekstu z warstwą cenową Standardowa (S).  

Utwórz nową funkcję o nazwie `analyze_example()` , która wywołuje funkcję `beginAnalyze()` . Wynikiem będzie długotrwała operacja, która zostanie sondowana w celu wyszukiwania wyników.

```javascript
async function analyze_example(client) {
  const documents = [
    "Microsoft was founded by Bill Gates and Paul Allen.",
  ];

  const actions = {
    recognizeEntitiesActions: [{ modelVersion: "latest" }],
  };
  const poller = await client.beginAnalyzeBatchActions(documents, actions, "en");

  console.log(
    `The analyze batch actions operation was created on ${poller.getOperationState().createdOn}`
  );
  console.log(
    `The analyze batch actions operation results will expire on ${
      poller.getOperationState().expiresOn
    }`
  );
  const resultPages = await poller.pollUntilDone();
  for await (const page of resultPages) {
    const entitiesAction = page.recognizeEntitiesResults[0];
    if (!entitiesAction.error) {
      for (const doc of entitiesAction.results) {
        console.log(`- Document ${doc.id}`);
        if (!doc.error) {
          console.log("\tEntities:");
          for (const entity of doc.entities) {
            console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
          }
        } else {
          console.error("\tError:", doc.error);
        }
      }
    }
  }
}

analyze_example(textAnalyticsClient);
```

### <a name="output"></a>Dane wyjściowe

```console
The analyze batch actions operation was created on Fri Mar 12 2021 09:53:49 GMT-0800 (Pacific Standard Time)
The analyze batch actions operation results will expire on Sat Mar 13 2021 09:53:49 GMT-0800 (Pacific Standard Time)
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
```

Za pomocą operacji Analizowanie można również wykrywać dane pii, rozpoznawać połączone jednostki i wyodrębniać frazy kluczowe. Zapoznaj się z tematem Analyze samples for JavaScript and TypeScript (Analizowanie przykładów [dla języków JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript) [i TypeScript)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/typescript/src) w witrynie GitHub.

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Ta funkcja nie jest dostępna w wersji 3.0.

---

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```console
node index.js
```
