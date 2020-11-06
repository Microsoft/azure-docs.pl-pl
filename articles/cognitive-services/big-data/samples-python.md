---
title: Cognitive Services danych Big Data — przykłady dla środowiska Python
description: Wypróbuj Cognitive Services przykłady w języku Python, aby Azure Databricks do uruchamiania potoku MMLSpark na potrzeby danych Big Data.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 1a4fe2492433aa793d1a7e4be41c5f93043848a5
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337869"
---
# <a name="python-samples-for-cognitive-services-for-big-data"></a>Przykłady języka Python dla Cognitive Services danych Big Data

Poniższe fragmenty kodu są gotowe do uruchomienia i pomogą Ci rozpocząć korzystanie z Cognitive Services na platformie Spark przy użyciu języka Python.

W przykładach w tym artykule są używane następujące Cognitive Services:

- Analiza tekstu — Pobierz tonacji (lub nastrój) zestawu zdań.
- Przetwarzanie obrazów — Pobierz Tagi (opisy jednego słowa) skojarzone z zestawem obrazów.
- Wyszukiwanie obrazów Bing — Przeszukaj w sieci Web obrazy związane z zapytaniem w języku naturalnym.
- Zamiana mowy na tekst — transkrypcja pliki audio, aby wyodrębnić transkrypcje tekstowe.
- Detektor anomalii — wykrywa anomalie w danych szeregów czasowych.

## <a name="prerequisites"></a>Wymagania wstępne

1. Wykonaj kroki opisane w sekcji [wprowadzenie](getting-started.md) , aby skonfigurować środowisko Azure Databricks i Cognitive Services. W tym samouczku pokazano, jak zainstalować MMLSpark i jak utworzyć klaster Spark w kostkach danych.
1. Po utworzeniu nowego notesu w Azure Databricks Skopiuj poniższy **kod współużytkowany** i wklej go do nowej komórki w notesie.
1. Wybierz przykład usługi poniżej i skopiuj go do drugiej nowej komórki w notesie.
1. Zastąp wszystkie symbole zastępcze klucza subskrypcji usługi własnym kluczem.
1. Wybierz przycisk Uruchom (ikona trójkąta) w prawym górnym rogu komórki, a następnie wybierz pozycję **Uruchom komórkę**.
1. Wyświetl wyniki w tabeli poniżej komórki.

## <a name="shared-code"></a>Udostępniony kod

Aby rozpocząć, należy dodać ten kod do projektu:

```python
from mmlspark.cognitive import *

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key = "ADD_YOUR_SUBSCRIPION_KEY"
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY"
# An Anomaly Dectector subscription key
anomaly_key = "ADD_YOUR_SUBSCRIPION_KEY"

# Validate the key
assert service_key != "ADD_YOUR_SUBSCRIPION_KEY"
```    

## <a name="text-analytics-sample"></a>Przykład analiza tekstu

Usługa [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) udostępnia kilka algorytmów służących do wyodrębniania inteligentnych analiz z tekstu. Na przykład możemy znaleźć tonacji danego tekstu wejściowego. Usługa zwróci wynik z przedziału od 0,0 do 1,0, gdzie niskie wyniki wskazują, że wartość ujemna tonacji i wysoka Ocena wskazuje pozytywną tonacji.  Ten przykład używa trzech prostych zdań i zwraca tonacji dla każdego z nich.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format
display(sentiment.transform(df).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-result"></a>Oczekiwany wynik

| tekst                                      | tonacji                                             |
|:------------------------------------------|:------------------------------------------------------|
| Dziś mam szczęście, Sunny!           | positive                                              |
| Jestem sfrustrowani przez ten szczytuy ruch godzinny | negative                                              |
| Usługi poznawcze w usłudze Spark aint są nieprawidłowe  | positive                                              |

## <a name="computer-vision-sample"></a>Przykład przetwarzanie obrazów

[Przetwarzanie obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) analizuje obrazy w celu zidentyfikowania struktury, takiej jak twarze, obiekty i opisy języka naturalnego. W tym przykładzie oznaczmy listę obrazów. Tagi to jednowyrazowe opisy elementów na obrazie, takich jak rozpoznawalne obiekty, ludzie, scenerii i akcje.

```python

# Create a dataframe with the image URLs
df = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select("image", "analysis_results.description.tags"))
```

### <a name="expected-result"></a>Oczekiwany wynik

| image (obraz) | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ["naznanie" "człowiek" "mężczyzna" "," "," "," "," "," "," "," "," "," "," "," "" — "" "
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ["Dog" "," "," "," "," "," "," "," "," "," "," "," "" "" "" "" "" "" "" "" "" "" "                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ["zewnętrzny" "" "" "" "" "" "" "" "" "" "" "" "" "" "" — "" "" "" "" kanap "", "", "", "", "", "", "", "", "", "", "", "" "


## <a name="bing-image-search-sample"></a>Przykład wyszukiwanie obrazów Bing

[Wyszukiwanie obrazów Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview) przeszukuje sieć Web, aby pobrać obrazy powiązane z zapytaniem w języku naturalnym użytkownika. W tym przykładzie używamy zapytania tekstowego, które wyszukuje obrazy z cudzysłowami. Zwraca listę adresów URL obrazów, które zawierają zdjęcia powiązane z naszym zapytaniem.

```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 10
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(100)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bing_search_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")

# This displays the full results returned, uncomment to use
# display(bingSearch.transform(bingParameters))

# Since we have two services, they are put into a pipeline
pipeline = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
display(pipeline.transform(bingParameters))
```

### <a name="expected-result"></a>Oczekiwany wynik

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |


## <a name="speech-to-text-sample"></a>Przykład zamiany mowy na tekst
Usługa [zamiany mowy na tekst](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text) konwertuje strumienie lub pliki głosowego dźwięku na tekst. W tym przykładzie transkrypcjamy dwa pliki audio. Pierwszy plik jest łatwy do zrozumienia, a drugi jest trudniejszy.

```python

# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",),
                           ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```

### <a name="expected-result"></a>Oczekiwany wynik

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Niestandardowa funkcja mowy udostępnia narzędzia umożliwiające wizualne badanie jakości rozpoznawania modelu przez porównanie danych audio z odpowiednim wynikiem rozpoznawania z niestandardowego portalu mowy. Możesz odtworzyć przekazany dźwięk i określić, czy podany wynik rozpoznawania jest poprawny. To narzędzie umożliwia szybkie sprawdzenie jakości linii bazowej firmy Microsoft do modelu tekstu lub przeszkolonego modelu niestandardowego bez konieczności transkrypcja jakichkolwiek danych audio. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Dodaj gentlemaną do zamyślenia.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Słyszę. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Mam również gwarancję, że jest to możliwe. |


## <a name="anomaly-detector-sample"></a>Przykład wykrywania anomalii

[Detektor anomalii](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/) doskonale nadaje się do wykrywania niezgodności w danych szeregów czasowych. W tym przykładzie używamy usługi, aby znaleźć anomalie w całej szeregu czasowym.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0),
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0)
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomaly_key)
  .setLocation("eastus")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
