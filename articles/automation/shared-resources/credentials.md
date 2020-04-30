---
title: Zarządzanie poświadczeniami w Azure Automation
description: Zasoby poświadczeń w Azure Automation zawierają poświadczenia zabezpieczeń, których można użyć do uwierzytelniania w zasobach, do których uzyskuje się dostęp za pomocą elementu Runbook lub konfiguracji DSC. W tym artykule opisano sposób tworzenia zasobów poświadczeń i używania ich w konfiguracji elementu Runbook lub DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4226a625918be378b14e14c55fe4dd4ca5c398d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136689"
---
# <a name="manage-credentials-in-azure-automation"></a>Zarządzanie poświadczeniami w Azure Automation

Zasób poświadczenia usługi Automation zawiera obiekt zawierający poświadczenia zabezpieczeń, takie jak nazwa użytkownika i hasło. Elementy Runbook i konfiguracje DSC używają poleceń cmdlet, które akceptują obiekt [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) na potrzeby uwierzytelniania. Alternatywnie mogą wyodrębnić nazwę użytkownika i hasło `PSCredential` obiektu, aby zapewnić niektórym aplikacji lub usłudze wymaganie uwierzytelniania. 

> [!NOTE]
> Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Te zasoby są szyfrowane i przechowywane w Azure Automation przy użyciu unikatowego klucza wygenerowanego dla każdego konta usługi Automation. Ten klucz jest przechowywany w Key Vault. Przed zapisaniem bezpiecznego elementu zawartości klucz jest ładowany z Key Vault a następnie używany do szyfrowania elementu zawartości.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Azure PowerShell AZ poleceń cmdlet używanych na potrzeby zasobów poświadczeń

