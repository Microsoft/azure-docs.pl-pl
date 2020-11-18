---
title: Przykładowe notesy Jupyter korzystające z danych NOAA
titleSuffix: Azure Open Datasets
description: Użyj przykładowych notesów Jupyter dla platformy Azure Otwórz zestawy danych, aby dowiedzieć się, jak ładować otwarte zestawy danych i używać ich do wzbogacania dane demonstracyjne. Techniki obejmują użycie platformy Spark i Pandas do przetwarzania danych.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: 470ed0ea4b129c12041007487b61929843b1de34
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654800"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Przykładowe notesy Jupyter pokazują, jak wzbogacać dane za pomocą otwartych zestawów danych 
Przykład Jupyter notesów dla usługi Azure Open DataSets pokazują, jak ładować otwarte zestawy danych i używać ich do wzbogacania dane demonstracyjne. Techniki obejmują używanie Apache Spark i Pandas do przetwarzania danych.

>[!IMPORTANT]
>W przypadku pracy w środowisku innym niż platforma Spark otwarte zestawy danych umożliwiają pobieranie tylko jednego miesiąca z danymi jednocześnie z określonymi klasami w celu uniknięcia MemoryError z dużymi zestawami.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>NOAA obciążenia — dane zintegrowanej bazy danych (ISD) 
|Notes        | Opis                                    |
|----------------|------------------------------------------------|
|[Załaduj jeden ostatni miesiąc danych pogody do Pandas Dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Dowiedz się, jak ładować historyczne dane pogodowe do ulubionej ramki dataPandas. |
|[Załaduj jeden ostatni miesiąc danych pogody do ramki Dataframe platformy Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Dowiedz się, jak ładować historyczne dane pogodowe do ulubionej ramki Dataframe.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Dołącz dane demonstracyjne za pomocą danych NOAA ISD 
|Notes        | Opis                                    |
|----------------|------------------------------------------------|
|[Dołączanie danych demonstracyjnych za pomocą danych pogody — Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Dołącz do 1-miesięcznego demonstracyjnego zestawu danych lokalizacji czujników z odczytami pogody w Pandas Dataframe.  |
|[Dołączanie danych demonstracyjnych za pomocą danych pogody — Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Dołącz do demonstracyjnego zestawu danych lokalizacji czujników z odczytami pogody w ramce Dataframe. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Dołącz dane z NYC taksówki przy użyciu danych NOAA ISD 
|Notes        | Opis                                    |
|----------------|------------------------------------------------|
|[Dane o podróży z taksówkami wzbogacone o dane pogodowe — Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Ładuj NYCe dane dotyczące oddziałów zielonych (ponad 1 miesiąc) i Wzbogacaj je o dane pogodowe w ramce dataPandas. Ten przykład zastępuje metodę `get_pandas_limit` i równoważy wydajność ładowania danych z ilością danych.|
|[Dane o podróży z taksówkami wzbogacone o dane pogodowe — Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Ładuj NYCe dane dotyczące oddziałów zielonych i Wzbogacaj je o dane pogodowe w usłudze Spark Dataframe.  |

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Modelowanie regresji z automatycznym uczeniem maszynowym i otwartym zestawem danych](../machine-learning/tutorial-auto-train-models.md?context=azure%252fopen-datasets%252fcontext%252fopen-datasets-context)
* [Zestaw SDK języka Python dla otwartych zestawów danych](/python/api/azureml-opendatasets/azureml.opendatasets)
* [Katalog usługi Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/)
* [Utwórz zestaw danych Azure Machine Learning z otwartego zestawu danych](how-to-create-azure-machine-learning-dataset-from-open-dataset.md)
