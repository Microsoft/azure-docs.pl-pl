---
title: Pozyskiwanie danych przy użyciu usługi Azure Data Factory
titleSuffix: Azure Machine Learning
description: Poznaj dostępne opcje tworzenia potoku pozyskiwania danych z Azure Data Factory i korzyściami z każdego z nich.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b842934ea6bb458a59a53ea7068aa9ece98aacf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98796157"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Pozyskiwanie danych przy użyciu usługi Azure Data Factory

Ten artykuł zawiera informacje o dostępnych opcjach tworzenia potoku pozyskiwania danych przy użyciu [Azure Data Factory](../data-factory/introduction.md). Ten potok Azure Data Factory służy do pozyskiwania danych do użycia z [Azure Machine Learning](overview-what-is-azure-ml.md). Data Factory umożliwia łatwe wyodrębnianie, przekształcanie i ładowanie danych (ETL). Gdy dane zostaną przekształcone i załadowane do magazynu, mogą służyć do uczenia modeli uczenia maszynowego w Azure Machine Learning.

Prostą transformację danych można obsługiwać przy użyciu natywnych działań Data Factory i instrumentów, takich jak [przepływ danych](../data-factory/control-flow-execute-data-flow-activity.md). Gdy jest to bardziej skomplikowane scenariusze, dane mogą być przetwarzane z niestandardowym kodem. Na przykład kod Python lub R.

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>Porównanie potoków pozyskiwania danych Azure Data Factory 
Istnieje kilka typowych technik używania Data Factory do przekształcania danych podczas pozyskiwania. Każda Technika ma zalety i wady, które ułatwiają ustalenie, czy jest to dobre dopasowanie do określonego przypadku użycia:

| Technika | Zalety | Wady |
| ----- | ----- | ----- |
| Data Factory + Azure Functions | <li> Małe opóźnienia, obliczanie bezserwerowe<li>Funkcje stanowe<li>Funkcje wielokrotnego użytku | Dobre tylko dla krótkich przebiegów przetwarzania |
| Data Factory i składnik niestandardowy | <li>Obliczenia równoległe na dużą skalę<li>Odpowiednie dla ciężkich algorytmów | <li>Wymaga zawijania kodu do pliku wykonywalnego<li>Złożoność obsługi zależności i operacji we/wy |
| Notes Data Factory + Azure Databricks |<li> Apache Spark<li>Natywne środowisko języka Python |<li>Może być kosztowne<li>Trwa wstępne tworzenie klastrów i Dodawanie opóźnień

## <a name="azure-data-factory-with-azure-functions"></a>Azure Data Factory z usługą Azure Functions

Azure Functions umożliwia uruchamianie małych fragmentów kodu (funkcji) bez obaw o infrastrukturę aplikacji. W tej opcji dane są przetwarzane z niestandardowym kodem w języku Python opakowanym w funkcję platformy Azure. 

Funkcja jest wywoływana z [Azure Data Factory działania funkcji platformy Azure](../data-factory/control-flow-azure-function-activity.md). To podejście jest dobrym rozwiązaniem w przypadku uproszczonych transformacji danych. 

![Diagram przedstawia potok Azure Data Factory przy użyciu funkcji platformy Azure i uruchomienia potoku, a potoku Azure Machine Learning, z modelem uczenia i sposobu, w jaki współpracują z danymi pierwotnymi i przygotowane dane.](media/how-to-data-ingest-adf/adf-function.png)



* Zalety:
    * Dane są przetwarzane w przypadku obliczeń bezserwerowych z stosunkowo małym opóźnieniem
    * Potok Data Factory może wywołać [trwałą funkcję platformy Azure](../azure-functions/durable/durable-functions-overview.md) , która może implementować zaawansowany przepływ transformacji danych 
    * Szczegóły transformacji danych są wyodrębniane z funkcji platformy Azure, która może być ponownie używana i wywoływana z innych miejsc
