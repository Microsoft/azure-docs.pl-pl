---
title: 'Samouczek: kompilowanie aplikacji do uczenia maszynowego przy użyciu programu Microsoft Machine Learning dla Apache Spark (wersja zapoznawcza)'
description: Dowiedz się, jak za pomocą programu Microsoft Machine Learning Apache Spark tworzyć aplikacje uczenia maszynowego w usłudze Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruxu
ms.author: ruxu
ms.openlocfilehash: a3899b83133b3f951547fae0b11c044bfa85a5fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589603"
---
# <a name="tutorial-build-machine-learning-applications-using-microsoft-machine-learning-for-apache-spark-preview"></a>Samouczek: kompilowanie aplikacji do uczenia maszynowego przy użyciu programu Microsoft Machine Learning dla Apache Spark (wersja zapoznawcza)

W tym artykule dowiesz się, jak używać programu Microsoft Machine Learning for Apache Spark ([MMLSpark](https://github.com/Azure/mmlspark)) do tworzenia aplikacji do uczenia maszynowego. MMLSpark rozszerza rozbudowane rozwiązanie uczenia maszynowego Apache Spark przez dodanie wielu narzędzi do uczenia i nauki o danych, takich jak [Azure Cognitive Services](../../cognitive-services/big-data/cognitive-services-for-big-data.md), [OpenCV](https://opencv.org/), [LightGBM](https://github.com/Microsoft/LightGBM) i innych.  MMLSpark umożliwia tworzenie zaawansowanych i wysoce skalowalnych modeli predykcyjnych i analitycznych z różnych źródeł danych platformy Spark.
Synapse Spark udostępnia wbudowane biblioteki MMLSpark, w tym:

- [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit) — usługi bibliotek do uczenia maszynowego, aby umożliwić analizę tekstu, taką jak tonacji Analysis w Tweetach.
- [Cognitive Services na platformie Spark](../../cognitive-services/big-data/cognitive-services-for-big-data.md) — aby połączyć funkcję usługi Azure Cognitive Services w potokach SparkML, aby utworzyć projekt rozwiązania dla usług modelowania danych poznawczych, takich jak wykrywanie anomalii.
- [LightBGM](https://github.com/Azure/mmlspark/blob/master/docs/lightgbm.md) — model uczenia maszynowego, aby umożliwić uczenie modelu na potrzeby analizy predykcyjnej, takiej jak wykrywanie identyfikatorów czołowych.
- KNN warunkowo skalowalne modele KNN z kwerendami warunkowymi.
- [Http na platformie Spark](https://github.com/Azure/mmlspark/blob/master/docs/http.md) — umożliwia organizowanie dystrybucji mikrousług w ramach integracji opartych na protokole Spark i http.

W tym samouczku omówiono przykłady korzystania z platformy Azure Cognitive Services w programie MMLSpark for 

- Analiza tekstu — Pobierz tonacji (lub nastrój) zestawu zdań.
- Przetwarzanie obrazów — Pobierz Tagi (opisy jednego słowa) skojarzone z zestawem obrazów.
- Wyszukiwanie obrazów Bing — Przeszukaj w sieci Web obrazy związane z zapytaniem w języku naturalnym.
- Detektor anomalii — wykrywa anomalie w danych szeregów czasowych.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne 

- [Obszar roboczy usługi Azure Synapse Analytics](../get-started-create-workspace.md) z kontem magazynu Azure Data Lake Storage Gen2 skonfigurowanym jako magazyn domyślny. Musisz być *współautorem danych obiektów blob magazynu* w systemie plików Data Lake Storage Gen2, z którym pracujesz.
- Pula platformy Spark w obszarze roboczym usługi Azure Synapse Analytics. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie puli platformy Spark w usłudze Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Kroki przed rozpoczęciem konfiguracji opisane w samouczku [konfigurowanie Cognitive Services w usłudze Azure Synapse](./tutorial-configure-cognitive-services-synapse.md).


## <a name="get-started"></a>Rozpoczęcie pracy
Aby rozpocząć, zaimportuj klucze usług mmlspark i configurate.

```python
import mmlspark
mmlspark.__spark_package_version__ # current version: 1.0.0-rc3-6-a862d6b1-SNAPSHOT

from mmlspark.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY" 
# An Anomaly Dectector subscription key
anomaly_key =  "ADD_YOUR_SUBSCRIPION_KEY" 


cognitive_service_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", service_key)
bingsearch_service_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", bing_search_key)
anomalydetector_key = mssparkutils.credentials.getSecret("keyvaultForSynapse", anomaly_key)

```

## <a name="text-analytics-sample"></a>Przykład analizy tekstu

Usługa [Analiza tekstu](../../cognitive-services/text-analytics/index.yml) udostępnia kilka algorytmów służących do wyodrębniania inteligentnych analiz z tekstu. Na przykład możemy znaleźć tonacji danego tekstu wejściowego. Usługa zwróci wynik z przedziału od 0,0 do 1,0, gdzie niskie wyniki wskazują, że wartość ujemna tonacji i wysoka Ocena wskazuje pozytywną tonacji. Ten przykład używa trzech prostych zdań i zwraca tonacji dla każdego z nich.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>Oczekiwane wyniki

|tekst | tonacji|
|--|--|
| Jestem sfrustrowani przez ten szczytuy ruch godzinny! | negative |
| to jest pies | neutral |
| Dziś mam szczęście, Sunny! | positive |

## <a name="computer-vision-sample"></a>Przykładowa obsługa komputera
[Przetwarzanie obrazów](../../cognitive-services/computer-vision/index.yml) analizuje obrazy w celu zidentyfikowania struktury, takiej jak twarze, obiekty i opisy języka naturalnego. W tym przykładzie Tagi obserwujemy. Tagi to jednowyrazowe opisy elementów na obrazie, takich jak rozpoznawalne obiekty, ludzie, scenerii i akcje.


![image (obraz)](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>Oczekiwane wyniki

|image (obraz) | tags|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [Napływanie, osoba, człowiek, na zewnątrz, Jazda, Sport, Skateboard, młode, płyty, Koszulka, powietrze, parkowanie, Boy, boczne, przechodzenie, rampa, lewę, jazda |

## <a name="bing-image-search-sample"></a>Przykład wyszukiwania obrazów Bing
[Wyszukiwanie obrazów Bing](../../cognitive-services/bing-image-search/overview.md) przeszukuje sieć Web, aby pobrać obrazy powiązane z zapytaniem w języku naturalnym użytkownika. W tym przykładzie używamy zapytania tekstowego, które wyszukuje obrazy z cudzysłowami. Zwraca listę adresów URL obrazów, które zawierają zdjęcia powiązane z naszym zapytaniem.


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>Oczekiwane wyniki

|image (obraz) | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>Przykład wykrywania anomalii

[Detektor anomalii](../../cognitive-services/anomaly-detector/index.yml) doskonale nadaje się do wykrywania niezgodności w danych szeregów czasowych. W tym przykładzie używamy usługi, aby znaleźć anomalie w całej szeregu czasowym.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>Oczekiwane wyniki

|sygnatura czasowa | wartość | isanomalia |
|--|--|--|
| 1972 R-01-01T00:00:00Z|826,0|fałsz|
|1972 — 02-01T00:00:00Z|799,0|fałsz|
|1972 – 03-01T00:00:00Z|890,0|fałsz|
|1972 – 04-01T00:00:00Z|900,0|fałsz|
|1972 – 05-01T00:00:00Z|766,0|fałsz|
|1972 – 06-01T00:00:00Z|805,0|fałsz|
|1972 — 07-01T00:00:00Z|821,0|fałsz|
|1972-08-01T00:00:00Z|20000,0|true|
|1972 – 09-01T00:00:00Z|883,0|fałsz|
|1972-10-01T00:00:00Z|898,0|fałsz|
|1972-11-01T00:00:00Z|957,0|fałsz|
|1972-12-01T00:00:00Z|924,0|fałsz|
|1973-01-01T00:00:00Z|881,0|fałsz|
|1973-02-01T00:00:00Z|837,0|fałsz|
|1973-03-01T00:00:00Z|9000,0|true|

## <a name="next-steps"></a>Następne kroki

* [Zapoznaj się z przykładowymi notesami Synapse](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks) 
* [Repozytorium GitHub MMLSpark](https://github.com/Azure/mmlspark)