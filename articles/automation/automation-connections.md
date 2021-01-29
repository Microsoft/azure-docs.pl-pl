---
title: Zarządzanie połączeniami w Azure Automation
description: W tym artykule opisano, jak zarządzać połączeniami Azure Automation z usługami lub aplikacjami zewnętrznymi oraz jak korzystać z nich w elementach Runbook.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 5f6494eb72084c683ddbb8b27a49acdb1fbc2bfc
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054267"
---
# <a name="manage-connections-in-azure-automation"></a>Zarządzanie połączeniami w Azure Automation

Zasób połączenia Azure Automation zawiera wymienione poniżej informacje. Te informacje są wymagane do nawiązania połączenia z zewnętrzną usługą lub aplikacją z poziomu elementu Runbook lub konfiguracji DSC. 

* Informacje wymagające uwierzytelniania, takie jak nazwa użytkownika i hasło
* Informacje o połączeniu, takie jak adres URL lub port

Zasób połączenia utrzymuje wszystkie właściwości dotyczące łączenia się z konkretną aplikacją, co sprawia, że nie jest konieczne tworzenie wielu zmiennych. Można edytować wartości połączenia w jednym miejscu i można przekazać nazwę połączenia do elementu Runbook lub konfiguracji DSC w jednym parametrze. Element Runbook lub Konfiguracja uzyskują dostęp do właściwości połączenia za pomocą wewnętrznego `Get-AutomationConnection` polecenia cmdlet.

Podczas tworzenia połączenia należy określić typ połączenia. Typ połączenia to szablon, który definiuje zestaw właściwości. Typ połączenia można dodać do Azure Automation przy użyciu modułu integracji z plikiem metadanych. Istnieje również możliwość utworzenia typu połączenia przy użyciu [interfejsu API Azure Automation](/previous-versions/azure/reference/mt163818(v=azure.100)) , jeśli moduł integracji zawiera typ połączenia i zostanie zaimportowany do konta usługi Automation. 

>[!NOTE]
>Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Te zasoby są szyfrowane i przechowywane w Azure Automation przy użyciu unikatowego klucza wygenerowanego dla każdego konta usługi Automation. Azure Automation przechowuje klucz w Key Vault zarządzanych przez system. Przed zapisaniem bezpiecznego elementu zawartości Usługa Automation ładuje klucz z Key Vault a następnie używa go do zaszyfrowania elementu zawartości. 

## <a name="connection-types"></a>Typy połączeń

Azure Automation udostępnia następujące wbudowane typy połączeń:

* `Azure` -Reprezentuje połączenie używane do zarządzania klasycznymi zasobami.
* `AzureServicePrincipal` -Reprezentuje połączenie używane przez konto Uruchom jako platformy Azure.
* `AzureClassicCertificate` -Reprezentuje połączenie używane przez klasyczne konto Uruchom jako platformy Azure.

W większości przypadków nie trzeba tworzyć zasobu połączenia, ponieważ jest on tworzony podczas tworzenia [konta Uruchom jako](automation-security-overview.md).

## <a name="powershell-cmdlets-to-access-connections"></a>Polecenia cmdlet programu PowerShell umożliwiające dostęp do połączeń

