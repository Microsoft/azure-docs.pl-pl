---
title: Tworzenie predykcyjnych potoków danych
description: Dowiedz się, jak utworzyć potok predykcyjny przy użyciu Azure Machine Learning Studio (klasyczny) — działania wykonywania wsadowego w programie Azure Data Factory.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/16/2020
ms.openlocfilehash: 37a31891c3c1d812b396548036c4b59cc6523c2d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375667"
---
# <a name="create-a-predictive-pipeline-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Utwórz potok predykcyjny przy użyciu Azure Machine Learning Studio (klasyczny) i Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Bieżąca wersja](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Machine Learning Studio (klasyczny)](https://azure.microsoft.com/documentation/services/machine-learning/) umożliwia tworzenie, testowanie i wdrażanie rozwiązań do analizy predykcyjnej. Z punktu widzenia wysokiego poziomu można wykonać trzy kroki:

1. **Utwórz eksperyment szkoleniowy**. Ten krok można wykonać przy użyciu Azure Machine Learning Studio (klasyczne). Azure Machine Learning Studio (klasyczny) to wspólne środowisko programistyczne wizualne, które służy do uczenia i testowania modelu analizy predykcyjnej przy użyciu danych szkoleniowych.
2. **Przekonwertuj go na eksperyment predykcyjny**. Po przeszkoleniu modelu z istniejących danych i przygotowaniu go do oceny nowych danych możesz przygotować i usprawnić eksperyment do oceniania.
3. **Wdróż go jako usługę sieci Web**. Eksperyment oceniania można opublikować jako usługę sieci Web platformy Azure. Dane można wysyłać do modelu za pośrednictwem tego punktu końcowego usługi sieci Web i odbierać przewidywania wyników z modelu.

