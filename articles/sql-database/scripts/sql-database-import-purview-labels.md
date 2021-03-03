---
title: Klasyfikowanie danych usługi Azure SQL przy użyciu etykiet usługi Azure kontrolą
description: Zaimportuj swoją klasyfikację z usługi Azure kontrolą Azure SQL Database do usługi Azure Synpase Analytics
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: azurepowershell
ms.topic: sample
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
ms.openlocfilehash: 2eab7c535ff0c68da772e8a45ead12420734279c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714905"
---
# <a name="classify-your-azure-sql-data-using-azure-purview-labels"></a>Klasyfikowanie danych usługi Azure SQL przy użyciu etykiet usługi Azure kontrolą
[!INCLUDE[appliesto-sqldb-asa](../../azure-sql/includes/appliesto-sqldb-asa.md)]

W tym dokumencie opisano sposób dodawania etykiet usługi Azure kontrolą do Azure SQL Database i usługi Azure Synapse Analytics (dawniej SQL DW).

## <a name="create-an-application"></a>Tworzenie aplikacji

1. W Azure Portal Otwórz **Azure Active Directory**.
2. W obszarze **Zarządzaj** wybierz pozycję **Rejestracja aplikacji**.
3. Utwórz nową aplikację Azure Active Directory, wybierając pozycję **Nowa aplikacja**.
4. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **zarejestruj**.
5. Po utworzeniu aplikacji otwórz pozycję **certyfikaty & wpisy tajne** w obszarze **Menedżer**.
6. Utwórz nowy wpis tajny klienta, wybierając pozycję **Nowy wpis tajny klienta** w obszarze wpisy **tajne klienta**.
7. Dodaj opis do wpisu tajnego klienta, wybierz okres ważności, a następnie wybierz pozycję **Dodaj**.
8. Zachowaj **wartość** do użycia w przyszłości.

   > [!NOTE]
   > Po zamknięciu strony wartość zostanie zamaskowane. Jeśli wrócisz do strony, nie będzie można pobrać wartości klucza tajnego klienta. Konieczne będzie wygenerowanie nowego klucza tajnego klienta.

9. Wróć do strony przeglądu nowo utworzonej aplikacji i skopiuj następujące wartości do użycia w przyszłości:
    1. Identyfikator aplikacji (klienta)
    1. Identyfikator katalogu (dzierżawcy)

## <a name="provide-permissions-to-the-application"></a>Podaj uprawnienia do aplikacji

1. W Azure Portal Wyszukaj **konta usługi kontrolą**.
2. Wybierz konto kontrolą, w którym klasyfikowane są bazy danych SQL i Synapse.
3. Otwórz przystawkę **Kontrola dostępu (IAM)**, a następnie wybierz pozycję **Dodaj**.

4. Wybierz pozycję **Dodaj przypisanie roli**.
5. W sekcji **rola** Wyszukaj pozycję **kontrolą Data Reader** i wybierz ją.
6. W sekcji **Wybierz** Wyszukaj wcześniej utworzoną aplikację, wybierz ją i kliknij przycisk **Zapisz**.

## <a name="extract-the-classification-from-azure-purview"></a>Wyodrębnij klasyfikację z usługi Azure kontrolą

1. Otwórz konto usługi kontrolą, a na stronie głównej Wyszukaj swoją Azure SQL Database lub usługę Azure Synapse Analytics, w której chcesz skopiować etykiety.
2. Skopiuj kwalifikowana właściwość w obszarze **Właściwości** i Zachowaj ją do użycia w przyszłości.
3. Otwórz powłokę programu PowerShell.

4. Skopiuj jeden z poniższych skryptów zgodnie z typem zasobu SQL (Azure SQL Database lub Azure Synapse).
5. Wypełnij parametry wartościami skopiowanymi powyżej:

   a. $TenantID: sekcja 1, krok 9
   
   b. $ClientID: sekcja 1, krok 9
   
   c. $SecretID: sekcja 1, krok 8
   
   d. $purviewAccountName: sekcja 2, krok 2
   
   e. $sqlDatabaseName: sekcja 3, krok 2

6. Skopiuj dane wyjściowe skryptu do użycia w przyszłości.

### <a name="for-azure-sql-database"></a>Dla Azure SQL Database

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$sqlDatabaseName="mssql://sql_server_name.database.windows.net/db_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_db?attr:qualifiedName=" + $sqlDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

### <a name="for-azure-synapse-analytics"></a>Dla usługi Azure Synapse Analytics

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$dwDatabaseName="mssql://dw_server_name.database.windows.net/dw_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_dw?attr:qualifiedName=" + $dwDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_dw_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

## <a name="run-the-t-sql-command-on-your-sql-asset"></a>Uruchamianie polecenia T-SQL w elemencie zawartości SQL

1. Połącz się ze swoją Azure SQL Database lub z usługą Azure Synapse za pomocą wybranego przez siebie narzędzia.
2. Uruchom polecenie T-SQL skopiowane z poprzedniej sekcji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/).

Aby uzyskać więcej informacji na temat usługi Azure kontrolą, zobacz [dokumentację usługi Azure kontrolą](../../purview/index.yml).