---
title: Włączanie tożsamości zarządzanej dla konta Azure Automation (wersja zapoznawcza)
description: W tym artykule opisano sposób skonfigurowania tożsamości zarządzanej dla Azure Automation kont.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 93c55c21bf740f2851cac1926bc673cebcd914b0
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514805"
---
# <a name="enable-a-managed-identity-for-your-azure-automation-account-preview"></a>Włączanie tożsamości zarządzanej dla konta Azure Automation (wersja zapoznawcza)

W tym temacie pokazano, jak utworzyć tożsamość zarządzaną dla konta Azure Automation oraz jak używać jej do uzyskiwania dostępu do innych zasobów. Aby uzyskać więcej informacji na temat współpracy tożsamości zarządzanej z Azure Automation, zobacz [Tożsamości zarządzane](automation-security-overview.md#managed-identities-preview).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). Zarówno tożsamość zarządzana, jak i docelowe zasoby platformy Azure zarządzane przez element Runbook przy użyciu tej tożsamości muszą znajdować się w tej samej subskrypcji platformy Azure.

- Najnowsza wersja modułów Azure Automation kont. Obecnie jest to 1.6.0. (Aby uzyskać szczegółowe informacje o tej wersji, zobacz [Az.Automation 1.6.0).](https://www.powershellgallery.com/packages/Az.Automation/1.6.0)

- Zasób platformy Azure, do którego chcesz uzyskać dostęp z element runbook usługi Automation. Ten zasób musi mieć rolę zdefiniowaną dla tożsamości zarządzanej, co ułatwia uwierzytelnianie dostępu do zasobu za pomocą uwierzytelniania przez ten zasób. Aby dodać role, musisz być właścicielem zasobu w odpowiedniej dzierżawie usługi Azure AD.

- Jeśli chcesz wykonywać zadania hybrydowe przy użyciu tożsamości zarządzanej, zaktualizuj hybrydowy proces roboczy runbook do najnowszej wersji. Minimalne wymagane wersje to:

   - Hybrydowy proces roboczy runbook systemu Windows: wersja 7.3.1125.0
   - Hybrydowy proces roboczy runbook systemu Linux: wersja 1.7.4.0

## <a name="enable-system-assigned-identity"></a>Włączanie tożsamości przypisanej przez system

>[!IMPORTANT]
>Nowa tożsamość na poziomie konta usługi Automation zastąpi wszystkie poprzednie tożsamości przypisane przez system na poziomie maszyny wirtualnej (które opisano w tece Use runbook authentication with managed identities (Używanie uwierzytelniania runbook z [tożsamościami zarządzanymi).](/automation-hrw-run-runbooks#runbook-auth-managed-identities) Jeśli uruchamiasz zadania hybrydowe na maszynach wirtualnych platformy Azure, które używają przypisanej przez system tożsamości maszyny wirtualnej do uzyskiwania dostępu do zasobów runbook, tożsamość konta usługi Automation będzie używana na użytek zadań hybrydowych. Oznacza to, że na wykonywanie istniejącego zadania może mieć wpływ funkcja Kluczy zarządzanych przez klienta na koncie usługi Automation.<br/><br/>Jeśli chcesz nadal używać tożsamości zarządzanej maszyny wirtualnej, nie należy włączać tożsamości na poziomie konta usługi Automation. Jeśli została już włączona, możesz wyłączyć tożsamość zarządzaną konta usługi Automation. Zobacz [Wyłączanie tożsamości zarządzanej Azure Automation konta.](https://docs.microsoft.com/azure/automation/disable-managed-identity-for-automation)

Konfigurowanie tożsamości przypisanych przez system dla Azure Automation można wykonać na jeden z dwóch sposobów. Możesz użyć interfejsu Azure Portal lub interfejsu API REST platformy Azure.

>[!NOTE]
>Tożsamości przypisane przez użytkownika nie są jeszcze obsługiwane.

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Włączanie tożsamości przypisanej przez system w Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do konta usługi Automation i wybierz pozycję **Tożsamość w** obszarze **Ustawienia konta.**

1. Ustaw opcję **Przypisana przez system** na **wartość Wł.,** a następnie naciśnij **przycisk Zapisz.** Po wyświetleniu monitu o potwierdzenie wybierz pozycję **Tak.**

:::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Włączanie tożsamości przypisanej przez system w Azure Portal.":::

Konto usługi Automation może teraz używać tożsamości przypisanej przez system, która jest zarejestrowana w usłudze Azure Active Directory (Azure AD) i jest reprezentowana przez identyfikator obiektu.

:::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="Identyfikator obiektu tożsamości zarządzanej.":::

### <a name="enable-system-assigned-identity-through-the-rest-api"></a>Włączanie tożsamości przypisanej przez system za pomocą interfejsu API REST

Tożsamość zarządzaną przypisaną przez system do konta usługi Automation można skonfigurować przy użyciu następującego wywołania interfejsu API REST.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Treść żądania
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 .
 .
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
.
.
}
```

|Właściwość (JSON) | Wartość | Opis|
|----------|-----------|------------|
| principalid | \<principal-ID\> | Unikatowy identyfikator globalny (GUID) obiektu jednostki usługi dla tożsamości zarządzanej, która reprezentuje konto usługi Automation w dzierżawie usługi Azure AD. Ten identyfikator GUID jest czasami wyświetlany jako "identyfikator obiektu" lub identyfikator obiektu. |
| tenantid | \<Azure-AD-tenant-ID\> | Unikatowy identyfikator globalny (GUID) reprezentujący dzierżawę usługi Azure AD, której członkiem jest teraz konto usługi Automation. Wewnątrz dzierżawy usługi Azure AD nazwa główna usługi ma taką samą nazwę jak konto usługi Automation. |

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>Zapewnianie tożsamości dostępu do zasobów platformy Azure przez uzyskanie tokenu

Konto usługi Automation może używać swojej tożsamości zarządzanej do uzyskiwania tokenów dostępu do innych zasobów chronionych przez usługę Azure AD, takich jak Azure Key Vault. Te tokeny nie reprezentują żadnego określonego użytkownika aplikacji. Zamiast tego reprezentują aplikację, która uzyskuje dostęp do zasobu. Na przykład w tym przypadku token reprezentuje konto usługi Automation.

Przed użyciem tożsamości zarządzanej przypisanej przez system do uwierzytelniania skonfiguruj dostęp dla tej tożsamości w zasobie platformy Azure, w którym planujesz używać tożsamości. Aby wykonać to zadanie, przypisz odpowiednią rolę do tej tożsamości w docelowym zasobie platformy Azure.

W tym przykładzie Azure PowerShell, aby pokazać, jak przypisać rolę Współautor w subskrypcji do docelowego zasobu platformy Azure. Rola Współautor jest używana jako przykład i w Twoim przypadku może być wymagana lub nie.

```powershell
New-AzRoleAssignment -ObjectId <automation-Identity-object-id> -Scope "/subscriptions/<subscription-id>" -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-managed-identity"></a>Uwierzytelnianie dostępu przy użyciu tożsamości zarządzanej

Po włączeniu tożsamości zarządzanej dla konta usługi Automation i nadasz tożsamości dostęp do zasobu docelowego, możesz określić tę tożsamość w elementach Runbook względem zasobów, które obsługują tożsamość zarządzaną. Aby uzyskać obsługę tożsamości, użyj polecenia `Connect-AzAccount` cmdlet Az. Zobacz [Connect-AzAccount w](/powershell/module/az.accounts/Connect-AzAccount) odwołaniu do programu PowerShell.

```powershell
Connect-AzAccount -Identity
```

>[!NOTE]
>Jeśli Twoja organizacja nadal używa przestarzałych polecenia cmdlet azureRM, możesz użyć polecenia `Connect-AzureRMAccount -Identity` .

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Generowanie tokenu dostępu bez użycia polecenia cmdlet platformy Azure

W przypadku punktów końcowych HTTP upewnij się, że:
- Nagłówek metadanych musi być obecny i powinien być ustawiony na wartość "true".
- Zasób musi zostać przekazany wraz z żądaniem jako parametr zapytania dla żądania GET i jako dane formularza dla żądania POST.
- Dla nagłówka X-IDENTITY-HEADER należy ustawić wartość zmiennej środowiskowej IDENTITY_HEADER hybrydowych pracowników runbook. 
- Typem zawartości żądania Post musi być "application/x-www-form-urlencoded". 

### <a name="sample-get-request"></a>Przykładowe żądanie GET

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="sample-post-request"></a>Przykładowe żądanie POST
```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

## <a name="sample-runbooks-using-managed-identity"></a>Przykładowe podręczniki Runbook używające tożsamości zarządzanej

### <a name="sample-runbook-to-access-a-sql-database-without-using-azure-cmdlets"></a>Przykładowy program Runbook do uzyskiwania dostępu do bazy danych SQL bez użycia poleceń cmdlet platformy Azure

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

### <a name="sample-runbook-to-access-a-key-vault-using-azure-cmdlets"></a>Przykładowy element Runbook do uzyskiwania dostępu do magazynu kluczy przy użyciu polecenia cmdlet platformy Azure

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="sample-python-runbook-to-get-a-token"></a>Przykładowy kod Runbook języka Python do pobierania tokenu
 
```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

## <a name="next-steps"></a>Następne kroki

- Jeśli musisz wyłączyć tożsamość zarządzaną, zobacz Wyłączanie tożsamości zarządzanej Azure Automation [(wersja zapoznawcza).](disable-managed-identity-for-automation.md)

- Aby uzyskać omówienie zabezpieczeń Azure Automation, zobacz [Automation account authentication overview (Omówienie uwierzytelniania konta usługi Automation).](automation-security-overview.md)