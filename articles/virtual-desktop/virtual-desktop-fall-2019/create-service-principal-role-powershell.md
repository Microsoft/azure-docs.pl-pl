---
title: Przypisanie roli głównej usługi pulpitu wirtualnego systemu Windows (klasycznej) — Azure
description: Jak utworzyć jednostki usługi i przypisać role przy użyciu programu PowerShell na pulpicie wirtualnym systemu Windows (klasyczny).
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: lizross
ms.openlocfilehash: 91bb14a174d5bd5c16b38513825579097e1d6f7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89078532"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell-in-windows-virtual-desktop-classic"></a>Samouczek: tworzenie jednostek usługi i przypisań ról przy użyciu programu PowerShell na pulpicie wirtualnym systemu Windows (klasyczny)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows.

Nazwy główne usług są tożsamościami, które można utworzyć w Azure Active Directory, aby przypisać role i uprawnienia do określonego celu. Na pulpicie wirtualnym systemu Windows można utworzyć jednostkę usługi, aby:

- Automatyzowanie określonych zadań zarządzania pulpitem wirtualnym systemu Windows.
- Użyj jako poświadczeń zamiast użytkowników wymaganych przez uwierzytelnianie wieloskładnikowe podczas uruchamiania dowolnego szablonu Azure Resource Manager dla pulpitu wirtualnego systemu Windows.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz nazwę główną usługi w Azure Active Directory.
> * Utwórz przypisanie roli na pulpicie wirtualnym systemu Windows.
> * Zaloguj się do pulpitu wirtualnego systemu Windows przy użyciu nazwy głównej usługi.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było tworzyć jednostki usługi i przypisania ról, należy wykonać trzy czynności:

1. Zainstaluj moduł AzureAD. Aby zainstalować moduł, uruchom program PowerShell jako administrator i uruchom następujące polecenie cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/).

3. Wykonaj wszystkie instrukcje zawarte w tym artykule w tej samej sesji programu PowerShell. Proces może nie zadziałać, jeśli sesja programu PowerShell zostanie przerwana przez zamknięcie okna i ponowne jego otwarcie.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Tworzenie nazwy głównej usługi w usłudze Azure Active Directory

Po spełnieniu wymagań wstępnych w sesji programu PowerShell uruchom następujące polecenia cmdlet programu PowerShell, aby utworzyć nazwę główną usługi wielodostępnej na platformie Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Wyświetlanie poświadczeń w programie PowerShell

Przed utworzeniem przypisania roli dla jednostki usługi, Wyświetl swoje poświadczenia i Zapisz je w celu uwzględnienia w przyszłości. Hasło jest szczególnie ważne, ponieważ nie będzie można go pobrać po zamknięciu sesji programu PowerShell.

Poniżej przedstawiono trzy poświadczenia, które należy napisać, i polecenia cmdlet, które należy uruchomić, aby je pobrać:

- Hasło:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Identyfikator dzierżawy:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Identyfikator aplikacji:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Tworzenie przypisania roli na pulpicie wirtualnym systemu Windows

Następnie należy utworzyć przypisanie roli, aby nazwa główna usługi mogła zalogować się do pulpitu wirtualnego systemu Windows. Upewnij się, że logujesz się przy użyciu konta z uprawnieniami do tworzenia przypisań ról.

Najpierw [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono.

Uruchom następujące polecenia cmdlet programu PowerShell, aby połączyć się z pulpitem wirtualnym systemu Windows i wyświetlić dzierżawców.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Po znalezieniu nazwy dzierżawy dla dzierżawy, dla której chcesz utworzyć przypisanie roli, Użyj tej nazwy w następującym poleceniu cmdlet:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Zaloguj się przy użyciu nazwy głównej usługi

Po utworzeniu przypisania roli dla jednostki usługi upewnij się, że jednostka usługi może się zalogować do pulpitu wirtualnego systemu Windows, uruchamiając następujące polecenie cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Po zalogowaniu się upewnij się, że wszystko działa, testując kilka poleceń cmdlet programu PowerShell dla pulpitu wirtualnego systemu Windows z jednostką usługi.

## <a name="next-steps"></a>Następne kroki

Po utworzeniu jednostki usługi i przypisaniu jej do roli w dzierżawie pulpitu wirtualnego systemu Windows możesz użyć jej do utworzenia puli hostów. Aby dowiedzieć się więcej na temat pul hostów, przejdź do samouczka dotyczącego tworzenia puli hostów w programie Virtual Desktop systemu Windows.

 > [!div class="nextstepaction"]
 > [Tworzenie puli hostów za pomocą witryny Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
