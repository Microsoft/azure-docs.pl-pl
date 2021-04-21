---
title: Zarządzanie połączeniami w Azure Automation
description: W tym artykule opisano sposób zarządzania połączeniami Azure Automation z zewnętrznymi usługami lub aplikacjami oraz pracy z nimi w podręcznikach Runbook.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: 4b5983d6ea4ea9065d1292a2c5ee0c094cea093b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834898"
---
# <a name="manage-connections-in-azure-automation"></a>Zarządzanie połączeniami w Azure Automation

Zasób Azure Automation zawiera informacje wymienione poniżej. Te informacje są wymagane do nawiązaniu połączenia z zewnętrzną usługą lub aplikacją z elementem Runbook lub konfiguracją DSC. 

* Informacje wymagane do uwierzytelniania, takie jak nazwa użytkownika i hasło
* Informacje o połączeniu, takie jak adres URL lub port

Zasób połączenia przechowuje wszystkie właściwości na potrzeby nawiązywania połączenia z określoną aplikacją, co sprawia, że nie trzeba tworzyć wielu zmiennych. Wartości połączenia można edytować w jednym miejscu, a nazwę połączenia można przekazać do pliku Runbook lub konfiguracji DSC w jednym parametrze. Za pomocą wewnętrznego polecenia cmdlet program Runbook lub konfiguracja uzyskuje dostęp do właściwości `Get-AutomationConnection` połączenia.

Podczas tworzenia połączenia należy określić typ połączenia. Typ połączenia to szablon, który definiuje zestaw właściwości. Typ połączenia można dodać do usługi Azure Automation modułu integracji z plikiem metadanych. Istnieje również możliwość utworzenia typu połączenia przy użyciu interfejsu API usługi [Azure Automation,](/previous-versions/azure/reference/mt163818(v=azure.100)) jeśli moduł integracji zawiera typ połączenia i jest importowany na konto usługi Automation. 

>[!NOTE]
>Zabezpieczanie zasobów w Azure Automation obejmują poświadczenia, certyfikaty, połączenia i zaszyfrowane zmienne. Te zasoby są szyfrowane i przechowywane w Azure Automation przy użyciu unikatowego klucza generowanego dla każdego konta usługi Automation. Azure Automation przechowuje klucz w zarządzanym przez system Key Vault. Przed zapisaniem bezpiecznego zasobu automatyzacja ładuje klucz z Key Vault, a następnie używa go do szyfrowania zasobu. 

## <a name="connection-types"></a>Typy połączeń

Azure Automation udostępnia następujące wbudowane typy połączeń:

* `Azure` - Reprezentuje połączenie używane do zarządzania zasobami klasycznymi.
* `AzureServicePrincipal` - Reprezentuje połączenie używane przez konto Uruchom jako platformy Azure.
* `AzureClassicCertificate` - Reprezentuje połączenie używane przez klasyczne konto Uruchom jako platformy Azure.

W większości przypadków nie trzeba tworzyć zasobu połączenia, ponieważ jest on tworzony podczas tworzenia [konta Uruchom jako.](automation-security-overview.md)

## <a name="powershell-cmdlets-to-access-connections"></a>Polecenia cmdlet programu PowerShell do uzyskiwania dostępu do połączeń