### <a name="data-factory-and-azure-machine-learning-studio-classic-together"></a>Data Factory i Azure Machine Learning Studio (klasyczne)
Azure Data Factory umożliwia łatwe tworzenie potoków korzystających z opublikowanej usługi sieci Web [Azure Machine Learning Studio (klasycznej)](https://azure.microsoft.com/documentation/services/machine-learning) na potrzeby analizy predykcyjnej. Korzystając z **działania wykonywania wsadowego** w potoku Azure Data Factory, można wywołać usługę sieci Web Azure Machine Learning Studio (klasyczną), aby wykonywać przewidywania dotyczące danych w usłudze Batch.

W miarę upływu czasu modele predykcyjne w Azure Machine Learning Studio (klasyczne) eksperymenty oceniania muszą być ponownie przenoszone przy użyciu nowych wejściowych zestawów danych. Możesz ponownie przeprowadzić uczenie modelu z potoku Data Factory, wykonując następujące czynności:

1. Opublikuj eksperyment szkoleniowy (nie eksperyment predykcyjny) jako usługę sieci Web. Ten krok należy wykonać w Azure Machine Learning Studio (klasyczny), ponieważ udało Ci się uwidocznić eksperyment predykcyjny jako usługę sieci Web w poprzednim scenariuszu.
2. Użyj działania wykonywania wsadowego Azure Machine Learning Studio (klasycznego), aby wywołać usługę sieci Web dla eksperymentu szkoleniowego. W zasadzie można użyć działania wykonywania wsadowego Azure Machine Learning Studio (klasycznego) do wywołania zarówno szkoleń usługi sieci Web, jak i usługi sieci Web oceniania.

Po wykonaniu ponownych szkoleń zaktualizuj usługę sieci Web oceniania (eksperyment predykcyjny uwidoczniony jako usługa sieci Web) z nowym przeszkolonym modelem przy użyciu **działania aktualizacji zasobów Azure Machine Learning Studio (klasyczny)**. Aby uzyskać szczegółowe informacje, zobacz [aktualizowanie modeli za pomocą artykułu aktualizowanie zasobów](update-machine-learning-models.md) .

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Połączona usługa Azure Machine Learning Studio (klasyczna)

Utworzysz połączoną usługę **Azure Machine Learning Studio (klasyczną)** , aby połączyć usługę sieci Web Azure Machine Learning Studio (klasyczną) z fabryką danych Azure. Połączona usługa jest używana przez działanie wykonywania wsadowego programu Azure Machine Learning Studio (klasycznej) i [aktualizuje aktywność zasobów](update-machine-learning-models.md).

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Informacje o właściwościach w definicji JSON można znaleźć w artykule dotyczącym [połączonych usług obliczeniowych](compute-linked-services.md) .

Azure Machine Learning Studio (klasyczny) obsługuje zarówno klasyczne usługi sieci Web, jak i nowe usługi sieci Web na potrzeby eksperymentu predykcyjnego. Możesz wybrać jeden z nich, który ma być używany przez Data Factory. Aby uzyskać informacje wymagane do utworzenia połączonej usługi Azure Machine Learning Studio (klasycznej), przejdź do https://services.azureml.net lokalizacji, w której znajdują się wszystkie (nowe) usługi sieci Web i klasyczne usługi sieci Web. Kliknij usługę sieci Web, do której chcesz uzyskać dostęp, a następnie **kliknij pozycję Użyj** strony. Skopiuj **klucz podstawowy** dla właściwości **ApiKey** oraz **żądania wsadowe** dla właściwości **mlEndpoint** .

![Azure Machine Learning Studio (klasyczne) usługi sieci Web](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Działanie wykonywania wsadowego w Azure Machine Learning Studio (klasycznej)

Poniższy fragment kodu JSON definiuje działanie wykonywania wsadowego Azure Machine Learning Studio (klasycznego). Definicja działania ma odwołanie do utworzonej wcześniej połączonej usługi Azure Machine Learning Studio (klasycznej).

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Właściwość          | Opis                              | Wymagane |
| :---------------- | :--------------------------------------- | :------- |
| name              | Nazwa działania w potoku     | Tak      |
| description (opis)       | Tekst opisujący działanie działania.  | Nie       |
| typ              | W przypadku Data Lake Analytics działania U-SQL typ działania to **AzureMLBatchExecution**. | Tak      |
| linkedServiceName | Połączone usługi z połączoną usługą Azure Machine Learning Studio (klasyczną). Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz artykuł dotyczący [połączonych usług obliczeniowych](compute-linked-services.md) . | Tak      |
| webServiceInputs  | Klucza, par wartości, Mapowanie nazw danych wejściowych usługi sieci Web Azure Machine Learning Studio (klasycznych). Klucz musi być zgodny z parametrami wejściowymi zdefiniowanymi w opublikowanej usłudze sieci Web Azure Machine Learning Studio (klasycznej). Wartość to para właściwości usług połączonych i FilePath usługi Azure Storage określających wejściowe lokalizacje obiektów BLOB. | Nie       |
| webServiceOutputs | Klucza, par wartości, Mapowanie nazw danych wyjściowych usługi sieci Web Azure Machine Learning Studio (klasycznych). Klucz musi być zgodny z parametrami wyjściowymi zdefiniowanymi w opublikowanej usłudze sieci Web Azure Machine Learning Studio (klasycznej). Wartość to para właściwości usług połączonych i FilePath usługi Azure Storage określających wyjściowe lokalizacje obiektów BLOB. | Nie       |
| globalParameters  | Klucz i pary wartości, które mają zostać przesłane do punktu końcowego usługi wykonywania wsadowego w Azure Machine Learning Studio (klasycznym). Klucze muszą być zgodne z nazwami parametrów usługi sieci Web zdefiniowanych w opublikowanej usłudze sieci Web Azure Machine Learning Studio (klasycznej). Wartości są przesyłane we Azure Machine Learning Studio właściwości GlobalParameters (klasyczne) żądania wykonania partii | Nie       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenariusz 1: eksperymenty wykorzystujące dane wejściowe/wyjściowe usługi sieci Web, które odwołują się do danych w usłudze Azure Blob Storage

W tym scenariuszu usługa sieci Web Azure Machine Learning Studio (klasyczna) dokonuje prognozowania przy użyciu danych z pliku w magazynie obiektów blob platformy Azure i zapisuje wyniki prognozowania w magazynie obiektów BLOB. Poniższy kod JSON definiuje potok Data Factory za pomocą działania AzureMLBatchExecution. Dane wejściowe i wyjściowe w magazynie blogów platformy Azure są przywoływane przy użyciu połączonej paryname i FilePath. W przykładowej połączonej usłudze danych wejściowych i wyjściowych są różne, można użyć różnych połączonych usług dla każdego z danych wejściowych/wyjściowych dla Data Factory, aby można było wybrać odpowiednie pliki i wysłać je do Azure Machine Learning Studio (klasycznego) usługi sieci Web.

> [!IMPORTANT]
> W ramach eksperymentu Azure Machine Learning Studio (klasycznego), portów danych wejściowych i wyjściowych usługi sieci Web oraz parametrów globalnych mają nazwy domyślne ("INPUT1", "input2"), które można dostosować. Nazwy używane dla ustawień webServiceInputs, webServiceOutputs i globalParameters muszą dokładnie pasować do nazw eksperymentów. Przykładowy ładunek żądania można wyświetlić na stronie pomocy wykonywania wsadowego dla punktu końcowego Azure Machine Learning Studio (klasycznego), aby sprawdzić oczekiwane mapowanie.


```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenariusz 2: eksperymenty korzystające z modułów czytnika/składnika zapisywania do odwoływania się do danych w różnych magazynach
Innym typowym scenariuszem podczas tworzenia Azure Machine Learning Studio (klasycznego) eksperymentów jest użycie danych import i danych wyjściowych. Moduł Importuj dane jest używany do ładowania danych do eksperymentu, a moduł danych wyjściowych polega na zapisywaniu danych z eksperymentów. Aby uzyskać szczegółowe informacje na temat importowania danych i modułów danych wyjściowych, zobacz temat [Importowanie danych](/azure/machine-learning/studio-module-reference/import-data) i [danych wyjściowych](/azure/machine-learning/studio-module-reference/export-data) w bibliotece MSDN.

W przypadku korzystania z modułów import danych i danych wyjściowych dobrym sposobem jest użycie parametru usługi sieci Web dla każdej właściwości tych modułów. Te parametry sieci Web umożliwiają skonfigurowanie wartości w czasie wykonywania. Można na przykład utworzyć eksperyment z modułem import danych, który używa Azure SQL Database: XXX.database.windows.net. Po wdrożeniu usługi sieci Web należy umożliwić klientom usługi sieci Web określenie innego serwera logicznego programu SQL Server o nazwie `YYY.database.windows.net` . Możesz użyć parametru usługi sieci Web, aby umożliwić skonfigurowanie tej wartości.

> [!NOTE]
> Dane wejściowe i wyjściowe usługi sieci Web różnią się od parametrów usługi sieci Web. W pierwszym scenariuszu pokazano, jak można określić dane wejściowe i wyjściowe dla usługi sieci Web Azure Machine Learning Studio (klasyczna). W tym scenariuszu przekazywane są parametry usługi sieci Web odpowiadające właściwościom modułów danych import/dane wyjściowe.

Przyjrzyjmy się scenariuszowi używania parametrów usługi sieci Web. Masz wdrożoną Azure Machine Learning Studio (klasyczną) usługę sieci Web, która używa modułu czytnika do odczytywania danych z jednego ze źródeł danych obsługiwanych przez Azure Machine Learning Studio (klasyczny) (na przykład: Azure SQL Database). Po wykonaniu zadania wsadowego wyniki są zapisywane przy użyciu modułu składnika zapisywania (Azure SQL Database).  W eksperymentach nie są zdefiniowane żadne dane wejściowe i wyjściowe usługi sieci Web. W tym przypadku zalecamy skonfigurowanie odpowiednich parametrów usługi sieci Web dla modułów czytnika i składnika zapisywania. Ta konfiguracja umożliwia skonfigurowanie modułów czytnika/składnika zapisywania przy użyciu działania AzureMLBatchExecution. Parametry usługi sieci Web należy określić w sekcji **globalParameters** w kodzie JSON działania w następujący sposób.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> W parametrach usługi sieci Web rozróżniana jest wielkość liter, dlatego należy upewnić się, że nazwy określone w formacie JSON działania są zgodne z tymi, które są udostępniane przez usługę sieci Web.

Po wykonaniu ponownych szkoleń zaktualizuj usługę sieci Web oceniania (eksperyment predykcyjny uwidoczniony jako usługa sieci Web) z nowym przeszkolonym modelem przy użyciu **działania aktualizacji zasobów Azure Machine Learning Studio (klasyczny)**. Aby uzyskać szczegółowe informacje, zobacz [aktualizowanie modeli za pomocą artykułu aktualizowanie zasobów](update-machine-learning-models.md) .

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami, które wyjaśniają sposób przekształcania danych w inny sposób:

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie Hive](transform-data-using-hadoop-hive.md)
* [Aktywność trzody chlewnej](transform-data-using-hadoop-pig.md)
* [Działanie MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działanie przesyłania strumieniowego Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)