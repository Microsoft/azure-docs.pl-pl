---
title: Ciągła integracja i dostarczanie w fabryce danych platformy Azure
description: Dowiedz się, jak używać ciągłej integracji i dostarczania do przenoszenia potoków fabryki danych z jednego środowiska (deweloperskiego, testowego, produkcyjnego) do innego.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 8ce954e956da62d645e9b1e852ce7a7318c85791
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415366"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Ciągła integracja i dostarczanie w fabryce danych platformy Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Omówienie

Ciągła integracja jest praktyką testowania każdej zmiany wprowadzonej do bazy kodu automatycznie i tak wcześnie, jak to możliwe.Ciągłe dostarczanie następuje po testach, które mają miejsce podczas ciągłej integracji i wypycha zmiany do systemu przejściowego lub produkcyjnego.

W usłudze Azure Data Factory ciągła integracja i dostarczanie (CI/CD) oznacza przenoszenie potoków fabryki danych z jednego środowiska (programistycznego, testowego, produkcyjnego) do innego. Do wykonania ciągłej integracji/dysku CD usługi Data Factory UX z szablonami usługi Azure Resource Manager można użyć.

W ux fabryki danych można wygenerować szablon Menedżera zasobów z menu rozwijanego **Szablon ARM.** Po **wybraniu opcji Eksportuj szablon ARM**portal generuje szablon Menedżera zasobów dla fabryki danych oraz plik konfiguracyjny zawierający wszystkie parametry połączenia i inne parametry. Następnie należy utworzyć jeden plik konfiguracyjny dla każdego środowiska (rozwój, test, produkcja). Główny plik szablonu Menedżera zasobów pozostaje taki sam dla wszystkich środowisk.

Aby zapoznać się z dziewięciominutowym wprowadzeniem do tej funkcji i prezentacją, obejrzyj ten film:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Cykl życia ciągłej integracji/ciągłego wdrażania

Poniżej przedstawiono przykładowe omówienie cyklu życia ciągłej integracji/ciągłego wdrażania w fabryce danych platformy Azure skonfigurowanej przy usłudze Azure Repos Git. Aby uzyskać więcej informacji na temat konfigurowania repozytorium Git, zobacz [Formant źródła w usłudze Azure Data Factory](source-control.md).

1.  Fabryka danych deweloperskich jest tworzona i konfigurowana za pomocą usługi Azure Repos Git. Wszyscy deweloperzy powinni mieć uprawnienia do tworzenia zasobów usługi Data Factory, takich jak potoki i zestawy danych.

1.  Jak deweloperzy wprowadzać zmiany w swoich gałęziach funkcji, debugują ich potoku działa z ich najnowszych zmian. Aby uzyskać więcej informacji na temat debugowania przebiegu [potoku, zobacz Iteracyjne opracowywanie i debugowanie za pomocą usługi Azure Data Factory](iterative-development-debugging.md).

1.  Gdy deweloperzy są zadowoleni ze swoich zmian, tworzą żądanie ściągnięcia z gałęzi funkcji do gałęzi głównej lub współpracy, aby uzyskać ich zmiany przeglądane przez elementy równorzędne.

1.  Po zatwierdzeniu żądania ściągnięcia i scaleniu zmian w gałęzi głównej zmiany mogą zostać opublikowane w fabryce deweloperów.

1.  Gdy zespół jest gotowy do wdrożenia zmian w fabryce testowej, a następnie do fabryki produkcyjnej, zespół eksportuje szablon Menedżera zasobów z gałęzi głównej.

1.  Wyeksportowany szablon Menedżera zasobów jest wdrażany z różnymi plikami parametrów w fabryce testowej i fabryce produkcji.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Tworzenie szablonu Menedżera zasobów dla każdego środowiska

1. Na liście **Szablon ARM** wybierz **pozycję Eksportuj szablon ARM,** aby wyeksportować szablon Menedżera zasobów dla fabryki danych w środowisku programistycznym.

   ![Eksportowanie szablonu Menedżera zasobów](media/continuous-integration-deployment/continuous-integration-image1.png)

