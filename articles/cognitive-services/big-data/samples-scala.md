---
title: Cognitive Services przykładów Scala danych Big Data
description: Użyj Cognitive Services, aby Azure Databricks do uruchamiania potoku MMLSpark na potrzeby danych Big Data.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: c47aa803774343b39efeabe3452f1b256cc64c0d
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363276"
---
# <a name="quick-examples"></a>Szybkie przykłady

Poniższe fragmenty kodu są gotowe do uruchomienia i pomogą Ci rozpocząć pracę z usługą Cognitive Services na platformie Spark. Poniższe przykłady znajdują się w Scala.

W przykładach są używane następujące Cognitive Services:

- Analiza tekstu — Pobierz tonacji (lub nastrój) zestawu zdań.
- Przetwarzanie obrazów — Pobierz Tagi (opisy jednego słowa) skojarzone z zestawem obrazów.
- Wyszukiwanie obrazów Bing — Przeszukaj w sieci Web obrazy związane z zapytaniem w języku naturalnym.
- Zamiana mowy na tekst — transkrypcja pliki audio, aby wyodrębnić transkrypcje tekstowe.
- Detektor anomalii — wykrywa anomalie w danych szeregów czasowych.

## <a name="prerequisites"></a>Wymagania wstępne

1. Wykonaj kroki opisane w sekcji [wprowadzenie](getting-started.md) , aby skonfigurować środowisko Azure Databricks i Cognitive Services. Ten samouczek zawiera informacje na temat sposobu instalowania programu MMLSpark i tworzenia klastra Spark w kostkach danych.
1. Po utworzeniu nowego notesu w Azure Databricks Skopiuj poniższy **kod współużytkowany** i wklej go do nowej komórki w notesie.
1. Wybierz przykład usługi poniżej i skopiuj go do drugiej nowej komórki w notesie.
1. Zastąp wszystkie symbole zastępcze klucza subskrypcji usługi własnym kluczem.
1. Wybierz przycisk Uruchom (ikona trójkąta) w prawym górnym rogu komórki, a następnie wybierz pozycję **Uruchom komórkę**.
1. Wyświetl wyniki w tabeli poniżej komórki.

## <a name="shared-code"></a>Udostępniony kod
Aby rozpocząć, Dodaj ten kod do projektu:

```scala
import com.microsoft.ml.spark.cognitive._
import spark.implicits._ 

val serviceKey = "ADD-YOUR-SUBSCRIPTION-KEY"
val location = "eastus"
```

## <a name="text-analytics"></a>Analiza tekstu

Usługa [Analiza tekstu](../text-analytics/index.yml) udostępnia kilka algorytmów służących do wyodrębniania inteligentnych analiz z tekstu. Na przykład możemy znaleźć tonacji danego tekstu wejściowego. Usługa zwróci wynik z przedziału `0.0` , `1.0` gdzie niskie wyniki wskazują negatywną tonacji, a wysoka Punktacja wskazuje pozytywną tonacji.  Poniższy przykład używa trzech prostych zdań i zwraca wynik tonacji dla każdego z nich.

```scala
import org.apache.spark.sql.functions.col

val df = Seq(
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US")
).toDF("text", "language")

val sentiment = new TextSentiment()
    .setTextCol("text")
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language")

display(sentiment.transform(df).select(col("text"), col("sentiment")(0).getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>Oczekiwany wynik

| tekst                                      | tonacji                                             |
|:------------------------------------------|:------------------------------------------------------|
| Dziś mam szczęście, Sunny!           | 0.9789592027664185                                    |
| Jestem sfrustrowani przez ten szczytuy ruch godzinny | 0.023795604705810547                                  |
| Usługi poznawcze w usłudze Spark aint są nieprawidłowe  | 0.8888956308364868                                    |

## <a name="computer-vision"></a>Przetwarzanie obrazów

[Przetwarzanie obrazów](../computer-vision/index.yml) analizuje obrazy w celu zidentyfikowania struktury, takiej jak twarze, obiekty i opisy języka naturalnego.
W tym przykładzie oznaczmy listę obrazów. Tagi to jednowyrazowe opisy elementów na obrazie, takich jak rozpoznawalne obiekty, ludzie, scenerii i akcje.

```scala
// Create a dataframe with the image URLs
val df = Seq(
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg")
).toDF("image")

// Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
val analysis = new AnalyzeImage()
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setVisualFeatures(Seq("Categories","Color","Description","Faces","Objects","Tags"))
    .setOutputCol("results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

// Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select(col("image"), col("results").getItem("tags").getItem("name")).alias("results")))

// Uncomment for full results with all visual feature requests
//display(analysis.transform(df).select(col("image"), col("results")))
``` 

### <a name="expected-result"></a>Oczekiwany wynik

| image (obraz) | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ["naznanie" "człowiek" "mężczyzna" "," "," "," "," "," "," "," "," "," "," "," "" — "" "
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ["Dog" "," "," "," "," "," "," "," "," "," "," "," "" "" "" "" "" "" "" "" "" "" "                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ["zewnętrzny" "" "" "" "" "" "" "" "" "" "" "" "" "" "" — "" "" "" "" kanap "", "", "", "", "", "", "", "", "", "", "", "" " 

## <a name="bing-image-search"></a>Wyszukiwanie obrazów w usłudze Bing

[Wyszukiwanie obrazów Bing](../bing-image-search/overview.md) przeszukuje sieć Web, aby pobrać obrazy powiązane z zapytaniem w języku naturalnym użytkownika. W tym przykładzie używamy zapytania tekstowego, które wyszukuje obrazy z cudzysłowami. Zwraca listę adresów URL obrazów, które zawierają zdjęcia powiązane z naszym zapytaniem.


```scala
import org.apache.spark.ml.Pipeline

// Number of images Bing will return per query
val imgsPerBatch = 10

// A list of offsets, used to page into the search results
val df = (0 until 100).map(o => Tuple1(o*imgsPerBatch)).toSeq.toDF("offset")

// Run the Bing Image Search service with our text query
val bingSearch = new BingImageSearch()
    .setSubscriptionKey(bingSearchKey)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images")

// Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
val getUrls = BingImageSearch.getUrlTransformer("images", "url")

// This displays the full results returned, uncomment to use
// display(bingSearch.transform(bingParameters))

// Since we have two services, they are put into a pipeline
val pipeline = new Pipeline().setStages(Array(bingSearch, getUrls))

// Show the results of your search: image URLs
display(pipeline.fit(df).transform(df))
```

### <a name="expected-result"></a>Oczekiwany wynik

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Usługa [zamiany mowy na tekst](../speech-service/index-speech-to-text.yml) konwertuje strumienie lub pliki głosowego dźwięku na tekst. W tym przykładzie transkrypcjamy dwa pliki audio. Pierwszy plik jest łatwy do zrozumienia, a drugi jest trudniejszy.

```scala
import org.apache.spark.sql.functions.col

// Create a dataframe with audio URLs, tied to the column called "url"
val df = Seq(("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav"),
             ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3")).toDF("url")

// Run the Speech-to-text service to translate the audio into text
val speechToText = new SpeechToTextSDK()
    .setSubscriptionKey(serviceKey)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked")

// Show the results of the translation
display(speechToText.transform(df).select(col("url"), col("text").getItem("DisplayText")))
```

### <a name="expected-result"></a>Oczekiwany wynik

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Niestandardowa funkcja mowy udostępnia narzędzia umożliwiające wizualne badanie jakości rozpoznawania modelu przez porównanie danych audio z odpowiednim wynikiem rozpoznawania z niestandardowego portalu mowy. Możesz odtworzyć przekazany dźwięk i określić, czy podany wynik rozpoznawania jest poprawny. To narzędzie umożliwia szybkie sprawdzenie jakości linii bazowej firmy Microsoft do modelu tekstu lub przeszkolonego modelu niestandardowego bez konieczności transkrypcja jakichkolwiek danych audio. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Dodaj gentlemaną do zamyślenia.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Słyszę. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Mam również gwarancję, że jest to możliwe. |

## <a name="anomaly-detector"></a>Narzędzie do wykrywania anomalii

[Detektor anomalii](../anomaly-detector/index.yml) doskonale nadaje się do wykrywania niezgodności w danych szeregów czasowych. W tym przykładzie używamy usługi, aby znaleźć anomalie w całej szeregu czasowym.

```scala
import org.apache.spark.sql.functions.{col, lit}

val anomalyKey = "84a2c303cc7e49f6a44d692c27fb9967"

val df = Seq(
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
  ).toDF("timestamp", "value").withColumn("group", lit("series1"))

// Run the Anomaly Detector service to look for irregular data
val anamolyDetector = new SimpleDetectAnomalies()
    .setSubscriptionKey(anomalyKey)
    .setLocation("eastus")
    .setTimestampCol("timestamp")
    .setValueCol("value")
    .setOutputCol("anomalies")
    .setGroupbyCol("group")
    .setGranularity("monthly")

// Show the full results of the analysis with the anomalies marked as "True"
display(anamolyDetector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
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