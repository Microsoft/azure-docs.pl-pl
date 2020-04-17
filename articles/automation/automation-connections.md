---
title: Zasoby połączeń w usłudze Azure Automation
description: Zasoby połączeń w usłudze Azure Automation zawierają informacje wymagane do nawiązania połączenia z usługą lub aplikacją z konfiguracji systemu runbook lub DSC. W tym artykule wyjaśniono szczegóły połączeń i sposób pracy z nimi zarówno w tekście, jak i w grafice.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: d1ce59322b0cbfaf5a4f7b31371f0d0929a3a3ab
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457726"
---
# <a name="connection-assets-in-azure-automation"></a>Zasoby połączeń w usłudze Azure Automation

Zasób połączenia automatyzacji zawiera informacje wymagane do nawiązania połączenia z usługą lub aplikacją z konfiguracji systemu runbook lub DSC. Może to obejmować informacje wymagane do uwierzytelniania, takie jak nazwa użytkownika i hasło oprócz informacji o połączeniu, takich jak adres URL lub port. Wartość połączenia jest utrzymanie wszystkich właściwości do łączenia się z określoną aplikacją w jednym zasobie, w przeciwieństwie do tworzenia wielu zmiennych. Użytkownik może edytować wartości połączenia w jednym miejscu i można przekazać nazwę połączenia do konfiguracji wiązki uruchomieniu lub DSC w jednym parametrze. Właściwości połączenia są dostępne w konfiguracji elementu runbook lub DSC z działaniem. `Get-AutomationConnection`

Podczas tworzenia połączenia należy określić typ połączenia. Typ połączenia jest szablonem definiujący zestaw właściwości. Połączenie definiuje wartości dla każdej właściwości zdefiniowanej w jego typie połączenia. Typy połączeń są dodawane do usługi Azure Automation w modułach integracji lub tworzone za pomocą [interfejsu API automatyzacji platformy Azure,](/previous-versions/azure/reference/mt163818(v=azure.100)) jeśli moduł integracji zawiera typ połączenia i jest importowany do konta automatyzacji. W przeciwnym razie należy utworzyć plik metadanych, aby określić typ połączenia automatyzacji. Aby uzyskać więcej informacji na ten temat, zobacz [Moduły integracji](automation-integration-modules.md).

>[!NOTE]
>Bezpieczne zasoby w usłudze Azure Automation obejmują poświadczenia, certyfikaty, połączenia i zaszyfrowane zmienne. Te zasoby są szyfrowane i przechowywane w usłudze Azure Automation przy użyciu unikatowego klucza, który jest generowany dla każdego konta automatyzacji. Ten klucz jest przechowywany w zarządzanym przez system magazynie kluczy. Przed zapisaniem bezpiecznego zasobu klucz jest ładowany z usługi Key Vault, a następnie używany do szyfrowania zasobu. Ten proces jest zarządzany przez usługę Azure Automation.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="connection-types"></a>Typy połączeń

Istnieją trzy typy wbudowanych połączeń dostępnych w usłudze Azure Automation:

* **Platforma Azure** — to połączenie może służyć do zarządzania zasobami klasycznymi.
* **AzureClassicCertificate** — to połączenie jest używane przez konto **AzureClassicRunAs.**
* **AzureServicePrincipal** — to połączenie jest używane przez konto **AzureRunAs.**

