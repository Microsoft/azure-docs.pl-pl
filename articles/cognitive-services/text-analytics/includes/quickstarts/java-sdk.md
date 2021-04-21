---
title: 'Szybki start: analiza tekstu klienta w wersji 3 dla języka Java | Microsoft Docs'
description: Rozpoczynanie pracy z biblioteką klienta analiza tekstu w wersji 3 dla języka Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: d39f40d4059b43246f523b4d01ca92c0360bc574
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765114"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

[Dokumentacja referencyjna](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-preview)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.1.0-beta.5/sdk/textanalytics/azure-ai-textanalytics)  |  [Pakiet](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.5)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.1.0-beta.5/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

[Dokumentacja referencyjna](/java/api/overview/azure/ai-textanalytics-readme)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics)  |  [Pakiet](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* [Zestaw Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) w wersji 8 lub nowszej
* Po utworzeniu subskrypcji platformy Azure utwórz zasób usługi analiza tekstu zasób analiza tekstu w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> klucz i punkt </a> końcowy.  Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
    * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z interfejsem API analiza tekstu API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    * Możesz użyć warstwy cenowej Bezpłatna ( ), aby wypróbować usługę, i przejść później na warstwę płatną `F0` na użytek produkcji.
* Aby korzystać z funkcji Analizuj, musisz mieć zasób analiza tekstu z warstwą cenową Standardowa (S).

## <a name="setting-up"></a>Konfigurowanie

### <a name="add-the-client-library"></a>Dodawanie biblioteki klienta

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

Utwórz projekt Maven w preferowanym środowisku IDE lub środowisku projektowym. Następnie dodaj następującą zależność do plikupom.xml *projektu.* Składnię implementacji dla innych narzędzi [kompilacji można](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.5) znaleźć w trybie online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0-beta.5</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Utwórz projekt Maven w preferowanym środowisku IDE lub środowisku projektowym. Następnie dodaj następującą zależność do plikupom.xml *projektu.* Składnię implementacji dla innych narzędzi [kompilacji](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) można znaleźć w trybie online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w [witrynie GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java)która zawiera przykłady kodu w tym przewodniku Szybki start. 

---

Utwórz plik Java o nazwie `TextAnalyticsSamples.java` . Otwórz plik i dodaj następujące `import` instrukcje:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

W pliku java dodaj nową klasę i dodaj klucz i punkt końcowy zasobu platformy Azure, jak pokazano poniżej.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Dodaj następującą metodę główną do klasy . Metody wywoływane w tym miejscu zdefiniuje się później.

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisWithOpinionMiningExample(client)
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
        AnalyzeOperationExample(client)
}
```

---


## <a name="object-model"></a>Model obiektów

Klient analiza tekstu to obiekt, który uwierzytelnia się na platformie Azure przy użyciu klucza i udostępnia funkcje do akceptowania tekstu jako pojedynczych ciągów `TextAnalyticsClient` lub jako partii. Tekst do interfejsu API można wysyłać synchronicznie lub asynchronicznie. Obiekt odpowiedzi będzie zawierać informacje analityczne dla każdego dokumentu, który wyślesz. 

## <a name="code-examples"></a>Przykłady kodu

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis) 
* [Wyszukiwanie opinii](#opinion-mining)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie nazwanych jednostek](#named-entity-recognition-ner)
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz metodę , aby utworzyć obiekt z kluczem i punktem `TextAnalyticsClient` końcowym dla zasobu analiza tekstu zasobów. Ten przykład jest taki sam dla wersji 3.0 i 3.1 interfejsu API.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


W metodzie programu `main()` wywołaj metodę uwierzytelniania, aby utworzyć wystąpienia klienta.

## <a name="sentiment-analysis"></a>Analiza tonacji

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

> [!NOTE]
> W wersji `3.1` :
> * analiza tonacji analizę wyszukiwania opinii, która jest opcjonalną flagą. 
> * Wyszukiwanie opinii zawiera opinie na poziomie aspektu i opinii. 

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `sentimentAnalysisExample()` i wywołującą jego `analyzeSentiment()` funkcję. Zwrócony `AnalyzeSentimentResult` obiekt będzie zawierać elementy `documentSentiment` i , jeśli to się `sentenceSentiments` powiedzie, lub element , jeśli `errorMessage` nie. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

### <a name="opinion-mining"></a>Wyszukiwanie opinii

Aby przeprowadzić analizę tonacji za pomocą wyszukiwania opinii, utwórz nową funkcję o nazwie , która przyjmuje utworzonego wcześniej klienta i wywołaj jego funkcję za pomocą `sentimentAnalysisWithOpinionMiningExample()` `analyzeSentiment()` obiektu opcji ustawienia `AnalyzeSentimentOptions` . Zwrócony `AnalyzeSentimentResult` obiekt będzie zawierać elementy `documentSentiment` i , jeśli to się `sentenceSentiments` powiedzie, lub element , jeśli `errorMessage` nie. 


```java
 static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
 {
     // The document that needs be analyzed.
     String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

     System.out.printf("Document = %s%n", document);

     AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
     final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
     SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
     System.out.printf(
             "Recognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
             documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());


     documentSentiment.getSentences().forEach(sentenceSentiment -> {
         SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
         System.out.printf("\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                 sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
         sentenceSentiment.getOpinions().forEach(opinion -> {
             TargetSentiment targetSentiment = opinion.getTarget();
             System.out.printf("\t\tTarget sentiment: %s, target text: %s%n", targetSentiment.getSentiment(),
                     targetSentiment.getText());
             for (AssessmentSentiment assessmentSentiment : opinion.getAssessments()) {
                 System.out.printf("\t\t\t'%s' assessment sentiment because of \"%s\". Is the assessment negated: %s.%n",
                         assessmentSentiment.getSentiment(), assessmentSentiment.getText(), assessmentSentiment.isNegated());
             }
         });
     });
 }
```

### <a name="output"></a>Dane wyjściowe

```console
Document = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: atmosphere
            'negative' assessment sentiment because of "bad". Is the assessment negated: false.
    Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: Staff
            'negative' assessment sentiment because of "friendly". Is the assessment negated: true.
            'negative' assessment sentiment because of "helpful". Is the assessment negated: true.
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `sentimentAnalysisExample()` i wywołującą jego `analyzeSentiment()` funkcję. Zwrócony `AnalyzeSentimentResult` obiekt będzie zawierać elementy `documentSentiment` i , jeśli to się `sentenceSentiments` powiedzie, lub element , jeśli `errorMessage` nie. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

---

## <a name="language-detection"></a>Wykrywanie języka

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `detectLanguageExample()` i wywołującą jego `detectLanguage()` funkcję. Zwrócony obiekt będzie zawierać wykryty język podstawowy, listę innych języków wykrytych w przypadku powodzenia lub , jeśli `DetectLanguageResult` `errorMessage` nie. Ten przykład jest taki sam dla wersji 3.0 i 3.1 interfejsu API.

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić języki na podstawie danych wejściowych. Możesz użyć `countryHint` parametru , aby określić 2-literowy kod kraju. Domyślnie interfejs API używa wartości "US" jako domyślnej wartości countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `countryHint = ""` . Aby ustawić inną wartość domyślną, ustaw `TextAnalyticsClientOptions.DefaultCountryHint` właściwość i przekaż ją podczas inicjowania klienta.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>Dane wyjściowe

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```

## <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

> [!NOTE]
> W wersji `3.1` :
> * Ner obejmuje oddzielne metody wykrywania danych osobowych. 
> * Łączenie jednostek jest oddzielnym żądaniem niż NER.

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `recognizeEntitiesExample()` i wywołującą jego `recognizeEntities()` funkcję. Zwrócony `CategorizedEntityCollection` obiekt będzie zawierać listę , jeśli się `CategorizedEntity` powiedzie, lub , `errorMessage` jeśli nie.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```

### <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `recognizeLinkedEntitiesExample()` i wywołującą jego `recognizeLinkedEntities()` funkcję. Zwrócony `LinkedEntityCollection` obiekt będzie zawierać listę , jeśli to `LinkedEntity` się powiedzie, lub , `errorMessage` jeśli nie. Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są grupowane w obiekcie `LinkedEntity` jako lista `LinkedEntityMatch` obiektów.


```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```


### <a name="personally-identifiable-information-recognition"></a>Rozpoznawanie danych osobowych

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `recognizePiiEntitiesExample()` i wywołującą jego `recognizePiiEntities()` funkcję. Zwrócony `PiiEntityCollection` obiekt będzie zawierać listę , jeśli to `PiiEntity` się powiedzie, lub , `errorMessage` jeśli nie. Będzie on również zawierać z redagowany tekst, który składa się z tekstu wejściowego ze wszystkimi rozpoznawalnymi jednostkami zastąpionymi przez `*****` .

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>Dane wyjściowe

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

> [!NOTE]
> W wersji `3.0` :
> * Ner zawiera oddzielne metody wykrywania danych osobowych. 
> * Łączenie jednostek jest oddzielnym żądaniem niż NER.

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `recognizeEntitiesExample()` i wywołującą jego `recognizeEntities()` funkcję. Zwrócony `CategorizedEntityCollection` obiekt będzie zawierać listę , jeśli się `CategorizedEntity` powiedzie, lub , jeśli `errorMessage` nie.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

### <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `recognizeLinkedEntitiesExample()` i wywołującą jego `recognizeLinkedEntities()` funkcję. Zwrócony `LinkedEntityCollection` obiekt będzie zawierać listę , jeśli się `LinkedEntity` powiedzie, lub , jeśli `errorMessage` nie. Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są grupowane pod obiektem `LinkedEntity` jako lista `LinkedEntityMatch` obiektów.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```

---

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `extractKeyPhrasesExample()` i wywołującą jego `extractKeyPhrases()` funkcję. Zwrócony obiekt będzie zawierać listę kluczowych fraz, jeśli to `ExtractKeyPhraseResult` się powiedzie, lub element , `errorMessage` jeśli nie. Ten przykład jest taki sam dla wersji 3.0 i 3.1 interfejsu API.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized phrases: 
cat
veterinarian
```
---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Asynchroniczne używanie interfejsu API z operacją Analizowanie

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

Utwórz nową funkcję o nazwie `analyzeBatchActionsExample()` , która wywołuje funkcję `beginAnalyzeBatchActions()` . Wynikiem będzie długotrwała operacja, która będzie sondowana w celu wyszukiwania wyników.

```java
static void analyzeBatchActionsExample(TextAnalyticsClient client)
{
        List<TextDocumentInput> documents = Arrays.asList(
                        new TextDocumentInput("0", "Microsoft was founded by Bill Gates and Paul Allen.")
                        );

        
        SyncPoller<AnalyzeBatchActionsOperationDetail, PagedIterable<AnalyzeBatchActionsResult>> syncPoller =
                client.beginAnalyzeBatchActions(documents,
                        new TextAnalyticsActions().setDisplayName("Analyze Batch Actions Quickstart")
                                .setRecognizeEntitiesOptions(new RecognizeEntitiesOptions()),
                        new AnalyzeBatchActionsOptions().setIncludeStatistics(false),
                        Context.NONE);

        // Task operation statistics
        while (syncPoller.poll().getStatus() == LongRunningOperationStatus.IN_PROGRESS) {
            final AnalyzeBatchActionsOperationDetail operationResult = syncPoller.poll().getValue();
            System.out.printf("Action display name: %s, Successfully completed actions: %d, in-process actions: %d, failed actions: %d, total actions: %d%n",
                    operationResult.getDisplayName(), operationResult.getActionsSucceeded(),
                    operationResult.getActionsInProgress(), operationResult.getActionsFailed(),
                    operationResult.getActionsInTotal());
        }

        syncPoller.waitForCompletion();

        Iterable<PagedResponse<AnalyzeBatchActionsResult>> pagedResults = syncPoller.getFinalResult().iterableByPage();
        for (PagedResponse<AnalyzeBatchActionsResult> page : pagedResults) {
            System.out.printf("Response code: %d, Continuation Token: %s.%n", page.getStatusCode(), page.getContinuationToken());
            page.getElements().forEach(analyzeBatchActionsResult -> {
                System.out.println("Entities recognition action results:");
                IterableStream<RecognizeEntitiesActionResult> recognizeEntitiesActionResults =
                        analyzeBatchActionsResult.getRecognizeEntitiesActionResults();
                if (recognizeEntitiesActionResults != null) {
                    recognizeEntitiesActionResults.forEach(actionResult -> {
                        if (!actionResult.isError()) {
                            // Recognized entities for each of documents from a batch of documents
                            AtomicInteger counter = new AtomicInteger();
                            for (RecognizeEntitiesResult documentResult : actionResult.getResult()) {
                                System.out.printf("%n%s%n", documents.get(counter.getAndIncrement()));
                                if (documentResult.isError()) {
                                    // Erroneous document
                                    System.out.printf("Cannot recognize entities. Error: %s%n",
                                            documentResult.getError().getMessage());
                                } else {
                                    // Valid document
                                    documentResult.getEntities().forEach(entity -> System.out.printf(
                                            "Recognized entity: %s, entity category: %s, entity subcategory: %s, confidence score: %f.%n",
                                            entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
                                }
                            }
                        } else {
                            TextAnalyticsError actionError = actionResult.getError();
                            // Erroneous action
                            System.out.printf("Cannot execute Entities Recognition action. Error: %s%n", actionError.getMessage());
                        }
                    });
                }
            });
        }
    }
```

Po dodaniu tego przykładu do aplikacji wywołaj go w `main()` metodzie .

```java
analyzeBatchActionsExample(client);
```

### <a name="output"></a>Dane wyjściowe

```console
Action display name: Analyze Batch Actions Quickstart, Successfully completed actions: 0, in-process actions: 1, failed actions: 0, total actions: 1
Response code: 200, Continuation Token: null.
Entities recognition action results:

Text = Microsoft was founded by Bill Gates and Paul Allen., Id = 0, Language = null
Recognized entity: Microsoft, entity category: Organization, entity subcategory: null, confidence score: 0.970000.
Recognized entity: Bill Gates, entity category: Person, entity subcategory: null, confidence score: 1.000000.
Recognized entity: Paul Allen, entity category: Person, entity subcategory: null, confidence score: 0.990000.
```

Za pomocą operacji Analizuj można również wykrywać dane piI, rozpoznawać połączone jednostki i wyodrębniać frazy kluczowe. Zobacz przykład [Analyze w witrynie](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro) GitHub.

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Ta funkcja nie jest dostępna w wersji 3.0.

---