Polecenia cmdlet w poniższej tabeli tworzą połączenia automatyzacji i zarządzają nimi przy użyciu programu PowerShell. Są one dostarczane jako część [AZ modules](shared-resources/modules.md#az-modules).

|Polecenie cmdlet|Opis|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|Pobiera informacje o połączeniu.|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|Tworzy nowe połączenie.|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|Usuwa istniejące połączenie.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|Ustawia wartość określonego pola dla istniejącego połączenia.|

## <a name="internal-cmdlets-to-access-connections"></a>Wewnętrzne polecenia cmdlet do uzyskiwania dostępu do połączeń

Wewnętrzne polecenie cmdlet w poniższej tabeli służy do uzyskiwania dostępu do połączeń w elementach Runbook i konfiguracjach DSC. To polecenie cmdlet jest dołączone do modułu globalnego `Orchestrator.AssetManagement.Cmdlets` . Aby uzyskać więcej informacji, zobacz [wewnętrzne polecenia cmdlet](shared-resources/modules.md#internal-cmdlets).

|Wewnętrzne polecenie cmdlet|Opis|
|---|---|
|`Get-AutomationConnection` | Pobiera wartości różnych pól w połączeniu i zwraca je jako [tablicę skrótów](/powershell/module/microsoft.powershell.core/about/about_hash_tables). Następnie można użyć tego obiektu Hashtable z odpowiednimi poleceniami w konfiguracji elementu Runbook lub DSC.|

>[!NOTE]
>Należy unikać używania zmiennych z `Name` parametrem `Get-AutomationConnection` . Użycie zmiennych w tym przypadku może spowodować skomplikowanie wykrywania zależności między elementami Runbook a konfiguracjami DSC i zasobami połączeń w czasie projektowania.

## <a name="python-functions-to-access-connections"></a>Funkcje języka Python do uzyskiwania dostępu do połączeń

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do połączeń w elemencie Runbook języka Python 2 i 3. Elementy Runbook języka Python 3 są obecnie dostępne w wersji zapoznawczej.

| Funkcja | Opis |
|:---|:---|
| `automationassets.get_automation_connection` | Pobiera połączenie. Zwraca słownik z właściwościami połączenia. |

> [!NOTE]
> `automationassets`Aby uzyskać dostęp do funkcji zasobów, należy zaimportować moduł w górnej części elementu Runbook języka Python.

## <a name="create-a-new-connection"></a>Tworzenie nowego połączenia

### <a name="create-a-new-connection-with-the-azure-portal"></a>Utwórz nowe połączenie z Azure Portal

Aby utworzyć nowe połączenie w Azure Portal:

1. Na koncie usługi Automation kliknij pozycję **połączenia** w obszarze **zasoby udostępnione**.
2. Kliknij pozycję **+ Dodaj połączenie** na stronie połączenia.
4. W polu **Typ** w okienku nowe połączenie wybierz typ połączenia do utworzenia. Dostępne są następujące opcje `Azure` , `AzureServicePrincipal` i `AzureClassicCertificate` . 
5. Formularz przedstawia właściwości wybranego typu połączenia. Wypełnij formularz i kliknij przycisk **Utwórz** , aby zapisać nowe połączenie.

### <a name="create-a-new-connection-with-windows-powershell"></a>Tworzenie nowego połączenia przy użyciu programu Windows PowerShell

Utwórz nowe połączenie za pomocą programu Windows PowerShell przy użyciu `New-AzAutomationConnection` polecenia cmdlet. To polecenie cmdlet ma `ConnectionFieldValues` parametr, który oczekuje, że element Hashtable definiuje wartości dla każdej właściwości zdefiniowanej przez typ połączenia.

Poniższe przykładowe polecenia można użyć jako alternatywy dla tworzenia konta Uruchom jako w portalu w celu utworzenia nowego zasobu połączenia.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Podczas tworzenia konta usługi Automation domyślnie zawiera on kilka modułów globalnych wraz z typem połączenia `AzureServicePrincipal` w celu utworzenia `AzureRunAsConnection` zasobu połączenia. Jeśli podjęto próbę utworzenia nowego zasobu połączenia w celu nawiązania połączenia z usługą lub aplikacją z inną metodą uwierzytelniania, operacja nie powiedzie się, ponieważ typ połączenia nie jest już zdefiniowany na koncie usługi Automation. Aby uzyskać więcej informacji na temat tworzenia własnego typu połączenia dla niestandardowego modułu, zobacz [Dodawanie typu połączenia](#add-a-connection-type).

## <a name="add-a-connection-type"></a>Dodaj typ połączenia

Jeśli element Runbook lub Konfiguracja DSC nawiązuje połączenie z usługą zewnętrzną, należy zdefiniować typ połączenia w [module niestandardowym](shared-resources/modules.md#custom-modules) o nazwie moduł integracji. Ten moduł zawiera plik metadanych, który określa właściwości typu połączenia i ma nazwę **&lt; modulename &gt;-Automation.jsna**, znajdującą się w folderze modułu skompresowanego pliku **zip** . Ten plik zawiera pola połączenia, które są wymagane do nawiązania połączenia z systemem lub usługą, którą reprezentuje moduł. Korzystając z tego pliku, można ustawić nazwy pól, typy danych, stan szyfrowania i opcjonalny stan dla typu połączenia. 

Poniższy przykład to szablon w formacie pliku **JSON** , który definiuje właściwości nazwy użytkownika i hasła dla niestandardowego typu połączenia o nazwie `MyModuleConnection` :

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

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Pobieranie połączenia w elemencie Runbook lub konfiguracji DSC

Pobierz połączenie w elemencie Runbook lub konfiguracji DSC przy użyciu wewnętrznego `Get-AutomationConnection` polecenia cmdlet. To polecenie cmdlet jest preferowane za pośrednictwem `Get-AzAutomationConnection` polecenia cmdlet, ponieważ pobiera wartości połączenia zamiast informacji o połączeniu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Poniższy przykład pokazuje, jak używać konta Uruchom jako do uwierzytelniania za pomocą zasobów Azure Resource Manager w elemencie Runbook. Używa zasobu połączenia reprezentującego konto Uruchom jako, które odwołuje się do jednostki usługi opartej na certyfikatach.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

Poniższy przykład przedstawia sposób uwierzytelniania przy użyciu połączenia Uruchom jako w elemencie Runbook języka Python 2 i 3.

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

Możesz dodać działanie dla wewnętrznego `Get-AutomationConnection` polecenia cmdlet do graficznego elementu Runbook. Kliknij prawym przyciskiem myszy połączenie w okienku Biblioteka w edytorze graficznym i wybierz polecenie **Dodaj do kanwy**.

![Dodaj do kanwy](media/automation-connections/connection-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia obiektu połączenia w graficznym elemencie Runbook. W tym przykładzie używany jest `Constant value` zestaw danych dla `Get RunAs Connection` działania, który używa obiektu połączenia do uwierzytelniania. [Link potoku](automation-graphical-authoring-intro.md#use-links-for-workflow) jest używany w tym miejscu, ponieważ `ServicePrincipalCertificate` zestaw parametrów oczekuje pojedynczego obiektu.

![Pobierz połączenia](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat poleceń cmdlet służących do uzyskiwania dostępu do połączeń, zobacz [Zarządzanie modułami w Azure Automation](shared-resources/modules.md).
* Aby uzyskać ogólne informacje o elementach Runbook, zobacz [wykonywanie elementów Runbook w Azure Automation](automation-runbook-execution.md).
* Aby uzyskać szczegółowe informacje na temat konfiguracji DSC, zobacz [Konfiguracja stanu — przegląd](automation-dsc-overview.md).