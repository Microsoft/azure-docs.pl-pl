---
title: Zarządzanie poświadczeniami w usłudze Azure Automation
description: W tym artykule opisano sposób tworzenia zasobów poświadczeń i używania ich w konfiguracji runbook lub DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6220a44e952aa4d9856ac5fc2077d254103d4a2c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834286"
---
# <a name="manage-credentials-in-azure-automation"></a>Zarządzanie poświadczeniami w usłudze Azure Automation

Zasób poświadczeń usługi Automation zawiera obiekt zawierający poświadczenia zabezpieczeń, takie jak nazwa użytkownika i hasło. W przypadku konfiguracji Runbook i DSC do uwierzytelniania są wymagane polecenia cmdlet akceptujące obiekt [PSCredential.](/dotnet/api/system.management.automation.pscredential) Alternatywnie mogą wyodrębnić nazwę użytkownika i hasło obiektu w celu zapewnienia dla niektórych aplikacji lub usług wymagających `PSCredential` uwierzytelniania.

>[!NOTE]
>Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zaszyfrowane zmienne. Te zasoby są szyfrowane i przechowywane Azure Automation przy użyciu unikatowego klucza generowanego dla każdego konta usługi Automation. Azure Automation przechowuje klucz w zarządzanym przez system Key Vault. Przed zapisaniem bezpiecznego zasobu automatyzacja ładuje klucz z Key Vault, a następnie używa go do szyfrowania zasobu. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>Polecenia cmdlet programu PowerShell używane do uzyskiwania dostępu do poświadczeń

Polecenia cmdlet w poniższej tabeli tworzą poświadczenia usługi Automation i zarządzają nimi za pomocą programu PowerShell. Są one dostępne w ramach modułów [Az](modules.md#az-modules).

| Polecenie cmdlet | Opis |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential) |Pobiera obiekt [CredentialInfo](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo) zawierający metadane dotyczące poświadczeń. Polecenie cmdlet nie pobiera samego `PSCredential` obiektu.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential) |Tworzy nowe poświadczenie usługi Automation. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential) |Usuwa poświadczenia usługi Automation. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential) |Ustawia właściwości istniejącego poświadczenia usługi Automation. |

## <a name="other-cmdlets-used-to-access-credentials"></a>Inne polecenia cmdlet używane do uzyskiwania dostępu do poświadczeń

Polecenia cmdlet w poniższej tabeli służą do uzyskiwania dostępu do poświadczeń w konfiguracjach runbook i DSC. 