* Wada
    * Azure Functions należy utworzyć przed użyciem z funkcją ADF
    * Azure Functions jest dobre tylko w przypadku krótko działającego przetwarzania danych

## <a name="azure-data-factory-with-custom-component-activity"></a>Azure Data Factory z niestandardowym działaniem składnika

W tej opcji dane są przetwarzane z niestandardowym kodem w języku Python opakowanym w plik wykonywalny. Jest on wywoływany z [ niestandardowym działaniem składnika Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md). To podejście jest lepszym rozwiązaniem w przypadku dużych ilości danych niż w przypadku poprzedniej techniki.

![Diagram przedstawia potok Azure Data Factory, ze składnikiem niestandardowym i uruchomienie potoku M L oraz potok Azure Machine Learning, z modelem uczenia i sposób, w jaki współpracują z danymi pierwotnymi i przygotowane dane.](media/how-to-data-ingest-adf/adf-customcomponent.png)

* Zalety:
    * Dane są przetwarzane w puli [Azure Batch](../batch/batch-technical-overview.md) , która zapewnia dużą skalę obliczenia równoległe i o wysokiej wydajności
    * Może służyć do uruchamiania ciężkich algorytmów i przetwarzania znaczących ilości danych
* Wady:
    * Przed użyciem z Data Factory należy utworzyć pulę Azure Batch
    * Za pośrednictwem inżynierii związanej z pakowaniem kodu w języku Python do pliku wykonywalnego. Złożoność obsługi zależności i parametrów wejściowych/wyjściowych

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>Azure Data Factory z Azure Databricks notesem Python

