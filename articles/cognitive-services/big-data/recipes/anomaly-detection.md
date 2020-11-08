---
title: 'Przepis: konserwacja predykcyjna z Cognitive Servicesami dla danych Big Data'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start przedstawia sposób wykonywania rozproszonego wykrywania anomalii przy użyciu Cognitive Services danych Big Data
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: d2995f39bc61ae5bb87abafd674f411271e57ca2
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366285"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>Przepis: konserwacja predykcyjna z Cognitive Servicesami dla danych Big Data

W tym przepisie pokazano, jak za pomocą usługi Azure Synapse Analytics i Cognitive Services na Apache Spark na potrzeby konserwacji predykcyjnej urządzeń IoT. Będziemy postępować zgodnie z przykładem [linku CosmosDB i Synapse](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) . Aby zachować prostotę, w tym przepisie odczytamy dane bezpośrednio z pliku CSV, zamiast pobierać przesyłane strumieniowo dane za pośrednictwem CosmosDB i Synapse. Zdecydowanie zachęcamy do przeszukiwania przykładu linku Synapse.

## <a name="hypothetical-scenario"></a>Hipotetyczny scenariusz

Hipotetycznym scenariuszem jest elektrownia, w której urządzenia IoT monitorują [turbiny parowe](https://en.wikipedia.org/wiki/Steam_turbine). Kolekcja IoTSignals ma obroty na minutę (RPM) i Megawatts (MW) dla każdej turbiny. Sygnały z turbin parowych są analizowane i wykrywane są nietypowe sygnały.

Dane można wyróżnić losowo z częstotliwości. W takich sytuacjach wartości RPM zostaną wystawione i w przypadku ochrony obwodu zostanie wystawiony wynik MW. Pomysłem jest wyświetlenie danych w tym samym czasie, ale z różnymi sygnałami.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* [Obszar roboczy usługi Azure Synapse](../../../synapse-analytics/quickstart-create-workspace.md) skonfigurowany przy użyciu [puli Apache Spark bezserwerowej](../../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

## <a name="setup"></a>Konfigurowanie

### <a name="create-an-anomaly-detector-resource"></a>Tworzenie zasobu wykrywania anomalii

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla translatora przy użyciu [Azure Portal](../../cognitive-services-apis-create-account.md) lub [interfejsu wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md). Możesz również wykonać następujące czynności:

- Wyświetl istniejący zasób w  [Azure Portal](https://portal.azure.com/).

Zanotuj punkt końcowy i klucz dla tego zasobu, który będzie potrzebny w tym przewodniku.

## <a name="enter-your-service-keys"></a>Wprowadź klucze usługi

Zacznijmy od dodania klucza i lokalizacji.

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>Odczytywanie danych do ramki Dataframe

Następnie przeczytaj plik IoTSignals w ramce Dataframe. Otwórz nowy Notes w obszarze roboczym Synapse i Utwórz ramkę danych z pliku.

```python
df_device_info = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>Uruchamianie wykrywania anomalii przy użyciu Cognitive Services na platformie Spark

Celem jest znalezienie wystąpień, w których sygnały z urządzeń IoT były używane do wyprowadzania nietypowych wartości, dzięki czemu możemy zobaczyć, kiedy coś się nie dzieje, i przeprowadzić konserwację predykcyjną. Aby to zrobić, użyjmy wykrywania anomalii na platformie Spark:

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

Spójrzmy na dane:

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

Ta komórka powinna dawać wynik, który wygląda następująco:

| sygnatura czasowa           |   value | deviceId   | isanomalia   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | dev-7      | Fałsz       |
| 2020-05-01 18:33:52 |    2976 | dev-7      | Fałsz       |
| 2020-05-01 18:33:53 |    2714 | dev-7      | Fałsz       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>Wizualizuj anomalie dla jednego z urządzeń

IoTSignals.csv ma sygnały z wielu urządzeń IoT. Będziemy skupić się na konkretnym urządzeniu i wizualizować anomalie wyjściowe z urządzenia.

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

Po utworzeniu ramki danych, która reprezentuje anomalie dla konkretnego urządzenia, możemy wizualizować te anomalie:

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

Jeśli to się powiedzie, dane wyjściowe będą wyglądać następująco:

![Wykres krycia anomalii](../media/anomaly-output.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak przeprowadzić konserwację predykcyjną na dużą skalę za pomocą usługi Azure Cognitive Services, usługi Azure Synapse Analytics i usługi Azure CosmosDB. Aby uzyskać więcej informacji, zobacz pełny przykład w witrynie [GitHub](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples).