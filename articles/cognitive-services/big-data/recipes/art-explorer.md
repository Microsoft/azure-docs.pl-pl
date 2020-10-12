---
title: 'Przepis: inteligentne Eksplorowanie dzieła przy użyciu Cognitive Services na potrzeby danych Big Data'
titleSuffix: Azure Cognitive Services
description: Ten przepis pokazuje, jak utworzyć bazę danych z możliwością wyszukiwania przy użyciu Azure Search i MMLSpark.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 0a94c66eb51298db226ceec5da5c86666576052a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850495"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>Przepis: inteligentne Eksplorowanie dzieła przy użyciu Cognitive Services na potrzeby danych Big Data

W tym przykładzie użyjemy Cognitive Services na potrzeby danych Big Data, aby dodać inteligentne adnotacje do otwartej kolekcji dostępu z metropolitalnych muzeów grafiki (MET). Umożliwi to tworzenie inteligentnego aparatu wyszukiwania przy użyciu Azure Search nawet bez ręcznej adnotacji. 

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz mieć klucz subskrypcji dla przetwarzanie obrazów i Wyszukiwanie poznawcze. Postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , aby subskrybować przetwarzanie obrazów i uzyskać klucz.
  > [!NOTE]
  > Aby uzyskać informacje o cenach, zobacz [Azure wyszukiwanie poznawcze](https://azure.microsoft.com/services/search/#pricing).

## <a name="import-libraries"></a>Importuj biblioteki

Uruchom następujące polecenie, aby zaimportować biblioteki dla tego przepisu.

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>Konfigurowanie kluczy subskrypcji

Uruchom następujące polecenie, aby skonfigurować zmienne dla kluczy usługi. Wstaw klucze subskrypcji dla przetwarzanie obrazów i Wyszukiwanie poznawcze platformy Azure.

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>Odczytaj dane

Uruchom następujące polecenie, aby załadować dane z otwartej kolekcji dostępu.

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>Analizowanie obrazów

Uruchom następujące polecenie, aby użyć przetwarzanie obrazów w kolekcji "otwarte grafiki dostępu". W efekcie uzyskasz funkcje wizualne z kompozycji.

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>Utwórz indeks wyszukiwania

Uruchom następujące polecenie, aby napisać wyniki do Azure Search, aby utworzyć aparat wyszukiwania kompozycji z ulepszonymi metadanymi z przetwarzanie obrazów.

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>Wykonywanie zapytań względem indeksu wyszukiwania

Uruchom następujące polecenie, aby wykonać zapytanie dotyczące indeksu Azure Search.

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak za pomocą [Cognitive Services danych Big Data wykrywać anomalie](anomaly-detection.md).

