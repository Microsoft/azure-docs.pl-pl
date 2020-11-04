---
title: Pozyskiwanie danych przy użyciu usługi Azure Data Factory
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć potok pozyskiwania danych za pomocą Azure Data Factory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 8cf0abdeaf3a7fe71213b6fa4f78f057bf2f92eb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307358"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Pozyskiwanie danych przy użyciu usługi Azure Data Factory

W tym artykule dowiesz się, jak utworzyć potok pozyskiwania danych z użyciem Azure Data Factory (ADF). Ten potok służy do pozyskiwania danych do użycia z Azure Machine Learning. Azure Data Factory umożliwia łatwe wyodrębnianie, przekształcanie i ładowanie danych (ETL). Gdy dane zostaną przekształcone i załadowane do magazynu, mogą służyć do uczenia modeli uczenia maszynowego.

Prostą transformację danych można obsługiwać przy użyciu natywnych działań i instrumentów ADF, takich jak [przepływ danych](../data-factory/control-flow-execute-data-flow-activity.md). Gdy jest to bardziej skomplikowane scenariusze, dane mogą być przetwarzane z niestandardowym kodem. Na przykład kod Python lub R.

Istnieje kilka typowych technik używania Azure Data Factory do przekształcania danych podczas pozyskiwania. Każda Technika ma wady i wady, które określają, czy jest to dobre dopasowanie do określonego przypadku użycia:

| Technika | Zalety | Wady |
| ----- | ----- | ----- |
| ADF i Azure Functions | Małe opóźnienia, obliczanie bezserwerowe</br>Funkcje stanowe</br>Funkcje wielokrotnego użytku | Dobre tylko dla krótkich przebiegów przetwarzania |
| ADF i składnik niestandardowy | Obliczenia równoległe na dużą skalę</br>Odpowiednie dla ciężkich algorytmów | Zawijanie kodu do pliku wykonywalnego</br>Złożoność obsługi zależności i operacji we/wy |
| Podajnik APD i Azure Databricks Notes | Apache Spark</br>Natywne środowisko języka Python | Może być kosztowne</br>Trwa wstępne tworzenie klastrów i Dodawanie opóźnień

## <a name="adf-with-azure-functions"></a>ADF za pomocą usługi Azure Functions

![Diagram przedstawia potok Azure Data Factory przy użyciu funkcji platformy Azure i uruchamiania potoku M L oraz potoku Azure Machine Learning, z modelem uczenia oraz z możliwością współdziałania z danymi pierwotnymi i przygotowanymi danymi.](media/how-to-data-ingest-adf/adf-function.png)

Azure Functions umożliwia uruchamianie małych fragmentów kodu (funkcji) bez obaw o infrastrukturę aplikacji. W tej opcji dane są przetwarzane z niestandardowym kodem w języku Python opakowanym w funkcję platformy Azure. 

Funkcja jest wywoływana za pomocą [działania funkcji Azure ADF](../data-factory/control-flow-azure-function-activity.md). To podejście jest dobrym rozwiązaniem w przypadku uproszczonych transformacji danych. 

* Formaty
    * Dane są przetwarzane w przypadku obliczeń bezserwerowych z stosunkowo małym opóźnieniem
    * Potok ADF może wywołać [trwałą funkcję platformy Azure](../azure-functions/durable/durable-functions-overview.md) , która może implementować zaawansowany przepływ transformacji danych 
    * Szczegóły transformacji danych są wyodrębniane z funkcji platformy Azure, która może być ponownie używana i wywoływana z innych miejsc
* Wada
    * Azure Functions należy utworzyć przed użyciem z funkcją ADF
    * Azure Functions jest dobre tylko w przypadku krótko działającego przetwarzania danych

## <a name="adf-with-custom-component-activity"></a>ADF z niestandardowym działaniem składników

![Diagram przedstawia potok Azure Data Factory, ze składnikiem niestandardowym i uruchomienie potoku M L oraz potok Azure Machine Learning, z modelem uczenia i sposób, w jaki współpracują z danymi pierwotnymi i przygotowane dane.](media/how-to-data-ingest-adf/adf-customcomponent.png)

W tej opcji dane są przetwarzane z niestandardowym kodem w języku Python opakowanym w plik wykonywalny. Jest wywoływana z [niestandardowym działaniem składnika ADF](../data-factory/transform-data-using-dotnet-custom-activity.md). To podejście jest lepszym rozwiązaniem w przypadku dużych ilości danych niż w przypadku poprzedniej techniki.

