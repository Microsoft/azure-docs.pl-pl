---
title: Zarządzanie poświadczeniami w usłudze Azure Automation
description: W tym artykule opisano sposób tworzenia zasobów poświadczeń i używania ich w konfiguracji elementu Runbook lub DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 9b9e42d55a982aeb55d7c9e26f7b1a6cbca32e0a
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052808"
---
# <a name="manage-credentials-in-azure-automation"></a>Zarządzanie poświadczeniami w usłudze Azure Automation

Zasób poświadczenia usługi Automation zawiera obiekt zawierający poświadczenia zabezpieczeń, takie jak nazwa użytkownika i hasło. Elementy Runbook i konfiguracje DSC używają poleceń cmdlet, które akceptują obiekt [PSCredential](/dotnet/api/system.management.automation.pscredential) na potrzeby uwierzytelniania. Alternatywnie mogą wyodrębnić nazwę użytkownika i hasło `PSCredential` obiektu, aby zapewnić niektórym aplikacji lub usłudze wymaganie uwierzytelniania.

>[!NOTE]
>Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Te zasoby są szyfrowane i przechowywane w Azure Automation przy użyciu unikatowego klucza wygenerowanego dla każdego konta usługi Automation. Azure Automation przechowuje klucz w Key Vault zarządzanych przez system. Przed zapisaniem bezpiecznego elementu zawartości Usługa Automation ładuje klucz z Key Vault a następnie używa go do zaszyfrowania elementu zawartości. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>Polecenia cmdlet programu PowerShell służące do uzyskiwania dostępu do poświadczeń

Polecenia cmdlet w poniższej tabeli tworzą i zarządzają poświadczeniami usługi Automation przy użyciu programu PowerShell. Są one dostarczane jako część [AZ modules](modules.md#az-modules).

| Polecenie cmdlet | Opis |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential) |Pobiera obiekt [CredentialInfo](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo) zawierający metadane dotyczące poświadczeń. Polecenie cmdlet nie pobiera `PSCredential` samego obiektu.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential) |Tworzy nowe poświadczenie usługi Automation. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential) |Usuwa poświadczenia usługi Automation. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential) |Ustawia właściwości dla istniejącego poświadczenia usługi Automation. |

## <a name="other-cmdlets-used-to-access-credentials"></a>Inne polecenia cmdlet służące do uzyskiwania dostępu do poświadczeń

Polecenia cmdlet w poniższej tabeli służą do uzyskiwania dostępu do poświadczeń w elementach Runbook i konfiguracjach DSC. 

