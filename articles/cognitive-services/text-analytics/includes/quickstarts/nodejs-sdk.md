---
title: 'Szybki start: biblioteka klienta analizy tekstu w wersji 3 dla pliku Node.js | Dokumenty firmy Microsoft'
description: Wprowadzenie do biblioteki klienta analizy tekstu w wersji 3 dla pliku Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 1414d86577e5aa17cb42762403b3767948c1e30c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642906"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

[v3 Dokumentacja](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [referencyjna v3 Kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | źródłowy biblioteki[v3 Pakiet (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3 Przykłady](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

[v2 Dokumentacja](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [referencyjna v2 Kod](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | źródłowy biblioteki[v2 Pakiet (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2 Przykłady](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Bieżąca wersja [pliku Node.js](https://nodejs.org/).
* Po utworzeniu subskrypcji <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="platformy Azure utwórz"  target="_blank">zasób <span class="docon docon-navigate-external x-hidden-focus"></span> </a> analizy tekstu w witrynie Azure portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu kliknij przycisk **Przejdź do zasobu**.
    * Potrzebny będzie klucz i punkt końcowy z zasobu, który tworzysz, aby połączyć aplikację z interfejsem API analizy tekstu. Wklej klucz i punkt końcowy do kodu poniżej w dalszej części przewodnika Szybki start.
    * Można użyć bezpłatnej warstwy`F0`cenowej ( ), aby wypróbować usługę, a następnie uaktualnić do warstwy płatnej dla produkcji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog aplikacji i przejdź do niego. 

```console
mkdir myapp 

cd myapp
```

Uruchom `npm init` polecenie, aby utworzyć aplikację `package.json` węzła z plikiem. 

```console
npm init
```
### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Zainstaluj `@azure/ai-text-analytics` pakiety NPM:

```console
npm install --save @azure/ai-text-analytics@1.0.0-preview.4
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu naraz? Można go znaleźć [na GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), który zawiera przykłady kodu w tym przewodniku Szybki start. 

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Zainstaluj `@azure/cognitiveservices-textanalytics` pakiety NPM:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu naraz? Można go znaleźć [na GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js), który zawiera przykłady kodu w tym przewodniku Szybki start. 

---

`package.json` Plik aplikacji zostanie zaktualizowany o zależności.
Utwórz plik `index.js` o nazwie i dodaj następujące elementy:

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

Tworzenie zmiennych dla punktu końcowego i klucza platformy Azure zasobu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Model obiektu

Klient analizy tekstu `TextAnalyticsClient` jest obiektem, który uwierzytelnia się na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu, jako pojedynczy ciąg lub partii.

Tekst jest wysyłany do interfejsu `documents`API `dictionary` jako lista , `id`które `text`są `language` obiektami zawierającymi kombinację , i atrybutów w zależności od zastosowanej metody. Atrybut `text` przechowuje tekst do analizy w `language`źródle i `id` może być dowolną wartością. 

Obiekt odpowiedzi jest listą zawierającą informacje analityczne dla każdego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

* [Uwierzytelnianie klienta](#client-authentication)
* [Analiza tonacji](#sentiment-analysis) 
* [Wykrywanie języka](#language-detection)
* [Uznanie nazwanej encji](#named-entity-recognition-ner)
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="client-authentication"></a>Uwierzytelnianie klienta

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz `TextAnalyticsClient` nowy obiekt z kluczem i punktem końcowym jako parametrami.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Utwórz nowy [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) obiektu z `credentials` i `endpoint` jako parametr.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analiza tonacji

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz tablicę ciągów zawierających dokument, który chcesz analizować. Wywołanie `analyzeSentiment()` metody klienta i uzyskać `SentimentBatchResult` zwrócony obiekt. Iteruje przez listę wyników i wydrukuj identyfikator każdego dokumentu, poziom dokumentu tonacji z wynikami zaufania. Dla każdego dokumentu wynik zawiera tonację poziomu zdania wraz z przesunięciami, długością i wynikami zaufania.

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

Uruchom kod `node index.js` w oknie konsoli.

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

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Utwórz listę obiektów słownika zawierającą dokumenty, które chcesz analizować. Wywołanie metody [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) klienta i uzyskać [zwrócony SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Iteruje listę wyników i wydrukuj identyfikator i wynik tonacji każdego dokumentu. Wynik bliższy 0 wskazuje na negatywne nastroje, podczas gdy wynik bliższy 1 wskazuje na pozytywne nastroje.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Uruchom kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Wykrywanie języka

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz tablicę ciągów zawierających dokument, który chcesz analizować. Wywołanie `detectLanguage()` metody klienta i uzyskać `DetectLanguageResultCollection`zwrócone . Następnie iteruj przez wyniki i wydrukuj identyfikator każdego dokumentu za pomocą odpowiedniego języka podstawowego.

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

Uruchom kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Utwórz listę obiektów słownikowych zawierających dokumenty. Wywołanie klienta [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) metoda i uzyskać zwrócony [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Następnie iteruj przez wyniki i wydrukuj identyfikator i język każdego dokumentu.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Uruchom kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznawanie nazwanych jednostek (NER)

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

> [!NOTE]
> W `3.0-preview`wersji :
> * Łączenie jednostek jest osobnym żądaniem niż NER.

Utwórz tablicę ciągów zawierających dokument, który chcesz analizować. Wywołanie `recognizeEntities()` metody klienta i `RecognizeEntitiesResult` uzyskać obiekt. Iteracja za pośrednictwem listy wyników i wydrukuj nazwę encji, typ, podtyp, przesunięcie, długość i wynik.

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
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Uruchom kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 1
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.67
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.81
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: interpreters      Category: PersonType    Subcategory: N/A
        Score: 0.6
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.96
        Name: Redmond   Category: Location      Subcategory: GPE
        Score: 0.09
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.31
```

## <a name="entity-linking"></a>Łączenie jednostek

Utwórz tablicę ciągów zawierających dokument, który chcesz analizować. Wywołanie `recognizeLinkedEntities()` metody klienta i `RecognizeLinkedEntitiesResult` uzyskać obiekt. Iteruje listę wyników i wydrukuj nazwę jednostki, identyfikator, źródło danych, adres URL i dopasowania. Każdy obiekt `matches` w tablicy będzie zawierać przesunięcie, długość i wynik dla tego dopasowania.

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
                console.log(`\t\tText: ${match.text} \tScore: ${match.score.toFixed(2)}`);
            });
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Uruchom kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.78
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.55
                Text: Gates     Score: 0.55
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.53
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.47
                Text: Microsoft         Score: 0.47
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.25
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.28
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

> [!NOTE]
> W wersji 2.1 łączenie jednostek jest zawarte w odpowiedzi NER.

Utwórz listę obiektów zawierającą dokumenty. Wywołanie metody [entities()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) klienta i uzyskać [obiekt EntitiesBatchResult.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) Iteruje listę wyników i wydrukuj identyfikator każdego dokumentu. Dla każdej wykrytej jednostki wydrukuj jej nazwę wikipedii, typ i podtypy (jeśli istnieje), a także lokalizacje w oryginalnym tekście.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Uruchom kod `node index.js` w oknie konsoli.

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

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz tablicę ciągów zawierających dokument, który chcesz analizować. Wywołanie `extractKeyPhrases()` metody klienta i uzyskać `ExtractKeyPhrasesResult` zwrócony obiekt. Iteruje wyniki i wydrukuj identyfikator każdego dokumentu oraz wszelkie wykryte frazy kluczowe.

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

Uruchom kod `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Utwórz listę obiektów zawierającą dokumenty. Wywołanie metody [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) klienta i uzyskać [zwrócony KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) obiektu. Iteruje wyniki i wydrukuj identyfikator każdego dokumentu oraz wszelkie wykryte frazy kluczowe.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Uruchom kod `node index.js` w oknie konsoli.

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

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `node` pomocą polecenia w pliku szybkiego startu.

```console
node index.js
```
