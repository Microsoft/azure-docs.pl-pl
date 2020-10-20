---
title: Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell
description: W tym artykule opisano, jak używać Azure PowerShell do zarządzania kontami Data Lake Analytics, źródłami danych, użytkownikami i zadaniami &.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/29/2018
ms.openlocfilehash: afa21e6aae769e69e8bc83b9fa0d4f9b76396f7e
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220316"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

W tym artykule opisano sposób zarządzania kontami Azure Data Lake Analytics, źródłami danych, użytkownikami i zadaniami przy użyciu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby użyć programu PowerShell z Data Lake Analytics, Zbierz następujące informacje:

* **Identyfikator subskrypcji**: Identyfikator subskrypcji platformy Azure, która zawiera konto Data Lake Analytics.
* **Grupa zasobów**: Nazwa grupy zasobów platformy Azure, która zawiera konto Data Lake Analytics.
* **Nazwa konta Data Lake Analytics**: nazwa konta Data Lake Analytics.
* **Domyślna nazwa konta Data Lake Store**: każde konto Data Lake Analytics ma domyślne konto Data Lake Store.
* **Lokalizacja**: lokalizacja konta Data Lake Analytics, na przykład "Wschodnie stany USA 2" lub inne obsługiwane lokalizacje.

We fragmentach kodu programu PowerShell w ramach tego samouczka do przechowywania tych informacji są używane następujące zmienne

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

### <a name="log-in-using-interactive-user-authentication"></a>Logowanie przy użyciu interakcyjnego uwierzytelniania użytkowników

Zaloguj się przy użyciu identyfikatora subskrypcji lub nazwy subskrypcji

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname
```

## <a name="saving-authentication-context"></a>Zapisywanie kontekstu uwierzytelniania

`Connect-AzAccount`Polecenie cmdlet zawsze poprosi o poświadczenia. Można uniknąć wyświetlania monitu przy użyciu następujących poleceń cmdlet:

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Logowanie przy użyciu tożsamości głównej usługi (SPI)

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname"
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>Zarządzanie kontami

### <a name="list-accounts"></a>Wyświetlanie listy kont

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>Tworzenie konta

Każde konto usługi Data Lake Analytics wymaga domyślnego konta usługi Data Lake Store używanego do przechowywania dzienników. Możesz użyć istniejącego konta lub utworzyć konto.

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Pobierz informacje o koncie

Pobierz szczegóły dotyczące konta.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Sprawdź, czy konto istnieje

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Zarządzanie źródłami danych

Azure Data Lake Analytics obecnie obsługuje następujące źródła danych:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Każde konto Data Lake Analytics ma domyślne konto Data Lake Store. Domyślne konto Data Lake Store służy do przechowywania metadanych zadań i dzienników inspekcji zadań.

### <a name="find-the-default-data-lake-store-account"></a>Znajdź domyślne konto Data Lake Store

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Domyślne konto Data Lake Store można znaleźć, filtrując listę źródeł danych według `IsDefault` Właściwości:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault }
```

### <a name="add-a-data-source"></a>Dodawanie źródła danych

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName
```

### <a name="list-data-sources"></a>Wyświetl listę źródeł danych

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Przesyłanie zadań U-SQL

### <a name="submit-a-string-as-a-u-sql-job"></a>Prześlij ciąg jako zadanie U-SQL

```powershell
$script = @"
@a  =
    SELECT * FROM
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-job"></a>Prześlij plik jako zadanie U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>Wyświetl listę zadań

Dane wyjściowe obejmują obecnie uruchomione oraz niedawno ukończone zadania.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Wyświetl listę pierwszych N zadań

Domyślnie lista zadań jest sortowana na czas przesyłania. Ostatnio przesłane zadania są wyświetlane jako pierwsze. Domyślnie konto ADLA zapamiętuje zadania dla 180 dni, ale polecenie cmdlet Get-AdlJob domyślnie zwraca tylko pierwsze 500. Użyj parametru-Top, aby wyświetlić określoną liczbę zadań.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Wyświetl listę zadań według stanu zadania

Przy użyciu `-State` parametru. Można połączyć dowolne z tych wartości:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

### <a name="list-jobs-by-job-result"></a>Wyświetl listę zadań według wyniku zadania

Użyj `-Result` parametru, aby wykryć, czy zakończone zadania zostały ukończone pomyślnie. Ma następujące wartości:

* Anulowano
* Niepowodzenie
* Brak
* Sukces

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Wyświetl listę zadań według osoby przesyłającej zadanie

`-Submitter`Parametr pomaga określić, kto przesłał zadanie.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Wyświetl listę zadań według czasu przesłania

`-SubmittedAfter`Jest przydatny do filtrowania do zakresu czasu.