Polecenia cmdlet w poniższej tabeli tworzą połączenia usługi Automation i zarządzają nimi za pomocą programu PowerShell. Są one częścią modułów [Az](shared-resources/modules.md#az-modules).

|Polecenie cmdlet|Opis|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|Pobiera informacje o połączeniu.|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|Tworzy nowe połączenie.|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|Usuwa istniejące połączenie.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|Ustawia wartość określonego pola dla istniejącego połączenia.|

## <a name="internal-cmdlets-to-access-connections"></a>Wewnętrzne polecenia cmdlet do uzyskiwania dostępu do połączeń

Wewnętrzne polecenie cmdlet w poniższej tabeli służy do uzyskiwania dostępu do połączeń w elementy Runbook i konfiguracje DSC. To polecenie cmdlet zawiera moduł globalny `Orchestrator.AssetManagement.Cmdlets` . Aby uzyskać więcej informacji, zobacz [Wewnętrzne polecenia cmdlet](shared-resources/modules.md#internal-cmdlets).

|Wewnętrzne polecenie cmdlet|Opis|
|---|---|
|`Get-AutomationConnection` | Pobiera wartości różnych pól w połączeniu i zwraca je jako [tablicę skrótów](/powershell/module/microsoft.powershell.core/about/about_hash_tables). Następnie możesz użyć tej tablicy skrótów z odpowiednimi poleceniami w konfiguracji runbook lub DSC.|

>[!NOTE]
>Unikaj używania zmiennych z `Name` parametrem `Get-AutomationConnection` . Użycie zmiennych w tym przypadku może skomplikować odnajdywanie zależności między zasobami runbook lub konfiguracjami DSC i zasobami połączenia w czasie projektowania.

## <a name="python-functions-to-access-connections"></a>Funkcje języka Python do uzyskiwania dostępu do połączeń

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do połączeń w runbook języka Python 2 i 3. Aplikacje Runbook języka Python 3 są obecnie dostępne w wersji zapoznawczej.

| Funkcja | Opis |
|:---|:---|
| `automationassets.get_automation_connection` | Pobiera połączenie. Zwraca słownik z właściwościami połączenia. |

> [!NOTE]
> Aby uzyskać dostęp do funkcji zasobów, należy zaimportować moduł w górnej części modułu `automationassets` Runbook języka Python.

## <a name="create-a-new-connection"></a>Tworzenie nowego połączenia

### <a name="create-a-new-connection-with-the-azure-portal"></a>Tworzenie nowego połączenia za pomocą Azure Portal

Aby utworzyć nowe połączenie w Azure Portal:

1. Na koncie usługi Automation kliknij pozycję **Połączenia w** obszarze **Udostępnione zasoby.**
2. Kliknij **pozycję + Dodaj połączenie** na stronie Połączenia.
4. W polu **Typ** w okienku Nowe połączenie wybierz typ połączenia do utworzenia. Dostępne opcje to `Azure` , `AzureServicePrincipal` i `AzureClassicCertificate` . 
5. Formularz przedstawia właściwości wybranego typu połączenia. Wypełnij formularz i kliknij przycisk **Utwórz,** aby zapisać nowe połączenie.

### <a name="create-a-new-connection-with-windows-powershell"></a>Tworzenie nowego połączenia za pomocą Windows PowerShell

Utwórz nowe połączenie z Windows PowerShell pomocą `New-AzAutomationConnection` polecenia cmdlet . To polecenie cmdlet ma parametr, który oczekuje wartości definiujących skróty dla każdej właściwości `ConnectionFieldValues` zdefiniowanej przez typ połączenia.

Poniższe przykładowe polecenia mogą stanowić alternatywę dla tworzenia konta Uruchom jako w portalu w celu utworzenia nowego zasobu połączenia.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Podczas tworzenia konta usługi Automation domyślnie obejmuje ono kilka modułów globalnych wraz z typem połączenia w celu `AzureServicePrincipal` utworzenia `AzureRunAsConnection` zasobu połączenia. Jeśli spróbujesz utworzyć nowy zasób połączenia w celu nawiązania połączenia z usługą lub aplikacją przy użyciu innej metody uwierzytelniania, operacja zakończy się niepowodzeniem, ponieważ typ połączenia nie został jeszcze zdefiniowany na koncie usługi Automation. Aby uzyskać więcej informacji na temat tworzenia własnego typu połączenia dla modułu niestandardowego, zobacz [Dodawanie typu połączenia](#add-a-connection-type).

## <a name="add-a-connection-type"></a>Dodawanie typu połączenia

Jeśli element Runbook lub konfiguracja DSC łączą się z usługą [](shared-resources/modules.md#custom-modules) zewnętrzną, należy zdefiniować typ połączenia w module niestandardowym nazywanym modułem integracji. Ten moduł zawiera plik metadanych, który określa właściwości typu połączenia i ma nazwę **&lt; ModuleName &gt;-Automation.jsw** folderze modułu skompresowanego pliku **zip.** Ten plik zawiera pola połączenia wymagane do nawiązania połączenia z systemem lub usługą, które reprezentuje moduł. Za pomocą tego pliku można ustawić nazwy pól, typy danych, stan szyfrowania i stan opcjonalny dla typu połączenia. 

Poniższy przykład to szablon w formacie pliku **JSON,** który definiuje właściwości nazwy użytkownika i hasła dla niestandardowego typu połączenia o nazwie `MyModuleConnection` :

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Uzyskiwanie połączenia w konfiguracji runbook lub DSC

Pobieranie połączenia w pliku Runbook lub konfiguracji DSC za pomocą wewnętrznego `Get-AutomationConnection` polecenia cmdlet. To polecenie cmdlet jest preferowane przez polecenie cmdlet, ponieważ pobiera wartości połączenia zamiast informacji `Get-AzAutomationConnection` o połączeniu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

W poniższym przykładzie pokazano, jak używać konta Uruchom jako do uwierzytelniania za pomocą Azure Resource Manager zasobów w twoim runbook. Używa ona zasobu połączenia reprezentującego konto Uruchom jako, które odwołuje się do jednostki usługi opartej na certyfikatach.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

W poniższym przykładzie pokazano sposób uwierzytelniania przy użyciu połączenia Uruchom jako w elementy Runbook języka Python 2 i 3.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resource manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

---

### <a name="graphical-runbook-examples"></a>Przykłady graficznych elementów Runbook

Do graficznego element runbook można dodać działanie dla wewnętrznego `Get-AutomationConnection` polecenia cmdlet. Kliknij prawym przyciskiem myszy połączenie w okienku Biblioteka edytora graficznego i wybierz **polecenie Dodaj do kanwy.**

![dodawanie do kanwy](media/automation-connections/connection-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia obiektu połączenia w graficznym elementie Runbook. W tym przykładzie `Constant value` użyto zestawu danych dla `Get RunAs Connection` działania , które używa obiektu połączenia do uwierzytelniania. W [tym miejscu jest](automation-graphical-authoring-intro.md#use-links-for-workflow) używane łącze potoku, ponieważ zestaw parametrów oczekuje pojedynczego `ServicePrincipalCertificate` obiektu.

![uzyskiwanie połączeń](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o poleceniach cmdlet używanych do uzyskiwania dostępu do połączeń, zobacz [Zarządzanie modułami w](shared-resources/modules.md)Azure Automation .
* Aby uzyskać ogólne informacje na temat runbook, zobacz [Runbook execution in Azure Automation (Wykonywanie runbook w programie Azure Automation](automation-runbook-execution.md)).
* Aby uzyskać szczegółowe informacje o konfiguracjach DSC, [zobacz State Configuration omówienie .](automation-dsc-overview.md)
