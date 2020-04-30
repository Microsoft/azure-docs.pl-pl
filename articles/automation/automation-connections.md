---
title: Zarządzanie połączeniami w Azure Automation
description: Zasoby połączeń w Azure Automation zawierają informacje wymagane do nawiązania połączenia z zewnętrzną usługą lub aplikacją z poziomu elementu Runbook lub konfiguracji DSC. W tym artykule opisano szczegółowe informacje o połączeniach i sposobach pracy z nimi zarówno w tworzeniu tekstu, jak i w formie graficznej.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 39a41a60f4cabe995ebd458c4b906438d1e31bde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82097119"
---
# <a name="manage-connections-in-azure-automation"></a>Zarządzanie połączeniami w Azure Automation

Zasób połączenia usługi Automation zawiera informacje wymagane do nawiązania połączenia z zewnętrzną usługą lub aplikacją z poziomu elementu Runbook lub konfiguracji DSC. Może to obejmować informacje wymagane do uwierzytelniania, takie jak nazwa użytkownika i hasło, a także informacje dotyczące połączenia, takie jak adres URL lub port. Wartość połączenia zachowuje wszystkie właściwości łączenia się z konkretną aplikacją w jednym z zasobów, zamiast tworzyć wiele zmiennych. Użytkownik może edytować wartości połączenia w jednym miejscu i można przekazać nazwę połączenia do elementu Runbook lub konfiguracji DSC w jednym parametrze. Dostęp do właściwości połączenia można uzyskać za pomocą `Get-AutomationConnection` działania elementu Runbook lub konfiguracji DSC.

Podczas tworzenia połączenia należy określić typ połączenia. Typ połączenia to szablon, który definiuje zestaw właściwości. Połączenie definiuje wartości dla każdej właściwości zdefiniowanej w jej typie połączenia. Typy połączeń są dodawane do Azure Automation w modułach integracji lub tworzone za pomocą [interfejsu API Azure Automation](/previous-versions/azure/reference/mt163818(v=azure.100)) , jeśli moduł integracji zawiera typ połączenia i zostanie zaimportowany do konta usługi Automation. W przeciwnym razie należy utworzyć plik metadanych, aby określić typ połączenia automatyzacji. Aby uzyskać więcej informacji na ten temat, zobacz [moduły integracji](automation-integration-modules.md).

>[!NOTE]
>Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Te zasoby są szyfrowane i przechowywane w Azure Automation przy użyciu unikatowego klucza wygenerowanego dla każdego konta usługi Automation. Ten klucz jest przechowywany w Key Vault zarządzanym przez system. Przed zapisaniem bezpiecznego elementu zawartości klucz jest ładowany z Key Vault a następnie używany do szyfrowania elementu zawartości. Ten proces jest zarządzany przez Azure Automation.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="connection-types"></a>Typy połączeń

Istnieją trzy typy wbudowanych połączeń dostępnych w Azure Automation:

* **Azure** — to połączenie może służyć do zarządzania klasycznymi zasobami.
* **AzureClassicCertificate** — to połączenie jest używane przez konto **AzureClassicRunAs** .
* **AzureServicePrincipal** — to połączenie jest używane przez konto **AzureRunAs** .

