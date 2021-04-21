---
title: Użycie usługi Azure AD w usłudze Azure Automation do uwierzytelniania na platformie Azure
description: W tym artykule opisano sposób używania usługi Azure AD w Azure Automation jako dostawcy do uwierzytelniania na platformie Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34033589a297b1a3a2abb97d346f1da478f950e6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830290"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Uwierzytelnianie na platformie Azure za pomocą usługi Azure AD

Usługa [Azure Active Directory (AD)](../active-directory/fundamentals/active-directory-whatis.md) umożliwia wykonywanie wielu zadań administracyjnych, takich jak zarządzanie użytkownikami, zarządzanie domenami i konfiguracja logowania pojedynczego. W tym artykule opisano, jak używać usługi Azure AD Azure Automation jako dostawcy do uwierzytelniania na platformie Azure. 

## <a name="install-azure-ad-modules"></a>Instalowanie modułów usługi Azure AD

Usługę Azure AD można włączyć za pomocą następujących modułów programu PowerShell:

* Azure Active Directory PowerShell dla programu Graph (moduły AzureRM i Az). Azure Automation jest dostarczany z modułem AzureRM i jego ostatnim uaktualnieniem, modułem Az. Funkcje obejmują uwierzytelnianie nieinterakcyjne na platformie Azure przy użyciu uwierzytelniania opartego na poświadczeniach użytkownika usługi Azure AD (OrgId). Zobacz [Azure AD 2.0.2.76.](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76)

* Microsoft Azure Active Directory for Windows PowerShell (moduł MSOnline). Ten moduł umożliwia interakcję z usługą Microsoft Online, w tym z Microsoft 365.

>[!NOTE]
>PowerShell Core nie obsługuje modułu MSOnline. Aby użyć tych polecenia cmdlet modułu, należy uruchomić je z Windows PowerShell. Zachęcamy do korzystania z nowszej Azure Active Directory PowerShell dla programu Graph zamiast modułu MSOnline. 

### <a name="preinstallation"></a>Preinstalacji

Przed zainstalowaniem modułów usługi Azure AD na komputerze:

* Odinstaluj wszystkie poprzednie wersje modułu AzureRM/Az i modułu MSOnline. 

* Odinstaluj asystenta Sign-In Microsoft Online Services, aby zapewnić prawidłowe działanie nowych modułów programu PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Instalowanie modułów AzureRM i Az

>[!NOTE]
>Aby pracować z tymi modułami, należy użyć programu PowerShell w wersji 5.1 lub nowszej z 64-bitową wersją systemu Windows. 

1. Zainstaluj Windows Management Framework (WMF) 5.1. Zobacz [Instalowanie i konfigurowanie programu WMF 5.1.](/powershell/scripting/wmf/setup/install-configure)

2. Zainstaluj usługę AzureRM i/lub Az, korzystając z instrukcji Azure PowerShell [instalacji w systemie Windows za pomocą polecenia PowerShellGet.](/powershell/azure/azurerm/install-azurerm-ps)

### <a name="install-the-msonline-module"></a>Instalowanie modułu MSOnline

>[!NOTE]
>Aby zainstalować moduł MSOnline, musisz być członkiem roli administratora. Zobacz [Informacje o rolach administratorów.](/microsoft-365/admin/add-users/about-admin-roles)

1. Upewnij się, Microsoft .NET na komputerze jest włączona funkcja programu Framework 3.5.x. Najprawdopodobniej na komputerze jest zainstalowana nowsza wersja, ale zgodność ze starszymi wersjami .NET Framework można włączyć lub wyłączyć. 

