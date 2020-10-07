---
title: 'Szybki Start: analiza tekstu wersja kliencka klienta dla języka Java | Microsoft Docs'
description: Rozpocznij pracę z biblioteką kliencką programu v3 analiza tekstu dla środowiska Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 09/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 2788b22d2b262cfef4b214d75813a203067462cb
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779673"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/version-3-1)

[Dokumentacja](https://docs.microsoft.com/java/api/overview/azure/ai-textanalytics-readme?view=azure-java-stable)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.1.0-beta.1/sdk/textanalytics/azure-ai-textanalytics)  |  źródłowy biblioteki [Pakiet](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.1)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.1.0-beta.1/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

[Dokumentacja](https://docs.microsoft.com/java/api/overview/azure/ai-textanalytics-readme?view=azure-java-stable)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics)  |  źródłowy biblioteki [Pakiet](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

W tym artykule opisano tylko wersję 3. x interfejsu API.

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* [Zestaw Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) z wersją 8 lub nowszą
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" Utwórz zasób analiza tekstu "  target="_blank"> utwórz zasób analiza tekstu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy.  Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API analizy tekstu. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="add-the-client-library"></a>Dodawanie biblioteki klienta

# <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/version-3-1)

Utwórz projekt Maven w preferowanym środowisku IDE lub środowiska deweloperskiego. Następnie Dodaj następujący zależność do pliku *pom.xml* projektu. Możesz znaleźć składnię implementacji [dla innych narzędzi kompilacji w](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.1) trybie online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0-beta.1</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Utwórz projekt Maven w preferowanym środowisku IDE lub środowiska deweloperskiego. Następnie Dodaj następujący zależność do pliku *pom.xml* projektu. Możesz znaleźć składnię implementacji [dla innych narzędzi kompilacji w](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) trybie online.

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
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć [w usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), która zawiera przykłady kodu w tym przewodniku Szybki Start. 

# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

W tym artykule opisano tylko wersję 3. x interfejsu API.

---

Utwórz plik języka Java o nazwie `TextAnalyticsSamples.java` . Otwórz plik i Dodaj następujące `import` instrukcje:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

W pliku Java Dodaj nową klasę i Dodaj klucz i punkt końcowy zasobu platformy Azure, jak pokazano poniżej.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Dodaj następującą metodę główną do klasy. Metody wywoływane tutaj zostaną zdefiniowane później.

# <a name="version-31-preview"></a>[W wersji 3,1 (wersja zapoznawcza)](#tab/version-3-1)

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

# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

W tym artykule opisano tylko wersję 3. x interfejsu API.

---


## <a name="object-model"></a>Model obiektów

Klient analiza tekstu jest `TextAnalyticsClient` obiektem, który jest uwierzytelniany na platformie Azure przy użyciu klucza, i udostępnia funkcje do akceptowania tekstu jako pojedyncze ciągi lub jako partii. Możesz wysyłać tekst do interfejsu API synchronicznie lub asynchronicznie. Obiekt Response będzie zawierać informacje o analizie dla każdego wysyłanego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis) 
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek nazwanych](#named-entity-recognition-ner)
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz metodę, aby utworzyć wystąpienie `TextAnalyticsClient` obiektu za pomocą klucza i punktu końcowego dla zasobu analiza tekstu. Ten przykład jest taki sam dla wersji 3,0 i 3,1 interfejsu API.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


W `main()` metodzie programu Wywołaj metodę uwierzytelniania w celu utworzenia wystąpienia klienta.

## <a name="sentiment-analysis"></a>Analiza tonacji

# <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/version-3-1)

> [!NOTE]
> W wersji `3.1` :
> * Analiza tonacji zawiera opinię o analizie wyszukiwania, która jest opcjonalną flagą. 
> * Wyszukiwanie opinii zawiera aspekty i poziom opinii tonacji. 

Utwórz nową funkcję o nazwie `sentimentAnalysisExample()` , która przybiera wcześniej utworzoną klienta i Wywołaj `analyzeSentiment()` funkcję. Zwrócony `AnalyzeSentimentResult` obiekt będzie zawierać `documentSentiment` i `sentenceSentiments` Jeśli powodzenie, lub `errorMessage` Jeśli nie. 

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

Aby przeprowadzić analizę tonacji z opinią wyszukiwania, Utwórz nową funkcję o nazwie, `sentimentAnalysisWithOpinionMiningExample()` która przyjmuje wcześniej utworzoną klienta i Wywołaj `analyzeSentiment()` funkcję z obiektem opcji ustawień `AnalyzeSentimentOptions` . Zwrócony `AnalyzeSentimentResult` obiekt będzie zawierać `documentSentiment` i `sentenceSentiments` Jeśli powodzenie, lub `errorMessage` Jeśli nie. 


```java
static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    // The Document that needs be analyzed.
    String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

    System.out.printf("Document = %s%n", document);

    AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
    final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
    SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
    System.out.printf(
            "\tRecognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
            documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());

    documentSentiment.getSentences().forEach(sentenceSentiment -> {
        SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
        System.out.printf("\t\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
        sentenceSentiment.getMinedOpinions().forEach(minedOpinions -> {
            AspectSentiment aspectSentiment = minedOpinions.getAspect();
            System.out.printf("\t\t\tAspect sentiment: %s, aspect text: %s%n", aspectSentiment.getSentiment(),
                    aspectSentiment.getText());
            for (OpinionSentiment opinionSentiment : minedOpinions.getOpinions()) {
                System.out.printf("\t\t\t\t'%s' opinion sentiment because of \"%s\". Is the opinion negated: %s.%n",
                        opinionSentiment.getSentiment(), opinionSentiment.getText(), opinionSentiment.isNegated());
            }
        });
    });
}
```

### <a name="output"></a>Dane wyjściowe

```console
Text = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
    Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
        Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
            Aspect sentiment: negative, aspect text: atmosphere
                'negative' opinion sentiment because of "bad". Is the opinion negated: false.
        Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
        Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
            Aspect sentiment: negative, aspect text: Staff
                'negative' opinion sentiment because of "friendly". Is the opinion negated: true.
                'negative' opinion sentiment because of "helpful". Is the opinion negated: true.

Process finished with exit code 0
```

# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

Utwórz nową funkcję o nazwie `sentimentAnalysisExample()` , która przybiera wcześniej utworzoną klienta i Wywołaj `analyzeSentiment()` funkcję. Zwrócony `AnalyzeSentimentResult` obiekt będzie zawierać `documentSentiment` i `sentenceSentiments` Jeśli powodzenie, lub `errorMessage` Jeśli nie. 

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

# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

W tym artykule opisano tylko wersję 3. x interfejsu API.

---

## <a name="language-detection"></a>Wykrywanie języka

Utwórz nową funkcję o nazwie `detectLanguageExample()` , która przybiera wcześniej utworzoną klienta i Wywołaj `detectLanguage()` funkcję. Zwrócony `DetectLanguageResult` obiekt będzie zawierał wykryty język podstawowy, listę innych języków wykrytych w przypadku powodzenia `errorMessage` . Ten przykład jest taki sam dla wersji 3,0 i 3,1 interfejsu API.

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić Języki w oparciu o dane wejściowe. Możesz użyć parametru, `countryHint` Aby określić 2-literowy kod kraju. Domyślnie interfejs API używa "US" jako domyślnego countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `countryHint = ""` . Aby ustawić inne ustawienia domyślne, należy ustawić `TextAnalyticsClientOptions.DefaultCountryHint` Właściwość i przekazać ją podczas inicjowania klienta.

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

# <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/version-3-1)

> [!NOTE]
> W wersji `3.1` :
> * NER obejmuje oddzielne metody wykrywania informacji osobistych. 
> * Łączenie jednostek to oddzielne żądanie niż NER.

Utwórz nową funkcję o nazwie `recognizeEntitiesExample()` , która przybiera wcześniej utworzoną klienta i Wywołaj `recognizeEntities()` funkcję. Zwrócony `CategorizedEntityCollection` obiekt będzie zawierać listę `CategorizedEntity` , jeśli powiodła się, lub `errorMessage` Jeśli nie.

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

Utwórz nową funkcję o nazwie `recognizeLinkedEntitiesExample()` , która przybiera wcześniej utworzoną klienta i Wywołaj `recognizeLinkedEntities()` funkcję. Zwrócony `LinkedEntityCollection` obiekt będzie zawierać listę `LinkedEntity` , jeśli powiodła się, lub `errorMessage` Jeśli nie. Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są pogrupowane pod `LinkedEntity` obiektem jako lista `LinkedEntityMatch` obiektów.


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


### <a name="personally-identifiable-information-recognition"></a>Rozpoznawanie informacji umożliwiających identyfikację użytkownika

Utwórz nową funkcję o nazwie `recognizePiiEntitiesExample()` , która przybiera wcześniej utworzoną klienta i Wywołaj `recognizePiiEntities()` funkcję. Zwrócony `PiiEntityCollection` obiekt będzie zawierać listę `PiiEntity` , jeśli powiodła się, lub `errorMessage` Jeśli nie. Będzie również zawierać tekst redagowane, który składa się z tekstu wejściowego ze wszystkimi identyfikowanymi obiektami zastępowanymi przez `*****` .

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

# <a name="version-30"></a>[Wersja 3,0](#tab/version-3)

> [!NOTE]
> W wersji `3.0` :
> * NER obejmuje oddzielne metody wykrywania informacji osobistych. 
> * Łączenie jednostek to oddzielne żądanie niż NER.

Utwórz nową funkcję o nazwie `recognizeEntitiesExample()` , która przybiera wcześniej utworzoną klienta i Wywołaj `recognizeEntities()` funkcję. Zwrócony `CategorizedEntityCollection` obiekt będzie zawierać listę `CategorizedEntity` , jeśli powiodła się, lub `errorMessage` Jeśli nie.

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

Utwórz nową funkcję o nazwie `recognizeLinkedEntitiesExample()` , która przybiera wcześniej utworzoną klienta i Wywołaj `recognizeLinkedEntities()` funkcję. Zwrócony `LinkedEntityCollection` obiekt będzie zawierać listę `LinkedEntity` , jeśli powiodła się, lub `errorMessage` Jeśli nie. Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są pogrupowane pod `LinkedEntity` obiektem jako lista `LinkedEntityMatch` obiektów.

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

# <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

W tym artykule opisano tylko wersję 3. x interfejsu API.

---

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Utwórz nową funkcję o nazwie `extractKeyPhrasesExample()` , która przybiera wcześniej utworzoną klienta i Wywołaj `extractKeyPhrases()` funkcję. Zwrócony `ExtractKeyPhraseResult` obiekt będzie zawierać listę kluczowych fraz w przypadku powodzenia, lub `errorMessage` Jeśli nie. Ten przykład jest taki sam dla wersji 3,0 i 3,1 interfejsu API.

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