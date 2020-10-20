---
title: Uruchamianie pakietu SSIS za pomocą działania procedury składowanej — Azure
description: W tym artykule opisano sposób uruchamiania pakietu SQL Server Integration Services (SSIS) w potoku Azure Data Factory za pomocą działania procedury składowanej.
services: data-factory
documentationcenter: ''
author: swinarko
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b2bad99a836da7aab64e8cc0d454423fd56ffce1
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217800"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Uruchamianie pakietu SSIS za pomocą działania Procedura składowana w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób uruchamiania pakietu usług SSIS w potoku Azure Data Factory przy użyciu działania procedury składowanej. 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-sql-database"></a>Azure SQL Database 
W przewodniku w tym artykule używa się Azure SQL Database do hostowania wykazu usług SSIS. Można również użyć wystąpienia zarządzanego Azure SQL.

## <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime
Utwórz środowisko Azure-SSIS Integration Runtime, jeśli go nie masz, postępując zgodnie z instrukcjami krok po kroku w [samouczku: Wdrażanie pakietów usług SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Interfejs użytkownika Data Factory (Azure Portal)
W tej sekcji użyto Data Factory interfejsu użytkownika do utworzenia potoku Data Factory za pomocą działania procedury składowanej, które wywołuje pakiet SSIS.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Pierwszym krokiem jest utworzenie fabryki danych przy użyciu Azure Portal. 

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. Przejdź do [Azure Portal](https://portal.azure.com). 
3. Kliknij przycisk **Nowy** w lewym menu, kliknij pozycję **Dane + analiza**, a następnie kliknij pozycję **Data Factory**. 
   
   ![Nowy->Fabryka danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na stronie **Nowa fabryka danych** wprowadź wartość **ADFTutorialDataFactory** w polu **Nazwa**. 
      
     ![Strona Nowa fabryka danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Nazwa fabryki danych Azure musi być **globalnie unikatowa**. Jeśli dla pola nazwy wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory). Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
     ![Nazwa nie jest dostępna — błąd](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
4. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
     Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
4. Wybierz opcję **V2** w obszarze **Wersja**.
5. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko lokalizacje obsługiwane przez usługę Data Factory. Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych lokalizacjach.
6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
7. Kliknij pozycję **Utwórz**.
8. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **wdrażanie fabryki danych**. 

     ![kafelek Wdrażanie fabryki danych](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
     ![Strona główna fabryki danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tworzenie potoku za pomocą działania procedury składowanej
W tym kroku użyjesz interfejsu użytkownika Data Factory do utworzenia potoku. Należy dodać działanie procedury składowanej do potoku i skonfigurować go tak, aby uruchamiał pakiet usług SSIS przy użyciu sp_executesql procedury składowanej. 

1. Na stronie Wprowadzenie kliknij pozycję **Utwórz potok**: 

    ![Strona Wprowadzenie](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. W przyborniku **działania** rozwiń węzeł **Ogólne**, a następnie przeciągnij i upuść działanie **procedury składowanej** do powierzchni projektanta potoku. 

    ![Działanie procedury składowanej przeciągnij i upuść](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. W oknie właściwości działania procedury składowanej przejdź do karty **konto SQL** , a następnie kliknij pozycję **+ Nowy**. Tworzysz połączenie z bazą danych w Azure SQL Database, która hostuje wykaz usług SSIS (SSIDB Database). 
   
    ![Przycisk Nowa połączona usługa](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. W oknie **Nowa połączona usługa** wykonaj następujące czynności: 

    1. Wybierz **Azure SQL Database** dla **typu**.
    2. Wybierz **domyślną** Azure Integration Runtime, aby nawiązać połączenie z Azure SQL Database, który hostuje `SSISDB` bazę danych.
    3. W polu **Nazwa serwera** wybierz Azure SQL Database, który hostuje bazę danych SSISDB.
    4. Wybierz pozycję **SSISDB** dla **nazwy bazy danych**.
    5. W polu **Nazwa użytkownika**wprowadź nazwę użytkownika, który ma dostęp do bazy danych.
    6. W polu **hasło**wprowadź hasło użytkownika. 
    7. Przetestuj połączenie z bazą danych, klikając przycisk **Test connection** .
    8. Zapisz połączoną usługę, klikając przycisk **Zapisz** . 

        ![Zrzut ekranu pokazujący proces dodawania nowej połączonej usługi.](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. W oknie właściwości przejdź do karty **procedura składowana** na karcie **konto SQL** i wykonaj następujące czynności: 

    1. Wybierz pozycję **Edytuj**. 
    2. W polu **nazwa procedury składowanej** wprowadź wartość `sp_executesql` . 
    3. Kliknij pozycję **+ Nowy** w sekcji **parametry procedury składowanej** . 
    4. W polu **Nazwa** parametru wprowadź **stmt**. 
    5. Dla **typu** parametru wprowadź **ciąg**. 
    6. W polu **wartość** parametru wprowadź następujące zapytanie SQL:

        W zapytaniu SQL określ odpowiednie wartości parametrów **folder_name**, **Project_Name**i **package_name** . 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Połączona usługa Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Aby sprawdzić poprawność konfiguracji potoku, kliknij pozycję **Weryfikuj** na pasku narzędzi. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>**.

    ![Weryfikowanie potoku](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Opublikuj potok w Data Factory, klikając przycisk **Opublikuj wszystko** . 

    ![Opublikuj](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Uruchamianie i monitorowanie potoku
W tej sekcji zostanie wyzwolone uruchomienie potoku, a następnie jego monitorowanie. 

1. Aby wyzwolić uruchomienie potoku, kliknij pozycję **Wyzwól** na pasku narzędzi, a następnie kliknij pozycję **Wyzwól teraz**. 

    ![Wyzwól teraz](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. W oknie **Uruchomienie potoku** wybierz pozycję **Zakończ**. 
3. Przejdź do karty **Monitorowanie** po lewej stronie. Zobaczysz uruchomienie potoku i jego stan wraz z innymi informacjami (takimi jak godzina rozpoczęcia uruchamiania). Aby odświeżyć widok, kliknij przycisk **Odśwież**.

    ![Uruchomienia potoków](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Kliknij link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Zobaczysz tylko jedno uruchomienie działania, ponieważ potok ma tylko jedno działanie (działanie procedury składowanej).

    ![Uruchomienia działania](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Można uruchomić następujące **zapytanie** względem bazy danych SSISDB w SQL Database, aby sprawdzić, czy pakiet został uruchomiony. 

    ```sql
    select * from catalog.executions
    ```

    ![Weryfikuj wykonania pakietu](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Możesz również utworzyć zaplanowany wyzwalacz dla potoku, aby potok działał zgodnie z harmonogramem (co godzinę, codziennie itd.). Aby zapoznać się z przykładem, zobacz temat [Tworzenie fabryki danych — Data Factory interfejsu użytkownika](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tej sekcji użyjesz Azure PowerShell, aby utworzyć potok Data Factory za pomocą działania procedury składowanej, które wywołuje pakiet SSIS. 

Zainstaluj najnowsze moduły Azure PowerShell, postępując zgodnie z instrukcjami w temacie [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Możesz użyć tej samej fabryki danych, która ma Azure-SSIS IR, lub utworzyć oddzielną fabrykę danych. Poniższa procedura zawiera procedurę tworzenia fabryki danych. Tworzysz potok za pomocą działania procedury składowanej w tej fabryce danych. Działanie procedury składowanej wykonuje procedurę przechowywaną w bazie danych SSISDB w celu uruchomienia pakietu usług SSIS. 

1. Zdefiniuj zmienną nazwy grupy zasobów, której użyjesz później w poleceniach programu PowerShell. Skopiuj poniższy tekst polecenia do programu PowerShell, podaj nazwę [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md) w podwójnych cudzysłowach, a następnie uruchom polecenie. Na przykład: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$ResourceGroupName` i ponownie uruchom polecenie.
2. Aby utworzyć grupę zasobów platformy Azure, uruchom następujące polecenie: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$ResourceGroupName` i ponownie uruchom polecenie. 
3. Zdefiniuj zmienną nazwy fabryki danych. 

    > [!IMPORTANT]
    >  Zaktualizuj nazwę fabryki danych, aby była unikatowa w skali globalnej, 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Aby utworzyć fabrykę danych, uruchom następujące polecenie cmdlet **Set-AzDataFactoryV2** , używając właściwości Location i ResourceGroupName ze zmiennej $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli **współautora** lub **właściciela** albo **administratorem** subskrypcji platformy Azure.
* Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.

### <a name="create-an-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi Azure SQL Database
Utwórz połączoną usługę, aby połączyć bazę danych, która hostuje katalog usług SSIS w fabryce danych. Data Factory używa informacji w tej połączonej usłudze, aby nawiązać połączenie z bazą danych SSISDB i wykonać procedurę przechowywaną w celu uruchomienia pakietu usług SSIS. 

1. Utwórz plik JSON o nazwie **AzureSqlDatabaseLinkedService.js** w folderze **C:\ADF\RunSSISPackage** o następującej zawartości: 

    > [!IMPORTANT]
    > Zastąp &lt; &gt; wartości ServerName, &lt; username &gt; i &lt; Password &gt; wartościami Azure SQL Database przed zapisaniem pliku.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. W **Azure PowerShell**przejdź do folderu **C:\ADF\RunSSISPackage** .

3. Uruchom polecenie cmdlet **Set-AzDataFactoryV2LinkedService** , aby utworzyć połączoną usługę: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tworzenie potoku za pomocą działania procedury składowanej 
W tym kroku utworzysz potok z działaniem procedury składowanej. Działanie wywołuje sp_executesql procedury składowanej w celu uruchomienia pakietu usług SSIS. 

1. Utwórz plik JSON o nazwie **RunSSISPackagePipeline.js** w folderze **C:\ADF\RunSSISPackage** o następującej zawartości:

    > [!IMPORTANT]
    > &lt; &gt; Przed zapisaniem pliku Zastąp wartość Nazwa folderu, &lt; Nazwa projektu &gt; , &lt; Nazwa pakietu &gt; z nazwami folderu, projektu i pakietu w wykazie usług SSIS. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. Aby utworzyć potok: **RunSSISPackagePipeline**, uruchom polecenie cmdlet **Set-AzDataFactoryV2Pipeline** .

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Tworzenie uruchomienia potoku
Użyj polecenia cmdlet **Invoke-AzDataFactoryV2Pipeline** , aby uruchomić potok. Polecenie cmdlet zwraca identyfikator uruchomienia potoku w celu monitorowania w przyszłości.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

Uruchom następujący skrypt programu PowerShell, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych. Skopiuj/wklej poniższy skrypt w oknie programu PowerShell i naciśnij klawisz ENTER. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>Tworzenie wyzwalacza
W poprzednim kroku został wywołany potok na żądanie. Możesz również utworzyć wyzwalacz harmonogramu, aby uruchomić potok zgodnie z harmonogramem (co godzinę, codziennie itd.).

1. Utwórz plik JSON o nazwie **MyTrigger.js** w folderze **C:\ADF\RunSSISPackage** o następującej zawartości: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. W **Azure PowerShell**przejdź do folderu **C:\ADF\RunSSISPackage** .
3. Uruchom polecenie cmdlet **Set-AzDataFactoryV2Trigger** , które tworzy wyzwalacz. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Domyślnie wyzwalacz jest w stanie zatrzymanym. Uruchom wyzwalacz, uruchamiając polecenie cmdlet **Start-AzDataFactoryV2Trigger** . 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Upewnij się, że wyzwalacz został uruchomiony, uruchamiając polecenie cmdlet **Get-AzDataFactoryV2Trigger** . 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Uruchom następujące polecenie po następnej godzinie. Na przykład, jeśli bieżący czas to 3:25 PM UTC, uruchom polecenie o godzinie 4 PM (UTC). 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Można uruchomić następujące zapytanie względem bazy danych SSISDB w SQL Database, aby sprawdzić, czy pakiet został uruchomiony. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Następne kroki
Możesz również monitorować potok przy użyciu Azure Portal. Instrukcje krok po kroku znajdują się w sekcji [monitorowanie potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
