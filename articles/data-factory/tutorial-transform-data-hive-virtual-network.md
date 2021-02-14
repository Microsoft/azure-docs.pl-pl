---
title: Przekształcanie danych przy użyciu programu Hive na platformie Azure Virtual Network
description: Użyj Azure PowerShell, aby utworzyć potok Data Factory, który przekształca dane przy użyciu działania programu Hive w klastrze usługi HDInsight, który znajduje się w Virtual Network platformy Azure.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/22/2018
ms.openlocfilehash: 85f51fd52ce3224b37c27cea6c49a8a386fbea2c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377775"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Przekształcanie danych w usłudze Azure Virtual Network przy użyciu działania programu Hive w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym samouczku program Azure PowerShell umożliwia tworzenie potoku fabryki danych, który przekształca dane przy użyciu działania programu Hive w klastrze usługi HDInsight, który znajduje się w usłudze Azure Virtual Network (VNet). Ten samouczek obejmuje następujące procedury:

> [!div class="checklist"]
> * Tworzenie fabryki danych. 
> * Redagowanie i konfigurowanie własnego środowiska Integration Runtime
> * Tworzenie i wdrażanie połączonych usług
> * Redagowanie i wdrażanie potoku zawierającego działanie programu Hive
> * Uruchom potok.
> * Monitorowanie działania potoku 
> * Sprawdzanie danych wyjściowych 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Konto usługi Azure Storage**. Utwórz skrypt programu Hive i przekaż go do usługi Azure Storage. Dane wyjściowe skryptu programu Hive są przechowywane na tym koncie magazynu. W tym przykładzie klaster usługi HDInsight używa tego konta usługi Azure Storage jako magazynu głównego. 
- **Virtual Network platformy Azure.** Jeśli nie masz sieci wirtualnej platformy Azure, utwórz ją, wykonując [te instrukcje](../virtual-network/quick-create-portal.md). W tym przykładzie usługa HDInsight znajduje się w usłudze Azure Virtual Network. Oto przykładowa konfiguracja usługi Azure Virtual Network. 

    ![Tworzenie sieci wirtualnej](media/tutorial-transform-data-using-hive-in-vnet/create-virtual-network.png)
- **Klaster usługi HDInsight.** Utwórz klaster usługi HDInsight i przyłącz go do sieci wirtualnej utworzonej w poprzednim kroku, postępując zgodnie z opisem podanym w tym artykule: [Extend Azure HDInsight using an Azure Virtual Network (Rozszerzenie usługi Azure HDInsight za pomocą usługi Azure Virtual Network)](../hdinsight/hdinsight-plan-virtual-network-deployment.md). Oto przykładowa konfiguracja usługi HDInsight w sieci wirtualnej. 

    ![Usługa HDInsight w sieci wirtualnej](media/tutorial-transform-data-using-hive-in-vnet/hdinsight-in-vnet-configuration.png)
- Zainstalowanie programu **Azure PowerShell**. Wykonaj instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Przekazywanie skryptu programu Hive do konta usługi Blob Storage

1. Utwórz plik SQL programu Hive SQL o nazwie **hivescript.hql** i następującej zawartości:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. W usłudze Azure Blob Storage utwórz kontener o nazwie **adftutorial**, jeśli nie istnieje.
3. Utwórz folder o nazwie **hivescripts**.
4. Przekaż plik **hivescript.hql** do podfolderu **hivescripts**.

  

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych


1. Ustaw nazwę grupy zasobów. Ten samouczek zawiera procedurę tworzenia grupy zasobów. Możesz jednak użyć istniejącej grupy zasobów. 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```
2. Podaj nazwę fabryki danych. Musi ona być unikatowa w skali globalnej.

    ```powershell
    $dataFactoryName = "MyDataFactory09142017"
    ```
3. Określ nazwę potoku. 

    ```powershell
    $pipelineName = "MyHivePipeline" # 
    ```
4. Określ nazwę środowiska Integration Runtime (Self-Hosted). Środowisko Integration Runtime (Self-Hosted) jest wymagane, gdy fabryka danych musi uzyskać dostęp do zasobów (takich jak usługa Azure SQL Database) w sieci wirtualnej. 
    ```powershell
    $selfHostedIntegrationRuntimeName = "MySelfHostedIR09142017" 
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
3. Utwórz grupę zasobów: ADFTutorialResourceGroup, jeśli jeszcze nie istnieje w Twojej subskrypcji. 

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

## <a name="create-self-hosted-ir"></a>Tworzenie środowiska IR (Self-hosted)
W tej sekcji utworzysz środowisko Integration Runtime (Self-hosted) i skojarzysz je z maszyną wirtualną platformy Azure w tej samej usłudze Azure Virtual Network, gdzie znajduje się Twój klaster usługi HDInsight.