W większości przypadków nie trzeba tworzyć zasobu połączenia, ponieważ jest on tworzony podczas tworzenia [konta Uruchom jako](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Polecenia cmdlet programu Windows PowerShell

Polecenia cmdlet w poniższej tabeli są używane do tworzenia połączeń automatyzacji i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane jako część [modułu Azure PowerShell](/powershell/azure/overview), który jest dostępny do użycia w systemach runbook automatyzacji i konfiguracji DSC.

|Polecenie cmdlet|Opis|
|---|---|
|[Połączenie Get-AzAutomation](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Pobiera połączenie. Zawiera tabelę mieszania z wartościami pól połączenia.|
|[Nowe połączenie AzAutomation](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Tworzy nowe połączenie.|
|[Usuń-AzAutomationPołączenie](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Usuwa istniejące połączenie.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Ustawia wartość określonego pola dla istniejącego połączenia.|

## <a name="activities"></a>Działania

Działania w poniższej tabeli są używane do uzyskiwania dostępu do połączeń w konfiguracji elementów runbook lub DSC.

|Działania|Opis|
|---|---|
|`Get-AutomationConnection` | Pobiera połączenie do użycia. Zwraca wartość mieszania z właściwościami połączenia.|

>[!NOTE]
>Unikaj używania zmiennych `Get-AutomationConnection`z parametrem `Name` . Użycie tego parametru może skomplikować odnajdowanie zależności między elementami runbook lub konfiguracjami DSC i zasobami połączenia w czasie projektowania.

## <a name="python-2-functions"></a>Funkcje Pythona 2

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do połączeń w uruchomieniu języka Python 2.

| Funkcja | Opis |
|:---|:---|
| `automationassets.get_automation_connection` | Pobiera połączenie. Zwraca słownik z właściwościami połączenia. |

> [!NOTE]
> Aby uzyskać `automationassets` dostęp do funkcji zasobu, należy zaimportować moduł u góry podręcznika python.

## <a name="creating-a-new-connection"></a>Tworzenie nowego połączenia

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Aby utworzyć nowe połączenie z witryną Azure portal

1. Na koncie automatyzacji kliknij część **Zasoby,** aby otworzyć blok **Zasoby.**
2. Kliknij część **Połączenia,** aby otworzyć blok **Połączenia.**
3. Kliknij **pozycję Dodaj połączenie** w górnej części bloku.
4. Z listy rozwijanej **Typ** wybierz typ połączenia, które chcesz utworzyć. Formularz przedstawi właściwości dla tego określonego typu.
5. Wypełnij formularz i kliknij przycisk **Utwórz,** aby zapisać nowe połączenie.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Aby utworzyć nowe połączenie z programem Windows PowerShell

Utwórz nowe połączenie z programem `New-AzAutomationConnection` Windows PowerShell przy użyciu polecenia cmdlet. To polecenie cmdlet `ConnectionFieldValues` ma parametr o nazwie, który oczekuje [skrótu](https://technet.microsoft.com/library/hh847780.aspx) definiowania wartości dla każdej właściwości zdefiniowanych przez typ połączenia.

Poniższe przykładowe polecenia można użyć jako alternatywy dla utworzenia konta Uruchom jako z portalu w celu utworzenia nowego zasobu połączenia.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Skrypt można użyć do utworzenia zasobu połączenia, ponieważ podczas tworzenia konta automatyzacji automatycznie zawiera kilka modułów globalnych domyślnie wraz z typem `AzureServicePrincipal` połączenia, aby utworzyć zasób `AzureRunAsConnection` połączenia. Jest to ważne, aby pamiętać, ponieważ w przypadku próby utworzenia nowego zasobu połączenia, aby połączyć się z usługą lub aplikacją przy użyciu innej metody uwierzytelniania, zakończy się niepowodzeniem, ponieważ typ połączenia nie jest jeszcze zdefiniowany na koncie automatyzacji. Aby uzyskać więcej informacji na temat tworzenia własnego typu połączenia dla niestandardowego lub modułu z [Galerii programu PowerShell,](https://www.powershellgallery.com)zobacz [Moduły integracji](automation-integration-modules.md).

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Korzystanie z połączenia w konfiguracji systemu runbook lub DSC

Pobieranie połączenia w konfiguracji wiązki ś.d. lub DSC za pomocą polecenia `Get-AutomationConnection` cmdlet. Nie można użyć `Get-AzAutomationConnection` działania. To działanie pobiera wartości różnych pól w połączeniu i zwraca je jako [hashtable](https://go.microsoft.com/fwlink/?LinkID=324844). Ten skrót może być następnie używany z odpowiednimi poleceniami w konfiguracji runbook lub DSC.

### <a name="textual-runbook-sample"></a>Przykładowy tekstowy podręcznik

Poniższe przykładowe polecenia pokazują, jak używać konta Uruchom jako wymienione wcześniej, aby uwierzytelnić się przy użyciu zasobów usługi Azure Resource Manager w bieślicie. Używa zasobu połączenia reprezentującego konto Uruchom jako, które odwołuje się do jednostki usługi opartej na certyfikatach, a nie poświadczeń.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> W przypadku niegraficzowych wiązków czągo programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` aliasów [connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Można użyć tych poleceń cmdlet lub można [zaktualizować moduły](automation-update-azure-modules.md) na koncie automatyzacji do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli właśnie utworzono nowe konto automatyzacji.

### <a name="graphical-runbook-samples"></a>Przykłady graficznego ekscesu

Działanie można dodać do graficznego podręcznika runbooka, klikając prawym przyciskiem myszy połączenie w okienku Biblioteka edytora graficznego i wybierając pozycję **Dodaj do kanwy**. `Get-AutomationConnection`

![dodaj do kanwy](media/automation-connections/connection-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia połączenia w graficznym uruchomieniu wiązku. Jest to ten sam przykład, jak pokazano powyżej do uwierzytelniania przy użyciu uruchom jako konto z tekstowego podstawowego podstawowego. W tym przykładzie `Constant value` użyto `Get RunAs Connection` zestawu danych dla działania, które używa obiektu połączenia do uwierzytelniania. [Łącze potoku](automation-graphical-authoring-intro.md#links-and-workflow) jest `ServicePrincipalCertificate` w tym miejscu używane, ponieważ zestaw parametrów oczekuje pojedynczego obiektu.

![uzyskać połączenia](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Przykład owy podręcznik python 2

W poniższym przykładzie pokazano, jak uwierzytelnić przy użyciu połączenia Uruchom jako w uruchomieniu 2 elementów runbook języka Python.

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

- Przejrzyj [łącza w tworzenia graficznym,](automation-graphical-authoring-intro.md#links-and-workflow) aby zrozumieć, jak kierować i kontrolować przepływ logiki w elementach runbook.
* Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Aby dowiedzieć się więcej na temat korzystania przez usługę Azure Automation z modułów programu PowerShell i najlepszych rozwiązań dotyczących tworzenia własnych modułów programu PowerShell do pracy jako moduły integracji w ramach automatyzacji platformy Azure, zobacz [Moduły integracji.](automation-integration-modules.md)