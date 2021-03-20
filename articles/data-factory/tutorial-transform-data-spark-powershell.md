---
title: 'Przekształcanie danych przy użyciu platformy Spark w Azure Data Factory '
description: Ten samouczek zawiera instrukcje krok po kroku przekształcania danych za pomocą platformy Spark w usłudze Azure Data Factory.
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
author: nabhishek
ms.author: abnarain
ms.openlocfilehash: cf425ecb1a8aebf594828bfba50e9aba8ca9dd7e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377622"
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Przekształcanie danych w chmurze za pomocą działania platformy Spark w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku użyjesz programu Azure PowerShell do utworzenia potoku fabryki danych, który przekształca dane przy użyciu działania platformy Spark i połączonej usługi HDInsight na żądanie. Ten samouczek obejmuje następujące procedury:

> [!div class="checklist"]
> * Tworzenie fabryki danych. 
> * Tworzenie i wdrażanie połączonych usług
> * Redagowanie i wdrażanie potoku. 
> * Uruchom potok.
> * Monitorowanie uruchomienia potoku.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Konto usługi Azure Storage**. Utworzysz skrypt w języku Python i plik wejściowy, a następnie przekażesz je do usługi Azure Storage. Dane wyjściowe programu platformy Spark są przechowywane na tym koncie magazynu. Klaster platformy Spark na żądanie używa tego samego konta magazynu, jako swojego podstawowego magazynu.  
* Zainstalowanie programu **Azure PowerShell**. Wykonaj instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Przekazywanie skryptu w języku Python do konta usługi Blob Storage
1. Utwórz plik w języku Python o nazwie **WordCount_Spark. PR** z następującą zawartością: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Zastąp **&lt; storageAccountName &gt;** nazwą konta usługi Azure Storage. Następnie zapisz plik. 
3. W usłudze Azure Blob Storage utwórz kontener o nazwie **adftutorial**, jeśli nie istnieje. 
4. Utwórz folder o nazwie **spark**.
5. Utwórz podfolder o nazwie **script** w folderze **spark**. 
6. Przekaż plik **WordCount_Spark.py** do podfolderu **script**. 


### <a name="upload-the-input-file"></a>Przekazywanie pliku wejściowego
1. Utwórz plik o nazwie **minecraftstory.txt** zawierający tekst. Program platformy Spark zlicza liczbę słów w tym tekście. 
2. Utwórz podfolder o nazwie `inputfiles` w folderze `spark`. 
3. Przekaż `minecraftstory.txt` do podfolderu `inputfiles`. 

## <a name="author-linked-services"></a>Redagowanie połączonych usług
W tej sekcji zredagujesz dwie połączone usługi: 
    
- Połączoną usługę Azure Storage, która łączy konto usługi Azure Storage z fabryką danych. Ten magazyn jest używany przez klaster usługi HDInsight na żądanie. Zawiera on także skrypt platformy Spark do wykonania. 
- Połączona usługa HDInsight na żądanie. Usługa Azure Data Factory automatycznie tworzy klaster usługi HDInsight, uruchamia program platformy Spark i usuwa klaster usługi HDInsight, gdy jest on bezczynny przez wstępnie skonfigurowany czas. 

### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Utwórz plik w formacie JSON za pomocą preferowanego edytora, skopiuj poniższą definicję formatu JSON dotyczącą połączonej usługi Azure Storage, a następnie zapisz plik jako **MyStorageLinkedService.json**.  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>"
      }
    }
}
```
Zaktualizuj wartości parametrów &lt;storageAccountName&gt; i &lt;storageAccountKey&gt; nazwą konta usługi Azure Storage i jego kluczem. 


### <a name="on-demand-hdinsight-linked-service"></a>Połączona usługa HDInsight na żądanie
Utwórz plik w formacie JSON za pomocą preferowanego edytora, skopiuj poniższą definicję formatu JSON dotyczącą połączonej usługi Azure HDIsight, a następnie zapisz plik jako **MyOnDemandSparkLinkedService.json**.  

```json
{
    "name": "MyOnDemandSparkLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
        "clusterSize": 2,
        "clusterType": "spark",
        "timeToLive": "00:15:00",
        "hostSubscriptionId": "<subscriptionID> ",
        "servicePrincipalId": "<servicePrincipalID>",
        "servicePrincipalKey": {
          "value": "<servicePrincipalKey>",
          "type": "SecureString"
        },
        "tenant": "<tenant ID>",
        "clusterResourceGroup": "<resourceGroupofHDICluster>",
        "version": "3.6",
        "osType": "Linux",
        "clusterNamePrefix":"ADFSparkSample",
        "linkedServiceName": {
          "referenceName": "MyStorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }
}
```
Zaktualizuj wartości następujących właściwości w definicji połączonej usługi: 

- **hostSubscriptionId**. Zastąp właściwość &lt;SubscriptionId&gt; identyfikatorem subskrypcji platformy Azure. Klaster usługi HDInsight na żądanie jest tworzony w tej subskrypcji. 
- **dzierżawca**. Zastąp właściwość &lt;tenantID&gt; identyfikatorem dzierżawy platformy Azure. 
- **servicePrincipalId**, **servicePrincipalKey**. Zastąp właściwości &lt;servicePrincipalID&gt; i &lt;servicePrincipalKey&gt; identyfikatorem i kluczem jednostki usługi w usłudze Azure Active Directory. Jednostka usługi musi być członkiem roli współautora subskrypcji lub grupy zasobów, gdzie został utworzony klaster. Aby uzyskać szczegółowe informacje, zobacz [Create Azure Active Directory application and service principal (Tworzenie jednostki usługi i aplikacji usługi Azure Active Directory)](../active-directory/develop/howto-create-service-principal-portal.md). **Identyfikator jednostki usługi** jest odpowiednikiem *identyfikatora aplikacji* , a klucz jednostki **usługi** jest równoważny z wartością *wpisu tajnego klienta*.
- **clusterResourceGroup**. Zastąp właściwość &lt;resourceGroupOfHDICluster&gt; nazwą grupy zasobów, w której ma zostać utworzony klaster usługi HDInsight. 

> [!NOTE]
> Usługa Azure HDInsight ma ograniczenia całkowitej liczby rdzeni, których możesz użyć w każdym obsługiwanym przez nią regionie platformy Azure. Dla połączonej usługi HDInsight na żądanie zostanie utworzony klaster usługi HDInsight w tej samej lokalizacji usługi Azure Storage użytej jako jej podstawowy magazyn. Upewnij się, że masz wystarczająco duże limity przydziału dla klastra, aby można go było pomyślnie utworzyć. Aby uzyskać więcej informacji, zobacz [Set up clusters in HDInsight with Hadoop, Spark, Kafka and more (Konfigurowanie klastrów w usłudze HDInsight za pomocą platform Hadoop, Spark, Kafka i innych)](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 


## <a name="author-a-pipeline"></a>Redagowanie potoku 
W tym kroku utworzysz nowy potok za pomocą działania platformy Spark. Działanie wykorzystuje próbkę **liczby słów**. Jeśli jeszcze tego nie zrobiono, pobierz zawartość z tej lokalizacji.

Utwórz plik w formacie JSON za pomocą preferowanego edytora, skopiuj poniższą definicję formatu JSON dotyczącą definicji potoku, a następnie zapisz go jako **MySparkOnDemandPipeline.json**. 

```json
{
  "name": "MySparkOnDemandPipeline",
  "properties": {
    "activities": [
      {
        "name": "MySparkActivity",
        "type": "HDInsightSpark",
        "linkedServiceName": {
            "referenceName": "MyOnDemandSparkLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "rootPath": "adftutorial/spark",
          "entryFilePath": "script/WordCount_Spark.py",
          "getDebugInfo": "Failure",
          "sparkJobLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}
```

Pamiętaj o następujących kwestiach: 

- Właściwość rootPath wskazuje folder platformy Spark kontenera adftutorial. 
- Właściwość entryFilePath wskazuje plik WordCount_Spark.py w podfolderze skryptu folderu Spark. 


## <a name="create-a-data-factory"></a>Tworzenie fabryki danych 
W plikach w formacie JSON zostały zredagowane połączona usługa i definicje potoku. Teraz Utwórzmy fabrykę danych i wdrożono pliki JSON połączonej usługi i potoku za pomocą poleceń cmdlet programu PowerShell. Uruchom następujące polecenia programu PowerShell jedno po drugim: 

1. Ustaw zmienne jedną po drugiej.

    **Nazwa grupy zasobów**
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```

    **Nazwa Data Factory. Musi być globalnie unikatowa** 
    ```powershell
    $dataFactoryName = "MyDataFactory09102017"
    ```
    
    **Nazwa potoku**
    ```powershell
    $pipelineName = "MySparkOnDemandPipeline" # Name of the pipeline
    ```
2. Uruchom program **PowerShell**. Nie zamykaj programu Azure PowerShell aż do końca tego samouczka Szybki start. Jeśli go zamkniesz i otworzysz ponownie, musisz uruchomić te polecenia jeszcze raz. Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.

    Uruchom poniższe polecenie i wprowadź nazwę użytkownika oraz hasło, których używasz do logowania się w witrynie Azure Portal:
        
    ```powershell
    Connect-AzAccount
    ```        
    Uruchom poniższe polecenie, aby wyświetlić wszystkie subskrypcje dla tego konta:

    ```powershell
    Get-AzSubscription
    ```
    Uruchom poniższe polecenie, aby wybrać subskrypcję, z którą chcesz pracować. Zastąp **Identyfikator subskrypcji ID** subskrypcji platformy Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. Utwórz grupę zasobów: ADFTutorialResourceGroup. 

    ```powershell
    New-AzResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Utwórz fabrykę danych. 

    ```powershell
     $df = Set-AzDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Wykonaj następujące polecenie, aby wyświetlić dane wyjściowe: 

    ```powershell
    $df
    ```
5. Przejdź do folderu, w którym zostały utworzone pliki w formacie JSON, a następnie uruchom następujące polecenie, aby wdrożyć połączoną usługę Azure Storage: 
       
    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
6. Uruchom następujące polecenie, aby wdrożyć połączoną usługę Spark na żądanie: 
       
    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyOnDemandSparkLinkedService" -File "MyOnDemandSparkLinkedService.json"
    ```
7. Uruchom następujące polecenie, aby wdrożyć potok: 
       
    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MySparkOnDemandPipeline.json"
    ```
    
## <a name="start-and-monitor-a-pipeline-run"></a>Uruchamianie i monitorowanie działania potoku  

1. Uruchom potok. Umożliwia to również przechwycenie identyfikatora uruchomienia potoku w celu monitorowania w przyszłości.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName  
    ```
2. Uruchom następujący skrypt, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia jego działania.

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    
        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }

    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n" 
    ```  
3. Oto dane wyjściowe przykładowego uruchomienia: 

    ```
    Pipeline run status: In Progress
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : 
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : 
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 
    DurationInMs      : 
    Status            : InProgress
    Error             :
    …
    
    Pipeline ' MySparkOnDemandPipeline' run finished. Result:
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : MyDataFactory09102017
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : {clusterInUse, jobId, ExecutionProgress, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 9/20/2017 6:46:30 AM
    DurationInMs      : 763466
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "clusterInUse": "https://ADFSparkSamplexxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurehdinsight.net/"
    "jobId": "0"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MySparkActivity"
    ```
4. Potwierdź, że folder o nazwie `outputfiles` jest tworzony w folderze `spark` kontenera adftutorial zawierającym dane wyjściowe z programu platformy Spark. 


## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. W tym samouczku omówiono: 

> [!div class="checklist"]
> * Tworzenie fabryki danych. 
> * Tworzenie i wdrażanie połączonych usług
> * Redagowanie i wdrażanie potoku. 
> * Uruchom potok.
> * Monitorowanie uruchomienia potoku.

Przejdź do następnego samouczka, aby dowiedzieć się, jak przekształcać dane, uruchamiając skrypt programu Hive w klastrze usługi Azure HDInsight, który znajduje się w sieci wirtualnej. 

> [!div class="nextstepaction"]
> [Samouczek: Przekształcanie danych przy użyciu usługi Hive w usłudze Azure Virtual Network](tutorial-transform-data-hive-virtual-network.md).





