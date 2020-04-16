---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: aahi
ms.openlocfilehash: d58f294195efc393c07ecc3886c29e33dba02e6d
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81422118"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

[v3 Dokumentacja](https://aka.ms/azsdk-python-textanalytics-ref-docs) | [referencyjna v3 Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | źródłowy biblioteki[v3 Pakiet (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3 Przykłady](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

[v2 Dokumentacja](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [referencyjna v2 Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | źródłowy biblioteki[v2 Pakiet (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2 Przykłady](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* Po utworzeniu subskrypcji <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="platformy Azure utwórz"  target="_blank">zasób <span class="docon docon-navigate-external x-hidden-focus"></span> </a> analizy tekstu w witrynie Azure portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu kliknij przycisk **Przejdź do zasobu**.
    * Potrzebny będzie klucz i punkt końcowy z zasobu, który tworzysz, aby połączyć aplikację z interfejsem API analizy tekstu. Wklej klucz i punkt końcowy do kodu poniżej w dalszej części przewodnika Szybki start.
    * Można użyć bezpłatnej warstwy`F0`cenowej ( ), aby wypróbować usługę, a następnie uaktualnić do warstwy płatnej dla produkcji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Po zainstalowaniu języka Python można zainstalować bibliotekę klienta za pomocą:

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu naraz? Można go znaleźć [na GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), który zawiera przykłady kodu w tym przewodniku Szybki start. 

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu naraz? Można go znaleźć [na GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py), który zawiera przykłady kodu w tym przewodniku Szybki start. 

---

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji języka Python

Utwórz nowy plik języka Python i utwórz zmienne dla punktu końcowego i klucza subskrypcji platformy Azure.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Model obiektu

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Klient analizy tekstu `TextAnalyticsClient` jest obiektem, który uwierzytelnia się na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu jako partii. 

Gdy tekst przetwarzania wsadowego jest wysyłany do `dictionary` interfejsu API jako `id` `text`lista `language` `documents`, które są obiektami zawierającymi kombinację , i atrybutami w zależności od zastosowanej metody. Atrybut `text` przechowuje tekst do analizy w `language`źródle i `id` może być dowolną wartością. 

Obiekt odpowiedzi jest listą zawierającą informacje analityczne dla każdego dokumentu. 

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Klient analizy tekstu jest [textanalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) obiektu, który uwierzytelnia się na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu, jako pojedynczy ciąg lub partii. 

Tekst jest wysyłany do interfejsu `documents`API `dictionary` jako lista , `id`które `text`są `language` obiektami zawierającymi kombinację , i atrybutów w zależności od zastosowanej metody. Atrybut `text` przechowuje tekst do analizy w `language`źródle i `id` może być dowolną wartością. 

---

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta analizy tekstu dla języka Python:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Uznanie nazwanej encji](#named-entity-recognition-ner) 
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie fraz kluczowych](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz funkcję tworzenia wystąpienia `TextAnalyticsClient` obiektu `key` z `endpoint` and utworzone powyżej. Następnie utwórz nowego klienta. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Utwórz funkcję tworzenia wystąpienia `TextAnalyticsClient` obiektu `key` z `endpoint` and utworzone powyżej. Następnie utwórz nowego klienta. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Analiza tonacji

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz nową `sentiment_analysis_example()` funkcję o nazwie, która przyjmuje `analyze_sentiment()` klienta jako argument, a następnie wywołuje funkcję. Zwrócony obiekt odpowiedzi będzie zawierał etykietę tonacji i wynik całego dokumentu wejściowego, a także analizę tonacji dla każdego zdania.


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
        print("[Length: {}]".format(sentence.grapheme_length))
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

[Length: 30]
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

[Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Uwierzytelnij obiekt klienta i wywołaj funkcję [sentiment().](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) Iteracji przez wyniki i wydrukować identyfikator każdego dokumentu i wynik tonacji. Wynik bliższy 0 wskazuje na negatywne nastroje, podczas gdy wynik bliższy 1 wskazuje na pozytywne nastroje.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>Wykrywanie języka

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz nową `language_detection_example()` funkcję o nazwie, która przyjmuje `detect_language()` klienta jako argument, a następnie wywołuje funkcję. Zwrócony obiekt odpowiedzi będzie zawierać `primary_language` wykryty język `error` w if successful, a jeśli nie.

> [!Tip]
> W niektórych przypadkach może być trudne do odróżnienia języków na podstawie danych wejściowych. Za pomocą `country_hint` tego parametru można określić 2-literowy kod kraju. Domyślnie interfejs API używa "US" jako domyślnego krajuHint, aby usunąć to zachowanie, możesz `country_hint : ""`zresetować ten parametr, ustawiając tę wartość na pusty ciąg . 

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

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Przy użyciu klienta utworzonego wcześniej, wywołaj [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) i uzyskaj wynik. Następnie iterować przez wyniki i wydrukować identyfikator każdego dokumentu i pierwszy zwrócony język.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


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

Utwórz nową `entity_recognition_example` funkcję o nazwie, która przyjmuje `recognize_entities()` klienta jako argument, a następnie wywołuje funkcję i iteruje za pośrednictwem wyników. Zwrócony obiekt odpowiedzi będzie zawierać listę wykrytych jednostek `entity` `error` w if successful, a jeśli nie. Dla każdej wykrytej jednostki wydrukuj jej kategorię i podkategorię, jeśli istnieje.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tLength: \t", entity.grapheme_length, "\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Named Entities:

    Text:    Seattle        Category:        Location       SubCategory:     GPE
    Length:          7      Confidence Score:        0.92

    Text:    last week      Category:        DateTime       SubCategory:     DateRange
    Length:          9      Confidence Score:        0.8
```

## <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową `entity_linking_example()` funkcję o nazwie, która przyjmuje `recognize_linked_entities()` klienta jako argument, a następnie wywołuje funkcję i iteruje za pośrednictwem wyników. Zwrócony obiekt odpowiedzi będzie zawierać listę wykrytych jednostek `entities` `error` w if successful, a jeśli nie. Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej `entity` samej encji `match` są grupowane pod obiektem jako lista obiektów.

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
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score), "\tLength: {}\n".format(match.grapheme_length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Dane wyjściowe

```console
Linked Entities:

    Name:  Altair 8800     Id:  Altair 8800     Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Confidence Score: 0.00     Length: 11

    Name:  Bill Gates     Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Confidence Score: 0.00     Length: 10

        Text: Gates
        Confidence Score: 0.00     Length: 5

    Name:  Paul Allen     Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Confidence Score: 0.00     Length: 10

    Name:  Microsoft     Id:  Microsoft     Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Confidence Score: 0.00     Length: 9

        Text: Microsoft
        Confidence Score: 0.00     Length: 9

    Name:  April 4     Id:  April 4     Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Confidence Score: 0.00     Length: 7

    Name:  BASIC     Id:  BASIC     Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Confidence Score: 0.00     Length: 5
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

> [!NOTE]
> W wersji 2.1 łączenie jednostek jest zawarte w odpowiedzi NER.

Korzystając z klienta utworzonego wcześniej, wywołaj funkcję [entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) i uzyskaj wynik. Następnie iterować przez wyniki i wydrukować identyfikator każdego dokumentu i jednostek zawartych w nim.

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

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

Utwórz nową `key_phrase_extraction_example()` funkcję o nazwie, która przyjmuje `extract_key_phrases()` klienta jako argument, a następnie wywołuje funkcję. Wynik będzie zawierać listę wykrytych fraz `key_phrases` kluczowych w if `error` successful, a jeśli nie. Drukowanie wykrytych fraz kluczowych.

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

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Korzystając z klienta utworzonego wcześniej, należy wywołać funkcję [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) i uzyskać wynik. Następnie iterować przez wyniki i wydrukować identyfikator każdego dokumentu i kluczowe frazy zawarte w nim.

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

---