W większości przypadków nie trzeba tworzyć zasobu połączenia, ponieważ jest on tworzony podczas tworzenia [konta Uruchom jako](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Polecenia cmdlet programu Windows PowerShell

Polecenia cmdlet w poniższej tabeli służą do tworzenia połączeń automatyzacji i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane jako część [modułu Azure PowerShell](/powershell/azure/overview), który jest dostępny do użycia w elementach Runbook usługi Automation i konfiguracjach DSC.

|Polecenie cmdlet|Opis|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Pobiera połączenie. Zawiera tablicę skrótów z wartościami pól połączeń.|
|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Tworzy nowe połączenie.|
|[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Usuwa istniejące połączenie.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Ustawia wartość określonego pola dla istniejącego połączenia.|

## <a name="activities"></a>Działania

Działania w poniższej tabeli służą do uzyskiwania dostępu do połączeń w elemencie Runbook lub konfiguracji DSC.

|Działania|Opis|
|---|---|
|`Get-AutomationConnection` | Pobiera połączenie do użycia. Zwraca tablicę skrótów z właściwościami połączenia.|

>[!NOTE]
>Należy unikać używania zmiennych z `Name` parametrem `Get-AutomationConnection`. Użycie tego parametru może utrudnić odnajdywanie zależności między elementami Runbook lub konfiguracjami DSC oraz zasobami połączeń w czasie projektowania.

## <a name="python-2-functions"></a>Funkcje języka Python 2

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do połączeń w elemencie Runbook języka Python 2.

| Funkcja | Opis |
|:---|:---|
| `automationassets.get_automation_connection` | Pobiera połączenie. Zwraca słownik z właściwościami połączenia. |

> [!NOTE]
> Aby uzyskać dostęp do `automationassets` funkcji zasobów, należy zaimportować moduł w górnej części elementu Runbook języka Python.

## <a name="creating-a-new-connection"></a>Tworzenie nowego połączenia

### <a name="create-a-new-connection-with-the-azure-portal"></a>Utwórz nowe połączenie z Azure Portal

1. Na koncie usługi Automation kliknij składnik **zasoby** , aby otworzyć blok **zasoby** .
2. Kliknij część **połączenia** , aby otworzyć blok **połączenia** .
3. Kliknij pozycję **Dodaj połączenie** w górnej części bloku.
4. Z listy rozwijanej **Typ** wybierz typ połączenia, które chcesz utworzyć. Formularz będzie widział właściwości tego określonego typu.
5. Wypełnij formularz i kliknij przycisk **Utwórz** , aby zapisać nowe połączenie.

### <a name="create-a-new-connection-with-windows-powershell"></a>Tworzenie nowego połączenia przy użyciu programu Windows PowerShell

Utwórz nowe połączenie za pomocą programu Windows PowerShell przy `New-AzAutomationConnection` użyciu polecenia cmdlet. To polecenie cmdlet ma parametr o `ConnectionFieldValues` nazwie, który oczekuje, że element [Hashtable](https://technet.microsoft.com/library/hh847780.aspx) definiuje wartości dla każdej właściwości zdefiniowanej przez typ połączenia.

Poniższe przykładowe polecenia można użyć jako alternatywy dla tworzenia konta Uruchom jako w portalu w celu utworzenia nowego zasobu połączenia.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Podczas tworzenia konta usługi Automation domyślnie zawiera on kilka modułów globalnych wraz z typem `AzureServicePrincipal` połączenia w celu utworzenia zasobu `AzureRunAsConnection` połączenia. Jeśli podjęto próbę utworzenia nowego zasobu połączenia w celu nawiązania połączenia z usługą lub aplikacją z inną metodą uwierzytelniania, operacja nie powiedzie się, ponieważ typ połączenia nie jest już zdefiniowany na koncie usługi Automation. Aby uzyskać więcej informacji na temat tworzenia własnego typu połączenia dla niestandardowego modułu [Galeria programu PowerShell](https://www.powershellgallery.com) , zobacz [moduły integracji](automation-integration-modules.md).

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Korzystanie z połączenia w elemencie Runbook lub konfiguracji DSC

Pobierz połączenie w elemencie Runbook lub konfiguracji DSC przy użyciu `Get-AutomationConnection` polecenia cmdlet. Nie można użyć `Get-AzAutomationConnection` działania. To działanie pobiera wartości różnych pól w połączeniu i zwraca je jako [tablicę skrótów](https://go.microsoft.com/fwlink/?LinkID=324844). Tego elementu Hashtable można następnie użyć z odpowiednimi poleceniami w elemencie Runbook lub konfiguracji DSC.

### <a name="textual-runbook-sample"></a>Przykład tekstu elementu Runbook

Następujące przykładowe polecenia pokazują, jak używać konta Uruchom jako wymienionego wcześniej, aby uwierzytelniać się przy użyciu zasobów Azure Resource Manager w elemencie Runbook. Używa ona zasobu Connection reprezentującego konto Uruchom jako, które odwołuje się do nazwy głównej usługi opartej na certyfikatach, a nie poświadczeń.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> Dla niegraficznych elementów Runbook programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są aliasami dla polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Możesz użyć tych poleceń cmdlet lub [zaktualizować moduły](automation-update-azure-modules.md) na koncie usługi Automation do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli utworzono nowe konto usługi Automation.

### <a name="graphical-runbook-samples"></a>Graficzne przykłady elementów Runbook

Aby dodać `Get-AutomationConnection` działanie do graficznego elementu Runbook, kliknij prawym przyciskiem myszy połączenie w okienku Biblioteka w edytorze graficznym i wybierz polecenie **Dodaj do kanwy**.

![Dodaj do kanwy](media/automation-connections/connection-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia połączenia w graficznym elemencie Runbook. Jest to ten sam przykład, jak pokazano powyżej, aby uwierzytelniać przy użyciu konta Uruchom jako z tekstem elementu Runbook. W tym przykładzie zastosowano zestaw `Constant value` danych dla `Get RunAs Connection` działania, które używa obiektu połączenia do uwierzytelniania. [Link potoku](automation-graphical-authoring-intro.md#links-and-workflow) jest używany w tym miejscu `ServicePrincipalCertificate` , ponieważ zestaw parametrów oczekuje pojedynczego obiektu.

![Pobierz połączenia](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Przykład elementu Runbook języka Python 2

Poniższy przykład pokazuje sposób uwierzytelniania przy użyciu połączenia Uruchom jako w elemencie Runbook języka Python 2.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resoruce manager """
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

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [linki w temacie Tworzenie graficzne](automation-graphical-authoring-intro.md#links-and-workflow) , aby zrozumieć, jak skierować i kontrolować przepływ logiki w elementach Runbook.
* Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Aby dowiedzieć się więcej Azure Automation o używaniu modułów programu PowerShell i najlepszych rozwiązaniach w zakresie tworzenia własnych modułów programu PowerShell do pracy jako modułów integracji w programie Azure Automation, zobacz [moduły integracji](automation-integration-modules.md).