| Polecenie cmdlet | Opis |
|:--- |:--- |
| `Get-AutomationPSCredential` |Pobiera obiekt `PSCredential` do użycia w konfiguracji runbook lub DSC. Najczęściej należy używać tego wewnętrznego [polecenia cmdlet](modules.md#internal-cmdlets) zamiast polecenia cmdlet , ponieważ to drugie polecenie pobiera tylko `Get-AzAutomationCredential` informacje o poświadczeniach. Te informacje nie są zwykle przydatne do przekazania do innego polecenia cmdlet. |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) |Pobiera poświadczenia z monitem o nazwę użytkownika i hasło. To polecenie cmdlet jest częścią domyślnego modułu Microsoft.PowerShell.Security. Zobacz [Moduły domyślne.](modules.md#default-modules)|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) | Tworzy zasób poświadczeń. To polecenie cmdlet jest częścią domyślnego modułu platformy Azure. Zobacz [Moduły domyślne.](modules.md#default-modules)|

Aby pobrać `PSCredential` obiekty w kodzie, należy zaimportować `Orchestrator.AssetManagement.Cmdlets` moduł . Aby uzyskać więcej informacji, zobacz [Zarządzanie modułami w programie Azure Automation](modules.md).

```powershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Należy unikać używania zmiennych w `Name` parametrze `Get-AutomationPSCredential` . Ich użycie może skomplikować odnajdywanie zależności między podręcznikami Runbook lub konfiguracjami DSC i zasobami poświadczeń w czasie projektowania.

## <a name="python-functions-that-access-credentials"></a>Funkcje języka Python, które mają dostęp do poświadczeń

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do poświadczeń w runbook języka Python 2 i 3. Aplikacje Runbook języka Python 3 są obecnie dostępne w wersji zapoznawczej.

| Funkcja | Opis |
|:---|:---|
| `automationassets.get_automation_credential` | Pobiera informacje o zasobie poświadczeń. |

> [!NOTE]
> `automationassets`Zaimportuj moduł w górnej części modułu Runbook języka Python, aby uzyskać dostęp do funkcji zasobów.

## <a name="create-a-new-credential-asset"></a>Tworzenie nowego zasobu poświadczeń

Nowy zasób poświadczeń można utworzyć przy użyciu Azure Portal lub Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Tworzenie nowego zasobu poświadczeń przy użyciu Azure Portal

1. Na koncie usługi Automation w okienku po lewej stronie wybierz pozycję **Poświadczenia w** obszarze **Udostępnione zasoby.**
2. Na stronie **Poświadczenia** wybierz pozycję **Dodaj poświadczenie.**
3. W okienku Nowe poświadczenia wprowadź odpowiednią nazwę poświadczeń zgodnie ze standardami nazewnictwa.
4. Wpisz swój identyfikator dostępu w **polu Nazwa** użytkownika.
5. W obu polach hasła wprowadź klucz dostępu do klucza tajnego.

    ![Tworzenie nowego poświadczenia](../media/credentials/credential-create.png)

6. Jeśli pole uwierzytelniania wieloskładnikowego jest zaznaczone, usuń jego zaznaczenie.
7. Kliknij **przycisk Utwórz,** aby zapisać nowy zasób poświadczeń.

> [!NOTE]
> Azure Automation nie obsługuje kont użytkowników, które korzystają z uwierzytelniania wieloskładnikowego.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Tworzenie nowego zasobu poświadczeń za pomocą Windows PowerShell

W poniższym przykładzie pokazano, jak utworzyć nowy zasób poświadczeń usługi Automation. Obiekt jest najpierw tworzony przy użyciu nazwy i hasła, a następnie `PSCredential` używany do tworzenia zasobu poświadczeń. Zamiast tego można użyć polecenia cmdlet , aby monitować użytkownika o `Get-Credential` wpisanie nazwy i hasła.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Uzyskiwanie zasobu poświadczeń

Konfiguracja runbook lub DSC pobiera zasób poświadczeń za pomocą wewnętrznego `Get-AutomationPSCredential` polecenia cmdlet. To polecenie cmdlet pobiera `PSCredential` obiekt, który można użyć z poleceniem cmdlet, które wymaga poświadczeń. Możesz również pobrać właściwości obiektu poświadczeń do użycia indywidualnie. Obiekt ma właściwości nazwy użytkownika i bezpiecznego hasła. 

> [!NOTE]
> Polecenie `Get-AzAutomationCredential` cmdlet nie pobiera `PSCredential` obiektu, który może służyć do uwierzytelniania. Zawiera tylko informacje o poświadczeniach. Jeśli musisz użyć poświadczeń w obiekcie Runbook, musisz pobrać go jako `PSCredential` obiekt przy użyciu . `Get-AutomationPSCredential`

Alternatywnie możesz użyć metody [GetNetworkCredential,](/dotnet/api/system.management.automation.pscredential.getnetworkcredential) aby pobrać obiekt [NetworkCredential](/dotnet/api/system.net.networkcredential) reprezentujący niezabezpieczoną wersję hasła.

### <a name="textual-runbook-example"></a>Przykład tekstowego runbook

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

W poniższym przykładzie pokazano, jak używać poświadczeń programu PowerShell w elementy runbook. Pobiera ona poświadczenia i przypisuje nazwę użytkownika i hasło do zmiennych.

```powershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Możesz również użyć poświadczeń do uwierzytelniania na platformie Azure za [pomocą narzędzia Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) W większości przypadków należy użyć konta Uruchom [jako i](../automation-security-overview.md#run-as-accounts) pobrać połączenie za pomocą [get-AzAutomationConnection.](../automation-connections.md)

```powershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$securePassword)

Connect-AzAccount -Credential $myPsCred
```

# <a name="python-2"></a>[Python 2](#tab/python2)

W poniższym przykładzie pokazano przykład uzyskiwania dostępu do poświadczeń w elementach Runbook języka Python 2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

# <a name="python-3"></a>[Python 3](#tab/python3)

W poniższym przykładzie pokazano przykład uzyskiwania dostępu do poświadczeń w elementach Runbook języka Python 3 (wersja zapoznawcza).

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print (cred["username"])
print (cred["password"])
```

---

### <a name="graphical-runbook-example"></a>Przykład graficznego element runbook

Działanie wewnętrznego polecenia cmdlet można dodać do graficznego element runbook, klikając prawym przyciskiem myszy poświadczenia w okienku Biblioteka edytora graficznego i wybierając `Get-AutomationPSCredential` polecenie **Dodaj do kanwy.**

![Dodawanie polecenia cmdlet poświadczeń do kanwy](../media/credentials/credential-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia poświadczeń w graficznym elementie Runbook. W takim przypadku poświadczenie zapewnia uwierzytelnianie dla podręcznika Runbook w zasobach platformy Azure, zgodnie z opisem w tece Use Azure AD in Azure Automation to authenticate to Azure (Używanie usługi Azure AD w usłudze [Azure Automation do uwierzytelniania na platformie Azure).](../automation-use-azure-ad.md) Pierwsze działanie pobiera poświadczenia, które ma dostęp do subskrypcji platformy Azure. Następnie działanie połączenia konta używa tego poświadczenia do zapewnienia uwierzytelniania dla wszystkich działań, które po nim nas pochodzą. W [tym miejscu jest](../automation-graphical-authoring-intro.md#use-links-for-workflow) używane łącze potoku, ponieważ oczekuje pojedynczego `Get-AutomationPSCredential` obiektu.  

![Przykład przepływu pracy poświadczeń z linkiem potoku](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Używanie poświadczeń w konfiguracji DSC

Konfiguracje DSC w Azure Automation mogą pracować z zasobami poświadczeń przy użyciu programu , ale mogą również przekazywać zasoby `Get-AutomationPSCredential` poświadczeń za pośrednictwem parametrów. Aby uzyskać więcej informacji, zobacz [Kompilowanie konfiguracji w Azure Automation DSC.](../automation-dsc-compile.md#credential-assets)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o poleceniach cmdlet używanych do uzyskiwania dostępu do certyfikatów, zobacz Zarządzanie modułami w [programie Azure Automation](modules.md).
* Aby uzyskać ogólne informacje na temat runbook, zobacz [Runbook execution in Azure Automation (Wykonywanie runbook w programie Azure Automation](../automation-runbook-execution.md)).
* Aby uzyskać szczegółowe informacje o konfiguracjach DSC, [zobacz Azure Automation State Configuration omówienie](../automation-dsc-overview.md).