1. W fabrykach danych testowych i produkcyjnych wybierz pozycję **Importuj szablon ARM**. Ta akcja zostanie przesuń do witryny Azure Portal, gdzie można zaimportować wyeksportowany szablon. Wybierz **pozycję Zbuduj własny szablon w edytorze,** aby otworzyć edytor szablonów Menedżera zasobów.

   ![Tworzenie własnego szablonu](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Wybierz **pozycję Załaduj plik**, a następnie wybierz wygenerowany szablon Menedżera zasobów. Jest to plik **arm_template.json** znajdujący się w pliku zip wyeksportowanym w kroku 1.

   ![Edytuj szablon](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. W sekcji ustawienia wprowadź wartości konfiguracji, takie jak połączone poświadczenia usługi. Po zakończeniu wybierz **pozycję Zakup,** aby wdrożyć szablon Menedżera zasobów.

   ![Sekcja Ustawienia](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Parametry połączeń

Aby uzyskać informacje na temat konfigurowania ciągów połączeń, zobacz artykuł łącznika. Na przykład dla usługi Azure SQL Database zobacz [Kopiowanie danych do lub z usługi Azure SQL Database przy użyciu usługi Azure Data Factory](connector-azure-sql-database.md). Aby zweryfikować parametry połączenia, można otworzyć widok kodu zasobu w ux fabryki danych. W widoku kodu hasło lub część klucza konta ciągu połączenia jest usuwana. Aby otworzyć widok kodu, wybierz wyróżnioną tutaj ikonę:

![Otwórz widok kodu, aby wyświetlić parametry połączenia](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatyzacja ciągłej integracji przy użyciu wersji usługi Azure Pipelines

Poniżej przedstawiono przewodnik dotyczący konfigurowania wersji usługi Azure Pipelines, która automatyzuje wdrażanie fabryki danych w wielu środowiskach.

![Diagram ciągłej integracji z potokami platformy Azure](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Wymagania

-   Subskrypcja platformy Azure powiązana z programem Visual Studio Team Foundation Server lub repozytorium platformy Azure korzystającym z [punktu końcowego usługi Usługi Azure Resource Manager.](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)

-   Fabryka danych skonfigurowana z integracją usługi Azure Repos Git.

-    [Magazyn kluczy platformy Azure,](https://azure.microsoft.com/services/key-vault/) który zawiera wpisy tajne dla każdego środowiska.

### <a name="set-up-an-azure-pipelines-release"></a>Konfigurowanie wersji potoków platformy Azure

1.  W [usłudze Azure DevOps](https://dev.azure.com/)otwórz projekt skonfigurowany w fabryce danych.

1.  Po lewej stronie wybierz pozycję **Potoki**, a następnie wybierz pozycję **Zwalnia**.

    ![Wybierz potoki, wydania](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Wybierz **opcję Nowy potok**lub, jeśli masz istniejące potoki, wybierz pozycję **Nowy,** a następnie **nowy potok wydania**.

1.  Wybierz szablon **Puste zadanie.**

    ![Wybierz puste zadanie](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  W polu **Nazwa stołu montażowego** wprowadź nazwę swojego środowiska.

1.  Wybierz **pozycję Dodaj artefakt**, a następnie wybierz repozytorium skonfigurowane w fabryce danych. Wybierz **adf_publish** dla **gałęzi Domyślna**. W przypadku **wersji domyślnej**wybierz opcję **Najnowsze z domyślnej gałęzi**.

    ![Dodawanie artefaktu](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Dodaj zadanie wdrażania usługi Azure Resource Manager:

    a.  W widoku stołu montażowego wybierz pozycję **Wyświetl zadania stołu montażowego**.

    ![Widok etapu](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Utwórz nowe zadanie. Wyszukaj **wdrożenie grupy zasobów platformy Azure,** a następnie wybierz pozycję **Dodaj**.

    d.  W zadaniu Wdrażania wybierz subskrypcję, grupę zasobów i lokalizację dla docelowej fabryki danych. W razie potrzeby podaj poświadczenia.

    d.  Na liście **Akcja** wybierz pozycję **Utwórz lub zaktualizuj grupę zasobów**.

    e.  Wybierz przycisk wielokropka (**...**) obok pola **Szablon.** Wyszukaj szablon Usługi Azure Resource Manager utworzony przy użyciu **szablonu Importuj ARM** w [szablonie Tworzenie Menedżera zasobów dla każdego środowiska](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) w tym artykule. Poszukaj tego <FactoryName> pliku w folderze gałęzi adf_publish.

    f.  Wybierz **...** obok pola **Parametry szablonu,** aby wybrać plik parametrów. Wybranego pliku zależy od tego, czy utworzono kopię, czy używany jest plik domyślny, ARMTemplateParametersForFactory.json.

    g.  Wybierz **...** obok pola **Zastąd w ustawieniach parametrów szablonu** i wprowadź informacje dotyczące docelowej fabryki danych. W przypadku poświadczeń pochodzących z usługi Azure Key Vault wprowadź nazwę klucza tajnego między podwójnymi cudzysłowami. Na przykład, jeśli nazwa klucza tajnego jest cred1, wprowadź **"$(cred1)"** dla tej wartości.

    h. Wybierz **opcję Przyrostowa** dla **trybu wdrażania**.

    > [!WARNING]
    > W przypadku **wybrania opcji Zakończ** dla trybu **wdrażania**istniejące zasoby mogą zostać usunięte, łącznie ze wszystkimi zasobami w docelowej grupie zasobów, które nie są zdefiniowane w szablonie Menedżera zasobów.

    ![Wdrożenie prod fabryki danych](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Zapisz potok wydania.

1. Aby wyzwolić wydanie, wybierz pozycję **Utwórz zwolnienie**.

   ![Wybierz pozycję Utwórz zwolnienie](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> W scenariuszach ciągłej integracji/ciągłego wdrażania (IR) typ środowiska wykonawczego (IR) w różnych środowiskach musi być taki sam. Na przykład jeśli masz samodzielnie hostowane IR w środowisku programistycznym, ten sam podczerwone musi być również typu hostowane samodzielnie w innych środowiskach, takich jak testowanie i produkcja. Podobnie, jeśli udostępniasz środowiska wykonawcze integracji na wielu etapach, musisz skonfigurować środowiska wykonawcze integracji jako połączone hostowane samodzielnie we wszystkich środowiskach, takich jak programowanie, testowanie i produkcja.

### <a name="get-secrets-from-azure-key-vault"></a>Uzyskaj wpisy tajne z usługi Azure Key Vault

Jeśli masz wpisy tajne do przekazania w szablonie usługi Azure Resource Manager, zaleca się użycie usługi Azure Key Vault z wydaniem usługi Azure Potoki.

Istnieją dwa sposoby obsługi wpisów tajnych:

1.  Dodaj wpisy tajne do pliku parametrów. Aby uzyskać więcej informacji, zobacz [Przekazywanie wartości bezpiecznego parametru podczas wdrażania za pomocą usługi Azure Key Vault.](../azure-resource-manager/templates/key-vault-parameter.md)

    Utwórz kopię pliku parametrów, który jest przekazywany do gałęzi publikowania. Ustaw wartości parametrów, które chcesz uzyskać z usługi Key Vault, przy użyciu tego formatu:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Podczas korzystania z tej metody klucz tajny jest pobierany z magazynu kluczy automatycznie.

    Plik parametrów musi być również w gałęzi publikowania.

1. Dodaj [zadanie usługi Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) przed zadaniem wdrażania usługi Azure Resource Manager opisanym w poprzedniej sekcji:

    1.  Na karcie **Zadania** utwórz nowe zadanie. Wyszukaj **usługę Azure Key Vault** i dodaj ją.

    1.  W zadaniu Usługi Przechowalnia kluczy wybierz subskrypcję, w której utworzono magazyn kluczy. W razie potrzeby podaj poświadczenia, a następnie wybierz magazyn kluczy.

    ![Dodawanie zadania magazynu kluczy](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Udziel uprawnień agentowi usługi Azure Pipelines

Zadanie usługi Azure Key Vault może zakończyć się niepowodzeniem z błędem odmowy dostępu, jeśli nie są ustawione poprawne uprawnienia. Pobierz dzienniki dla wydania i znajdź plik .ps1, który zawiera polecenie, aby nadać uprawnienia agentowi usługi Azure Pipelines. Polecenie można uruchomić bezpośrednio. Możesz też skopiować identyfikator podmiotu zabezpieczeń z pliku i ręcznie dodać zasady dostępu w witrynie Azure portal. `Get`i `List` są minimalne wymagane uprawnienia.

### <a name="update-active-triggers"></a>Aktualizowanie aktywnych wyzwalaczy

Wdrożenie może zakończyć się niepowodzeniem, jeśli spróbujesz zaktualizować aktywne wyzwalacze. Aby zaktualizować aktywne wyzwalacze, należy ręcznie je zatrzymać, a następnie ponownie uruchomić je po wdrożeniu. Można to zrobić przy użyciu zadania programu Azure PowerShell:

1.  Na karcie **Zadania** w wersji dodaj zadanie **programu Azure PowerShell.** Wybierz wersję zadania 4.*. 

1.  Wybierz subskrypcję, w.

1.  Wybierz **ścieżkę pliku skryptu** jako typ skryptu. Wymaga to zapisania skryptu programu PowerShell w repozytorium. Do zatrzymania wyzwalaczy można użyć następującego skryptu programu PowerShell:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Podobne kroki (za pomocą `Start-AzDataFactoryV2Trigger` funkcji) można wykonać, aby ponownie uruchomić wyzwalacze po wdrożeniu.

### <a name="sample-pre--and-post-deployment-script"></a>Przykładowy skrypt przed i po wdrożeniu

Poniższy przykładowy skrypt może służyć do zatrzymania wyzwalaczy przed wdrożeniem i ponownego uruchomienia ich później. Skrypt zawiera również kod do usuwania zasobów, które zostały usunięte. Zapisz skrypt w repozytorium git usługi Azure DevOps i odwołaj się do niego za pomocą zadania programu Azure PowerShell w wersji 4.*.

Podczas uruchamiania skryptu przed wdrożeniem należy określić odmianę następujących parametrów w polu **Argumenty skryptu.**

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Podczas uruchamiania skryptu po wdrożeniu należy określić odmianę następujących parametrów w polu **Argumenty skryptu.**

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Zadanie programu Azure PowerShell](media/continuous-integration-deployment/continuous-integration-image11.png)

Oto skrypt, który może być używany do przed- i po wdrożeniu. Uwzględnia usunięte zasoby i odwołania do zasobów.

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        return $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Używanie parametrów niestandardowych w szablonach usługi Resource Manager

Jeśli jesteś w trybie GIT, możesz zastąpić domyślne właściwości w szablonie Menedżera zasobów, aby ustawić właściwości, które są sparametryzowane w szablonie i właściwości, które są zakodowane na komputerze. Można zastąpić domyślny szablon parametryzacji w następujących scenariuszach:

* Używasz zautomatyzowanej ciągłej integracji/ciągłej integracji/ciągłego wdrażania i chcesz zmienić niektóre właściwości podczas wdrażania Menedżera zasobów, ale właściwości nie są parametryzowane domyślnie.
* Fabryka jest tak duża, że domyślny szablon Menedżera zasobów jest nieprawidłowy, ponieważ ma więcej niż maksymalne dozwolone parametry (256).

W tych warunkach, aby zastąpić domyślny szablon parametryzacji, należy utworzyć plik o nazwie **arm-template-parameters-definition.json** w folderze określonym jako folder główny dla integracji git fabryki danych. Należy użyć tej dokładnej nazwy pliku. Usługa Data Factory odczytuje ten plik z dowolnej gałęzi, na której aktualnie korzystasz w portalu usługi Azure Data Factory, a nie tylko z gałęzi współpracy. Można utworzyć lub edytować plik z gałęzi prywatnej, gdzie można przetestować zmiany, wybierając **pozycję Eksportuj szablon ARM** w interfejsie użytkownika. Następnie można scalić plik z gałęzią współpracy. Jeśli nie zostanie znaleziony żaden plik, używany jest szablon domyślny.

> [!NOTE]
> Niestandardowy szablon parametryzacji nie zmienia limitu parametrów szablonu ARM wynoszącego 256. Umożliwia wybór i zmniejszenie liczby właściwości sparametryzowanych.

### <a name="syntax-of-a-custom-parameters-file"></a>Składnia pliku parametrów niestandardowych

Poniżej przedstawiono kilka wskazówek, które należy wykonać podczas tworzenia pliku parametrów niestandardowych. Plik składa się z sekcji dla każdego typu jednostki: wyzwalacz, potok, usługa połączona, zestaw danych, środowisko uruchomieniowe integracji i tak dalej.
* Wprowadź ścieżkę właściwości w obszarze odpowiedniego typu encji.
* Ustawienie nazwy właściwości, aby `*` wskazuje, że chcesz sparametryzować wszystkie właściwości pod nim (tylko do pierwszego poziomu, nie cyklicznie). Można również podać wyjątki od tej konfiguracji.
* Ustawienie wartości właściwości jako ciągu wskazuje, że chcesz sparametryzować właściwość. Użyj formatu `<action>:<name>:<stype>`.
   *  `<action>` może być jedną z następujących postaci:
      * `=` oznacza zachowanie bieżącej wartości jako wartości domyślnej dla parametru.
      * `-` oznacza, że nie zachowaj domyślnej wartości parametru.
      * `|` jest szczególnym przypadkiem dla wpisów tajnych z usługi Azure Key Vault dla ciągów połączeń lub kluczy.
   * `<name>` jest nazwą parametru. Jeśli jest puste, przyjmuje nazwę właściwości. Jeśli wartość zaczyna się `-` od znaku, nazwa jest skrócona. Na przykład, `AzureStorage1_properties_typeProperties_connectionString` zostanie skrócony do `AzureStorage1_connectionString`.
   * `<stype>` jest typem parametru. Jeśli `<stype>` jest pusty, domyślnym typem jest `string`. Obsługiwane wartości: `string` `bool`, `number` `object`, `securestring`, , i .
* Określenie tablicy w pliku definicji wskazuje, że pasująca właściwość w szablonie jest tablicą. Fabryka danych iteruje przez wszystkie obiekty w tablicy przy użyciu definicji, która jest określona w obiekcie środowiska wykonawczego integracji tablicy. Drugi obiekt, ciąg, staje się nazwą właściwości, która jest używana jako nazwa parametru dla każdej iteracji.
* Definicja nie może być specyficzna dla wystąpienia zasobu. Każda definicja ma zastosowanie do wszystkich zasobów tego typu.
* Domyślnie wszystkie bezpieczne ciągi ciągów, takie jak wpisy tajne usługi Key Vault i bezpieczne ciągi, takie jak parametry połączenia, klucze i tokeny, są parametryzowane.
 
### <a name="sample-parameterization-template"></a>Przykładowy szablon parametryzacji

Oto przykład tego, jak może wyglądać szablon parametryzacji:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Oto wyjaśnienie, jak poprzedni szablon jest skonstruowany w podziale według typu zasobu.

#### <a name="pipelines"></a>Potoki
    
* Każda właściwość `activities/typeProperties/waitTimeInSeconds` w ścieżce jest sparametryzowana. Każde działanie w potoku, który ma `waitTimeInSeconds` właściwość na `Wait` poziomie kodu o nazwie (na przykład działanie) jest parametryzowany jako liczba, o nazwie domyślnej. Ale nie będzie miał wartość domyślną w szablonie Menedżera zasobów. Będzie to obowiązkowe dane wejściowe podczas wdrażania Menedżera zasobów.
* Podobnie właściwość wywoływana `headers` (na przykład `Web` w działaniu) `object` jest sparametryzowana za pomocą typu (JObject). Ma wartość domyślną, która jest taką samą wartością jak w fabryce źródła.

#### <a name="integrationruntimes"></a>IntegracjaRuntimes

* Wszystkie właściwości pod `typeProperties` ścieżką są parametryzowane z ich odpowiednimi wartościami domyślnymi. Na przykład w obszarze `IntegrationRuntimes` właściwości typu znajdują `computeProperties` `ssisProperties`się dwie właściwości: i . Oba typy właściwości są tworzone z ich odpowiednich wartości domyślnych i typów (Object).

#### <a name="triggers"></a>Wyzwalacze

* W `typeProperties`obszarze dwie właściwości są sparametryzowane. Pierwszym z `maxConcurrency`nich jest , który jest określony,`string`aby mieć wartość domyślną i jest typu . Ma domyślną nazwę `<entityName>_properties_typeProperties_maxConcurrency`parametru .
* Właściwość `recurrence` jest również sparametryzowany. W obszarze niego wszystkie właściwości na tym poziomie są określone jako parametryzowane jako ciągi, z wartościami domyślnymi i nazwami parametrów. Wyjątkiem jest `interval` właściwość, która jest `number`sparametryzowana jako typ . Nazwa parametru jest sufiksem z . `<entityName>_properties_typeProperties_recurrence_triggerSuffix` Podobnie `freq` właściwość jest ciągiem i jest sparametryzowana jako ciąg. Jednak `freq` właściwość jest parametryzowany bez wartości domyślnej. Nazwa jest skrócona i sufiksem. Na przykład `<entityName>_freq`.

#### <a name="linkedservices"></a>Usługi linkedservices

* Połączone usługi są unikatowe. Ponieważ połączone usługi i zestawy danych mają szeroki zakres typów, można zapewnić dostosowanie specyficzne dla typu. W tym przykładzie dla wszystkich `AzureDataLakeStore`połączonych usług typu zostanie zastosowany określony szablon. Dla wszystkich innych `*`(za pośrednictwem), zostanie zastosowany inny szablon.
* Właściwość `connectionString` zostanie sparametryzowana jako `securestring` wartość. Nie będzie miał wartości domyślnej. Będzie miał skróconą nazwę parametru, która jest `connectionString`sufiksem z .
* Właściwość `secretAccessKey` jest `AzureKeyVaultSecret` (na przykład w usłudze połączonej z Amazon S3). Jest automatycznie sparametryzowany jako klucz tajny usługi Azure Key Vault i pobierany ze skonfigurowanego magazynu kluczy. Można również sparametryzować sam magazyn kluczy.

#### <a name="datasets"></a>Zestawy danych

* Chociaż dostosowanie specyficzne dla typu jest dostępne dla zestawów danych, \*można podać konfigurację bez jawnie o konfiguracji na poziomie . W poprzednim przykładzie wszystkie właściwości zestawu `typeProperties` danych w obszarze są parametryzowane.

### <a name="default-parameterization-template"></a>Domyślny szablon parametryzacji

Poniżej znajduje się bieżący domyślny szablon parametryzacji. Jeśli chcesz dodać tylko kilka parametrów, edytowanie tego szablonu bezpośrednio może być dobrym pomysłem, ponieważ nie utracisz istniejącej struktury parametryzacji.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

W poniższym przykładzie pokazano, jak dodać pojedynczą wartość do domyślnego szablonu parametryzacji. Chcemy tylko dodać istniejący identyfikator klastra interaktywnego usługi Azure Databricks dla usługi połączonej databricks do pliku parametrów. Należy zauważyć, że ten plik jest taki sam `existingClusterId` jak poprzedni plik, z wyjątkiem dodawania w obszarze właściwości pola `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Szablony Połączonego Menedżera zasobów

Jeśli skonfigurowałeś ciągłą integrację/dysk CD dla fabryk danych, możesz przekroczyć limity szablonów usługi Azure Resource Manager w miarę powiększania się fabryki. Na przykład jeden limit to maksymalna liczba zasobów w szablonie Menedżera zasobów. Aby pomieścić duże fabryki podczas generowania pełnego szablonu Menedżera zasobów dla fabryki, usługa Data Factory generuje teraz połączone szablony Menedżera zasobów. Dzięki tej funkcji cała ładunek fabryczny jest podzielony na kilka plików, dzięki czemu nie są ograniczone przez limity.

Jeśli skonfigurowano git, połączone szablony są generowane i zapisywane wraz z pełnymi szablonami Menedżera zasobów w gałęzi adf_publish w nowym folderze o nazwie linkedTemplates:

![Folder szablonów Połączonego Menedżera zasobów](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Połączone szablony Menedżera zasobów zwykle składają się z szablonu głównego i zestawu szablonów podrzędnych połączonych z wzorcem. Szablon nadrzędny jest nazywany ArmTemplate_master.json, a szablony podrzędne są nazwane wzorcem ArmTemplate_0.json, ArmTemplate_1.json i tak dalej. 

Aby użyć połączonych szablonów zamiast pełnego szablonu Menedżera zasobów, zaktualizuj zadanie ciągłej integracji/ciągłego wdrażania, aby wskazywało ArmTemplate_master.json zamiast ArmTemplateForFactory.json (pełny szablon Menedżera zasobów). Menedżer zasobów wymaga również przekazania połączonych szablonów na konto magazynu, aby platforma Azure mogła uzyskać do nich dostęp podczas wdrażania. Aby uzyskać więcej informacji, zobacz [Wdrażanie połączonych szablonów Menedżera zasobów za pomocą programu VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Pamiętaj, aby dodać skrypty fabryki danych w potoku ciągłej integracji/ciągłego wdrażania przed i po zadaniu wdrażania.

Jeśli nie masz skonfigurowanego Gita, możesz uzyskać dostęp do połączonych szablonów za pośrednictwem **szablonu Eksportuj ARM** na liście **Szablon ARM.**

## <a name="hotfix-production-branch"></a>Gałąź produkcji poprawek

Jeśli wdrożysz fabrykę w produkcji i zdasz sobie sprawę, że istnieje błąd, który musi zostać naprawiony od razu, ale nie można wdrożyć bieżącej gałęzi współpracy, może być konieczne wdrożenie poprawki. Takie podejście jest znane jako inżynieria quick-fix lub QFE.

1.    W usłudze Azure DevOps przejdź do wersji, która została wdrożona w wersji produkcyjnej. Znajdź ostatnie zatwierdzenie, które zostało wdrożone.

2.    Z komunikatu zatwierdzania pobierz identyfikator zatwierdzenia gałęzi współpracy.

3.    Utwórz nową gałąź poprawki z tego zatwierdzenia.

4.    Przejdź do środowiska użytkownika usługi Azure Data Factory i przełącz się do gałęzi poprawek.

5.    Za pomocą środowiska użytkownika usługi Azure Data Factory, naprawić błąd. Przetestuj swoje zmiany.

6.    Po zweryfikowaniu poprawki wybierz **pozycję Eksportuj szablon ARM,** aby uzyskać szablon Menedżera zasobów poprawki.

7.    Ręcznie sprawdź tę kompilację w gałęzi adf_publish.

8.    Jeśli skonfigurowano potok wersji do automatycznego wyzwalania na podstawie adf_publish zaewidencjonowania, nowa wersja zostanie uruchomiona automatycznie. W przeciwnym razie ręcznie kolejka wydania.

9.    Wdrażanie wersji poprawki w fabrykach testowych i produkcyjnych. Ta wersja zawiera poprzedni ładunek produkcyjny oraz poprawkę, która została wykonana w kroku 5.

10.    Dodaj zmiany z poprawki do gałęzi rozwoju, tak aby późniejsze wersje nie zawierały tego samego błędu.

## <a name="best-practices-for-cicd"></a>Najważniejsze wskazówki dotyczące ciągłej integracji/ciągłego wdrażania

Jeśli używasz integracji Git z fabryką danych i masz potok ciągłej integracji/ciągłego wdrażania, który przenosi zmiany z programowania do testu, a następnie do produkcji, zalecamy następujące najlepsze rozwiązania:

-   **Integracja Git**. Musisz skonfigurować tylko fabrykę danych deweloperskich z integracją Git. Zmiany w testach i produkcji są wdrażane za pośrednictwem ciągłej integracji/ciągłej integracji z dyskami CD i nie wymagają integracji z gitem.

-   **Skrypt ciągłej integracji/dysku CD fabryki danych**. Przed krokiem wdrażania Menedżera zasobów w ciągłej integracji/ciągłego wdrażania należy wykonać pewne zadania, takie jak zatrzymywanie i ponowne uruchamianie wyzwalaczy oraz czyszczenie. Zaleca się używanie skryptów programu PowerShell przed i po wdrożeniu. Aby uzyskać więcej informacji, zobacz [Aktualizowanie aktywnych wyzwalaczy](#update-active-triggers).

-   **Środowiska wykonawcze integracji i udostępnianie**. Środowiska wykonawcze integracji nie zmieniają się często i są podobne na wszystkich etapach ciągłej integracji/ciągłego wdrażania. Dlatego usługa Data Factory oczekuje, że masz taką samą nazwę i typ środowiska uruchomieniowego integracji na wszystkich etapach ciągłej integracji/dysku CD. Jeśli chcesz udostępnić środowiska wykonawcze integracji na wszystkich etapach, należy rozważyć użycie trójskładnikowej fabryki tylko do zawierapotywy integracji udostępnionej. Tej udostępnionej fabryki we wszystkich środowiskach można użyć jako typu środowiska uruchomieniowego połączonej integracji.

-   **Przechowalnia kluczy**. Korzystając z usług połączonych opartych na usłudze Azure Key Vault, można korzystać z nich dalej, zachowując oddzielne magazyny kluczy dla różnych środowisk. Można również skonfigurować oddzielne poziomy uprawnień dla każdego magazynu kluczy. Na przykład nie może chcesz, aby członkowie zespołu mają uprawnienia do wpisów tajnych produkcji. Jeśli zastosujesz się do tego podejścia, zaleca się zachować te same tajne nazwy na wszystkich etapach. Jeśli zachowasz te same nazwy, nie trzeba zmieniać szablonów Menedżera zasobów w środowiskach ciągłej integracji/ciągłego wdrażania, ponieważ jedyną rzeczą, która się zmienia, jest nazwa magazynu kluczy, która jest jednym z parametrów szablonu Menedżera zasobów.

## <a name="unsupported-features"></a>Nieobsługiwały się funkcje

- Zgodnie z projektem usługa Data Factory nie zezwala na cherry-picking zatwierdzeń lub selektywnego publikowania zasobów. Publikacje będą zawierać wszystkie zmiany wprowadzone w fabryce danych.

    - Jednostki fabryki danych zależą od siebie nawzajem. Na przykład wyzwalacze zależą od potoków, a potoki zależą od zestawów danych i innych potoków. Selektywne publikowanie podzbioru zasobów może prowadzić do nieoczekiwanych zachowań i błędów.
    - W rzadkich przypadkach, gdy potrzebujesz selektywnego publikowania, należy rozważyć użycie poprawki. Aby uzyskać więcej informacji, zobacz [Gałąź produkcji poprawek](#hotfix-production-branch).

-   Nie można publikować z prywatnych oddziałów.

-   Obecnie nie można hostować projektów na Bitbucket.
