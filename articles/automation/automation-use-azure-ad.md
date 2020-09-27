---
title: Użycie usługi Azure AD w usłudze Azure Automation do uwierzytelniania na platformie Azure
description: W tym artykule opisano, jak używać usługi Azure AD w ramach Azure Automation jako dostawcy do uwierzytelniania na platformie Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: bbd1f977b548c2b8e7980709ea125c07e22b1fa2
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400676"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Uwierzytelnianie na platformie Azure za pomocą usługi Azure AD

Usługa [Azure Active Directory (AD)](../active-directory/fundamentals/active-directory-whatis.md) umożliwia wykonywanie wielu zadań administracyjnych, takich jak zarządzanie użytkownikami, zarządzanie domeną i konfiguracja logowania jednokrotnego. W tym artykule opisano, jak używać usługi Azure AD w ramach Azure Automation jako dostawcy do uwierzytelniania na platformie Azure. 

## <a name="install-azure-ad-modules"></a>Instalowanie modułów usługi Azure AD

Usługę Azure AD można włączyć za pomocą następujących modułów programu PowerShell:

* Azure Active Directory PowerShell dla programu Graph (AzureRM i AZ modules). Azure Automation jest dostarczany z modułem AzureRM i jego ostatnim uaktualnieniem — moduł AZ module. Funkcja obejmuje uwierzytelnianie nieinteraktywne na platformie Azure przy użyciu uwierzytelniania opartego na poświadczeniach użytkownika usługi Azure AD (OrgId). Zobacz temat [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory dla środowiska Windows PowerShell (moduł MSOnline). Ten moduł umożliwia interakcję z usługą Microsoft Online, w tym Microsoft 365.

>[!NOTE]
>Program PowerShell Core nie obsługuje modułu MSOnline. Aby użyć poleceń cmdlet modułu, należy uruchomić je z programu Windows PowerShell. Zaleca się używanie nowszych Azure Active Directory programu PowerShell dla modułów grafu zamiast modułu MSOnline. 

### <a name="preinstallation"></a>Preinstalacji

Przed zainstalowaniem modułów usługi Azure AD na komputerze:

* Odinstaluj wszystkie poprzednie wersje modułu AzureRM/AZ i modułu MSOnline. 

* Odinstaluj Asystenta logowania w witrynie Microsoft Online Services, aby zapewnić poprawne działanie nowych modułów programu PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Instalowanie AzureRM i AZ modules

>[!NOTE]
>Aby można było korzystać z tych modułów, należy użyć programu PowerShell w wersji 5,1 lub nowszej z 64-bitową wersją systemu Windows. 

1. Zainstaluj program Windows Management Framework (WMF) 5,1. Zobacz [Instalowanie i Konfigurowanie WMF 5,1](/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Zainstaluj AzureRM i/lub AZ using instrukcje w [Install Azure PowerShell w systemie Windows z PowerShellGet](/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Instalowanie modułu MSOnline

>[!NOTE]
>Aby zainstalować moduł MSOnline, musisz być członkiem roli administratora. Zobacz [Informacje o rolach administratorów](/microsoft-365/admin/add-users/about-admin-roles).

1. Upewnij się, że na komputerze jest włączona funkcja Microsoft .NET Framework 3.5. x. Jest możliwe, że na komputerze jest zainstalowana nowsza wersja, ale zgodność z poprzednimi wersjami .NET Framework może być włączona lub wyłączona. 

2. Zainstaluj 64-bitową wersję [Asystenta logowania w witrynie Microsoft Online Services](https://www.microsoft.com/download/details.aspx?id=41950).

3. Uruchom program Windows PowerShell jako administrator, aby utworzyć wiersz polecenia programu Windows PowerShell z podwyższonym poziomem uprawnień.

4. Wdróż Azure Active Directory z [MSOnline 1,0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Jeśli zostanie wyświetlony monit o zainstalowanie dostawcy NuGet, wpisz Y i naciśnij klawisz ENTER.

6. Jeśli zostanie wyświetlony monit o zainstalowanie modułu z [PSGallery](https://www.powershellgallery.com/), wpisz Y i naciśnij klawisz ENTER.

### <a name="install-support-for-pscredential"></a>Zainstaluj obsługę PSCredential

Azure Automation używa klasy [PSCredential](/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) do reprezentowania zasobu poświadczeń. Skrypty pobierają `PSCredential` obiekty przy użyciu `Get-AutomationPSCredential` polecenia cmdlet. Aby uzyskać więcej informacji, zobacz [zasoby poświadczeń w Azure Automation](shared-resources/credentials.md).

## <a name="assign-a-subscription-administrator"></a>Przypisywanie administratora subskrypcji

Musisz przypisać administratora dla subskrypcji platformy Azure. Ta osoba ma rolę właściciela dla zakresu subskrypcji. Zobacz [Kontrola dostępu oparta na rolach w Azure Automation](automation-role-based-access-control.md). 

## <a name="change-the-azure-ad-users-password"></a>Zmień hasło użytkownika usługi Azure AD

Aby zmienić hasło użytkownika usługi Azure AD:

1. Wyloguj się z platformy Azure.

2. Zaloguj się do platformy Azure jako utworzony przez użytkownika usługi Azure AD przy użyciu pełnej nazwy użytkownika (w tym domeny) i hasła tymczasowego. 

3. Poprosi administratora o zmianę hasła po wyświetleniu monitu.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Konfigurowanie Azure Automation zarządzania subskrypcją platformy Azure

Aby uzyskać Azure Automation komunikowania się z usługą Azure AD, musisz pobrać poświadczenia skojarzone z połączeniem platformy Azure z usługą Azure AD. Przykładami tych poświadczeń są identyfikator dzierżawy, Identyfikator subskrypcji i podobne. Aby uzyskać więcej informacji na temat połączenia między platformą Azure i usługą Azure AD, zobacz [łączenie organizacji z Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops).

## <a name="create-a-credential-asset"></a>Utwórz zasób poświadczeń

Gdy dostępne są poświadczenia platformy Azure dla usługi Azure AD, należy utworzyć zasób poświadczeń Azure Automation, aby bezpiecznie przechowywać poświadczenia usługi Azure AD, dzięki czemu mogą uzyskiwać dostęp do tych elementów Runbook i skryptów konfiguracji stanu (DSC). Można to zrobić za pomocą poleceń cmdlet programu Azure Portal lub programu PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Utwórz zasób poświadczeń w Azure Portal

Za pomocą Azure Portal można utworzyć zasób poświadczenia. Wykonaj tę operację na koncie usługi Automation przy użyciu **poświadczeń** w obszarze **udostępnione zasoby**. Zobacz [zasoby poświadczeń w Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Tworzenie zasobu poświadczeń za pomocą programu Windows PowerShell

Aby przygotować nowy zasób poświadczeń w programie Windows PowerShell, skrypt najpierw tworzy `PSCredential` Obiekt przy użyciu przypisanej nazwy użytkownika i hasła. Skrypt następnie używa tego obiektu do utworzenia zasobu za pośrednictwem wywołania polecenia cmdlet [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential?view=azuresmps-4.0.0) . Alternatywnie skrypt może wywołać polecenie cmdlet [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) , aby monitować użytkownika o wpisanie nazwy i hasła. Zobacz [zasoby poświadczeń w Azure Automation](shared-resources/credentials.md). 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Zarządzanie zasobami platformy Azure z poziomu elementu Runbook Azure Automation

Zasobami platformy Azure można zarządzać z poziomu elementów Runbook Azure Automation przy użyciu zasobu Credential. Poniżej znajduje się przykładowy element Runbook programu PowerShell, który zbiera zasoby poświadczeń do zatrzymywania i uruchamiania maszyn wirtualnych w ramach subskrypcji platformy Azure. Ten element Runbook najpierw używa `Get-AutomationPSCredential` do pobierania poświadczeń do uwierzytelniania na platformie Azure. Następnie wywołuje polecenie cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) w celu nawiązania połączenia z platformą Azure przy użyciu poświadczenia. Skrypt używa polecenia cmdlet [SELECT-AzureSubscription](/powershell/module/servicemanagement/azure.service/select-azuresubscription?view=azuresmps-4.0.0) , aby wybrać subskrypcję, z którą chcesz współpracować. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje o używaniu poświadczeń, zobacz [Zarządzanie poświadczeniami w Azure Automation](shared-resources/credentials.md).
* Aby uzyskać informacje o modułach, zobacz [Zarządzanie modułami w Azure Automation](shared-resources/modules.md).
* Jeśli musisz uruchomić element Runbook, zobacz temat [Uruchamianie elementu Runbook w Azure Automation](start-runbooks.md).
* Aby uzyskać szczegółowe informacje dotyczące programu PowerShell, zobacz dokumentację [programu PowerShell](/powershell/scripting/overview).