W ramach Azure PowerShell AZ module polecenia cmdlet w poniższej tabeli służą do tworzenia zasobów poświadczeń usługi Automation i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane w [module AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), który jest dostępny do użycia w elementach Runbook usługi Automation i konfiguracjach DSC. Zobacz [AZ module Support in Azure Automation](https://docs.microsoft.com/azure/automation/az-modules).

| Polecenie cmdlet | Opis |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Pobiera obiekt [CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) zawierający metadane dotyczące poświadczeń. Polecenie cmdlet nie pobiera samego `PSCredential` obiektu.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Tworzy nowe poświadczenie usługi Automation. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Usuwa poświadczenia usługi Automation. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Ustawia właściwości dla istniejącego poświadczenia usługi Automation. |

## <a name="activities-used-to-access-credentials"></a>Działania używane do uzyskiwania dostępu do poświadczeń

Działania w poniższej tabeli służą do uzyskiwania dostępu do poświadczeń w graficznych elementach Runbook i konfiguracjach DSC. Przykłady użycia działań można znaleźć [w temacie Tworzenie graficzne w Azure Automation](../automation-graphical-authoring-intro.md#activities).

| Działanie | Opis |
|:--- |:--- |
| `Get-AutomationPSCredential` |Pobiera `PSCredential` obiekt do użycia w konfiguracji elementu RUNBOOK lub DSC. Najczęściej należy używać tego działania zamiast `Get-AzAutomationCredential` polecenia cmdlet, ponieważ to drugie pobiera tylko informacje o poświadczeniach. Te informacje nie są zwykle pomocne do przekazania do innego polecenia cmdlet. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Pobiera poświadczenie z monitem o podanie nazwy użytkownika i hasła. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Tworzy zasób poświadczenia. |

Aby pobrać `PSCredential` obiekty w kodzie, należy zaimportować `Orchestrator.AssetManagement.Cmdlets` moduł. Aby uzyskać więcej informacji, zobacz [Zarządzanie modułami w Azure Automation](modules.md).

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Należy unikać używania zmiennych w `Name` parametrze. `Get-AutomationPSCredential` Ich użycie może komplikuje odnajdywanie zależności między elementami Runbook a konfiguracjami DSC i zasobami poświadczeń w czasie projektowania.

## <a name="python-2-functions-that-access-credentials"></a>Funkcje języka Python 2, które uzyskują dostęp do poświadczeń

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do poświadczeń w elemencie Runbook języka Python 2.

| Funkcja | Opis |
|:---|:---|
| `automationassets.get_automation_credential` | Pobiera informacje o zawartości poświadczeń. |

> [!NOTE]
> Zaimportuj `automationassets` moduł w górnej części elementu Runbook języka Python, aby uzyskać dostęp do funkcji zasobów.

## <a name="creating-a-new-credential-asset"></a>Tworzenie nowego zasobu poświadczeń

Nowy zasób poświadczeń można utworzyć przy użyciu Azure Portal lub programu Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Utwórz nowy zasób poświadczeń przy użyciu Azure Portal

1. Z poziomu konta usługi Automation wybierz pozycję **poświadczenia** w obszarze **udostępnione zasoby**.
1. Wybierz pozycję **Dodaj poświadczenie**.
2. W okienku nowe poświadczenie wprowadź odpowiednią nazwę poświadczenia, postępując zgodnie ze standardami nazewnictwa. 
3. W polu **Nazwa użytkownika** wpisz swój identyfikator dostępu. 
4. W przypadku obu pól haseł wprowadź tajny klucz dostępu.

    ![Utwórz nowe poświadczenie](../media/credentials/credential-create.png)

5. Jeśli pole wyboru uwierzytelnianie wieloskładnikowe jest zaznaczone, usuń je. 
6. Kliknij przycisk **Utwórz** , aby zapisać nowy zasób poświadczenia.

> [!NOTE]
> Azure Automation nie obsługuje kont użytkowników korzystających z uwierzytelniania wieloskładnikowego.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Tworzenie nowego zasobu poświadczeń przy użyciu programu Windows PowerShell

Poniższy przykład pokazuje, jak utworzyć nowy zasób poświadczenia usługi Automation. `PSCredential` Obiekt jest najpierw tworzony przy użyciu nazwy i hasła, a następnie używany do tworzenia zasobu poświadczenia. Zamiast tego można użyć `Get-Credential` polecenia cmdlet, aby monitować użytkownika o wpisanie nazwy i hasła.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Korzystanie z poświadczeń programu PowerShell

Element Runbook lub Konfiguracja DSC pobiera zasób poświadczeń z `Get-AutomationPSCredential` działaniem. To działanie pobiera `PSCredential` obiekt, którego można użyć w przypadku działania lub polecenia cmdlet, które wymaga podania poświadczeń. Możesz również pobrać właściwości obiektu Credential, aby użyć go pojedynczo. Obiekt ma właściwości dla nazwy użytkownika i bezpiecznego hasła. Alternatywnie można użyć metody [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) , aby pobrać obiekt [NetworkCredential](/dotnet/api/system.net.networkcredential) , który reprezentuje niezabezpieczoną wersję hasła.

> [!NOTE]
> `Get-AzAutomationCredential`Program nie pobiera `PSCredential` obiektu, który może być używany do uwierzytelniania. Zawiera on tylko informacje o poświadczeniu. Jeśli musisz użyć poświadczeń w elemencie Runbook, musisz pobrać go jako `PSCredential` Obiekt przy użyciu. `Get-AutomationPSCredential`

### <a name="textual-runbook-example"></a>Przykład tekstowego elementu Runbook

Poniższy przykład pokazuje, jak używać poświadczeń programu PowerShell w elemencie Runbook. Pobiera poświadczenia i przypisuje swoją nazwę użytkownika i hasło do zmiennych.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Za pomocą poświadczeń można także uwierzytelniać się na platformie Azure za pomocą programu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). W większości przypadków należy użyć [konta Uruchom jako](../manage-runas-account.md) i pobrać połączenie z poleceniem [Get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Przykład graficzny elementu Runbook

`Get-AutomationPSCredential` Działanie można dodać do graficznego elementu Runbook, klikając prawym przyciskiem myszy poświadczenie w okienku Biblioteka w edytorze graficznym i wybierając pozycję **Dodaj do kanwy**.

![Dodaj poświadczenie do kanwy](../media/credentials/credential-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia poświadczeń w graficznym elemencie Runbook. W takim przypadku poświadczenia udostępniają uwierzytelnianie dla elementu Runbook dla zasobów platformy Azure, zgodnie z opisem w temacie [Korzystanie z usługi Azure AD w Azure Automation do uwierzytelniania na platformie Azure](../automation-use-azure-ad.md). Pierwsze działanie Pobiera poświadczenia, które mają dostęp do subskrypcji platformy Azure. Działanie połączenie z kontem korzysta następnie z tego poświadczenia, aby zapewnić uwierzytelnianie dla wszystkich działań, które są po niej dostępne. [Link potoku](../automation-graphical-authoring-intro.md#links-and-workflow) jest używany w tym `Get-AutomationPSCredential` miejscu, ponieważ oczekuje pojedynczego obiektu.  

![Dodaj poświadczenie do kanwy](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Korzystanie z poświadczeń w konfiguracji DSC

Konfiguracje DSC w Azure Automation mogą współpracować z zasobami poświadczeń przy użyciu `Get-AutomationPSCredential`programu, ale mogą również przekazywać zasoby poświadczeń za pośrednictwem parametrów. Aby uzyskać więcej informacji, zobacz [Kompilowanie konfiguracji w Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python-2"></a>Używanie poświadczeń w języku Python 2

Poniższy przykład przedstawia przykład uzyskiwania dostępu do poświadczeń w elementach Runbook języka Python 2.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat linków w tworzeniu grafiki, zobacz [linki w temacie Tworzenie graficzne](../automation-graphical-authoring-intro.md#links-and-workflow).
* Aby poznać różne metody uwierzytelniania dla automatyzacji, zobacz [Azure Automation zabezpieczenia](../automation-security-overview.md).
* Aby rozpocząć pracę z graficznymi elementami Runbook, zobacz [mój pierwszy graficzny element Runbook](../automation-first-runbook-graphical.md).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz [mój pierwszy element Runbook przepływu pracy programu PowerShell](../automation-first-runbook-textual.md).
* Aby rozpocząć pracę z elementami Runbook języka Python 2, zobacz [pierwszy element Runbook w języku Python 2](../automation-first-runbook-textual-python2.md). 