1. Utwórz środowisko Integration Runtime (Self-hosted). Użyj unikatowej nazwy w przypadku, gdy istnieje inne środowisko Integration Runtime o takiej samej nazwie.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted
   ```
    To polecenie tworzy logiczną rejestrację środowiska Integration Runtime (Self-hosted). 
2. Aby pobrać klucze uwierzytelniania w celu zarejestrowania środowiska Integration Runtime (Self-hosted), użyj programu PowerShell. Skopiuj jeden z kluczy w celu zarejestrowania środowiska Integration Runtime (Self-hosted).

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName | ConvertTo-Json
   ```

   Oto przykładowe dane wyjściowe: 

   ```powershell
   {
       "AuthKey1":  "IR@0000000000000000000000000000000000000=",
       "AuthKey2":  "IR@0000000000000000000000000000000000000="
   }
   ```
    Zanotuj wartość pozycji **AuthKey1** bez znaków cudzysłowu. 
3. Utwórz maszynę wirtualną platformy Azure i przyłącz ją do tej samej sieci wirtualnej, która zawiera Twój klaster usługi HDInsight. Aby uzyskać szczegółowe informacje, zobacz [How to create virtual machines (Jak utworzyć maszyny wirtualne)](../virtual-network/quick-create-portal.md#create-virtual-machines). Dołącz je do usługi Azure Virtual Network. 
4. Na maszynę wirtualną platformy Azure pobierz [środowisko Integration Runtime (Self-hosted)](https://www.microsoft.com/download/details.aspx?id=39717). Użyj klucza uwierzytelniania uzyskanego w poprzednim kroku, aby ręcznie zarejestrować środowisko Integration Runtime (Self-hosted). 

   ![Rejestrowanie środowiska Integration Runtime](media/tutorial-transform-data-using-hive-in-vnet/register-integration-runtime.png)

   Gdy środowisko Integration Runtime (Self-hosted) zostanie pomyślnie zarejestrowane, zostanie wyświetlony następujący komunikat: ![Pomyślnie zarejestrowane](media/tutorial-transform-data-using-hive-in-vnet/registered-successfully.png)

   Gdy węzeł zostanie połączony z usługą w chmurze, zostanie wyświetlona następująca strona: ![Węzeł jest połączony](media/tutorial-transform-data-using-hive-in-vnet/node-is-connected.png)

## <a name="author-linked-services"></a>Redagowanie połączonych usług

W tej sekcji zredagujesz i wdrożysz dwie połączone usługi:
- Połączoną usługę Azure Storage, która łączy konto usługi Azure Storage z fabryką danych. Ten magazyn jest podstawowym magazynem używanym przez Twój klaster usługi HDInsight. W takim przypadku użyjemy konta usługi Azure Storage również do przechowywania skryptu programu Hive i przekazywania skryptu na wyjście.
- Połączoną usługę Azure HDInsight. Usługa Azure Data Factory przesyła skrypt programu Hive do tego klastra usługi HDInsight w celu wykonania.

### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage

Utwórz plik w formacie JSON za pomocą preferowanego edytora, skopiuj poniższą definicję formatu JSON dotyczącą połączonej usługi Azure Storage, a następnie zapisz plik jako **MyStorageLinkedService.json**.

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>"
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }  
    }
}
```

Zastąp wartości **&lt; AccountName &gt; i &lt; accountkey &gt;** nazwą i kluczem konta usługi Azure Storage.

### <a name="hdinsight-linked-service"></a>Połączona usługa HDInsight

Utwórz plik w formacie JSON za pomocą preferowanego edytora, skopiuj poniższą definicję formatu JSON dotyczącą połączonej usługi Azure HDIsight, a następnie zapisz plik jako **MyHDInsightLinkedService.json**.

```
{
  "name": "MyHDInsightLinkedService",
  "properties": {     
      "type": "HDInsight",
      "typeProperties": {
          "clusterUri": "https://<clustername>.azurehdinsight.net",
          "userName": "<username>",
          "password": {
            "value": "<password>",
            "type": "SecureString"
          },
          "linkedServiceName": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }
  }
}
```

Zaktualizuj wartości następujących właściwości w definicji połączonej usługi:

- **Nazwa użytkownika**. Nazwa użytkownika logowania do klastra określona podczas tworzenia klastra. 
- **hasło**. Hasło dla użytkownika.
- **clusterUri**. Określ adres URL klastra usługi HDInsight w następującym formacie: `https://<clustername>.azurehdinsight.net` .  W tym artykule przyjęto założenie, że masz dostęp do klastra za pośrednictwem Internetu. Na przykład możesz połączyć się z klastrem pod adresem `https://clustername.azurehdinsight.net`. Ten adres używa publicznej bramy, która jest niedostępna w przypadku używania sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika (UDR) do ograniczania dostępu z Internetu. Aby fabryka danych mogła przekazywać zadania do klastrów usługi HDInsight w usłudze Azure Virtual Network, należy skonfigurować usługę Azure Virtual Network w taki sposób, aby adres URL mógł zostać rozpoznany jako prywatny adres IP bramy używanej przez usługę HDInsight.

  1. W witrynie Azure Portal otwórz sieć wirtualną, w której znajduje się usługa HDInsight. Otwórz interfejs sieciowy mający nazwę zaczynającą się od `nic-gateway-0`. Zanotuj jego prywatny adres IP. Na przykład 10.6.0.15. 
  2. Jeśli usługa Azure Virtual Network ma serwer usługi DNS, zaktualizuj rekord usługi DNS tak, aby adres URL klastra usługi HDInsight `https://<clustername>.azurehdinsight.net` można było rozpoznać jako `10.6.0.15`. Jest to zalecane podejście. Jeśli w usłudze Azure Virtual Network nie masz serwera DNS, możesz tymczasowo obejść to, edytując plik hosts (C:\Windows\System32\drivers\etc) wszystkich maszyn wirtualnych, które są zarejestrowane jako węzły środowiska Integration Runtime (Self-hosted) przez dodanie wpisu podobnego do tego: 
  
        `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-linked-services"></a>Tworzenie połączonych usług
W programie PowerShell przejdź do folderu, w którym zostały utworzone pliki w formacie JSON, a następnie uruchom następujące polecenie, aby wdrożyć połączone usługi: 

1. W programie PowerShell przejdź do folderu, w którym utworzono pliki w formacie JSON.
2. Uruchom poniższe polecenie, aby utworzyć połączoną usługę Azure Storage. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
3. Uruchom poniższe polecenie, aby utworzyć połączoną usługę Azure HDInsight. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyHDInsightLinkedService" -File "MyHDInsightLinkedService.json"
    ```

