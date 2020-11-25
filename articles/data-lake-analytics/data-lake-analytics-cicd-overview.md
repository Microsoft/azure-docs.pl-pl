---
title: Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania dla Azure Data Lake Analytics
description: Dowiedz się, jak skonfigurować ciągłą integrację i ciągłe wdrażanie dla Azure Data Lake Analytics.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 09/14/2018
ms.openlocfilehash: 95b638b85e0746d2995488f2a28a5fb2512b1063
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015268"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania dla Azure Data Lake Analytics  

W tym artykule dowiesz się, jak skonfigurować potok ciągłej integracji i wdrażania (CI/CD) dla zadań U-SQL i baz danych U-SQL.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Używanie ciągłej integracji/ciągłego wdrażania zadań U-SQL

Azure Data Lake Tools for Visual Studio udostępnia typ projektu U-SQL, który pomaga organizować skrypty U-SQL. Użycie projektu U-SQL do zarządzania kodem U-SQL sprawia, że dalsze scenariusze ciągłej integracji/ciągłego wdrażania.

## <a name="build-a-u-sql-project"></a>Kompilowanie projektu U-SQL

Projekt U-SQL można skompilować przy użyciu Microsoft Build Engine (MSBuild) przez przekazanie odpowiednich parametrów. Wykonaj kroki opisane w tym artykule, aby skonfigurować proces kompilacji dla projektu U-SQL.

### <a name="project-migration"></a>Migracja projektu

Przed skonfigurowaniem zadania kompilacji dla projektu U-SQL upewnij się, że masz najnowszą wersję projektu U-SQL. Otwórz plik projektu U-SQL w edytorze i sprawdź, czy masz następujące elementy importu:

```xml
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
```

Jeśli nie, dostępne są dwie opcje migracji projektu:

- Opcja 1: zmiana starego elementu importowania na poprzedni.
- Opcja 2: otwarcie starego projektu w Azure Data Lake Tools for Visual Studio. Użyj wersji nowszej niż 2.3.3000.0. Stary szablon projektu zostanie automatycznie uaktualniony do najnowszej wersji. Nowe projekty utworzone za pomocą wersji nowszej niż 2.3.3000.0 używają nowego szablonu.

### <a name="get-nuget"></a>Pobierz pakiet NuGet

Program MSBuild nie zapewnia wbudowanej obsługi projektów U-SQL. Aby uzyskać pomoc techniczną, musisz dodać odwołanie do rozwiązania do pakietu NuGet [Microsoft. Azure. datalake. USQL. SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) , który dodaje wymaganą usługę języka.