[Azure Databricks](https://azure.microsoft.com/services/databricks/) to oparta na Apache Spark platforma analityczna w chmurze firmy Microsoft.

W tej metodzie transformacja danych jest wykonywana przez Notes języka [Python](../data-factory/transform-data-using-databricks-notebook.md), uruchomiony w klastrze Azure Databricks. Jest to prawdopodobnie najczęstsze podejście, które wykorzystuje pełną moc usługi Azure Databricks. Jest ona przeznaczona do rozproszonego przetwarzania danych na dużą skalę.

![Diagram przedstawia potok Azure Data Factory przy użyciu Azure Databricks języka Python i przebiegu M L oraz potok Azure Machine Learning z modelem uczenia i sposób, w jaki współpracują z danymi pierwotnymi i przygotowane dane.](media/how-to-data-ingest-adf/adf-databricks.png)

* Zalety:
    * Dane są przekształcane w najbardziej zaawansowanej usłudze Azure Data Processing, która jest tworzona w oparciu o środowisko Apache Spark
    * Natywna obsługa języka Python wraz z strukturami i bibliotekami analizy danych, w tym TensorFlow, PyTorch i scikit — uczenie się
    * Nie ma potrzeby zawijania kodu w języku Python do funkcji lub modułów wykonywalnych. Kod działa zgodnie z oczekiwaniami.
* Wady:
    * Infrastrukturę Azure Databricks należy utworzyć przed użyciem z Data Factory
    * Może być kosztowne w zależności od konfiguracji Azure Databricks
    * Odłączenie klastrów obliczeniowych od trybu "zimnego" zajmuje trochę czasu, co zapewnia duże opóźnienie dla rozwiązania 
    

## <a name="consume-data-in-azure-machine-learning"></a>Korzystanie z danych w Azure Machine Learning 

Potok Data Factory zapisuje przygotowane dane do magazynu w chmurze (takiego jak Azure Blob lub Azure datalake). <br>
Korzystaj z przygotowanych danych w Azure Machine Learning przez, 

* Wywoływanie potoku Azure Machine Learning z potoku Data Factory.<br>**OR**
* Tworzenie [Azure Machine Learning magazynu](how-to-access-data.md#create-and-register-datastores) [danych i Azure Machine Learning DataSet](how-to-create-register-datasets.md) do użycia w późniejszym czasie.

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>Wywołaj potok Azure Machine Learning z Data Factory

Ta metoda jest zalecana w przypadku [przepływów pracy Machine Learning operacji (MLOps)](concept-model-management-and-deployment.md#what-is-mlops). Jeśli nie chcesz konfigurować potoku Azure Machine Learning, zobacz [odczytywanie danych bezpośrednio z magazynu](#read-data-directly-from-storage).

Za każdym razem, gdy zostanie uruchomiony potok Data Factory, 

1. Dane są zapisywane w innej lokalizacji w magazynie. 
1. Aby przekazać lokalizację do Azure Machine Learning, potok Data Factory wywołuje [potok Azure Machine Learning](concept-ml-pipelines.md). Podczas wywoływania potoku ML Lokalizacja danych i identyfikator uruchomienia są wysyłane jako parametry. 
1. Potok ML może następnie utworzyć Azure Machine Learning magazyn danych i zestaw danych z lokalizacją dane. Więcej informacji znajduje się w temacie [wykonywanie potoków Azure Machine Learning w Data Factory](../data-factory/transform-data-machine-learning-service.md).

![Diagram przedstawia potok Azure Data Factory i potok Azure Machine Learning i sposób współdziałania z danymi pierwotnymi i przygotowane dane. Potok Data Factory zbiera dane do przygotowanej bazy danych danych, która składa się z magazynu danych, w którym są dane, które są źródłami zbiorów w obszarze roboczym Machine Learning.](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> Zestawy danych [obsługują przechowywanie wersji](./how-to-version-track-datasets.md), więc potok ml może zarejestrować nową wersję zestawu danych, która wskazuje najnowsze dane z potoku ADF.

Gdy dane są dostępne za pomocą magazynu danych lub zestawu danych, można użyć go do uczenia modelu ML. Proces uczenia może być częścią tego samego potoku ML, który jest wywoływany z ADF. Lub może to być oddzielny proces, taki jak eksperymentowanie w notesie Jupyter.

Ponieważ zestawy danych obsługują przechowywanie wersji, a każdy przebieg z potoku tworzy nową wersję, można łatwo zrozumieć, która wersja dane została użyta do uczenia modelu.

### <a name="read-data-directly-from-storage"></a>Odczytaj dane bezpośrednio z magazynu

Jeśli nie chcesz tworzyć potoku z tablicą ML, możesz uzyskać dostęp do danych bezpośrednio z konta magazynu, w którym są zapisywane przygotowane dane, przy użyciu magazynu danych i zestawu danych Azure Machine Learning. 

Poniższy kod w języku Python pokazuje, jak utworzyć magazyn danych, który nawiązuje połączenie z magazynem usługi Azure datalake Generation 2. [Dowiedz się więcej o magazynach danych i miejscach, w których można znaleźć uprawnienia jednostki usługi](how-to-access-data.md#create-and-register-datastores).

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

Następnie Utwórz zestaw danych, aby odwoływać się do plików, których chcesz użyć w zadaniu uczenia maszynowego. 

Poniższy kod tworzy TabularDataset z pliku CSV `prepared-data.csv` . Dowiedz się więcej o [typach zestawów danych i akceptowanych formatach plików](how-to-create-register-datasets.md#dataset-types). 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

W tym miejscu Użyj, `prepared_dataset` Aby odwoływać się do przygotowanych danych, takich jak skrypty szkoleniowe. Dowiedz się, jak [szkolić modele z zestawami danych w Azure Machine Learning](./how-to-train-with-datasets.md).

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie notesu datakostks w Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Dostęp do danych w usługach Azure Storage](./how-to-access-data.md#create-and-register-datastores)
* [Uczenie modeli z zestawami danych w Azure Machine Learning](./how-to-train-with-datasets.md).
* [Metodyka DevOps dla potoku pozyskiwania danych](./how-to-cicd-data-ingestion.md)