## <a name="author-a-pipeline"></a>Redagowanie potoku
W tym kroku utworzysz nowy potok za pomocą działania programu Hive. Działanie wykonuje skrypt programu Hive służący do zwracania danych z przykładowej tabeli i zapisania ich w ramach ścieżki zdefiniowanej przez użytkownika. Utwórz plik w formacie JSON za pomocą preferowanego edytora, skopiuj poniższą definicję formatu JSON dotyczącą definicji potoku, a następnie zapisz go jako **MyHivePipeline.json**.


```json
{
  "name": "MyHivePipeline",
  "properties": {
    "activities": [
      {
        "name": "MyHiveActivity",
        "type": "HDInsightHive",
        "linkedServiceName": {
            "referenceName": "MyHDILinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "scriptPath": "adftutorial\\hivescripts\\hivescript.hql",
          "getDebugInfo": "Failure",
          "defines": {           
            "Output": "wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/"
          },
          "scriptLinkedService": {
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

- Argument **scriptPath** wskazuje ścieżkę do skryptu programu Hive na koncie Azure Storage użytym dla usługi MyStorageLinkedService. W ścieżce jest rozróżniana wielkość liter.
- **Output** jest argumentem używanym w skrypcie programu Hive. Użyj formatu `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`, aby wskazać istniejący folder w usłudze Azure Storage. W ścieżce jest rozróżniana wielkość liter. 

Przejdź do folderu, w którym zostały utworzone pliki w formacie JSON, a następnie uruchom następujące polecenie, aby wdrożyć potok: 


```powershell
Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MyHivePipeline.json"
```

## <a name="start-the-pipeline"></a>Uruchamianie potoku 

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

   Oto dane wyjściowe przykładowego uruchomienia:

    ```json
    Pipeline run status: In Progress
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 000000000-0000-0000-000000000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            :
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    :
    DurationInMs      :
    Status            : InProgress
    Error             :
    
    Pipeline ' MyHivePipeline' run finished. Result:
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 0000000-0000-0000-0000-000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            : {logLocation, clusterInUse, jobId, ExecutionProgress...}
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    : 9/18/2017 6:59:16 AM
    DurationInMs      : 63636
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "logLocation": "wasbs://adfjobs@adfv2samplestor.blob.core.windows.net/HiveQueryJobs/000000000-0000-47c3-9b28-1cdc7f3f2ba2/18_09_2017_06_58_18_023/Status"
    "clusterInUse": "https://adfv2HivePrivate.azurehdinsight.net"
    "jobId": "job_1505387997356_0024"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "MySelfhostedIR"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MyHiveActivity"
    ```
4. Sprawdź folder `outputfolder` dla nowego pliku utworzonego w wyniku zapytania programu Hive, powinien on wyglądać podobnie do następujących przykładowych danych wyjściowych: 

   ```
   8 en-US SCH-i500 California
   23 en-US Incredible Pennsylvania
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   246 en-US SCH-i500 District Of Columbia
   246 en-US SCH-i500 District Of Columbia
   ```

## <a name="next-steps"></a>Następne kroki
W ramach tego samouczka wykonano następujące procedury: 

> [!div class="checklist"]
> * Tworzenie fabryki danych. 
> * Redagowanie i konfigurowanie własnego środowiska Integration Runtime
> * Tworzenie i wdrażanie połączonych usług
> * Redagowanie i wdrażanie potoku zawierającego działanie programu Hive
> * Uruchom potok.
> * Monitorowanie działania potoku 
> * Sprawdzanie danych wyjściowych 

Przejdź do następującego samouczka, aby dowiedzieć się więcej o przekształcaniu danych za pomocą klastra Spark na platformie Azure:

> [!div class="nextstepaction"]
>[Branching and chaining Data Factory control flow (Rozgałęzianie i tworzenie łańcucha przepływu sterowania fabryki danych)](tutorial-control-flow.md)