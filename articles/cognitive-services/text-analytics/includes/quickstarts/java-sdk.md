---
title: 'Szybki start: biblioteka klienta analizy tekstu w wersji 3 dla oprogramowania Java | Dokumenty firmy Microsoft'
description: Wprowadzenie do biblioteki klienta analizy tekstu w wersji 3 dla oprogramowania Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 31afb7bc00250887841adccc8c3cc4dc69462d55
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642889"
---
<a name="HOLTop"></a>

[Dokumentacja](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [referencyjna Biblioteka kod](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [źródłowy Próbki pakietów](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.4) | [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* [Zestaw Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) w wersji 8 lub wyższej
* Po utworzeniu subskrypcji <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="platformy Azure utwórz"  target="_blank">zasób <span class="docon docon-navigate-external x-hidden-focus"></span> </a> analizy tekstu w witrynie Azure portal, aby uzyskać klucz i punkt końcowy.  Po wdrożeniu kliknij przycisk **Przejdź do zasobu**.
    * Potrzebny będzie klucz i punkt końcowy z zasobu, który tworzysz, aby połączyć aplikację z interfejsem API analizy tekstu. Wklej klucz i punkt końcowy do kodu poniżej w dalszej części przewodnika Szybki start.
    * Można użyć bezpłatnej warstwy`F0`cenowej ( ), aby wypróbować usługę, a następnie uaktualnić do warstwy płatnej dla produkcji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="add-the-client-library"></a>Dodawanie biblioteki klienta

Utwórz projekt Maven w preferowanym środowisku IDE lub rozwoju. Następnie dodaj następującą zależność do pliku *pom.xml* projektu. Składnię implementacji [innych narzędzi kompilacji](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.4) można znaleźć w trybie online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.4</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu naraz? Można go znaleźć [na GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), który zawiera przykłady kodu w tym przewodniku Szybki start. 

Utwórz plik `TextAnalyticsSamples.java`Java o nazwie . Otwórz plik i dodaj `import` następujące instrukcje:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

W pliku java dodaj nową klasę i dodaj klucz i punkt końcowy zasobu azure, jak pokazano poniżej.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Dodaj następującą metodę główną do klasy. Zdefiniujesz metody wywoływane tutaj później.

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

## <a name="object-model"></a>Model obiektu

Klient analizy tekstu `TextAnalyticsClient` jest obiektem, który uwierzytelnia się na platformie Azure przy użyciu klucza i udostępnia funkcje do akceptowania tekstu jako pojedyncze ciągi lub jako partia. Tekst można wysyłać synchronicznie lub asynchronicznie do interfejsu API. Obiekt odpowiedzi będzie zawierał informacje analityczne dla każdego wysyłanego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis) 
* [Wykrywanie języka](#language-detection)
* [Uznanie nazwanej encji](#named-entity-recognition-ner) 
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

Utwórz metodę tworzenia wystąpienia `TextAnalyticsClient` obiektu za pomocą klucza i punktu końcowego zasobu analizy tekstu.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

W `main()` metodzie programu wywołaj metodę uwierzytelniania, aby utworzyć wystąpienie klienta.

## <a name="sentiment-analysis"></a>Analiza tonacji

Utwórz nową `sentimentAnalysisExample()` funkcję o nazwie, która przyjmuje klienta, który został utworzony wcześniej i wywołać jego `analyzeSentiment()` funkcję. Zwrócony `AnalyzeSentimentResult` obiekt `documentSentiment` będzie `sentenceSentiments` zawierać i `errorMessage` jeśli zakończy się pomyślnie, lub jeśli nie. 

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
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="language-detection"></a>Wykrywanie języka

Utwórz nową `detectLanguageExample()` funkcję o nazwie, która przyjmuje klienta, który został utworzony wcześniej i wywołać jego `detectLanguage()` funkcję. Zwrócony `DetectLanguageResult` obiekt będzie zawierał wykryty język podstawowy, listę innych języków wykrytych w przypadku powodzenia lub `errorMessage` jeśli nie.

> [!Tip]
> W niektórych przypadkach może być trudne do odróżnienia języków na podstawie danych wejściowych. Za pomocą `countryHint` tego parametru można określić 2-literowy kod kraju. Domyślnie interfejs API używa "US" jako domyślnego krajuHint, aby usunąć to zachowanie, możesz `countryHint = ""`zresetować ten parametr, ustawiając tę wartość na pusty ciąg . Aby ustawić inną wartość `TextAnalyticsClientOptions.DefaultCountryHint` domyślną, należy ustawić właściwość i przekazać ją podczas inicjowania klienta.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getScore());
}
```

### <a name="output"></a>Dane wyjściowe

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>Rozpoznawanie nazwanych jednostek (NER)

> [!NOTE]
> W `3.0-preview`wersji :
> * NER zawiera oddzielne metody wykrywania danych osobowych. 
> * Łączenie jednostek jest osobnym żądaniem niż NER.

Utwórz nową `recognizeEntitiesExample()` funkcję o nazwie, która przyjmuje klienta, który został utworzony wcześniej i wywołać jego `recognizeEntities()` funkcję. Zwrócony `RecognizeEntitiesResult` obiekt będzie zawierał `NamedEntity` listę, jeśli `errorMessage` zakończy się pomyślnie, lub jeśli nie.

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
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

## <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową `recognizeLinkedEntitiesExample()` funkcję o nazwie, która przyjmuje klienta, który został utworzony wcześniej i wywołać jego `recognizeLinkedEntities()` funkcję. Zwrócony `RecognizeLinkedEntitiesResult` obiekt będzie zawierał `LinkedEntity` listę, jeśli `errorMessage` zakończy się pomyślnie, lub jeśli nie. Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej `LinkedEntity` samej encji `LinkedEntityMatch` są grupowane pod obiektem jako lista obiektów.

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
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
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
Text: Altair 8800, Score: 0.78
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.55
Text: Gates, Score: 0.55
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.53
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.47
Text: Microsoft, Score: 0.47
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.25
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.28
```
## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Utwórz nową `extractKeyPhrasesExample()` funkcję o nazwie, która przyjmuje klienta, który został utworzony wcześniej i wywołać jego `extractKeyPhrases()` funkcję. Zwrócony `ExtractKeyPhraseResult` obiekt będzie zawierał listę fraz kluczowych, `errorMessage` jeśli zakończy się pomyślnie, lub jeśli nie.

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