```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>Pobierz stan zadania

Uzyskaj stan określonego zadania.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="cancel-a-job"></a>Anulowanie zadania

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Poczekaj na zakończenie zadania

Zamiast powtarzania `Get-AdlAnalyticsJob` do momentu zakończenia zadania, można użyć `Wait-AdlJob` polecenia cmdlet, aby poczekać na zakończenie zadania.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>Analizowanie historii zadań

Używanie Azure PowerShell do analizowania historii zadań, które zostały uruchomione w Data Lake analizie, jest zaawansowaną techniką. Można go użyć, aby uzyskać wgląd w użycie i koszt. Więcej informacji można uzyskać, przeglądając [przykładowe repozytorium analizy historii zadań](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="list-job-pipelines-and-recurrences"></a>Wyświetlanie listy potoków zadań i cykli

Użyj `Get-AdlJobPipeline` polecenia cmdlet, aby wyświetlić informacje o potoku wcześniej przesłane zadania.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Użyj `Get-AdlJobRecurrence` polecenia cmdlet, aby wyświetlić informacje o cyklu dla wcześniej przesłanych zadań.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="manage-compute-policies"></a>Zarządzanie zasadami obliczeniowymi

### <a name="list-existing-compute-policies"></a>Wyświetlanie listy istniejących zasad obliczeniowych

`Get-AdlAnalyticsComputePolicy`Polecenie cmdlet pobiera informacje o zasadach obliczeniowych dla konta Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Tworzenie zasad obliczeniowych

`New-AdlAnalyticsComputePolicy`Polecenie cmdlet tworzy nowe zasady obliczeń dla konta Data Lake Analytics. Ten przykład ustawia maksymalną wartość dla określonego użytkownika na 50, a minimalny priorytet zadania na 250.

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="manage-files"></a>Zarządzanie plikami

### <a name="check-for-the-existence-of-a-file"></a>Sprawdź, czy istnieje plik

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Przekazywanie i pobieranie

Przekaż plik.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv"
```

Przekaż cały folder cyklicznie.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Pobierz plik.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Pobierz rekursywnie cały folder.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Jeśli proces przekazywania lub pobierania zostanie przerwany, można spróbować wznowić proces, ponownie uruchamiając polecenie cmdlet z ``-Resume`` flagą.

## <a name="manage-the-u-sql-catalog"></a>Zarządzanie wykazem U-SQL

Wykaz U-SQL służy do struktury danych i kodu, dzięki czemu mogą być współużytkowane przez skrypty U-SQL. Wykaz umożliwia najwyższą wydajność danych w Azure Data Lake. Więcej informacji znajduje się w temacie [Używanie katalogu U-SQL](./data-lake-analytics-u-sql-get-started.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Wyświetlanie listy elementów w wykazie U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>Wyświetl listę wszystkich zestawów w wykazie U-SQL

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Pobierz szczegóły dotyczące elementu katalogu

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Przechowywanie poświadczeń w wykazie

W ramach bazy danych U-SQL Utwórz obiekt Credential dla bazy danych hostowanej na platformie Azure. Obecnie poświadczenia U-SQL są jedynym typem elementu katalogu, który można utworzyć za pomocą programu PowerShell.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="manage-firewall-rules"></a>Zarządzanie regułami zapory

### <a name="list-firewall-rules"></a>Wyświetlanie listy reguł zapory

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>Dodawanie reguły zapory

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>Modyfikowanie reguły zapory

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Usuwanie reguły zapory

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Zezwalaj na adresy IP platformy Azure

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="working-with-azure"></a>Praca z platformą Azure

### <a name="get-error-details"></a>Pobierz szczegóły błędu

```powershell
Resolve-AzError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Sprawdź, czy używasz programu jako administratora na komputerze z systemem Windows

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Znajdź TenantID

Nazwa subskrypcji:

```powershell
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

Z identyfikatora subskrypcji:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
```

Z adresu domeny, takiego jak "contoso.com"

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Wyświetlanie listy wszystkich subskrypcji i identyfikatorów dzierżawy

```powershell
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Tworzenie konta Data Lake Analytics przy użyciu szablonu

Możesz również użyć szablonu grupy zasobów platformy Azure, korzystając z następującego przykładu: [Tworzenie konta Data Lake Analytics przy użyciu szablonu](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>Następne kroki
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Wprowadzenie do Data Lake Analytics przy użyciu [Azure Portal](data-lake-analytics-get-started-portal.md)  |  [Azure PowerShell](data-lake-analytics-get-started-powershell.md)  |  [interfejsu wiersza polecenia platformy Azure](data-lake-analytics-get-started-cli.md)
* Zarządzanie Azure Data Lake Analytics przy [Azure portal](data-lake-analytics-manage-use-portal.md)użyciu  |  [Azure PowerShell](data-lake-analytics-manage-use-powershell.md)  |  [interfejsu wiersza polecenia](data-lake-analytics-manage-use-cli.md) Azure Portal Azure PowerShell