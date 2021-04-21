---
title: 'Szybki start: analiza tekstu klienta dla języka C# | Microsoft Docs'
description: 'Wprowadzenie do biblioteki analiza tekstu klienta dla języka C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 1fd102f0f94f1ce53bebfba94d4f4c1a1f9e3812
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765146"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

Dokumentacja referencyjna w wersji [3.1](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-preview)  |  [Kod źródłowy biblioteki w wersji 3.1](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)  |  [Pakiet w wersji 3.1 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0-beta.5)  |  [Przykłady w wersji 3.1](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

[Dokumentacja referencyjna w wersji](/dotnet/api/azure.ai.textanalytics)  |  3 [Kod źródłowy biblioteki w wersji](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics)  |  3 [Pakiet w wersji 3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics)  |  [Przykłady w wersji 3](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Program [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób usługi analiza tekstu zasób analiza tekstu w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> klucz i punkt </a> końcowy.  Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
    * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z interfejsem API analiza tekstu API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    * Możesz użyć warstwy cenowej Bezpłatna ( ), aby wypróbować usługę, i przejść później na warstwę płatną `F0` na użytek produkcji.
* Aby korzystać z funkcji Analizuj, musisz mieć zasób analiza tekstu z warstwą cenową Standardowa (S).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-net-core-application"></a>Tworzenie nowej aplikacji .NET Core

Za pomocą Visual Studio IDE utwórz nową aplikację konsoli .NET Core. Spowoduje to utworzenie projektu "Hello world" z pojedynczym plikiem źródłowym języka C#: *program.cs.*

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

Zainstaluj bibliotekę klienta, klikając prawym przyciskiem myszy rozwiązanie w Eksplorator rozwiązań **i** wybierając pozycję **Zarządzaj pakietami NuGet.** W menedżerze pakietów, który zostanie otwarty, wybierz **pozycję Przeglądaj** i `Azure.AI.TextAnalytics` wyszukaj . Zaznacz pole **uwzględnij wersję wstępną,** wybierz pozycję wersja `5.1.0-beta.5` , a następnie pozycję **Zainstaluj.** Można również użyć konsoli [Menedżer pakietów .](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Zainstaluj bibliotekę klienta, klikając rozwiązanie prawym przyciskiem myszy w Eksplorator rozwiązań **i** wybierając polecenie **Zarządzaj pakietami NuGet.** W menedżerze pakietów, który zostanie otwarty, wybierz **pozycję Przeglądaj** i wyszukaj `Azure.AI.TextAnalytics` . Wybierz wersję `5.0.0` , a następnie pozycję **Zainstaluj.** Można również użyć konsoli [Menedżer pakietów .](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)


> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w [witrynie GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs)która zawiera przykłady kodu w tym przewodniku Szybki start. 

---

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

Otwórz *plik program.cs* i dodaj następujące `using` dyrektywy:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

W klasie aplikacji utwórz zmienne dla klucza i punktu końcowego `Program` zasobu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Zastąp metodę `Main` aplikacji. Metody wywoływane w tym miejscu zdefiniuje się później.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Otwórz *plik program.cs* i dodaj następujące `using` dyrektywy:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

W klasie aplikacji utwórz zmienne dla klucza i punktu końcowego `Program` zasobu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Zastąp metodę `Main` aplikacji. Metody wywoływane w tym miejscu zdefiniuje się później.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

---

## <a name="object-model"></a>Model obiektu

Klient analiza tekstu to obiekt, który uwierzytelnia się na platformie Azure przy użyciu klucza i udostępnia funkcje do akceptowania tekstu jako pojedynczych ciągów `TextAnalyticsClient` lub jako partii. Tekst do interfejsu API można wysyłać synchronicznie lub asynchronicznie. Obiekt odpowiedzi będzie zawierać informacje analityczne dla każdego dokumentu, który wyślesz. 

Jeśli używasz wersji usługi, możesz użyć opcjonalnego wystąpienia, aby zainicjować klienta z różnymi ustawieniami domyślnymi (na przykład domyślnym językiem lub wskazówką dla `3.x` `TextAnalyticsClientOptions` kraju/regionu). Możesz również uwierzytelnić się przy użyciu Azure Active Directory tokenu. 

## <a name="code-examples"></a>Przykłady kodu

* [Analiza tonacji](#sentiment-analysis)
* [Wyszukiwanie opinii](#opinion-mining)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek nazwanych](#named-entity-recognition-ner)
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

Upewnij się, że metoda główna z wcześniejszych wersji tworzy nowy obiekt klienta z punktem końcowym i poświadczeniami.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Upewnij się, że metoda główna z wcześniejszych wersji tworzy nowy obiekt klienta z punktem końcowym i poświadczeniami.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

---

## <a name="sentiment-analysis"></a>Analiza tonacji

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `SentimentAnalysisExample()` i wywołującą jego `AnalyzeSentiment()` funkcję. Zwrócony obiekt będzie zawierać etykietę tonacji i wynik całego dokumentu wejściowego, a także analizę tonacji dla każdego zdania, jeśli to `Response<DocumentSentiment>` się powiedzie. Jeśli wystąpił błąd, zostanie zrzucany element `RequestFailedException` .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

### <a name="opinion-mining"></a>Wyszukiwanie opinii

Utwórz nową funkcję o nazwie , która przyjmuje utworzonego wcześniej klienta i wywołaj jego funkcję `SentimentAnalysisWithOpinionMiningExample()` `AnalyzeSentimentBatch()` z `IncludeOpinionMining` opcją w `AnalyzeSentimentOptions` torbie. Zwrócony `AnalyzeSentimentResultCollection` obiekt będzie zawierać kolekcję , `AnalyzeSentimentResult` w której reprezentuje wartość `Response<DocumentSentiment>` . Różnica między wartościami i polega na tym, że te drugie będą zawarte w każdym zdaniu, które przedstawia przeanalizowany element docelowy i powiązane `SentimentAnalysis()` `SentimentAnalysisWithOpinionMiningExample()` `SentenceOpinion` oceny. Jeśli wystąpił błąd, zrzuci on wyjątek `RequestFailedException` .

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        IncludeOpinionMining = true
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        Console.WriteLine($"\tPositive score: {review.DocumentSentiment.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {review.DocumentSentiment.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {review.DocumentSentiment.ConfidenceScores.Neutral:0.00}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tSentence positive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tSentence negative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tSentence neutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (SentenceOpinion sentenceOpinion in sentence.Opinions)
            {
                Console.WriteLine($"\tTarget: {sentenceOpinion.Target.Text}, Value: {sentenceOpinion.Target.Sentiment}");
                Console.WriteLine($"\tTarget positive score: {sentenceOpinion.Target.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tTarget negative score: {sentenceOpinion.Target.ConfidenceScores.Negative:0.00}");
                foreach (AssessmentSentiment assessment in sentenceOpinion.Assessments)
                {
                    Console.WriteLine($"\t\tRelated Assessment: {assessment.Text}, Value: {assessment.Sentiment}");
                    Console.WriteLine($"\t\tRelated Assessment positive score: {assessment.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Assessment negative score: {assessment.ConfidenceScores.Negative:0.00}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Document sentiment: Positive

        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Sentence positive score: 0.84
        Sentence negative score: 0.16
        Sentence neutral score: 0.00

        Target: food, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: service, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: concierge, Value: Positive
        Target positive score: 1.00
        Target negative score: 0.00
                Related Assessment: nice, Value: Positive
                Related Assessment positive score: 1.00
                Related Assessment negative score: 0.00

Press any key to exit.
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `SentimentAnalysisExample()` i wywołującą jego `AnalyzeSentiment()` funkcję. Zwrócony obiekt będzie zawierać etykietę tonacji i wynik całego dokumentu wejściowego, a także analizę tonacji dla każdego zdania, jeśli to `Response<DocumentSentiment>` się powiedzie. Jeśli wystąpił błąd, zrzuci on wyjątek `RequestFailedException` .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

---

## <a name="language-detection"></a>Wykrywanie języka

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)


Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `LanguageDetectionExample()` i wywołującą jego  `DetectLanguage()` funkcję. Zwrócony obiekt będzie zawierać wykryty język wraz z jego nazwą `Response<DetectedLanguage>` i kodem ISO-6391. Jeśli wystąpił błąd, zrzuci on wyjątek `RequestFailedException` .

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić języki na podstawie danych wejściowych. Możesz użyć `countryHint` parametru , aby określić 2-literowy kod kraju/regionu. Domyślnie interfejs API używa wartości "US" jako domyślnej wartości countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `countryHint = ""` . Aby ustawić inną wartość domyślną, ustaw `TextAnalyticsClientOptions.DefaultCountryHint` właściwość i przekaż ją podczas inicjowania klienta.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Dane wyjściowe

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)


Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `LanguageDetectionExample()` i wywołującą jego  `DetectLanguage()` funkcję. Zwrócony obiekt będzie zawierać wykryty język wraz z jego nazwą `Response<DetectedLanguage>` i kodem ISO-6391. Jeśli wystąpił błąd, zrzuci on wyjątek `RequestFailedException` .

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić języki na podstawie danych wejściowych. Możesz użyć `countryHint` parametru , aby określić 2-literowy kod kraju/regionu. Domyślnie interfejs API używa wartości "US" jako domyślnej wartości countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `countryHint = ""` . Aby ustawić inną wartość domyślną, ustaw `TextAnalyticsClientOptions.DefaultCountryHint` właściwość i przekaż ją podczas inicjowania klienta.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Dane wyjściowe

```console
Language:
        French, ISO-6391: fr
```


---

## <a name="named-entity-recognition-ner"></a>rozpoznawanie jednostek nazwanych (NER)

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)


Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta, wywołaj jego funkcję i `EntityRecognitionExample()` `RecognizeEntities()` iteruj wyniki. Zwrócony `Response<CategorizedEntityCollection>` obiekt będzie zawierać kolekcję wykrytych jednostek `CategorizedEntity` . Jeśli wystąpił błąd, zostanie zrzucany element `RequestFailedException` .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

### <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta, wywołaj jego funkcję i `EntityLinkingExample()` `RecognizeLinkedEntities()` iteruj wyniki. Zwrócony `Response<LinkedEntityCollection>` obiekt będzie zawierać kolekcję wykrytych jednostek `LinkedEntity` . Jeśli wystąpił błąd, zostanie zrzucany element `RequestFailedException` . Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są grupowane pod obiektem `LinkedEntity` jako lista `LinkedEntityMatch` obiektów.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

### <a name="personally-identifiable-information-recognition"></a>Rozpoznawanie danych osobowych

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta, wywołaj jego funkcję i `RecognizePIIExample()` `RecognizePiiEntities()` iteruj wyniki. Zwrócona wartość `PiiEntityCollection` reprezentuje listę wykrytych jednostek piI. Jeśli wystąpił błąd, zostanie zrzucany element `RequestFailedException` .

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)


> [!NOTE]
> Nowość w wersji `3.0` :
> * Łączenie jednostek jest teraz oddzielone od rozpoznawania jednostek.


Utwórz nową funkcję o nazwie , która przyjmuje utworzonego wcześniej klienta, wywołaj jego funkcję i `EntityRecognitionExample()` `RecognizeEntities()` iteruj wyniki. Zwrócony `Response<IReadOnlyCollection<CategorizedEntity>>` obiekt będzie zawierać listę wykrytych jednostek. Jeśli wystąpił błąd, zrzuci on wyjątek `RequestFailedException` .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

### <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie , która przyjmuje utworzonego wcześniej klienta, wywołaj jego funkcję i `EntityLinkingExample()` `RecognizeLinkedEntities()` iteruj wyniki. Zwrócona `Response<IReadOnlyCollection<LinkedEntity>>` wartość reprezentuje listę wykrytych jednostek. Jeśli wystąpił błąd, zrzuci on wyjątek `RequestFailedException` . Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są grupowane w obiekcie `LinkedEntity` jako lista `LinkedEntityMatch` obiektów.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

--- 


### <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `KeyPhraseExtractionExample()` i wywołującą jego `ExtractKeyPhrases()` funkcję. Zwrócony `<Response<KeyPhraseCollection>` obiekt będzie zawierać listę wykrytych kluczowych fraz. Jeśli wystąpił błąd, zrzuci on wyjątek `RequestFailedException` .

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `KeyPhraseExtractionExample()` i wywołującą jego `ExtractKeyPhrases()` funkcję. Zwrócony `<Response<IReadOnlyCollection<string>>` obiekt będzie zawierać listę wykrytych kluczowych fraz. Jeśli wystąpił błąd, zrzuci on wyjątek `RequestFailedException` .

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Key phrases:
    cat
    veterinarian
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Asynchroniczne używanie interfejsu API z operacją analizy

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Utwórz nową funkcję o nazwie , która pobiera utworzonego wcześniej klienta `AnalyzeOperationExample()` i wywołującą jego `StartAnalyzeBatchActionsAsync()` funkcję. Zwrócony `AnalyzeBatchActionsOperation` obiekt będzie zawierać obiekt `Operation` interfejsu. Ponieważ jest to długotrwała operacja, `await` dla `operation.WaitForCompletionAsync()` wartości do zaktualizowania. Po `WaitForCompletionAsync()` zakończeniu kolekcji należy zaktualizować w . `operation.Value` Jeśli wystąpił błąd, zostanie zrzucany element `RequestFailedException` .


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
{
    string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

    var batchDocuments = new List<string> { inputText };


    TextAnalyticsActions actions = new TextAnalyticsActions()
    {
        RecognizeEntitiesOptions = new List<RecognizeEntitiesOptions>() { new RecognizeEntitiesOptions() },
        DisplayName = "Analyze Operation Quick Start Example"
    };

    AnalyzeBatchActionsOperation operation = await client.StartAnalyzeBatchActionsAsync(batchDocuments, actions);

    await operation.WaitForCompletionAsync();

    Console.WriteLine($"Status: {operation.Status}");
    Console.WriteLine($"Created On: {operation.CreatedOn}");
    Console.WriteLine($"Expires On: {operation.ExpiresOn}");
    Console.WriteLine($"Last modified: {operation.LastModified}");
    if (!string.IsNullOrEmpty(operation.DisplayName))
        Console.WriteLine($"Display name: {operation.DisplayName}");
    Console.WriteLine($"Total actions: {operation.TotalActions}");
    Console.WriteLine($"  Succeeded actions: {operation.ActionsSucceeded}");
    Console.WriteLine($"  Failed actions: {operation.ActionsFailed}");
    Console.WriteLine($"  In progress actions: {operation.ActionsInProgress}");

    await foreach (AnalyzeBatchActionsResult documentsInPage in operation.Value)
    {
        RecognizeEntitiesResultCollection entitiesResult = documentsInPage.RecognizeEntitiesActionsResults.FirstOrDefault().Result;

        Console.WriteLine("Recognized Entities");

        foreach (RecognizeEntitiesResult result in entitiesResult)
        {
            Console.WriteLine($"  Recognized the following {result.Entities.Count} entities:");

            foreach (CategorizedEntity entity in result.Entities)
            {
                Console.WriteLine($"  Entity: {entity.Text}");
                Console.WriteLine($"  Category: {entity.Category}");
                Console.WriteLine($"  Offset: {entity.Offset}");
                Console.WriteLine($"  Length: {entity.Length}");
                Console.WriteLine($"  ConfidenceScore: {entity.ConfidenceScore}");
                Console.WriteLine($"  SubCategory: {entity.SubCategory}");
            }
            Console.WriteLine("");
        }
    }
}
```

Po dodaniu tego przykładu do aplikacji wywołaj metodę w `main()` metodzie przy użyciu metody `await` .

```csharp
await AnalyzeOperationExample(client).ConfigureAwait(false);
```
### <a name="output"></a>Dane wyjściowe

```console
Status: succeeded
Created On: 3/10/2021 2:25:01 AM +00:00
Expires On: 3/11/2021 2:25:01 AM +00:00
Last modified: 3/10/2021 2:25:05 AM +00:00
Display name: Analyze Operation Quick Start Example
Total actions: 1
  Succeeded actions: 1
  Failed actions: 0
  In progress actions: 0
Recognized Entities
    Recognized the following 3 entities:
    Entity: Microsoft
    Category: Organization
    Offset: 0
    ConfidenceScore: 0.83
    SubCategory: 
    Entity: Bill Gates
    Category: Person
    Offset: 25
    ConfidenceScore: 0.85
    SubCategory: 
    Entity: Paul Allen
    Category: Person
    Offset: 40
    ConfidenceScore: 0.9
    SubCategory: 
```

Możesz również użyć operacji Analizuj, aby wykryć dane pii i wyodrębnianie kluczowych fraz. Zobacz przykład [Analyze w witrynie](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples) GitHub.

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Ta funkcja nie jest dostępna w wersji 3.0.

---
