---
title: Tworzenie potoków danych predykcyjnych
description: Dowiedz się, jak utworzyć potok predykcyjny przy użyciu usługi Azure Machine Learning — działanie wykonywania wsadowego w fabryce danych platformy Azure.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/20/2019
ms.openlocfilehash: 3587ee711864eb33fea9bc4e61fe226562e8f612
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418868"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Tworzenie potoków predykcyjnych przy użyciu usług Azure Machine Learning i Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Bieżąca wersja](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Usługa Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) umożliwia tworzenie, testowanie i wdrażanie rozwiązań analizy predykcyjnej. Z wysokiego poziomu jest to wykonywane w trzech krokach:

1. **Utwórz eksperyment szkoleniowy**. Ten krok należy wykonać przy użyciu usługi Azure Machine Learning Studio (klasyczny). Usługa Azure Machine Learning Studio (klasyczna) to wspólne środowisko programistyczne, którego używasz do uczenia i testowania modelu analizy predykcyjnej przy użyciu danych szkoleniowych.
2. **Przekonwertuj go na eksperyment predykcyjny**. Gdy model został przeszkolony z istniejącymi danymi i możesz go użyć do uzyskania nowych danych, przygotowujesz i usprawnisz eksperyment do oceniania.
3. **Wdrażanie go jako usługi sieci web**. Eksperyment oceniania można opublikować jako usługę sieci Web platformy Azure. Można wysyłać dane do modelu za pośrednictwem tego punktu końcowego usługi sieci web i odbierać prognozy wyników z modelu.

### <a name="data-factory-and-machine-learning-together"></a>Fabryka danych i uczenie maszynowe razem
Usługa Azure Data Factory umożliwia łatwe tworzenie potoków korzystających z opublikowanej usługi sieci Web [usługi Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning) do analizy predykcyjnej. Za pomocą **działania wykonywania wsadowego** w potoku usługi Azure Data Factory, można wywołać usługi azure machine learning studio (klasyczne) usługi sieci web, aby prognoz na dane w partii.

Z biegiem czasu modele predykcyjne w usłudze Azure Machine Learning Studio (klasyczne) oceniania eksperymentów muszą zostać przeszkolone przy użyciu nowych wejściowych zestawów danych. Można ponownie trenować model z potoku fabryki danych, wykonując następujące kroki:

1. Opublikuj eksperyment szkoleniowy (nie eksperyment predykcyjny) jako usługę sieci web. Ten krok należy wykonać w usłudze Azure Machine Learning Studio (klasyczne), tak jak w przypadku udostępnienia eksperymentu predykcyjnego jako usługi sieci web w poprzednim scenariuszu.
2. Użyj usługi Azure Machine Learning Studio (klasyczne) działanie wykonywania wsadowego do wywołania usługi sieci web dla eksperymentu szkoleniowego. Zasadniczo można użyć działania Azure Machine Learning Studio (klasyczne) wykonywanie wsadowe do wywoływania zarówno szkolenia usługi sieci web i oceniania usługi sieci web.

Po zakończeniu przekwalifikowania zaktualizuj usługę sieci web oceniania (eksperyment predykcyjny ujawniony jako usługa sieci web) przy użyciu nowo przeszkolonego modelu przy użyciu **działania zasobów aktualizacji usługi Azure Machine Learning Studio (klasycznej).** Aby uzyskać szczegółowe informacje, zobacz [Aktualizowanie modeli przy użyciu](update-machine-learning-models.md) artykułu Aktualizuj działanie zasobów.

## <a name="azure-machine-learning-linked-service"></a>Usługa połączona usługi usługi Azure Machine Learning

Utwórz usługę połączony **usługi Azure Machine Learning,** aby połączyć usługę azure machine learning web z fabryką danych platformy Azure. Usługa połączona jest używana przez działanie wykonywania wsadowego usługi Azure Machine Learning i [działanie zasobów aktualizacji](update-machine-learning-models.md).

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

Zobacz [Obliczenie połączonych usług](compute-linked-services.md) artykuł opisy dotyczące właściwości w definicji JSON.