* Formaty
    * Dane są przetwarzane w puli [Azure Batch](../batch/batch-technical-overview.md) , która zapewnia dużą skalę obliczenia równoległe i o wysokiej wydajności
    * Może służyć do uruchamiania ciężkich algorytmów i przetwarzania znaczących ilości danych
* Wada
    * Należy utworzyć pulę Azure Batch przed użyciem z funkcją ADF
    * Za pośrednictwem inżynierii związanej z pakowaniem kodu w języku Python do pliku wykonywalnego. Złożoność obsługi zależności i parametrów wejściowych/wyjściowych

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF z notesem Azure Databricks Python

![Diagram przedstawia potok Azure Data Factory przy użyciu Azure Databricks języka Python i przebiegu M L oraz potok Azure Machine Learning z modelem uczenia i sposób, w jaki współpracują z danymi pierwotnymi i przygotowane dane.](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) to oparta na Apache Spark platforma analityczna w chmurze firmy Microsoft.

W tej metodzie transformacja danych jest wykonywana przez Notes języka [Python](../data-factory/transform-data-using-databricks-notebook.md), uruchomiony w klastrze Azure Databricks. Jest to prawdopodobnie najczęstsze podejście, które wykorzystuje pełną moc usługi Azure Databricks. Jest ona przeznaczona do rozproszonego przetwarzania danych na dużą skalę.

* Formaty
    * Dane są przekształcane w najbardziej zaawansowanej usłudze Azure Data Processing, która jest tworzona w oparciu o środowisko Apache Spark
    * Natywna obsługa języka Python wraz z strukturami i bibliotekami analizy danych, w tym TensorFlow, PyTorch i scikit — uczenie się
    * Nie ma potrzeby zawijania kodu w języku Python do funkcji lub modułów wykonywalnych. Kod działa zgodnie z oczekiwaniami.
* Wada
    * Należy utworzyć infrastrukturę Azure Databricks przed użyciem z funkcją ADF
    * Może być kosztowne w zależności od konfiguracji Azure Databricks
    * Odłączenie klastrów obliczeniowych od trybu "zimnego" zajmuje trochę czasu, co zapewnia duże opóźnienie dla rozwiązania 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Używanie danych w potokach Azure Machine Learning

![Diagram przedstawia potok Azure Data Factory i potok Azure Machine Learning i sposób współdziałania z danymi pierwotnymi i przygotowane dane. Potok Data Factory zbiera dane do przygotowanej bazy danych danych, która składa się z magazynu danych, w którym są dane, które są źródłami zbiorów w obszarze roboczym Machine Learning.](media/how-to-data-ingest-adf/aml-dataset.png)

Przekształcone dane z potoku ADF są zapisywane w magazynie danych (na przykład w przypadku obiektów blob platformy Azure). Azure Machine Learning mogą uzyskiwać dostęp do tych danych przy użyciu [magazynów](./how-to-access-data.md#create-and-register-datastores) i [zestawów danych](./how-to-create-register-datasets.md).

Za każdym razem, gdy zostanie uruchomiony potok ADF, dane są zapisywane w innej lokalizacji w magazynie. Aby przekazać lokalizację do Azure Machine Learning, potok ADF wywołuje potok Azure Machine Learning. Podczas wywoływania potoku ML Lokalizacja danych i identyfikator uruchomienia są wysyłane jako parametry. Potok ML może następnie utworzyć magazyn danych/zestaw danych przy użyciu tej lokalizacji. 

> [!TIP]
> Zestawy danych [obsługują przechowywanie wersji](./how-to-version-track-datasets.md), więc potok ml może zarejestrować nową wersję zestawu danych, która wskazuje najnowsze dane z potoku ADF.

Gdy dane są dostępne za pomocą magazynu danych lub zestawu danych, można użyć go do uczenia modelu ML. Proces uczenia może być częścią tego samego potoku ML, który jest wywoływany z ADF. Lub może to być oddzielny proces, taki jak eksperymentowanie w notesie Jupyter.

Ponieważ zestawy danych obsługują przechowywanie wersji, a każdy przebieg z potoku tworzy nową wersję, można łatwo zrozumieć, która wersja dane została użyta do uczenia modelu.

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie notesu datakostks w Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Dostęp do danych w usługach Azure Storage](./how-to-access-data.md#create-and-register-datastores)
* [Uczenie modeli z zestawami danych w Azure Machine Learning](./how-to-train-with-datasets.md)
* [Metodyka DevOps dla potoku pozyskiwania danych](./how-to-cicd-data-ingestion.md)