```

### <a name="expected-result"></a>Oczekiwany wynik

| sygnatura czasowa            |   value | isanomalia   |
|:---------------------|--------:|:------------|
| 1972 R-01-01T00:00:00Z |     826 | Fałsz       |
| 1972 — 02-01T00:00:00Z |     799 | Fałsz       |
| 1972 – 03-01T00:00:00Z |     890 | Fałsz       |
| 1972 – 04-01T00:00:00Z |     900 | Fałsz       |
| 1972 – 05-01T00:00:00Z |     766 | Fałsz       |
| 1972 – 06-01T00:00:00Z |     805 | Fałsz       |
| 1972 — 07-01T00:00:00Z |     821 | Fałsz       |
| 1972-08-01T00:00:00Z |   20000 | Prawda        |
| 1972 – 09-01T00:00:00Z |     883 | Fałsz       |
| 1972-10-01T00:00:00Z |     898 | Fałsz       |
| 1972-11-01T00:00:00Z |     957 | Fałsz       |
| 1972-12-01T00:00:00Z |     924 | Fałsz       |
| 1973-01-01T00:00:00Z |     881 | Fałsz       |
| 1973-02-01T00:00:00Z |     837 | Fałsz       |
| 1973-03-01T00:00:00Z |    9000 | Prawda        |

## <a name="arbitrary-web-apis"></a>Dowolne interfejsy API sieci Web

W przypadku protokołu HTTP na platformie Spark w potoku danych Big Data może być używana jakakolwiek usługa sieci Web. W tym przykładzie używamy [interfejsu API Banku Światowego](http://api.worldbank.org/v2/country/) do uzyskiwania informacji o różnych krajach na całym świecie.

```python
from requests import Request
from mmlspark.io.http import HTTPTransformer, http_udf
from pyspark.sql.functions import udf, col

# Use any requests from the python requests library
def world_bank_request(country):
  return Request("GET", "http://api.worldbank.org/v2/country/{}?format=json".format(country))

# Create a dataframe with spcificies which countries we want data on
df = (spark.createDataFrame([("br",),("usa",)], ["country"])
  .withColumn("request", http_udf(world_bank_request)(col("country"))))

# Much faster for big data because of the concurrency :)
client = (HTTPTransformer()
      .setConcurrency(3)
      .setInputCol("request")
      .setOutputCol("response"))

# Get the body of the response
def get_response_body(resp):
  return resp.entity.content.decode()

# Show the details of the country data returned
display(client.transform(df).select("country", udf(get_response_body)(col("response")).alias("response")))
```

### <a name="expected-result"></a>Oczekiwany wynik

| country   | odpowiedź |
|:----------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| br | [{"page": 1, "Pages": 1, "per_page": "50", "Total": 1}, [{"ID": "BRA", "iso2Code": "BR", "name": "Brazylia", "region": {"ID": "LCN", "iso2Code": "ZJ", "value": "Ameryka Łacińska & Karaibów"}, "adminregion": {"ID": "LAC", "iso2Code": "XJ", "value": "Ameryka Łacińska & Karaibów (z wyłączeniem wysokich dochodów)"}, "incomeLevel": {"ID": "UMC", "iso2Code": "XT", "value": "górny dochód środkowy"}, "kredytytype": {"ID": "IBD", "iso2Code": "XF", "value": "IBRD"}, "capitalCity": "Brazylia", "Długość geograficzna": "-47.9292", "Latitude": "-15,7801"}]] |
| poniżej  | [{"page": 1, "strony": 1, "per_page": "50", "łącznie": 1}, [{"ID": "USA", "iso2Code": "US", "name": "Stany Zjednoczone", "region": {"ID": "NAC", "iso2Code": "XU", "value": "Ameryka Północna"}, "adminregion": {"ID": "", "iso2Code": "", "value": ""}, "incomeLevel": {"ID": "HIC", "iso2Code": "XD", "value": "High dochodu"}, "użyczenitype": {"ID": "LNX", "iso2Code": "XX", "value": "nie sklasyfikowano"}, "capitalCity": "Waszyngton Waszyngtonie", "Długość geograficzna": "-77.032", "Latitude": "38.8895"}]] |

## <a name="see-also"></a>Zobacz też

* [Przepis: wykrywanie anomalii](./recipes/anomaly-detection.md)
* [Przepis: Eksplorator grafiki](./recipes/art-explorer.md)