Aby dodać odwołanie do pakietu NuGet, kliknij prawym przyciskiem myszy rozwiązanie w programie Visual Studio Eksplorator rozwiązań i wybierz polecenie **Zarządzaj pakietami NuGet**. Można też dodać plik o nazwie `packages.config` w folderze rozwiązania i umieścić w nim następującą zawartość:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
```

### <a name="manage-u-sql-database-references"></a>Zarządzanie odwołaniami do bazy danych U-SQL

Skrypty u-SQL w projekcie U-SQL mogą zawierać instrukcje kwerendy dla obiektów bazy danych U-SQL. W takim przypadku należy odwołać się do odpowiedniego projektu bazy danych U-SQL, który zawiera definicję obiektu przed skompilowaniem projektu U-SQL. Przykładem jest kwerenda w tabeli U-SQL lub odwołanie do zestawu.

Dowiedz się więcej na temat [projektu bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
> Instrukcja DROP może spowodować Przypadkowe usunięcie. Aby włączyć instrukcję DROP, należy jawnie określić argumenty MSBuild. **AllowDropStatement** będzie włączać operacje usuwania powiązane z danymi, takie jak Drop Assembly i drop Table Value Function. **AllowDataDropStatement** spowoduje włączenie operacji usuwania powiązanej z danymi, takich jak Drop Table i drop Schema. Musisz włączyć AllowDropStatement przed użyciem AllowDataDropStatement.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Kompilowanie projektu U-SQL przy użyciu wiersza polecenia programu MSBuild

Najpierw Przeprowadź migrację projektu i Pobierz pakiet NuGet. Następnie Wywołaj standardowy wiersz polecenia MSBuild z następującymi dodatkowymi argumentami, aby skompilować projekt U-SQL:

```console
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
```

Definicje argumentów i wartości są następujące:

- **USQLSDKPath = \<U-SQL Nuget package> \build\runtime**. Ten parametr odnosi się do ścieżki instalacji pakietu NuGet dla usługi języka U-SQL.
- **USQLTargetType = Merge lub SyntaxCheck**:

  - **Scal**. Tryb scalania kompiluje pliki związane z kodem. Przykłady to **. cs**, **. PR** i **. r** . Definiuje on utworzoną przez użytkownika bibliotekę kodu do skryptu U-SQL. Przykłady to kod binarny biblioteki DLL, Python lub R.

  - **SyntaxCheck**. W trybie SyntaxCheck najpierw scala pliki związane z kodem do skryptu U-SQL. Następnie kompiluje skrypt U-SQL, aby sprawdzić poprawność kodu.

- **Dataroot = \<DataRoot path>**. Element dataroot jest wymagany tylko w przypadku trybu SyntaxCheck. Gdy kompiluje skrypt w trybie SyntaxCheck, MSBuild sprawdza odwołania do obiektów bazy danych w skrypcie. Przed skompilowaniem Skonfiguruj zgodne środowisko lokalne, które zawiera przywoływane obiekty z bazy danych U-SQL w folderze głównym usługi Machine Build. Tymi zależnościami bazy danych można również zarządzać, [odwołując się do projektu bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). Program MSBuild sprawdza tylko odwołania do obiektu bazy danych, a nie pliki.

- **EnableDeployment = true** lub **false**. EnableDeployment wskazuje, czy można wdrożyć przywoływane bazy danych U-SQL podczas procesu kompilacji. Jeśli odwołujesz się do projektu bazy danych U-SQL i zużywasz obiekty bazy danych w skrypcie U-SQL, ustaw ten parametr na **wartość true**.

### <a name="continuous-integration-through-azure-pipelines"></a>Ciągła integracja za Azure Pipelines

Oprócz wiersza polecenia można także użyć kompilacji programu Visual Studio lub zadania MSBuild do kompilowania projektów U-SQL w Azure Pipelines. Aby skonfigurować potok kompilacji, należy dodać dwa zadania w potoku kompilacji: zadanie przywracania NuGet i zadanie MSBuild.

![Zadanie MSBuild dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Dodaj zadanie przywracania NuGet, aby pobrać pakiet NuGet, do którego odwołuje się rozwiązanie, aby program `Azure.DataLake.USQL.SDK` MSBuild mógł znaleźć elementy docelowe języka U-SQL. Ustaw **Zaawansowany**  >  **katalog docelowy** , `$(Build.SourcesDirectory)/packages` Jeśli chcesz użyć próbki argumentów MSBuild bezpośrednio w kroku 2.

   ![Zadanie przywracania NuGet dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Ustaw argumenty programu MSBuild w narzędziach kompilacji programu Visual Studio lub w zadaniu MSBuild, jak pokazano w poniższym przykładzie. Lub można definiować zmienne dla tych argumentów w potoku kompilacji Azure Pipelines.

   ![Definiowanie zmiennych MSBuild/CD dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
   ```

### <a name="u-sql-project-build-output"></a>Dane wyjściowe kompilacji projektu U-SQL

Po uruchomieniu kompilacji wszystkie skrypty w projekcie U-SQL są kompilowane i wyprowadzane do pliku zip o nazwie `USQLProjectName.usqlpack` . Struktura folderów w projekcie jest przechowywana w spakowanej wyjściowej kompilacji.

> [!NOTE]
> Plik związany z kodem dla każdego skryptu U-SQL zostanie scalony jako instrukcja wbudowana do danych wyjściowych kompilacji skryptu.

