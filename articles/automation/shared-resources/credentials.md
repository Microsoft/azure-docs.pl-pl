---
title: Zarządzanie poświadczeniami w usłudze Azure Automation
description: Zasoby poświadczeń w usłudze Azure Automation zawierają poświadczenia zabezpieczeń, które mogą służyć do uwierzytelniania zasobów uzyskiwał dostęp do zasobów przez konfigurację systemu runbook lub DSC. W tym artykule opisano sposób tworzenia zasobów poświadczeń i używania ich w konfiguracji żylicy lub dsc.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59e32087d4489cbb155a9cff7d40094c0606c0cf
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732844"
---
# <a name="manage-credentials-in-azure-automation"></a>Zarządzanie poświadczeniami w usłudze Azure Automation

Zasób poświadczeń automatyzacji przechowuje obiekt zawierający poświadczenia zabezpieczeń, takie jak nazwa użytkownika i hasło. Runbooki i konfiguracje DSC używają poleceń cmdlet, które akceptują obiekt [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) do uwierzytelniania. Alternatywnie można wyodrębnić nazwę użytkownika i `PSCredential` hasło obiektu, aby zapewnić do niektórych aplikacji lub usługi wymagających uwierzytelniania. 

> [!NOTE]
> Bezpieczne zasoby w usłudze Azure Automation obejmują poświadczenia, certyfikaty, połączenia i zaszyfrowane zmienne. Te zasoby są szyfrowane i przechowywane w usłudze Azure Automation przy użyciu unikatowego klucza, który jest generowany dla każdego konta automatyzacji. Ten klucz jest przechowywany w magazynie kluczy. Przed zapisaniem bezpiecznego zasobu klucz jest ładowany z usługi Key Vault, a następnie używany do szyfrowania zasobu.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](../automation-update-azure-modules.md)

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Polecenia cmdlet usługi Azure PowerShell Az używane dla zasobów poświadczeń

