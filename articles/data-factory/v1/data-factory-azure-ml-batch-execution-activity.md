---
title: Tworzenie predykcyjnych potoków danych przy użyciu Azure Data Factory
description: Zawiera opis sposobu tworzenia potoku predykcyjnego przy użyciu Azure Data Factory i Azure Machine Learning Studio (klasyczny)
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 9b773eee27cd72562999e468f90dd87907cf9677
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776191"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Tworzenie potoków predykcyjnych przy użyciu Azure Machine Learning Studio (klasyczny) i Azure Data Factory

> [!div class="op_single_selector" title1="Działania transformacji"]
> * [Działanie Hive](data-factory-hive-activity.md)
> * [Aktywność trzody chlewnej](data-factory-pig-activity.md)
> * [Działanie MapReduce](data-factory-map-reduce.md)
> * [Działanie przesyłania strumieniowego Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działanie platformy Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Działanie aktualizowania zasobów w usłudze Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Niestandardowe działanie platformy .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Wprowadzenie
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Przekształć dane przy użyciu funkcji Uczenie maszynowe w Data Factory](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasyczny)
[Azure Machine Learning Studio (klasyczny)](https://azure.microsoft.com/documentation/services/machine-learning/) umożliwia tworzenie, testowanie i wdrażanie rozwiązań do analizy predykcyjnej. Z punktu widzenia wysokiego poziomu można wykonać trzy kroki:

1. **Utwórz eksperyment szkoleniowy**. Ten krok można wykonać przy użyciu Azure Machine Learning Studio (klasyczne). Azure Machine Learning Studio (klasyczny) to wspólne środowisko programistyczne wizualne, które służy do uczenia i testowania modelu analizy predykcyjnej przy użyciu danych szkoleniowych.
2. **Przekonwertuj go na eksperyment predykcyjny**. Po przeszkoleniu modelu z istniejących danych i przygotowaniu go do oceny nowych danych możesz przygotować i usprawnić eksperyment do oceniania.
3. **Wdróż go jako usługę sieci Web**. Eksperyment oceniania można opublikować jako usługę sieci Web platformy Azure. Dane można wysyłać do modelu za pośrednictwem tego punktu końcowego usługi sieci Web i odbierać przewidywania wyników z modelem.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory to oparta na chmurze usługa integracji danych, która organizuje i automatyzuje **przenoszenie** i **transformację** danych. Możesz tworzyć rozwiązania integracji danych za pomocą Azure Data Factory, które mogą pozyskiwanie danych z różnych magazynów danych, przekształcać/przetwarzać dane i publikować dane wynikowe w magazynach danych.

Usługa Data Factory pozwala tworzyć potoki danych służące do przenoszenia i przekształcania danych, a następnie uruchamiać te potoki zgodnie z zaplanowanym harmonogramem (co godzinę, codziennie, co tydzień itp.). Usługa ta udostępnia również rozbudowane wizualizacje umożliwiające wyświetlanie elementów powiązanych i zależności między potokami danych oraz monitorowanie wszystkich potoków danych w jednym zintegrowanym widoku, który ułatwia wykrywanie problemów i konfigurowanie alertów monitorowania.

Zapoznaj [się z artykułem wprowadzenie do Azure Data Factory](data-factory-introduction.md) i [Skompiluj pierwsze artykuły z potoku](data-factory-build-your-first-pipeline.md) , aby szybko rozpocząć pracę z usługą Azure Data Factory.

### <a name="data-factory-and-machine-learning-studio-classic-together"></a>Data Factory i Machine Learning Studio (klasyczne)
Azure Data Factory umożliwia łatwe tworzenie potoków korzystających z opublikowanej usługi sieci Web [Azure Machine Learning Studio (klasycznej)][azure-machine-learning] na potrzeby analizy predykcyjnej. Korzystając z **działania wykonywania wsadowego** w potoku Azure Data Factory, można wywołać usługę sieci Web Azure Machine Learning Studio (klasyczną), aby wykonywać przewidywania dotyczące danych w usłudze Batch. Aby uzyskać szczegółowe informacje, zobacz Wywoływanie usługi sieci Web Azure Machine Learning Studio (klasycznej) za pomocą sekcji działanie wykonywania wsadowego.

W miarę upływu czasu modele predykcyjne w Azure Machine Learning Studio (klasyczne) eksperymenty oceniania muszą być ponownie przenoszone przy użyciu nowych wejściowych zestawów danych. Możesz ponownie przeprowadzić uczenie modelu Azure Machine Learning Studio (klasycznego) z potoku Data Factory, wykonując następujące czynności:

1. Opublikuj eksperyment szkoleniowy (nie eksperyment predykcyjny) jako usługę sieci Web. Ten krok należy wykonać w Azure Machine Learning Studio (klasyczny), ponieważ udało Ci się uwidocznić eksperyment predykcyjny jako usługę sieci Web w poprzednim scenariuszu.
2. Użyj działania wykonywania wsadowego Azure Machine Learning Studio (klasycznego), aby wywołać usługę sieci Web dla eksperymentu szkoleniowego. W zasadzie można użyć działania wykonywania wsadowego Azure Machine Learning Studio (klasycznego) do wywołania zarówno szkoleń usługi sieci Web, jak i usługi sieci Web oceniania.

Po wykonaniu ponownych szkoleń zaktualizuj usługę sieci Web oceniania (eksperyment predykcyjny uwidoczniony jako usługa sieci Web) z nowym przeszkolonym modelem przy użyciu **działania aktualizacji zasobów Azure Machine Learning Studio (klasyczny)**. Aby uzyskać szczegółowe informacje, zobacz [aktualizowanie modeli za pomocą artykułu aktualizowanie zasobów](data-factory-azure-ml-update-resource-activity.md) .

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Wywoływanie usługi sieci Web przy użyciu działania wykonywania wsadowego
Za pomocą Azure Data Factory można organizować przenoszenie i przetwarzanie danych, a następnie wykonywać wykonywanie wsadowe przy użyciu Azure Machine Learning Studio (klasyczne). Poniżej przedstawiono kroki najwyższego poziomu:

1. Utwórz połączoną usługę Azure Machine Learning Studio (klasyczną). Potrzebne są następujące wartości:

   1. **Identyfikator URI żądania** dla interfejsu API wykonywania wsadowego. Identyfikator URI żądania można znaleźć, klikając link **wykonywania wsadowego** na stronie usługi sieci Web.
   2. **Klucz interfejsu API** dla opublikowanej Azure Machine Learning Studio (klasycznej) usługi sieci Web. Klucz interfejsu API można znaleźć, klikając opublikowaną usługę sieci Web.
   3. Użyj działania **AzureMLBatchExecution** .

      ![Pulpit nawigacyjny Machine Learning Studio (klasyczny)](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Identyfikator URI partii](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenariusz: eksperymenty wykorzystujące dane wejściowe/wyjściowe usługi sieci Web, które odwołują się do danych w usłudze Azure Blob Storage
W tym scenariuszu usługa sieci Web Azure Machine Learning Studio (klasyczna) dokonuje prognozowania przy użyciu danych z pliku w magazynie obiektów blob platformy Azure i zapisuje wyniki prognozowania w magazynie obiektów BLOB. Poniższy kod JSON definiuje potok Data Factory za pomocą działania AzureMLBatchExecution. Działanie ma zestaw danych **DecisionTreeInputBlob** jako dane wejściowe i **DecisionTreeResultBlob** jako dane wyjściowe. **DecisionTreeInputBlob** jest przenoszona jako dane wejściowe do usługi sieci Web przy użyciu właściwości JSON **webServiceInput** . **DecisionTreeResultBlob** jest przekazywane jako dane wyjściowe do usługi sieci Web przy użyciu właściwości JSON **webServiceOutputs** .

> [!IMPORTANT]
> Jeśli usługa sieci Web przyjmuje wiele danych wejściowych, użyj właściwości **webServiceInputs** zamiast opcji **webServiceInput**. Aby zapoznać się z przykładem użycia właściwości webServiceInputs, [Usługa sieci Web wymaga wielu danych wejściowych](#web-service-requires-multiple-inputs) .
>
> Zestawy danych, do których odwołują się właściwości **webServiceInput** / **webServiceInputs** i **webServiceOutputs** (w **typeProperties**), muszą również znajdować się w **danych wejściowych** i **wyjściowych**działania.
>
> W ramach eksperymentu Azure Machine Learning Studio (klasycznego) porty wejściowe i wyjściowe usługi sieci Web oraz parametry globalne mają nazwy domyślne ("INPUT1", "input2"), które można dostosować. Nazwy używane dla ustawień webServiceInputs, webServiceOutputs i globalParameters muszą dokładnie pasować do nazw eksperymentów. Przykładowy ładunek żądania można wyświetlić na stronie pomocy wykonywania wsadowego dla punktu końcowego Azure Machine Learning Studio (klasycznego), aby sprawdzić oczekiwane mapowanie.
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> Tylko dane wejściowe i wyjściowe działania AzureMLBatchExecution mogą być przesyłane jako parametry do usługi sieci Web. Na przykład w powyższym fragmencie kodu JSON DecisionTreeInputBlob to dane wejściowe działania AzureMLBatchExecution, które są przesyłane jako dane wejściowe do usługi sieci Web za pośrednictwem parametru webServiceInput.
>
>

### <a name="example"></a>Przykład
Ten przykład używa usługi Azure Storage do przechowywania danych wejściowych i wyjściowych.

Zalecamy zapoznanie się z samouczkiem [Kompilowanie pierwszego potoku przy użyciu Data Factory][adf-build-1st-pipeline] . Użyj edytora Data Factory, aby utworzyć Data Factory artefaktów (połączone usługi, zestawy danych, potok) w tym przykładzie.

1. Utwórz **połączoną usługę** dla usługi **Azure Storage**. Jeśli pliki wejściowe i wyjściowe znajdują się na różnych kontach magazynu, potrzebne są dwie połączone usługi. Oto przykład kodu JSON:

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. Utwórz **zestaw danych** **wejściowych** Azure Data Factory. W przeciwieństwie do innych zestawów danych Data Factory, te zestawy danych muszą zawierać zarówno wartości **folderPath** , jak i **filename** . Partycjonowanie można użyć do przetworzenia lub tworzenia unikatowych plików wejściowych i wyjściowych poszczególnych wykonań wsadowych (każdy wycinek danych). Może być konieczne dołączenie niektórych działań nadrzędnych w celu przekształcenia danych wejściowych w format pliku CSV i umieszczenia ich na koncie magazynu dla każdego wycinka. W takim przypadku nie należy uwzględniać ustawień **zewnętrznych** i **externalData** , które przedstawiono w poniższym przykładzie, a DecisionTreeInputBlob będzie wyjściowym zestawem danych innego działania.

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }
    ```

    Wejściowy plik CSV musi mieć wiersz nagłówka kolumny. Jeśli używasz **działania kopiowania** do tworzenia/przenoszenia woluminu CSV do magazynu obiektów blob, należy ustawić właściwość ujścia **blobWriterAddHeader** na **wartość true**. Przykład:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Jeśli plik CSV nie ma wiersza nagłówka, może zostać wyświetlony następujący błąd: **błąd w działaniu: błąd podczas odczytywania ciągu. Nieoczekiwany token: StartObject. Ścieżka "", wiersz 1, pozycja 1**.
3. Utwórz **zestaw danych** **wyjściowych** Azure Data Factory. Ten przykład używa partycjonowania, aby utworzyć unikatową ścieżkę wyjściową dla każdego wykonywania wycinków. Bez partycjonowania działanie spowoduje zastąpienie pliku.

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. Utwórz **połączoną usługę** typu: **połączenie**, podając klucz interfejsu API i adres URL wykonania partii modelu.

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. Na koniec Utwórz potok zawierający działanie **AzureMLBatchExecution** . W czasie wykonywania potok wykonuje następujące czynności:

   1. Pobiera lokalizację pliku wejściowego z wejściowych zestawów danych.
   2. Wywołuje interfejs API wykonywania wsadowego Azure Machine Learning Studio (klasyczny)
   3. Kopiuje dane wyjściowe wykonywania wsadowego do obiektu BLOB podanego w wyjściowym zestawie danych.

      > [!NOTE]
      > Działanie AzureMLBatchExecution może mieć zero lub więcej danych wejściowych i co najmniej jedno wyjście.
      >
      >

      ```JSON
      {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
      }
      ```

      **Początkowe** i **końcowe** daty i godziny muszą być w [formacie ISO](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41Z. Czas **zakończenia** jest opcjonalny. Jeśli nie określisz wartości właściwości **End** , zostanie ona obliczona jako "**Start + 48 godzin".** Aby uruchomić potok bezterminowo, określ **9999-09-09** jako wartość właściwości **end**. Szczegółowe informacje dotyczące właściwości kodu JSON znajdują się w artykule [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Dokumentacja dotycząca skryptów JSON).

      > [!NOTE]
      > Określanie danych wejściowych dla działania AzureMLBatchExecution jest opcjonalne.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenariusz: eksperymenty używające modułów czytnika/składnika zapisywania do odwoływania się do danych w różnych magazynach
Innym typowym scenariuszem podczas tworzenia Azure Machine Learning Studio (klasycznego) eksperymentów jest używanie modułów czytnika i modułu zapisywania. Moduł czytnika służy do ładowania danych do eksperymentu, a Moduł zapisywania służy do zapisywania danych z eksperymentów. Aby uzyskać szczegółowe informacje na temat modułów czytnika i składnika zapisywania, zobacz tematy dotyczące [czytelnika](https://msdn.microsoft.com/library/azure/dn905997.aspx) i [składnika zapisywania](https://msdn.microsoft.com/library/azure/dn905984.aspx) w bibliotece MSDN.

W przypadku korzystania z modułów czytnika i składnika zapisywania warto użyć parametru usługi sieci Web dla każdej właściwości tych modułów czytnika/składnika zapisywania. Te parametry sieci Web umożliwiają skonfigurowanie wartości w czasie wykonywania. Można na przykład utworzyć eksperyment z modułem czytnika korzystającym z Azure SQL Database: XXX.database.windows.net. Po wdrożeniu usługi sieci Web należy umożliwić użytkownikom usługi sieci Web określenie innego logicznego serwera SQL o nazwie YYY.database.windows.net. Możesz użyć parametru usługi sieci Web, aby umożliwić skonfigurowanie tej wartości.

> [!NOTE]
> Dane wejściowe i wyjściowe usługi sieci Web różnią się od parametrów usługi sieci Web. W pierwszym scenariuszu pokazano, jak można określić dane wejściowe i wyjściowe dla usługi sieci Web Azure Machine Learning Studio (klasyczna). W tym scenariuszu przekazywane są parametry usługi sieci Web odpowiadające właściwościom modułów czytnika/składnika zapisywania.
>
>

Przyjrzyjmy się scenariuszowi używania parametrów usługi sieci Web. Masz wdrożoną Azure Machine Learning Studio (klasyczną) usługę sieci Web, która używa modułu czytnika do odczytywania danych z jednego ze źródeł danych obsługiwanych przez Azure Machine Learning Studio (klasyczny) (na przykład: Azure SQL Database). Po wykonaniu zadania wsadowego wyniki są zapisywane przy użyciu modułu składnika zapisywania (Azure SQL Database).  W eksperymentach nie są zdefiniowane żadne dane wejściowe i wyjściowe usługi sieci Web. W tym przypadku zalecamy skonfigurowanie odpowiednich parametrów usługi sieci Web dla modułów czytnika i składnika zapisywania. Ta konfiguracja umożliwia skonfigurowanie modułów czytnika/składnika zapisywania przy użyciu działania AzureMLBatchExecution. Parametry usługi sieci Web należy określić w sekcji **globalParameters** w kodzie JSON działania w następujący sposób.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Możesz również użyć [funkcji Data Factory](data-factory-functions-variables.md) w przekazywaniu wartości parametrów usługi sieci Web, jak pokazano w następującym przykładzie:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> W parametrach usługi sieci Web rozróżniana jest wielkość liter, dlatego należy upewnić się, że nazwy określone w formacie JSON działania są zgodne z tymi, które są udostępniane przez usługę sieci Web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Używanie modułu czytnika do odczytywania danych z wielu plików w obiekcie blob platformy Azure
Potoki danych Big Data z działaniami, takimi jak świnie i Hive, mogą generować jeden lub więcej plików wyjściowych bez rozszerzeń. Na przykład podczas określania zewnętrznej tabeli programu Hive dane dla zewnętrznej tabeli programu Hive mogą być przechowywane w usłudze Azure Blob Storage o następującej nazwie 000000_0. Możesz użyć modułu czytnika w eksperymentie, aby odczytać wiele plików i użyć ich do przewidywania.

W przypadku korzystania z modułu czytnika w doświadczeniu Azure Machine Learning Studio (klasycznym) można określić obiekt blob platformy Azure jako dane wejściowe. Pliki w magazynie obiektów blob platformy Azure mogą być plikami wyjściowymi (przykład: 000000_0), które są tworzone przez skrypt trzody chlewnej i Hive działający w usłudze HDInsight. Moduł czytnika umożliwia odczytywanie plików (bez rozszerzeń) przez skonfigurowanie **ścieżki do kontenera, katalogu/obiektu BLOB**. **Ścieżka do kontenerów** wskazuje kontener i **Katalog/obiekt BLOB** do folderu, który zawiera pliki, jak pokazano na poniższej ilustracji. Gwiazdka oznacza, że \* **wszystkie pliki w kontenerze/folderze (czyli dane/aggregateddata/Year = 2014/miesiąc-6/ \* )** są odczytywane w ramach eksperymentu.

![Właściwości obiektów blob platformy Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Przykład
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Potok z działaniem AzureMLBatchExecution z parametrami usługi sieci Web

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning Studio (classic) model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

W powyższym przykładzie JSON:

* Wdrożona Azure Machine Learning Studio (klasyczna) usługa sieci Web używa czytnika i modułu składnika zapisywania do odczytu/zapisu danych z/do Azure SQL Database. Ta usługa sieci Web udostępnia następujące cztery parametry: Nazwa serwera bazy danych, nazwa bazy danych, nazwa konta użytkownika serwera i hasło konta użytkownika serwera.
* **Początkowe** i **końcowe** daty i godziny muszą być w [formacie ISO](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41Z. Czas **zakończenia** jest opcjonalny. Jeśli nie określisz wartości właściwości **End** , zostanie ona obliczona jako "**Start + 48 godzin".** Aby uruchomić potok bezterminowo, określ **9999-09-09** jako wartość właściwości **end**. Szczegółowe informacje dotyczące właściwości kodu JSON znajdują się w artykule [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) (Dokumentacja dotycząca skryptów JSON).

### <a name="other-scenarios"></a>Inne scenariusze
#### <a name="web-service-requires-multiple-inputs"></a>Usługa sieci Web wymaga wielu danych wejściowych
Jeśli usługa sieci Web przyjmuje wiele danych wejściowych, użyj właściwości **webServiceInputs** zamiast opcji **webServiceInput**. Zestawy danych, do których odwołuje się **webServiceInputs** , muszą być również zawarte w **danych wejściowych**działania.

W ramach eksperymentu Azure Machine Learning Studio (klasycznego) porty wejściowe i wyjściowe usługi sieci Web oraz parametry globalne mają nazwy domyślne ("INPUT1", "input2"), które można dostosować. Nazwy używane dla ustawień webServiceInputs, webServiceOutputs i globalParameters muszą dokładnie pasować do nazw eksperymentów. Przykładowy ładunek żądania można wyświetlić na stronie pomocy wykonywania wsadowego dla punktu końcowego Azure Machine Learning Studio (klasycznego), aby sprawdzić oczekiwane mapowanie.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Usługa sieci Web nie wymaga danych wejściowych
Azure Machine Learning Studio (klasyczny) usługi sieci Web wykonywania wsadowego mogą służyć do uruchamiania dowolnych przepływów pracy, na przykład skryptów języka R lub Python, które mogą nie wymagać żadnych danych wejściowych. Lub eksperyment można skonfigurować przy użyciu modułu czytnika, który nie ujawnia żadnych GlobalParameters. W takim przypadku działanie AzureMLBatchExecution można skonfigurować w następujący sposób:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Usługa sieci Web nie wymaga danych wejściowych/wyjściowych
Usługa sieci Web wykonywania wsadowego (Azure Machine Learning Studio) może nie mieć skonfigurowanych danych wyjściowych usługi sieci Web. W tym przykładzie nie ma żadnych skonfigurowanych GlobalParameters usługi sieci Web ani danych wyjściowych. Nadal istnieją dane wyjściowe skonfigurowane dla działania, ale nie są one określone jako webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Usługa sieci Web używa czytelników i autorów, a działanie działa tylko wtedy, gdy inne działania zakończyły się powodzeniem
Moduły czytnika i modułu zapisywania usługi sieci Web Azure Machine Learning Studio (klasyczne) mogą być skonfigurowane do uruchamiania z lub bez GlobalParameters. Można jednak osadzić wywołania usługi w potoku, który używa zależności zestawu danych do wywołania usługi tylko wtedy, gdy zostało zakończone pewne przetwarzanie nadrzędne. Możesz również wyzwolić inną akcję po zakończeniu wykonywania zadania wsadowego przy użyciu tego podejścia. W takim przypadku można wyznaczać zależności przy użyciu danych wejściowych i wyjściowych działania bez nazywania ich jako danych wejściowych lub wyjściowych usługi sieci Web.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

**Wnioski** są następujące:

* Jeśli punkt końcowy eksperymentu używa elementu webServiceInput: jest reprezentowany przez zestaw danych obiektów blob i znajduje się w danych wejściowych działania oraz we właściwości webServiceInput. W przeciwnym razie Właściwość webServiceInput zostanie pominięta.
* Jeśli punkt końcowy eksperymentu używa webServiceOutput (s): są reprezentowane przez zestawy danych obiektów blob i znajdują się w wyjściach działania i we właściwości webServiceOutputs. Dane wyjściowe działania i webServiceOutputs są mapowane według nazwy każdego elementu wyjściowego w eksperymentie. W przeciwnym razie Właściwość webServiceOutputs zostanie pominięta.
* Jeśli punkt końcowy eksperymentu ujawnia globalParametery, są one określone we właściwości globalParameters działania jako klucz, par wartości. W przeciwnym razie Właściwość globalParameters zostanie pominięta. W kluczach jest rozróżniana wielkość liter. W wartościach mogą być używane [funkcje Azure Data Factory](data-factory-functions-variables.md) .
* Dodatkowe zestawy danych mogą być zawarte we właściwościach danych wejściowych i wyjściowych działania bez odwoływania się do działania typeProperties. Te zestawy danych regulują wykonywanie przy użyciu zależności wycinków, ale w przeciwnym razie są ignorowane przez działanie AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Aktualizowanie modeli przy użyciu działania Update Resource
Po wykonaniu ponownych szkoleń zaktualizuj usługę sieci Web oceniania (eksperyment predykcyjny uwidoczniony jako usługa sieci Web) z nowym przeszkolonym modelem przy użyciu **działania aktualizacji zasobów Azure Machine Learning Studio (klasyczny)**. Aby uzyskać szczegółowe informacje, zobacz [aktualizowanie modeli za pomocą artykułu aktualizowanie zasobów](data-factory-azure-ml-update-resource-activity.md) .

### <a name="reader-and-writer-modules"></a>Moduły czytnika i składnika zapisywania
Typowym scenariuszem korzystania z parametrów usługi sieci Web jest korzystanie z czytelników i autorów usługi Azure SQL. Moduł czytnika służy do ładowania danych do eksperymentu z usług zarządzania danymi spoza Azure Machine Learning Studio (klasyczny). Moduł zapisywania służy do zapisywania danych z eksperymentów w usługach zarządzania danymi poza Azure Machine Learning Studio (klasyczny).

Aby uzyskać szczegółowe informacje na temat usługi Azure Blob/czytnika lub składnika zapisywania usługi Azure SQL, zobacz tematy dotyczące [czytnika](https://msdn.microsoft.com/library/azure/dn905997.aspx) i [zapisywania](https://msdn.microsoft.com/library/azure/dn905984.aspx) w bibliotece MSDN. W przykładzie w poprzedniej sekcji użyto czytnika obiektów blob platformy Azure i składnika zapisywania obiektów blob platformy Azure. W tej sekcji omówiono korzystanie z usługi Azure SQL Reader i składnika zapisywania usługi Azure SQL.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
**P:** Mam wiele plików, które są generowane przez moje potoki danych Big Data. Czy mogę użyć działania AzureMLBatchExecution do pracy nad wszystkimi plikami?

**Odpowiedź:** tak. Aby uzyskać szczegółowe informacje, zobacz sekcję **Używanie modułu czytnika do odczytywania danych z wielu plików w usłudze Azure Blob** .

## <a name="azure-machine-learning-studio-classic-batch-scoring-activity"></a>Działanie oceny wsadowej Azure Machine Learning Studio (klasycznej)
Jeśli używasz działania **AzureMLBatchScoring** do integrowania z Azure Machine Learning Studio (klasyczne), zalecamy użycie najnowszej aktywności **AzureMLBatchExecution** .

Działanie AzureMLBatchExecution jest wprowadzane w 2015 wydania zestawu Azure SDK i Azure PowerShell.

Jeśli chcesz nadal korzystać z działania AzureMLBatchScoring, Kontynuuj odczytywanie w tej sekcji.

### <a name="azure-machine-learning-studio-classic-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Działanie oceniania wsadowego Azure Machine Learning Studio (klasycznego) przy użyciu usługi Azure Storage dla danych wejściowych/wyjściowych

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Parametry usługi sieci Web
Aby określić wartości parametrów usługi sieci Web, Dodaj sekcję **typeProperties** do sekcji **AZUREMLBATCHSCORINGACTIVITY** w kodzie JSON potoku, jak pokazano w następującym przykładzie:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Możesz również użyć [funkcji Data Factory](data-factory-functions-variables.md) w przekazywaniu wartości parametrów usługi sieci Web, jak pokazano w następującym przykładzie:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> W parametrach usługi sieci Web rozróżniana jest wielkość liter, dlatego należy upewnić się, że nazwy określone w formacie JSON działania są zgodne z tymi, które są udostępniane przez usługę sieci Web.
>
>

## <a name="see-also"></a>Zobacz też
* [Wpis w blogu platformy Azure: wprowadzenie do Azure Data Factory i Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/