## <a name="test-u-sql-scripts"></a>Testowanie skryptów U-SQL

Azure Data Lake udostępnia projekty testowe dla skryptów U-SQL i C# UDO/UDAG/UDF:

- Dowiedz się [, jak dodać przypadki testowe dla skryptów U-SQL i rozszerzonego kodu w języku C#](data-lake-analytics-cicd-test.md#test-u-sql-scripts)

- Dowiedz się, jak [uruchamiać przypadki testowe w Azure Pipelines](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Wdrażanie zadania U-SQL

Po sprawdzeniu kodu w procesie kompilowania i testowania można przesłać zadania U-SQL bezpośrednio z Azure Pipelines za pośrednictwem zadania Azure PowerShell. Możesz również wdrożyć skrypt do Azure Data Lake Store lub Azure Blob Storage i [uruchamiać zaplanowane zadania za pomocą Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Prześlij zadania U-SQL za Azure Pipelines

Dane wyjściowe kompilacji projektu U-SQL to plik zip o nazwie **USQLProjectName. usqlpack**. Plik zip zawiera wszystkie skrypty U-SQL w projekcie. Możesz użyć [zadania Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell) w potokach z poniższym przykładowym skryptem programu PowerShell, aby przesłać zadania U-SQL bezpośrednio z Azure Pipelines.

```powershell
<#
   This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and submit all scripts one-by-one.

   Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.

   Example :
      USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

   return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
   $usqlFiles = GetUsqlFiles

   Write-Output "$($usqlFiles.Count) jobs to be submitted..."

   # Submit each usql script and wait for completion before moving ahead.
   foreach ($usqlFile in $usqlFiles)
   {
      $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

      Write-Output "Submitting job for '{$usqlFile}'"

      $jobToSubmit = Submit-AzDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism

      LogJobInformation $jobToSubmit

      Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
      $jobResult = Wait-AzDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
      LogJobInformation $jobResult
   }
}

Function LogJobInformation($jobInfo)
{
   Write-Output "************************************************************************"
   Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
   Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
   Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
   Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
   Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
   Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
   Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
   if ($item -ne $null)
   {
      return $item
   }
   return ""
}

Function Main()
{
   Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
   Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
   Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

   Write-Output "Starting USQL script deployment..."

   SubmitAnalyticsJob

   Write-Output "Finished deployment..."
}

Main
```

>[!NOTE]
> Polecenia: `Submit-AzDataLakeAnalyticsJob` i `Wait-AzDataLakeAnalyticsJob` są Azure PowerShell polecenia cmdlet dla Azure Data Lake Analytics w środowisku Azure Resource Manager. Zostanie potrzebne stacja robocza z zainstalowaną Azure PowerShell. Aby uzyskać więcej poleceń i przykładów, można zapoznać się z [listą poleceń](/powershell/module/Az.DataLakeAnalytics) .
>

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Wdróż zadania U-SQL za Azure Data Factory

Zadania U-SQL można przesłać bezpośrednio z Azure Pipelines. Można też przekazać skompilowane skrypty do Azure Data Lake Store lub magazynu obiektów blob platformy Azure i [uruchamiać zaplanowane zadania za pomocą Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md).

Użyj [zadania Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell) w Azure Pipelines z następującym przykładowym skryptem programu PowerShell, aby przekazać skrypty U-SQL do konta Azure Data Lake Store:

```powershell
<#
   This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.

   Example :
      FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DestinationFolder = "USQLScriptSource" # Destination folder in ADLS
)

Function UploadResources()
{
   Write-Host "************************************************************************"
   Write-Host "Uploading files to $ADLSName"
   Write-Host "***********************************************************************"

   $usqlScripts = GetUsqlFiles

   $files = @(get-childitem $usqlScripts -recurse)
   foreach($file in $files)
   {
      Write-Host "Uploading file: $($file.Name)"
      Import-AzDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DestinationFolder $file)" -Force
   }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>Ciągłej integracji/ciągłego dostarczania dla bazy danych U-SQL

Azure Data Lake Tools for Visual Studio udostępnia szablony projektu bazy danych U-SQL, które ułatwiają tworzenie i wdrażanie baz danych U-SQL oraz zarządzanie nimi. Dowiedz się więcej na temat [projektu bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Kompilowanie projektu bazy danych U-SQL

### <a name="get-the-nuget-package"></a>Pobierz pakiet NuGet

Program MSBuild nie zapewnia wbudowanej obsługi projektów bazy danych U-SQL. Aby skorzystać z tej możliwości, należy dodać odwołanie do rozwiązania do pakietu NuGet [Microsoft. Azure. datalake. USQL. SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) , który dodaje wymaganą usługę języka.

Aby dodać odwołanie do pakietu NuGet, kliknij prawym przyciskiem myszy rozwiązanie w programie Visual Studio Eksplorator rozwiązań. Wybierz pozycję **Zarządzaj pakietami NuGet**. Następnie wyszukaj i zainstaluj pakiet NuGet. Można też dodać plik o nazwie **packages.config** w folderze rozwiązania i umieścić w nim następującą zawartość:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Kompilowanie projektu bazy danych U-SQL przy użyciu wiersza polecenia MSBuild

Aby skompilować projekt bazy danych U-SQL, wywołaj standardowy wiersz polecenia programu MSBuild i przekaż odwołanie do pakietu NuGet U-SQL SDK jako dodatkowy argument. Zobacz poniższy przykład: 

```console
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Argument `USQLSDKPath=<U-SQL Nuget package>\build\runtime` odwołuje się do ścieżki instalacji pakietu NuGet dla usługi języka U-SQL.

### <a name="continuous-integration-with-azure-pipelines"></a>Ciągła integracja z użyciem usługi Azure Pipelines

Oprócz wiersza polecenia można użyć programu Visual Studio Build lub zadania MSBuild do kompilowania projektów bazy danych U-SQL w Azure Pipelines. Aby skonfigurować zadanie kompilacji, należy dodać dwa zadania w potoku kompilacji: zadanie przywracania NuGet i zadanie MSBuild.

   ![Zadanie MSBuild/CD dla elementu w projekcie U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Dodaj zadanie przywracania NuGet, aby uzyskać pakiet NuGet, do którego odwołuje się rozwiązanie, w tym `Azure.DataLake.USQL.SDK` , że program MSBuild może znaleźć elementy docelowe języka U-SQL. Ustaw **Zaawansowany**  >  **katalog docelowy** , `$(Build.SourcesDirectory)/packages` Jeśli chcesz użyć próbki argumentów MSBuild bezpośrednio w kroku 2.

   ![Zadanie narzędzia NuGet CI/CD dla projektu U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Ustaw argumenty programu MSBuild w narzędziach kompilacji programu Visual Studio lub w zadaniu MSBuild, jak pokazano w poniższym przykładzie. Lub można definiować zmienne dla tych argumentów w potoku kompilacji Azure Pipelines.

   ![Definiuj zmienne MSBuild/CD dla projektu bazy danych U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```

### <a name="u-sql-database-project-build-output"></a>Dane wyjściowe kompilacji projektu bazy danych U-SQL

Dane wyjściowe kompilacji dla projektu bazy danych U-SQL są pakietem wdrożeniowym bazy danych U-SQL o nazwie z sufiksem `.usqldbpack` . `.usqldbpack`Pakiet jest plikiem ZIP, który zawiera wszystkie instrukcje języka DDL w jednym skrypcie U-SQL w folderze DDL. Zawiera wszystkie **biblioteki DLL** i dodatkowe pliki dla zestawu w folderze tymczasowym.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Testowanie funkcji z wartościami przechowywanymi w tabeli i procedur składowanych

Dodawanie przypadków testowych dla funkcji z wartościami przechowywanymi w tabeli i procedur składowanych bezpośrednio nie jest obecnie obsługiwane. Aby obejść ten krok, można utworzyć projekt U-SQL, który zawiera skrypty U-SQL, które wywołują te funkcje, i zapisują przypadki testowe dla nich. Wykonaj następujące kroki, aby skonfigurować przypadki testowe dla funkcji z wartościami przechowywanymi w tabeli i procedur składowanych zdefiniowanych w projekcie bazy danych U-SQL:

1. Utwórz projekt U-SQL dla celów testowych i napisz skrypty U-SQL, wywołując funkcje z wartościami przechowywanymi w tabeli i procedury składowane.

1. Dodaj odwołanie do bazy danych do projektu U-SQL. Aby uzyskać definicję funkcji zwracającej tabelę i definicje procedury składowanej, należy odwołać się do projektu bazy danych, który zawiera instrukcję języka DDL. Dowiedz się więcej o [odwołaniach do bazy danych](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

1. Dodaj przypadki testowe dla skryptów U-SQL, które wywołują funkcje z wartościami przechowywanymi w tabeli i procedury składowane. Dowiedz się, jak [dodać przypadki testowe dla skryptów U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Wdrażanie bazy danych U-SQL za Azure Pipelines

`PackageDeploymentTool.exe` zapewnia programowanie i interfejsy wiersza polecenia ułatwiające wdrażanie pakietów wdrażania bazy danych U-SQL, **usqldbpack**. Zestaw SDK jest zawarty w [pakiecie NuGet zestawu SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), który znajduje się na stronie **kompilacja/środowisko uruchomieniowe/PackageDeploymentTool.exe**. Korzystając z programu `PackageDeploymentTool.exe` , można wdrażać bazy danych U-SQL zarówno na Azure Data Lake Analytics, jak i na kontach lokalnych.

> [!NOTE]
>
> Obsługa zadań w wierszu polecenia programu PowerShell i zadania wydania Azure Pipelines dla wdrożenia bazy danych U-SQL jest obecnie w stanie oczekiwania.
>

Wykonaj następujące kroki, aby skonfigurować zadanie wdrażania bazy danych w Azure Pipelines:

1. Dodaj zadanie skryptu programu PowerShell w potoku kompilacji lub wydania i wykonaj następujący skrypt programu PowerShell. To zadanie pomaga uzyskać zależności zestawu Azure SDK dla `PackageDeploymentTool.exe` i `PackageDeploymentTool.exe` . Można ustawić parametry **-AzureSDK** i **-DBDeploymentTool** w celu załadowania zależności i narzędzia wdrażania do określonych folderów. Przekaż ścieżkę **-AzureSDK** `PackageDeploymentTool.exe` jako parametr **-AzureSDKPath** w kroku 2.

   ```powershell
   <#
      This script is used for getting dependencies and SDKs for U-SQL database deployment.
      PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.

      Example :
          GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
   #>

   param (
      [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
      [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
      [string]$workingfolder = "" # Folder to execute these command lines
   )

   if ([string]::IsNullOrEmpty($workingfolder))
   {
      $scriptpath = $MyInvocation.MyCommand.Path
      $workingfolder = Split-Path $scriptpath
   }
   cd $workingfolder

   echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
   echo "Downloading required packages..."

   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
   iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

   echo "Extracting packages..."

   Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
   Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
   Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
   Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
   Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

   echo "Copy required DLLs to output folder..."

   mkdir $AzureSDK -Force
   mkdir $DBDeploymentTool -Force
   copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
   copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
   copy USQLSDK\build\runtime\*.* $DBDeploymentTool
   ```

2. Dodaj **zadanie wiersza polecenia** w potoku kompilacja lub wydanie i wypełnij skrypt, wywołując polecenie `PackageDeploymentTool.exe` . `PackageDeploymentTool.exe` znajduje się w obszarze **$DBDeploymentTool** zdefiniowanego folderu. Przykładowy skrypt jest następujący: 

   - Wdrażanie bazy danych U-SQL lokalnie:

      ```cmd
      PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
      ```

   - Użyj trybu uwierzytelniania interakcyjnego, aby wdrożyć bazę danych U-SQL na koncie Azure Data Lake Analytics:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
      ```

   - Użyj uwierzytelniania **tajnego** , aby wdrożyć bazę danych U-SQL na koncie Azure Data Lake Analytics:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
      ```

   - Użyj uwierzytelniania **certFile** do wdrożenia bazy danych U-SQL na koncie Azure Data Lake Analytics:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
      ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Opisy parametrów PackageDeploymentTool.exe

#### <a name="common-parameters"></a>Parametry wspólne

| Parametr | Description | Wartość domyślna | Wymagane |
|---------|-----------|-------------|--------|
|Pakiet|Ścieżka pakietu wdrożeniowego bazy danych U-SQL, który ma zostać wdrożony.|wartość null|true|
|baza danych|Nazwa bazy danych, która ma zostać wdrożona lub utworzona.|master|fałsz|
|Pliki|Ścieżka pliku do rejestrowania. Domyślnie dla standardowego (konsoli).|wartość null|fałsz|
|LogLevel|Poziom dziennika: verbose, Normal, Warning lub Error.|LogLevel. Normal|fałsz|

#### <a name="parameter-for-local-deployment"></a>Parametr wdrożenia lokalnego

|Parametr|Description|Wartość domyślna|Wymagane|
|---------|-----------|-------------|--------|
|DataRoot|Ścieżka do lokalnego folderu danych głównych.|wartość null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parametry wdrożenia Azure Data Lake Analytics

|Parametr|Description|Wartość domyślna|Wymagane|
|---------|-----------|-------------|--------|
|Konto|Określa, które konto Azure Data Lake Analytics ma zostać wdrożone na podstawie nazwy konta.|wartość null|true|
|ResourceGroup|Nazwa grupy zasobów platformy Azure dla konta Azure Data Lake Analytics.|wartość null|true|
|SubscriptionId|Identyfikator subskrypcji platformy Azure dla konta Azure Data Lake Analytics.|wartość null|true|
|Dzierżawa|Nazwa dzierżawy to nazwa domeny Azure Active Directory (Azure AD). Znajdź je na stronie zarządzania subskrypcjami w Azure Portal.|wartość null|true|
|AzureSDKPath|Ścieżka do przeszukiwania zestawów zależnych w zestawie Azure SDK.|wartość null|true|
|Interaktywne|Określa, czy do uwierzytelniania ma być używany tryb interaktywny.|fałsz|fałsz|
|ClientId|Identyfikator aplikacji usługi Azure AD wymagany do uwierzytelniania nieinterakcyjnego.|wartość null|Wymagane na potrzeby uwierzytelniania nieinterakcyjnego.|
|Wpis tajny|Wpis tajny lub hasło nieinterakcyjnego uwierzytelniania. Powinno być używane tylko w zaufanym i bezpiecznym środowisku.|wartość null|Wymagane na potrzeby uwierzytelniania nieinterakcyjnego lub w przeciwnym razie użyj SecreteFile.|
|SecreteFile|Plik zapisuje tajne lub hasło dla nieinteraktywnego uwierzytelniania. Upewnij się, że jest on możliwy do odczytania tylko przez bieżącego użytkownika.|wartość null|Wymagane na potrzeby uwierzytelniania nieinterakcyjnego lub użyć klucza tajnego.|
|CertFile|Plik zapisuje certyfikat X. 509 dla nieinteraktywnego uwierzytelniania. Wartością domyślną jest użycie uwierzytelniania tajnego klienta.|wartość null|fałsz|
| JobPrefix | Prefiks do wdrożenia bazy danych zadania DDL języka U-SQL. | Deploy_ + DateTime. Now | fałsz |

## <a name="next-steps"></a>Następne kroki

- [Jak przetestować kod Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
- [Uruchom skrypt U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md).
- [Użyj projektu bazy danych u-SQL, aby utworzyć bazę danych u-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).