W ramach modułu Az programu Azure PowerShell polecenia cmdlet w poniższej tabeli są używane do tworzenia zasobów poświadczeń automatyzacji i zarządzania nimi za pomocą programu Windows PowerShell. Są one dostarczane w [module Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), który jest dostępny do użytku w systemach runbook automation i konfiguracjach DSC. Zobacz [obsługę modułów Az w usłudze Azure Automation](https://docs.microsoft.com/azure/automation/az-modules).

| Polecenie cmdlet | Opis |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Pobiera informacje o zasobie poświadczeń. To polecenie cmdlet nie `PSCredential` zwraca obiektu.  |
| [Nowy-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Tworzy nowe poświadczenia automatyzacji. |
| [Usuń-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Usuwa poświadczenia automatyzacji. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Ustawia właściwości dla istniejących poświadczeń automatyzacji. |

## <a name="activities-used-to-access-credentials"></a>Działania używane do uzyskiwania dostępu do poświadczeń

Działania w poniższej tabeli są używane do uzyskiwania dostępu do poświadczeń w konfiguracjach elementów runbook i DSC.

| Działanie | Opis |
|:--- |:--- |
| `Get-AutomationPSCredential` |Pobiera poświadczenia do użycia w konfiguracji uruchomieniu lub DSC. Poświadczenie jest w `PSCredential` postaci obiektu. Aby uzyskać więcej informacji na temat polecenia cmdlet odpowiadającego temu działaniu, zobacz [Zasoby modułu w usłudze Azure Automation](modules.md). |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Pobiera poświadczenia z monitem o nazwę użytkownika i hasło. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Tworzy zasób poświadczeń. |

Aby pobrać `PSCredential` obiekty w kodzie, można zainstalować dodatek Microsoft Azure Automation ISE dla programu PowerShell ISE. Aby uzyskać więcej informacji, zobacz [Zasoby modułu w usłudze Azure Automation](modules.md).

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

Skrypt można również zaimportować wymagany moduł w razie potrzeby, jak w poniższym przykładzie: 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Należy unikać używania zmiennych w parametrze `Name` `Get-AutomationPSCredential`. Ich użycie może skomplikować odnajdowanie zależności między elementami runbook lub konfiguracjami DSC i zasobami poświadczeń w czasie projektowania.

## <a name="python-2-functions-that-access-credentials"></a>Funkcje języka Python 2, które uzyskują dostęp do poświadczeń

Funkcja w poniższej tabeli służy do uzyskiwania dostępu do poświadczeń w uruchomieniu elementu runbook języka Python 2.

| Funkcja | Opis |
|:---|:---|
| `automationassets.get_automation_credential` | Pobiera informacje o zasobie poświadczeń. |

> [!NOTE]
> Zaimportuj `automationassets` moduł u góry elementów runbook python, aby uzyskać dostęp do funkcji zasobu.

## <a name="creating-a-new-credential-asset"></a>Tworzenie nowego zasobu poświadczeń

Nowy zasób poświadczeń można utworzyć za pomocą witryny Azure Portal lub programu Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Tworzenie nowego zasobu poświadczeń za pomocą witryny Azure Portal

1. Na koncie automatyzacji wybierz pozycję **Poświadczenia** w obszarze **Zasoby udostępnione**.
1. Wybierz **pozycję Dodaj poświadczenie**.
2. W okienku Nowe poświadczenia wprowadź odpowiednią nazwę poświadczeń zgodnie ze standardami nazewnictwa. 
3. Wpisz swój identyfikator dostępu w polu **Nazwa użytkownika.** 
4. W przypadku obu pól hasła wprowadź tajny klucz dostępu.

    ![Tworzenie nowych poświadczeń](../media/credentials/credential-create.png)

5. Jeśli pole uwierzytelniania wieloskładnikowego jest zaznaczone, odznacz go. 
6. Kliknij **przycisk Utwórz,** aby zapisać nowy zasób poświadczeń.

> [!NOTE]
> Usługa Azure Automation nie obsługuje kont użytkowników korzystających z uwierzytelniania wieloskładnikowego.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Tworzenie nowego zasobu poświadczeń za pomocą programu Windows PowerShell

W poniższym przykładzie pokazano, jak utworzyć nowy zasób poświadczeń automatyzacji. Obiekt `PSCredential` jest najpierw tworzony z nazwą i hasłem, a następnie używany do tworzenia zasobu poświadczeń. Zamiast tego można użyć `Get-Credential` polecenia cmdlet, aby poprosić użytkownika o wpisanie nazwy i hasła.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Używanie poświadczeń programu PowerShell

Konfiguracja systemu runbook lub DSC pobiera `Get-AutomationPSCredential` zasób poświadczeń z działaniem. To działanie pobiera `PSCredential` obiekt, którego można użyć z działaniem lub poleceniem cmdlet, które wymaga poświadczenia. Można również pobrać właściwości obiektu poświadczeń do użycia indywidualnie. Obiekt ma właściwości nazwy użytkownika i bezpiecznego hasła. Alternatywnie można użyć [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) metody do pobierania [NetworkCredential](/dotnet/api/system.net.networkcredential) obiektu, który reprezentuje niezabezpieczoną wersję hasła.

> [!NOTE]
> `Get-AzAutomationCredential`nie pobiera `PSCredential` obiektu, który może służyć do uwierzytelniania. Zawiera tylko informacje o poświadczenia. Jeśli konieczne jest użycie poświadczeń w obiekcie `PSCredential` runbook, należy pobrać je jako obiekt przy użyciu programu `Get-AutomationPSCredential`.

### <a name="textual-runbook-example"></a>Przykład tekstowego podstawowego podstawowego podstawowego

W poniższym przykładzie pokazano, jak używać poświadczeń programu PowerShell w życiówce elementów runbook. Pobiera poświadczenia i przypisuje swoją nazwę użytkownika i hasło do zmiennych.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Poświadczenia można również użyć do uwierzytelniania na platformie Azure za pomocą [connect-azaccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). W większości przypadków należy użyć [konta Uruchom jako](../manage-runas-account.md) i pobrać połączenie z [Get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Przykład podręcznika graficznego

Działanie można `Get-AutomationPSCredential` dodać do graficznego podręcznika runbooka, klikając prawym przyciskiem myszy poświadczenia w okienku Biblioteka edytora graficznego i wybierając pozycję **Dodaj do kanwy**.

![Dodawanie poświadczeń do kanwy](../media/credentials/credential-add-canvas.png)

Na poniższej ilustracji przedstawiono przykład użycia poświadczeń w graficznym uruchomieniu bie. W takim przypadku poświadczenia zapewnia uwierzytelnianie dla uruchomieniu do zasobów platformy Azure, zgodnie z opisem w [użyj usługi Azure AD w usłudze Azure Automation do uwierzytelniania na platformie Azure.](../automation-use-azure-ad.md) Pierwsze działanie pobiera poświadczenia, które ma dostęp do subskrypcji platformy Azure. Działanie połączenia konta następnie używa tego poświadczenia, aby zapewnić uwierzytelnianie dla wszelkich działań, które przychodzą po nim. [Łącze potoku](../automation-graphical-authoring-intro.md#links-and-workflow) `Get-AutomationPSCredential` jest w tym miejscu używane, ponieważ oczekuje jednego obiektu.  

![Dodawanie poświadczeń do kanwy](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Używanie poświadczeń w konfiguracji DSC

Podczas gdy konfiguracje DSC w usłudze `Get-AutomationPSCredential`Azure Automation mogą pracować z zasobami poświadczeń przy użyciu , mogą również przekazywać zasoby poświadczeń za pomocą parametrów. Aby uzyskać więcej informacji, zobacz [Kompilowanie konfiguracji w usłudze Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python-2"></a>Używanie poświadczeń w języku Python 2

W poniższym przykładzie przedstawiono przykład uzyskiwania dostępu do poświadczeń w elementach runbook języka Python 2.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o łączach w tworzenia graficznym, zobacz [Łącza w tworzenia graficznym](../automation-graphical-authoring-intro.md#links-and-workflow).
* Aby zrozumieć różne metody uwierzytelniania dla automatyzacji, zobacz [Azure Automation Security](../automation-security-overview.md).
* Aby rozpocząć korzystanie z graficznych śmięty, zobacz [Mój pierwszy graficzny element runbook](../automation-first-runbook-graphical.md).
* Aby rozpocząć pracę z uruchomieniu przepływem pracy programu PowerShell, zobacz [Mój pierwszy program PowerShell .](../automation-first-runbook-textual.md)
* Aby rozpocząć korzystanie z śmięty Python 2, zobacz [Mój pierwszy podręcznik Pythona 2](../automation-first-runbook-textual-python2.md). 
