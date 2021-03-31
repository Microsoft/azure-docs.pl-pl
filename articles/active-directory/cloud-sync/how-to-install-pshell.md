---
title: Instalowanie Azure AD Connect agenta aprowizacji w chmurze przy użyciu programu PowerShell
description: Dowiedz się, jak zainstalować agenta aprowizacji Azure AD Connect w chmurze za pomocą poleceń cmdlet programu PowerShell.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c20cfb96b5cd6e1d05e332fa7157fe6e0cde8656
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98613696"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Zainstaluj agenta aprowizacji Azure AD Connect przy użyciu poleceń cmdlet programu PowerShell 
Poniższy dokument przedstawia sposób instalowania agenta aprowizacji Azure AD Connect przy użyciu poleceń cmdlet programu PowerShell.
 

## <a name="prerequisite"></a>Wymaganie wstępne: 


>[!IMPORTANT]
>W poniższych instrukcjach instalacji założono, że spełniono wszystkie [wymagania wstępne](how-to-prerequisites.md) .
>
> Przed zainstalowaniem agenta aprowizacji Azure AD Connect przy użyciu poleceń cmdlet programu PowerShell w systemie Windows Server należy włączyć protokół TLS 1,2. Aby włączyć protokół TLS 1,2, możesz użyć kroków znalezionych [tutaj](how-to-prerequisites.md#tls-requirements).

 

## <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Zainstaluj agenta aprowizacji Azure AD Connect przy użyciu poleceń cmdlet programu PowerShell 


 1. Zaloguj się do Azure Portal, a następnie przejdź do **Azure Active Directory**.
 2. W menu po lewej stronie wybierz pozycję **Azure AD Connect**.
 3. Wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)**  >  **Przejrzyj wszystkich agentów**.
 4. Pobierz agenta aprowizacji Azure AD Connect z Azure Portal do lokalnego.  

   ![Pobierz agenta lokalnego](media/how-to-install/install-9.png)</br>
 5. Na potrzeby tych instrukcji Agent został pobrany do następującego folderu: folder "C:\ProvisioningSetup". 
 6. Zainstaluj ProvisioningAgent w trybie cichym

   ```
   $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
   $installerProcess.WaitForExit()  
   ```
 7. Zaimportuj moduł PS agenta aprowizacji 

   ```
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll" 
   ```
 8. Łączenie się z usługą AzureAD przy użyciu poświadczeń administratora globalnego możesz dostosować tę sekcję, aby pobrać hasło z bezpiecznego magazynu. 

   ```
   $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 

   $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
   ```

   Connect-AADCloudSyncAzureAD — $globalAdminCreds poświadczeń 

 9. Dodaj konto gMSA, podaj poświadczenia administratora domeny, aby utworzyć domyślne konto gMSA 
 
   ```
   $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 

   Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
   ```
 10. Lub użyj powyższego polecenia cmdlet w celu zapewnienia wstępnie utworzonego konta gMSA 

 
   ```
   Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
   ```
 11. Dodaj domenę 

   ```
   $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
   ```
 12. Lub użyj powyższego polecenia cmdlet, jak pokazano poniżej, aby skonfigurować preferowane kontrolery domeny 

   ```
   $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
   ```
 13. Powtórz poprzedni krok, aby dodać więcej domen, Podaj nazwy kont i nazwy domen odpowiednich domen. 
 14. Uruchom ponownie usługę 
   ```
   Restart-Service -Name AADConnectProvisioningAgent  
   ```
 15.  Przejdź do witryny Azure Portal, aby utworzyć konfigurację synchronizacji z chmurą.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell gMSA agenta aprowizacji
Po zainstalowaniu agenta można zastosować bardziej szczegółowe uprawnienia do gMSA.  Aby uzyskać informacje i instrukcje krok po kroku dotyczące konfigurowania uprawnień, zobacz [Azure AD Connect poleceń cmdlet programu PowerShell gMSA w chmurze](how-to-gmsa-cmdlets.md) .

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Azure AD Connect poleceń cmdlet programu PowerShell gMSA agenta aprowizacji w chmurze](how-to-gmsa-cmdlets.md)
- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)