2. Zainstaluj 64-bitową wersję asystenta logowania usług [Online Services firmy Microsoft.](https://www.microsoft.com/Download/details.aspx?id=28177)

3. Uruchom Windows PowerShell jako administrator, aby utworzyć wiersz Windows PowerShell z podwyższonym poziomem uprawnień.

4. Wd Azure Active Directory z [usługi MSOnline 1.0.](http://www.powershellgallery.com/packages/MSOnline/1.0)

5. Jeśli zostanie wyświetlony monit o zainstalowanie dostawcy NuGet, wpisz Y i naciśnij klawisz ENTER.

6. Jeśli zostanie wyświetlony monit o zainstalowanie modułu z programu [PSGallery,](https://www.powershellgallery.com/)wpisz Y i naciśnij klawisz ENTER.

### <a name="install-support-for-pscredential"></a>Obsługa instalacji dla programu PSCredential

Azure Automation używa klasy [PSCredential](/dotnet/api/system.management.automation.pscredential) do reprezentowania zasobu poświadczeń. Skrypty `PSCredential` pobierają obiekty przy użyciu `Get-AutomationPSCredential` polecenia cmdlet . Aby uzyskać więcej informacji, zobacz [Zasoby poświadczeń w Azure Automation](shared-resources/credentials.md).

## <a name="assign-a-subscription-administrator"></a>Przypisywanie administratora subskrypcji

Musisz przypisać administratora subskrypcji platformy Azure. Ta osoba ma rolę właściciela w zakresie subskrypcji. Zobacz [Kontrola dostępu oparta na rolach w Azure Automation](automation-role-based-access-control.md). 

## <a name="change-the-azure-ad-users-password"></a>Zmienianie hasła użytkownika usługi Azure AD

Aby zmienić hasło użytkownika usługi Azure AD:

1. Wyloguj się z platformy Azure.

2. Administrator musi zalogować się do platformy Azure jako właśnie utworzony użytkownik usługi Azure AD przy użyciu pełnej nazwy użytkownika (w tym domeny) i hasła tymczasowego. 

3. Po wyświetleniu monitu poproś administratora o zmianę hasła.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Konfigurowanie Azure Automation do zarządzania subskrypcją platformy Azure

Aby Azure Automation z usługą Azure AD, musisz pobrać poświadczenia skojarzone z połączeniem platformy Azure z usługą Azure AD. Przykładami tych poświadczeń są identyfikator dzierżawy, identyfikator subskrypcji i podobne. Aby uzyskać więcej informacji na temat połączenia między platformą Azure i usługą Azure AD, zobacz Łączenie organizacji [z Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="create-a-credential-asset"></a>Tworzenie zasobu poświadczeń

Mając dostępne poświadczenia platformy Azure dla usługi Azure AD, należy utworzyć zasób poświadczeń usługi Azure Automation w celu bezpiecznego przechowywania poświadczeń usługi Azure AD, aby zapewnić dostęp do nich elementom Runbook i skryptom Desire State Configuration (DSC). Można to zrobić przy użyciu poleceń cmdlet programu Azure Portal lub PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Tworzenie zasobu poświadczeń w Azure Portal

Aby utworzyć zasób poświadczeń, Azure Portal użyć zasobu poświadczeń. Wykonaj tę operację z konta usługi Automation przy użyciu **poświadczeń w** obszarze **Udostępnione zasoby.** Zobacz [Zasoby poświadczeń w Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Tworzenie zasobu poświadczeń przy użyciu Windows PowerShell

Aby przygotować nowy zasób poświadczeń w Windows PowerShell, skrypt najpierw tworzy obiekt przy użyciu przypisanej `PSCredential` nazwy użytkownika i hasła. Następnie skrypt używa tego obiektu do utworzenia zasobu za pomocą wywołania polecenia cmdlet [New-AzureAutomationCredential.](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) Alternatywnie skrypt może wywołać polecenie cmdlet [Get-Credential,](/powershell/module/microsoft.powershell.security/get-credential) aby monitować użytkownika o wpisanie nazwy i hasła. Zobacz [Zasoby poświadczeń w Azure Automation](shared-resources/credentials.md). 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Zarządzanie zasobami platformy Azure z Azure Automation Runbook

Zasobami platformy Azure można zarządzać z Azure Automation Runbook przy użyciu zasobu poświadczeń. Poniżej znajduje się przykładowy element runbook programu PowerShell, który zbiera zasób poświadczeń, który ma być używane do zatrzymywania i uruchamiania maszyn wirtualnych w subskrypcji platformy Azure. Ten podręcznik Runbook najpierw używa `Get-AutomationPSCredential` do pobrania poświadczeń do użycia w celu uwierzytelnienia na platformie Azure. Następnie wywołuje polecenie cmdlet [Connect-AzAccount,](/powershell/module/az.accounts/connect-azaccount) aby nawiązać połączenie z platformą Azure przy użyciu poświadczeń. Skrypt używa polecenia cmdlet [Select-AzureSubscription,](/powershell/module/servicemanagement/azure.service/select-azuresubscription) aby wybrać subskrypcję do pracy. 

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

* Aby uzyskać szczegółowe informacje na temat użycia poświadczeń, zobacz [Zarządzanie poświadczeniami w Azure Automation](shared-resources/credentials.md).
* Aby uzyskać informacje o modułach, zobacz [Zarządzanie modułami w programie Azure Automation](shared-resources/modules.md).
* Jeśli musisz uruchomić runbook, zobacz Uruchamianie runbook w [programie Azure Automation](start-runbooks.md).
* Aby uzyskać szczegółowe informacje na temat programu PowerShell, zobacz [PowerShell Docs](/powershell/scripting/overview).