| Polecenie cmdlet | Opis |
|:--- |:--- |
| `Get-AutomationPSCredential` |Pobiera `PSCredential` obiekt do użycia w konfiguracji elementu Runbook lub DSC. Najczęściej należy używać tego [wewnętrznego polecenia cmdlet](modules.md#internal-cmdlets) zamiast `Get-AzAutomationCredential` polecenia cmdlet, ponieważ to drugie pobiera tylko informacje o poświadczeniach. Te informacje nie są zwykle pomocne do przekazania do innego polecenia cmdlet. |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) |Pobiera poświadczenie z monitem o podanie nazwy użytkownika i hasła. To polecenie cmdlet jest częścią domyślnego modułu Microsoft. PowerShell. Security. Zobacz [moduły domyślne](modules.md#default-modules).|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) | Tworzy zasób poświadczenia. To polecenie cmdlet jest częścią domyślnego modułu platformy Azure. Zobacz [moduły domyślne](modules.md#default-modules).|

Aby pobrać `PSCredential` obiekty w kodzie, należy zaimportować `Orchestrator.AssetManagement.Cmdlets` moduł. Aby uzyskać więcej informacji, zobacz [Zarządzanie modułami w Azure Automation](modules.md).

```powershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Należy unikać używania zmiennych w `Name` parametrze `Get-AutomationPSCredential` . Ich użycie może komplikuje odnajdywanie zależności między elementami Runbook a konfiguracjami DSC i zasobami poświadczeń w czasie projektowania.

## <a name="python-functions-that-access-credentials"></a>Funkcje języka Python, które uzyskują dostęp do poświadczeń

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do poświadczeń w elemencie Runbook języka Python 2 i 3. Elementy Runbook języka Python 3 są obecnie dostępne w wersji zapoznawczej.

| Funkcja | Opis |
|:---|:---|
| `automationassets.get_automation_credential` | Pobiera informacje o zawartości poświadczeń. |

> [!NOTE]
> Zaimportuj `automationassets` moduł w górnej części elementu Runbook języka Python, aby uzyskać dostęp do funkcji zasobów.

## <a name="create-a-new-credential-asset"></a>Utwórz nowy zasób poświadczeń

Nowy zasób poświadczeń można utworzyć przy użyciu Azure Portal lub programu Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Utwórz nowy zasób poświadczeń przy użyciu Azure Portal

1. Na koncie usługi Automation w okienku po lewej stronie wybierz pozycję **poświadczenia** w obszarze **udostępnione zasoby**.
2. Na stronie **poświadczenia** wybierz pozycję **Dodaj poświadczenie**.
3. W okienku nowe poświadczenie wprowadź odpowiednią nazwę poświadczenia, postępując zgodnie ze standardami nazewnictwa.
4. W polu **Nazwa użytkownika** wpisz swój identyfikator dostępu.
5. W przypadku obu pól haseł wprowadź tajny klucz dostępu.

    ![Utwórz nowe poświadczenie](../media/credentials/credential-create.png)

6. Jeśli pole wyboru uwierzytelnianie wieloskładnikowe jest zaznaczone, usuń je.
7. Kliknij przycisk **Utwórz** , aby zapisać nowy zasób poświadczenia.

> [!NOTE]
> Azure Automation nie obsługuje kont użytkowników korzystających z uwierzytelniania wieloskładnikowego.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Tworzenie nowego zasobu poświadczeń przy użyciu programu Windows PowerShell

Poniższy przykład pokazuje, jak utworzyć nowy zasób poświadczenia usługi Automation. `PSCredential`Obiekt jest najpierw tworzony przy użyciu nazwy i hasła, a następnie używany do tworzenia zasobu poświadczenia. Zamiast tego można użyć `Get-Credential` polecenia cmdlet, aby monitować użytkownika o wpisanie nazwy i hasła.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Pobierz zasób poświadczeń

Element Runbook lub Konfiguracja DSC pobiera zasób poświadczeń z wewnętrznym `Get-AutomationPSCredential` poleceniem cmdlet. To polecenie cmdlet pobiera `PSCredential` obiekt, którego można użyć z poleceniem cmdlet, które wymaga podania poświadczeń. Możesz również pobrać właściwości obiektu Credential, aby użyć go pojedynczo. Obiekt ma właściwości dla nazwy użytkownika i bezpiecznego hasła. 

> [!NOTE]
> `Get-AzAutomationCredential`Polecenie cmdlet nie pobiera `PSCredential` obiektu, który może być używany do uwierzytelniania. Zawiera on tylko informacje o poświadczeniu. Jeśli musisz użyć poświadczeń w elemencie Runbook, musisz pobrać go jako `PSCredential` Obiekt przy użyciu `Get-AutomationPSCredential` .

Alternatywnie można użyć metody [GetNetworkCredential](/dotnet/api/system.management.automation.pscredential.getnetworkcredential) , aby pobrać obiekt [NetworkCredential](/dotnet/api/system.net.networkcredential) , który reprezentuje niezabezpieczoną wersję hasła.

### <a name="textual-runbook-example"></a>Przykład tekstowego elementu Runbook

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Poniższy przykład pokazuje, jak używać poświadczeń programu PowerShell w elemencie Runbook. Pobiera poświadczenia i przypisuje swoją nazwę użytkownika i hasło do zmiennych.

```powershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Za pomocą poświadczeń można także uwierzytelniać się na platformie Azure za pomocą programu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). W większości przypadków należy użyć [konta Uruchom jako](../automation-security-overview.md#run-as-accounts) i pobrać połączenie z poleceniem [Get-AzAutomationConnection](../automation-connections.md).

```powershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$securePassword)

Connect-AzAccount -Credential $myPsCred
```

# <a name="python-2"></a>[Python 2](#tab/python2)

Poniższy przykład przedstawia przykład uzyskiwania dostępu do poświadczeń w elementach Runbook języka Python 2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

# <a name="python-3"></a>[Python 3](#tab/python3)

Poniższy przykład przedstawia przykład uzyskiwania dostępu do poświadczeń w elementach Runbook języka Python 3 (wersja zapoznawcza).

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print (cred["username"])
print (cred["password"])
```

---

### <a name="graphical-runbook-example"></a>Przykład graficzny elementu Runbook

Możesz dodać działanie dla wewnętrznego `Get-AutomationPSCredential` polecenia cmdlet do graficznego elementu Runbook, klikając prawym przyciskiem myszy poświadczenie w okienku Biblioteka w edytorze graficznym i wybierając pozycję **Dodaj do kanwy**.

![Dodaj polecenie cmdlet do kanwy](../media/credentials/credential-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia poświadczeń w graficznym elemencie Runbook. W takim przypadku poświadczenia udostępniają uwierzytelnianie dla elementu Runbook dla zasobów platformy Azure, zgodnie z opisem w temacie [Korzystanie z usługi Azure AD w Azure Automation do uwierzytelniania na platformie Azure](../automation-use-azure-ad.md). Pierwsze działanie Pobiera poświadczenia, które mają dostęp do subskrypcji platformy Azure. Działanie połączenie z kontem korzysta następnie z tego poświadczenia, aby zapewnić uwierzytelnianie dla wszystkich działań, które są po niej dostępne. [Link potoku](../automation-graphical-authoring-intro.md#use-links-for-workflow) jest używany w tym miejscu, ponieważ `Get-AutomationPSCredential` oczekuje pojedynczego obiektu.  

![Przykład przepływu pracy poświadczeń przy użyciu linku potoku](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Używanie poświadczeń w konfiguracji DSC

Konfiguracje DSC w Azure Automation mogą współpracować z zasobami poświadczeń przy użyciu programu `Get-AutomationPSCredential` , ale mogą również przekazywać zasoby poświadczeń za pośrednictwem parametrów. Aby uzyskać więcej informacji, zobacz [Kompilowanie konfiguracji w Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat poleceń cmdlet służących do uzyskiwania dostępu do certyfikatów, zobacz [Zarządzanie modułami w Azure Automation](modules.md).
* Aby uzyskać ogólne informacje o elementach Runbook, zobacz [wykonywanie elementów Runbook w Azure Automation](../automation-runbook-execution.md).
* Aby uzyskać szczegółowe informacje na temat konfiguracji DSC, zobacz [Konfiguracja stanu Azure Automation przegląd](../automation-dsc-overview.md).