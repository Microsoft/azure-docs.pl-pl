---
title: Uruchamianie pakietu SSIS z działaniem procedury składowanej — Azure
description: W tym artykule opisano sposób uruchamiania pakietu usług integracyjnych programu SQL Server (SSIS) w potoku usługi Azure Data Factory przy użyciu działania procedury składowanej.
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
ms.date: 04/17/2018
ms.author: sawinark
ms.openlocfilehash: 95214d797afcef97d974eeba2b4aea941009e1f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413132"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Uruchamianie pakietu SSIS za pomocą działania Procedura składowana w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób uruchamiania pakietu SSIS w potoku usługi Azure Data Factory przy użyciu działania procedury składowanej. 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-sql-database"></a>Azure SQL Database 
W przewodniku w tym artykule używa bazy danych SQL platformy Azure, która obsługuje wykaz SSIS. Można również użyć wystąpienia zarządzanego bazy danych SQL platformy Azure.

## <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime
Utwórz środowisko uruchomieniowe integracji platformy Azure-SSIS, jeśli go nie masz, postępując zgodnie z instrukcją krok po kroku w [samouczku: Wdrażanie pakietów SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Interfejs użytkownika fabryki danych (witryna Azure portal)
W tej sekcji służy data factory interfejsu użytkownika do utworzenia potoku fabryki danych z działania procedury składowanej, która wywołuje pakiet SSIS.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Pierwszym krokiem jest utworzenie fabryki danych przy użyciu witryny Azure portal. 

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. Przejdź do [witryny Azure portal](https://portal.azure.com). 
3. Kliknij przycisk **Nowy** w lewym menu, kliknij pozycję **Dane + analiza**, a następnie kliknij pozycję **Data Factory**. 
   
   ![Nowy->Fabryka danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na stronie **Nowa fabryka danych** wprowadź wartość **ADFTutorialDataFactory** w polu **Nazwa**. 
      
     ![Strona Nowa fabryka danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Nazwa fabryki danych platformy Azure musi być **unikatowa globalnie.** Jeśli dla pola nazwy wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaADFTutorialDataFactory). Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
     ![Nazwa nie jest dostępna — błąd](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
4. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
   - Wybierz **pozycję Utwórz nowy**i wprowadź nazwę grupy zasobów.   
         
     Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
4. Wybierz opcję **V2** w obszarze **Wersja**.
5. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko lokalizacje obsługiwane przez usługę Data Factory. Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych lokalizacjach.
6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
7. Kliknij przycisk **Utwórz**.
8. Na pulpicie nawigacyjnym jest widoczny następujący kafelek o stanie: **Wdrażanie fabryki danych**. 

     ![kafelek Wdrażanie fabryki danych](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
     ![Strona główna fabryki danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tworzenie potoku ze składowaną procedurą
W tym kroku służy do tworzenia interfejsu użytkownika fabryki danych, aby utworzyć potok. Dodaj działanie procedury składowanej do potoku i skonfigurować go do uruchamiania pakietu SSIS przy użyciu sp_executesql procedura składowana. 

1. Na stronie Wprowadzenie kliknij pozycję **Utwórz potok:** 

    ![Strona Wprowadzenie](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. W **przyborniku Działania** rozwiń rozwiń **Ogólne**i przeciągnij i upuść **procedura przechowywana** na powierzchni projektanta potoku. 

    ![Ćwiczenie procedury składowanej przeciągania i upuszczania](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. W oknie właściwości działania procedury składowanej przełącz się na kartę **Konto SQL** i kliknij przycisk **+ Nowy**. Utworzysz połączenie z bazą danych SQL platformy Azure, która obsługuje wykaz SSIS (baza danych SSIDB). 
   
    ![Przycisk Nowa połączona usługa](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. W oknie **Nowa połączona usługa** wykonaj następujące czynności: 

    1. Wybierz **usługę Azure SQL Database** dla **typu**.
    2. Wybierz **domyślny** środowiska uruchomieniowego integracji platformy Azure, aby połączyć się z bazą danych SQL platformy Azure, która obsługuje bazę `SSISDB` danych.
    3. Wybierz usługę Azure SQL Database, która obsługuje bazę danych SSISDB dla pola **nazwa serwera.**
    4. Wybierz **SSISDB** dla **nazwy bazy danych**.
    5. W przypadku **nazwy użytkownika**wprowadź nazwę użytkownika, który ma dostęp do bazy danych.
    6. W przypadku **hasła**wprowadź hasło użytkownika. 
    7. Przetestuj połączenie z bazą danych, klikając przycisk **Testuj połączenie.**
    8. Zapisz połączony serwis, klikając przycisk **Zapisz.** 

        ![Połączona usługa Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. W oknie właściwości przełącz się na kartę **Procedura przechowywana** na karcie **Konto SQL** i wykonaj następujące czynności: 

    1. Wybierz pozycję **Edit** (Edytuj). 
    2. W polu **Nazwa procedury składowanej** należy wprowadzić `sp_executesql`. 
    3. Kliknij **przycisk + Nowy** w sekcji Parametry **procedury składowanej.** 
    4. Aby uzyskać **nazwę** parametru, wprowadź **stmt**. 
    5. Dla **typu** parametru wprowadź **ciąg**. 
    6. Dla **wartości** parametru wprowadź następującą kwerendę SQL:

        W kwerendzie SQL określ odpowiednie wartości parametrów **folder_name** **, project_name**i **package_name.** 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Połączona usługa Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Aby sprawdzić poprawność konfiguracji potoku, kliknij przycisk **Sprawdź poprawność** na pasku narzędzi. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>**.

    ![Weryfikowanie potoku](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Opublikuj potok w fabryce danych, klikając przycisk **Opublikuj wszystko.** 

    ![Publikowanie](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Uruchamianie i monitorowanie potoku
W tej sekcji wyzwolić uruchomienie potoku, a następnie monitorować go. 

1. Aby wyzwolić uruchomienie potoku, kliknij przycisk **Wyzwalaj** na pasku narzędzi, a teraz kliknij przycisk **Wyzwalaj**. 

    ![Wyzwól teraz](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. W oknie **Uruchomienie potoku** wybierz pozycję **Zakończ**. 
3. Przejdź do karty **Monitorowanie** po lewej stronie. Widać uruchomić potoku i jego stan wraz z innymi informacjami (takimi jak czas rozpoczęcia wykonywania). Aby odświeżyć widok, kliknij przycisk **Odśwież**.

    ![Uruchomienia potoków](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Kliknij link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Widzisz tylko jedno działanie uruchomione, ponieważ potok ma tylko jedno działanie (działanie procedury składowanej).

    ![Uruchomienia działania](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Można uruchomić następującą **kwerendę** względem bazy danych SSISDB na serwerze SQL platformy Azure, aby sprawdzić, czy pakiet został wykonany. 

    ```sql
    select * from catalog.executions
    ```

    ![Weryfikowanie wykonań pakietów](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Można również utworzyć zaplanowany wyzwalacz dla potoku, tak aby potok był uruchamiany zgodnie z harmonogramem (co godzinę, codziennie itp.). Na przykład zobacz [Tworzenie fabryki danych — interfejs użytkownika fabryki danych](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tej sekcji używasz programu Azure PowerShell do utworzenia potoku fabryki danych ze składowaną procedurą, która wywołuje pakiet SSIS. 

Zainstaluj najnowsze moduły programu Azure PowerShell, postępując zgodnie z instrukcjami w [temacie Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Można użyć tej samej fabryki danych, która ma Azure-SSIS IR lub utworzyć oddzielną fabrykę danych. Poniższa procedura zawiera kroki, aby utworzyć fabrykę danych. Tworzenie potoku z działaniem procedury składowanej w tej fabryce danych. Działanie procedury składowanej wykonuje procedurę składowaną w bazie danych SSISDB, aby uruchomić pakiet SSIS. 

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

5. Aby utworzyć fabrykę danych, uruchom następujące polecenie cmdlet **Set-AzDataFactoryV2** przy użyciu właściwości Lokalizacja i ResourceGroupName ze zmiennej $ResGrp: 
    
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
Utwórz połączną usługę, aby połączyć bazę danych SQL platformy Azure, która obsługuje katalog SSIS z fabryką danych. Usługa Data Factory używa informacji w tej połączonej usłudze do łączenia się z bazą danych SSISDB i wykonuje procedurę składowaną w celu uruchomienia pakietu SSIS. 

1. Utwórz plik JSON o nazwie **AzureSqlDatabaseLinkedService.json** w folderze **C:\ADF\RunSSISPackage** z następującą zawartością: 

    > [!IMPORTANT]
    > Przed &lt;zapisaniem&gt; &lt;pliku&gt;zamień nazwę serwera, nazwę użytkownika i &lt;hasło&gt; na wartości bazy danych SQL Azure.

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

2. W **programie Azure PowerShell**przełącz się do folderu **C:\ADF\RunSSISPackage.**

3. Uruchom polecenie cmdlet **Set-AzDataFactoryV2LinkedService** w celu utworzenia połączonej usługi: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tworzenie potoku ze składowaną procedurą 
W tym kroku utworzysz potok ze składowaną procedurą działania. Działanie wywołuje sp_executesql procedura składowana do uruchomienia pakietu SSIS. 

1. Utwórz plik JSON o nazwie **RunSSISPackagePipeline.json** w folderze **C:\ADF\RunSSISPackage** z następującą zawartością:

    > [!IMPORTANT]
    > Przed &lt;zapisaniem pliku zamień nazwę&gt; &lt;folderu, nazwę&gt;projektu, &lt;nazwę&gt; pakietu na nazwy folderów, projektów i pakietów w katalogu SSIS. 

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

2. Aby utworzyć potok: **RunSSISPackagePipeline**, Uruchom polecenie cmdlet **Set-AzDataFactoryV2Pipeline.**

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
Użyj polecenia cmdlet **Invoke-AzDataFactoryV2Pipeline** do uruchomienia potoku. Polecenie cmdlet zwraca identyfikator uruchomienia potoku w celu monitorowania w przyszłości.

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
W poprzednim kroku wywoływano potok na żądanie. Można również utworzyć wyzwalacz harmonogramu, aby uruchomić potok zgodnie z harmonogramem (co godzinę, codziennie itp.).

1. Utwórz plik JSON o nazwie **MyTrigger.json** w folderze **C:\ADF\RunSSISPackage** z następującą zawartością: 

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
2. W **programie Azure PowerShell**przełącz się do folderu **C:\ADF\RunSSISPackage.**
3. Uruchom polecenie cmdlet **Set-AzDataFactoryV2Trigger,** które tworzy wyzwalacz. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Domyślnie wyzwalacz jest w stanie zatrzymania. Uruchom wyzwalacz, uruchamiając polecenie cmdlet **Start-AzDataFactoryV2Trigger.** 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Upewnij się, że wyzwalacz jest uruchamiany przez uruchomienie polecenia cmdlet **Get-AzDataFactoryV2Trigger.** 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Uruchom następujące polecenie po następnej godzinie. Na przykład jeśli bieżąca godzina wynosi 15:25 CZASU UTC, uruchom polecenie o godzinie 16:00 CZASU UTC. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Można uruchomić następującą kwerendę względem bazy danych SSISDB na serwerze SQL platformy Azure, aby sprawdzić, czy pakiet został wykonany. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Następne kroki
Potoku można również monitorować za pomocą witryny Azure portal. Aby uzyskać instrukcje krok po kroku, zobacz [Monitorowanie potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