Usługa Azure Machine Learning obsługuje zarówno klasyczne usługi sieci Web, jak i nowe usługi sieci Web w eksperymencie predykcyjnym. Możesz wybrać odpowiedni do użycia w fabryce danych. Aby uzyskać informacje wymagane do utworzenia usługi linked https://services.azureml.netservice usługi Azure Machine Learning, przejdź do witryny , gdzie znajdują się wszystkie (nowe) usługi sieci Web i klasyczne usługi sieci Web. Kliknij usługę sieci Web, do której chcesz uzyskać dostęp, a następnie kliknij pozycję **Konsumuj.** Skopiuj **klucz podstawowy** dla **apiKey** właściwości i **żądania partii** dla **mlEndpoint** właściwości.

![Usługi azure machine learning web](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Działanie wsadowe usługi Azure Machine Learning

Poniższy fragment kodu JSON definiuje działanie wsadowe usługi Azure Machine Learning. Definicja działania ma odwołanie do usługi połączonej usługi Azure Machine Learning utworzonej wcześniej.

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

| Właściwość          | Opis                              | Wymagany |
| :---------------- | :--------------------------------------- | :------- |
| name              | Nazwa działania w potoku     | Tak      |
| description       | Tekst opisujący działanie.  | Nie       |
| type              | W przypadku działania U-SQL usługi Data Lake Analytics typ działania to **AzureMLBatchExecution**. | Tak      |
| linkedServiceName | Połączone usługi z usługą linked service usługi Azure Machine Learning. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz Artykuł [dotyczący powiązanych usług obliczeń.](compute-linked-services.md) | Tak      |
| WebServiceInputs (WebServiceInputs)  | Klucz, pary wartości, mapowanie nazw danych wejściowych usługi azure machine learning web. Klucz musi odpowiadać parametrom wejściowym zdefiniowanym w opublikowanej usłudze azure machine learning web. Wartość to parę połączonych usług usługi usługi Azure Storage i właściwości Ścieżki plików określającą wejściowe lokalizacje obiektów blob. | Nie       |
| WebServiceOutputs | Klucz, pary wartości, mapowanie nazw produktów usługi azure machine learning web service. Klucz musi odpowiadać parametrom wyjściowym zdefiniowanym w opublikowanej usłudze azure machine learning web. Wartość to parę połączonych usług usługi usługi Azure Storage i właściwości Ścieżki plików określającą wyjściowe lokalizacje obiektów blob. | Nie       |
| globalParameters  | Klucz, pary wartości mają być przekazywane do punktu końcowego usługi azure machine learning studio (klasyczny) wsadowego usługi. Klucze muszą być zgodne z nazwami parametrów usługi sieci web zdefiniowanych w opublikowanej usłudze azure machine learning studio (klasycznej). Wartości są przekazywane we właściwości GlobalParameters żądania wykonania wsadowego usługi Azure Machine Learning Studio (klasyczne) | Nie       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenariusz 1: Eksperymenty przy użyciu danych wejściowych/wyjściowych usługi sieci Web, które odwołują się do danych w usłudze Azure Blob Storage

W tym scenariuszu usługa azure machine learning sieci Web sprawia, że prognoz przy użyciu danych z pliku w magazynie obiektów blob platformy Azure i przechowuje wyniki prognozowania w magazynie obiektów blob. Następujący JSON definiuje potok fabryki danych z działaniem AzureMLBatchExecution. Dane wejściowe i wyjściowe w usłudze Azure Blog Storage są przywoływale przy użyciu pary LinkedName i FilePath. W przykładowej usługi połączone dane wejściowe i wyjściowe są różne, można użyć różnych usług linked dla każdego wejścia/wyjścia dla usługi Data Factory, aby móc odebrać odpowiednie pliki i wysłać do usługi Azure Machine Learning Studio (klasyczny) usługi sieci Web.

> [!IMPORTANT]
> W swoim eksperymencie usługi Azure Machine Learning Studio (klasycznym) porty wejściowe i wyjściowe usługi sieci web oraz parametry globalne mają nazwy domyślne ("input1", "input2"), które można dostosować. Nazwy używane dla webServiceInputs, webServiceOutputs i globalParameters ustawienia muszą dokładnie odpowiadać nazwy w eksperymentach. Można wyświetlić ładunek żądania przykładu na stronie Pomocy wykonywania wsadu dla punktu końcowego usługi Azure Machine Learning Studio (klasyczny), aby zweryfikować oczekiwane mapowanie.
>
>

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
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenariusz 2: Eksperymenty przy użyciu modułów czytnika/modułów modułów modułów modułów modułów w celu odwoływania się do danych w różnych magazynach
Innym typowym scenariuszem podczas tworzenia eksperymentów usługi Azure Machine Learning Studio (klasyczne) jest użycie modułów Importuj dane i Dane wyjściowe. Moduł Importuj dane służy do ładowania danych do eksperymentu, a moduł Dane wyjściowe służy do zapisywania danych z eksperymentów. Aby uzyskać szczegółowe informacje na temat modułów Importuj dane i Dane wyjściowe, zobacz [Importowanie danych](https://msdn.microsoft.com/library/azure/dn905997.aspx) i [danych wyjściowych](https://msdn.microsoft.com/library/azure/dn905984.aspx) w bibliotece MSDN.

Podczas korzystania z importu danych i danych wyjściowych modułów, jest dobrą praktyką, aby użyć parametru usługi sieci Web dla każdej właściwości tych modułów. Te parametry sieci web umożliwiają skonfigurowanie wartości w czasie wykonywania. Na przykład można utworzyć eksperyment z modułem Importuj dane, który używa bazy danych SQL Azure: XXX.database.windows.net. Po wdrożeniu usługi sieci web chcesz włączyć konsumentów usługi sieci web, aby `YYY.database.windows.net`określić inny program Azure SQL Server o nazwie . Można użyć parametru usługi sieci Web, aby zezwolić na skonfigurowanie tej wartości.

> [!NOTE]
> Dane wejściowe i wyjściowe usługi sieci Web różnią się od parametrów usługi sieci Web. W pierwszym scenariuszu można zobaczyć, jak dane wejściowe i wyjściowe można określić dla usługi Azure Machine Learning Studio (klasycznej) usługi sieci Web. W tym scenariuszu należy przekazać parametry dla usługi sieci Web, które odpowiadają właściwościom modułów Importuj dane/Dane wyjściowe.
>
>

Przyjrzyjmy się scenariuszowi korzystania z parametrów usługi sieci Web. Masz wdrożoną usługę sieci web usługi Azure Machine Learning, która używa modułu czytnika do odczytu danych z jednego ze źródeł danych obsługiwanych przez usługę Azure Machine Learning (na przykład: Usługa Azure SQL Database). Po wykonaniu wsadowego wyniki są zapisywane przy użyciu modułu modułu writer (azure sql database).  W eksperymentach nie zdefiniowano żadnych wejść i wyjść usługi sieci web. W takim przypadku zaleca się skonfigurowanie odpowiednich parametrów usługi sieci web dla modułów czytnika i modułów modułów modułów modułów modułów modułów modułów. Ta konfiguracja umożliwia moduły czytnika/modułu zapisującego, które mają być skonfigurowane podczas korzystania z działania AzureMLBatchExecution. Parametry usługi sieci Web należy określić w sekcji **globalParameters** w działaniu JSON w następujący sposób.

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
> W parametrach usługi sieci Web rozróżniana jest wielkość liter, dlatego należy upewnić się, że nazwy określone w usłudze JSON są zgodne z nazwami ujawnionymi przez usługę sieci Web.
>

Po zakończeniu przekwalifikowania zaktualizuj usługę sieci web oceniania (eksperyment predykcyjny ujawniony jako usługa sieci web) przy użyciu nowo przeszkolonego modelu przy użyciu **działania zasobów aktualizacji usługi Azure Machine Learning Studio (klasycznej).** Aby uzyskać szczegółowe informacje, zobacz [Aktualizowanie modeli przy użyciu](update-machine-learning-models.md) artykułu Aktualizuj działanie zasobów.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób:

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktywność gałęzi](transform-data-using-hadoop-hive.md)
* [Aktywność świń](transform-data-using-hadoop-pig.md)
* [Działanie mapreduce](transform-data-using-hadoop-map-reduce.md)
* [Aktywność w serwisie Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Aktywność iskierki](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
