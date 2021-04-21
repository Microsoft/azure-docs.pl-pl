---
author: aahill
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.author: aahi
ms.openlocfilehash: cee201c11d0415e1f63e7e6a9157b96a059503ba
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765125"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

Dokumentacja referencyjna w wersji [3.1](/python/api/azure-ai-textanalytics/azure.ai.textanalytics)  |  [Kod źródłowy biblioteki w wersji](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics)  |  3.1 [Pakiet w wersji 3.1 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [Przykłady w wersji 3.1](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

[Dokumentacja referencyjna w wersji](/python/api/azure-ai-textanalytics/azure.ai.textanalytics)  |  3 [Kod źródłowy biblioteki w wersji](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics)  |  3 [Pakiet w wersji 3 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [Przykłady w wersji 3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— tworzenie bezpłatnej subskrypcji](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób analiza tekstu utwórz zasób analiza tekstu w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
    * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z analiza tekstu API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    * Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie przejść na warstwę płatną w `F0` środowisku produkcyjnym.
* Aby korzystać z funkcji Analizuj, musisz mieć zasób analiza tekstu z warstwą cenową Standardowa (S).

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Po zainstalowaniu języka Python możesz zainstalować bibliotekę klienta za pomocą:

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

```console
pip install azure-ai-textanalytics --pre
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w [witrynie GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py)która zawiera przykłady kodu w tym przewodniku Szybki start. 

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

```console
pip install --upgrade azure-ai-textanalytics
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w [witrynie GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py)która zawiera przykłady kodu w tym przewodniku Szybki start. 


---

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nowy plik w języku Python i utwórz zmienne dla punktu końcowego platformy Azure i klucza subskrypcji zasobu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Model obiektów

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

Klient analiza tekstu jest `TextAnalyticsClient` obiektem, który uwierzytelnia się na platformie Azure. Klient udostępnia kilka metod analizowania tekstu. 

Podczas przetwarzania tekstu jest wysyłany do interfejsu API jako lista , która jest albo listą ciągów, listą reprezentacji podobnej do dyktowania, albo listą `documents` `TextDocumentInput/DetectLanguageInput` . Obiekt `dict-like` zawiera kombinację `id` elementów , `text` i `language/country_hint` . Atrybut `text` przechowuje tekst do przeanalizowania w pochodzeniu , a wartość może być `country_hint` `id` dowolną wartością. 

Obiekt odpowiedzi jest listą zawierającą informacje o analizie dla każdego dokumentu. 

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Klient analiza tekstu to `TextAnalyticsClient` obiekt, który uwierzytelnia się na platformie Azure przy użyciu twojego klucza. Klient udostępnia kilka metod analizowania tekstu jako partii. 

Gdy tekst przetwarzania wsadowego jest wysyłany do interfejsu API jako lista , które są obiektami zawierającymi kombinację atrybutów , i w zależności `documents` `dictionary` od `id` `text` `language` użytej metody. Atrybut `text` przechowuje tekst do przeanalizowania w pochodzeniu , a wartość może być `language` `id` dowolną wartością. 

Obiekt odpowiedzi jest listą zawierającą analizowane informacje dla każdego dokumentu. 

---

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania przy użyciu analiza tekstu klienta dla języka Python:

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis)
* [Wyszukiwanie opinii](#opinion-mining)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek nazwanych](#named-entity-recognition-ner) 
* [Rozpoznawanie danych osobowych](#personally-identifiable-information-recognition) 
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)


# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie nazwanych jednostek](#named-entity-recognition-ner) 
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

---

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

Utwórz funkcję, aby utworzyć wystąpienia obiektu `TextAnalyticsClient` za pomocą funkcji AND `key` `endpoint` utworzonej powyżej. Następnie utwórz nowego klienta. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Utwórz funkcję, aby utworzyć wystąpienia obiektu `TextAnalyticsClient` za pomocą funkcji AND `key` `endpoint` utworzonej powyżej. Następnie utwórz nowego klienta. Należy `api_version=TextAnalyticsApiVersion.V3_0` pamiętać, że należy zdefiniować program do używania wersji 3.0.

```python
# use this code if you're using SDK version is 5.0.0
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential) 
    return text_analytics_client

client = authenticate_client()
```

Jeśli zainstalowano bibliotekę klienta w wersji 5.1.0 przy użyciu programu , można określić 3.0 interfejsu API usługi analiza tekstu za pomocą `pip install azure-ai-textanalytics --pre` parametru `api_version` klienta. Użyj następującej metody tylko wtedy, gdy klient `authenticate_client()` jest w wersji 5.1.0 lub nowszej.

```python
# Only use the following code sample if you're using v5.1.0 of the client library, 
# and are looking to specify v3.0 of the Text Analytics API for your client
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiVersion
from azure.core.credentials import AzureKeyCredential
def authenticate_client():
   ta_credential = AzureKeyCredential(key)
   text_analytics_client = TextAnalyticsClient(
     endpoint=endpoint,
     credential=ta_credential,
     api_version=TextAnalyticsApiVersion.V3_0
   )
   
client = authenticate_client()
```

--- 

## <a name="sentiment-analysis"></a>Analiza tonacji

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

Utwórz nową funkcję o nazwie `sentiment_analysis_example()` , która przyjmuje klienta jako argument, a następnie wywołuje funkcję `analyze_sentiment()` . Zwrócony obiekt odpowiedzi będzie zawierać etykietę tonacji i wynik całego dokumentu wejściowego, a także analizę tonacji dla każdego zdania.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents=documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

## <a name="opinion-mining"></a>Wyszukiwanie opinii

Aby wykonać analizę tonacji przy użyciu wyszukiwania opinii, utwórz nową funkcję o nazwie , która przyjmuje klienta jako argument, a następnie wywołuje funkcję z `sentiment_analysis_with_opinion_mining_example()` `analyze_sentiment()` flagą opcji `show_opinion_mining=True` . Zwrócony obiekt odpowiedzi będzie zawierać nie tylko etykietę tonacji i wynik całego dokumentu wejściowego z analizą tonacji dla każdego zdania, ale także analizę tonacji na poziomie aspektu i opinii.


```python
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable, but the concierge were nice"
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                target = mined_opinion.target
                print("......'{}' target '{}'".format(target.sentiment, target.text))
                print("......Target score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                    target.confidence_scores.positive,
                    target.confidence_scores.negative,
                ))
                for assessment in mined_opinion.assessments:
                    print("......'{}' assessment '{}'".format(assessment.sentiment, assessment.text))
                    print("......Assessment score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                        assessment.confidence_scores.positive,
                        assessment.confidence_scores.negative,
                    ))
            print("\n")
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Document Sentiment: positive
Overall scores: positive=0.84; neutral=0.00; negative=0.16

Sentence: The food and service were unacceptable, but the concierge were nice
Sentence sentiment: positive
Sentence score:
Positive=0.84
Neutral=0.00
Negative=0.16

......'negative' target 'food'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'negative' target 'service'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'positive' target 'concierge'
......Target score:
......Positive=1.00
......Negative=0.00

......'positive' assessment 'nice'
......Assessment score:
......Positive=1.00
......Negative=0.00





Press any key to continue . . .

```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Utwórz nową funkcję o nazwie `sentiment_analysis_example()` , która przyjmuje klienta jako argument, a następnie wywołuje funkcję `analyze_sentiment()` . Zwrócony obiekt odpowiedzi będzie zawierać etykietę tonacji i wynik całego dokumentu wejściowego, a także analizę tonacji dla każdego zdania.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

---

## <a name="language-detection"></a>Wykrywanie języka

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

Utwórz nową funkcję o nazwie `language_detection_example()` , która przyjmuje klienta jako argument, a następnie wywołuje funkcję `detect_language()` . Zwrócony obiekt odpowiedzi będzie zawierać wykryty język w przypadku powodzenia, a jeśli nie, `primary_language` to `error` .

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić języki na podstawie danych wejściowych. Możesz użyć `country_hint` parametru , aby określić 2-literowy kod kraju. Domyślnie interfejs API używa wartości "US" jako domyślnej wartości countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `country_hint : ""` . 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Dane wyjściowe

```console
Language:  French
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Utwórz nową funkcję o nazwie `language_detection_example()` , która przyjmuje klienta jako argument, a następnie wywołuje funkcję `detect_language()` . Zwrócony obiekt odpowiedzi będzie zawierać wykryty język w przypadku powodzenia, a jeśli nie, `primary_language` to `error` .

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić języki na podstawie danych wejściowych. Możesz użyć `country_hint` parametru , aby określić 2-literowy kod kraju. Domyślnie interfejs API używa wartości "US" jako domyślnej wartości countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `country_hint : ""` . 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Dane wyjściowe

```console
Language:  French
```


---

## <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)

# <a name="version-31-preview"></a>[Wersja 3.1 (wersja zapoznawcza)](#tab/version-3-1)

> [!NOTE]
> W wersji `3.1` : 
> * Łączenie jednostek jest oddzielnym żądaniem niż NER.

Utwórz nową funkcję o nazwie , która przyjmuje klienta jako argument, a następnie wywołuje funkcję i `entity_recognition_example` `recognize_entities()` iteruje po wynikach. Zwrócony obiekt odpowiedzi będzie zawierać listę wykrytych jednostek w programie , jeśli to się powiedzie, a jeśli nie, to `entity` `error` . Dla każdej wykrytej jednostki wydrukuj jej kategorię i Sub-Category jeśli istnieje.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```

### <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie , która przyjmuje klienta jako argument, a następnie wywołuje funkcję i `entity_linking_example()` `recognize_linked_entities()` iteruje po wynikach. Zwrócony obiekt odpowiedzi będzie zawierać listę wykrytych jednostek w programie , jeśli to się powiedzie, a jeśli nie, to `entities` `error` . Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są grupowane w obiekcie `entity` jako lista `match` obiektów.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Linked Entities:

        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 0
                Length: 9
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 168
                Length: 9
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Offset: 25
                Length: 10
                Text: Gates
                Confidence Score: 0.63
                Offset: 179
                Length: 5
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
                Offset: 40
                Length: 10
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
                Offset: 54
                Length: 7
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
                Offset: 98
                Length: 5
        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
                Offset: 125
                Length: 11
```

### <a name="personally-identifiable-information-recognition"></a>Rozpoznawanie danych osobowych

Utwórz nową funkcję o nazwie , która przyjmuje klienta jako argument, a następnie wywołuje funkcję i `pii_recognition_example` `recognize_pii_entities()` iteruje po wynikach. Zwrócony obiekt odpowiedzi będzie zawierać listę wykrytych jednostek w programie , jeśli to się powiedzie, a jeśli nie, to `entity` `error` . Dla każdej wykrytej jednostki wydrukuj jej kategorię i Sub-Category jeśli istnieje.

```python
def pii_recognition_example(client):
    documents = [
        "The employee's SSN is 859-98-0987.",
        "The employee's phone number is 555-555-5555."
    ]
    response = client.recognize_pii_entities(documents, language="en")
    result = [doc for doc in response if not doc.is_error]
    for doc in result:
        print("Redacted Text: {}".format(doc.redacted_text))
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("\tCategory: {}".format(entity.category))
            print("\tConfidence Score: {}".format(entity.confidence_score))
            print("\tOffset: {}".format(entity.offset))
            print("\tLength: {}".format(entity.length))
pii_recognition_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Redacted Text: The employee's SSN is ***********.
Entity: 859-98-0987
        Category: U.S. Social Security Number (SSN)
        Confidence Score: 0.65
        Offset: 22
        Length: 11
Redacted Text: The employee's phone number is ************.
Entity: 555-555-5555
        Category: Phone Number
        Confidence Score: 0.8
        Offset: 31
        Length: 12
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

> [!NOTE]
> W wersji `3.0` : 
> * Łączenie jednostek jest oddzielnym żądaniem niż NER.

Utwórz nową funkcję o nazwie , która przyjmuje klienta jako argument, a następnie wywołuje funkcję i `entity_recognition_example` `recognize_entities()` iteruje po wynikach. Zwrócony obiekt odpowiedzi będzie zawierać listę wykrytych jednostek w programie , jeśli to się powiedzie, a jeśli nie, to `entity` `error` . Dla każdej wykrytej jednostki wydrukuj jej kategorię i Sub-Category jeśli istnieje.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```

### <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie , która przyjmuje klienta jako argument, a następnie wywołuje funkcję i `entity_linking_example()` `recognize_linked_entities()` iteruje po wynikach. Zwrócony obiekt odpowiedzi będzie zawierać listę wykrytych jednostek w programie , jeśli to się `entities` powiedzie, a jeśli nie, to `error` . Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są grupowane pod obiektem `entity` jako lista `match` obiektów.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Linked Entities:

        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
```

---

### <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

Utwórz nową funkcję o nazwie `key_phrase_extraction_example()` , która przyjmuje klienta jako argument, a następnie wywołuje funkcję `extract_key_phrases()` . Wynik będzie zawierać listę wykrytych fraz kluczowych w przypadku powodzenia, a jeśli nie, to `key_phrases` `error` . Wydrukuj wszystkie wykryte frazy kluczowe.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Dane wyjściowe

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Utwórz nową funkcję o nazwie `key_phrase_extraction_example()` , która przyjmuje klienta jako argument, a następnie wywołuje funkcję `extract_key_phrases()` . Wynik będzie zawierać listę wykrytych fraz kluczowych w przypadku powodzenia, a jeśli nie, to `key_phrases` `error` . Wydrukuj wszystkie wykryte frazy kluczowe.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Dane wyjściowe

```console
    Key Phrases:
         cat
         veterinarian
```


---

## <a name="use-the-api-asynchronously-with-the-batch-analyze-operation"></a>Asynchroniczne używanie interfejsu API z operacją analizy wsadowej

# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Utwórz nową funkcję o nazwie `analyze_batch_actions_example()` , która przyjmuje klienta jako argument, a następnie wywołuje funkcję `begin_analyze_batch_actions()` . Wynikiem będzie długotrwała operacja, która będzie sondowana w celu wyszukiwania wyników.

```python
from azure.ai.textanalytics import (
    RecognizeEntitiesAction
)

def analyze_batch_example(client):
        documents = [
            "Microsoft was founded by Bill Gates and Paul Allen."
        ]

        poller = client.begin_analyze_batch_actions(
            documents,
            display_name="Sample Text Analysis",
            actions=[RecognizeEntitiesAction()]
        )

        result = poller.result()
        action_results = [action_result for action_result in list(result) if not action_result.is_error]

        entities_recognition_task_result = action_results[0]
        print("Results of Entities Recognition action:")
        docs = [doc for doc in entities_recognition_task_result.document_results if not doc.is_error]

        for idx, doc in enumerate(docs):
            print("\nDocument text: {}".format(documents[idx]))
            for entity in doc.entities:
                print("Entity: {}".format(entity.text))
                print("...Category: {}".format(entity.category))
                print("...Confidence Score: {}".format(entity.confidence_score))
                print("...Offset: {}".format(entity.offset))
            print("------------------------------------------")

analyze_batch_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Results of Entities Recognition task:
Document text: Microsoft was founded by Bill Gates and Paul Allen.
Entity: Microsoft
...Category: Organization
...Confidence Score: 0.83
...Offset: 0
Entity: Bill Gates
...Category: Person
...Confidence Score: 0.85
...Offset: 25
Entity: Paul Allen
...Category: Person
...Confidence Score: 0.9
...Offset: 40
------------------------------------------
```

Można również użyć operacji analizy wsadowej do wykrywania danych kluczowych i wyodrębniania kluczowych fraz. Zobacz przykład [batch analyze w witrynie](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples/sample_analyze_batch_actions.py) GitHub.

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

Ta funkcja nie jest dostępna w wersji 3.0.

---
