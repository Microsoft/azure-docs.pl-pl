---
title: Azure AD Connect poleceń cmdlet programu PowerShell gMSA agenta aprowizacji w chmurze
description: Dowiedz się Azure AD Connect, jak używać poleceń cmdlet programu PowerShell gMSA agenta aprowizacji w chmurze.
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
ms.openlocfilehash: d97128c15d974bfb704fdd446dbd9c727d7fa469
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613736"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Azure AD Connect poleceń cmdlet programu PowerShell gMSA agenta aprowizacji w chmurze

Celem tego dokumentu jest opisywanie Azure AD Connect poleceń cmdlet programu PowerShell gMSA Cloud Provisioning Agent. Te polecenia cmdlet umożliwiają zwiększenie stopnia szczegółowości uprawnień stosowanych na koncie usługi (gMSA). Domyślnie Azure AD Connect synchronizacji w chmurze mają zastosowanie wszystkie uprawnienia podobne do Azure AD Connect na domyślnym gMSA lub niestandardowym gMSA. 

Ten dokument obejmuje następujące polecenia cmdlet:  

`Set-AADCloudSyncRestrictedPermissions`

`Ste-AADCloudSyncPermissions` 

## <a name="how-to-use-the-cmdlets"></a>Jak używać poleceń cmdlet:  

Do korzystania z tych poleceń cmdlet wymagane są następujące wymagania wstępne.

1. Zainstaluj agenta aprowizacji. 
2. Zaimportuj moduł PS agenta aprowizacji do sesji programu PowerShell. 

 ```PowerShell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
 ```
3. Usuń istniejące uprawnienia.  Aby usunąć wszystkie istniejące uprawnienia do konta usługi, z wyjątkiem użycia samoobsługowego: `Set-AADCloudSyncRestrictedPermission` .  

    To polecenie cmdlet wymaga parametru o nazwie `Credential` , który może zostać przesłany, lub zostanie wyświetlony monit, jeśli zostanie wywołane bez niego.

    Aby utworzyć zmienną użycia  

   `$credential = Get-Credential` 

   Spowoduje to wyświetlenie monitu o podanie nazwy użytkownika i hasła. Poświadczenia muszą być co najmniej administrator domeny (domena, w której zainstalowano agenta) może być również administratorem przedsiębiorstwa. 

4.  Następnie można wywołać polecenie cmdlet w celu usunięcia dodatkowych uprawnień: 
   ```PowerShell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```
5. Lub można po prostu wywołać 

   `Set-AADCloudSyncRestrictedPermissions` co spowoduje wyświetlenie monitu o podanie poświadczeń. 

 6.  Dodaj określony typ uprawnienia.  Dodane uprawnienia są takie same jak Azure AD Connect.  Przykłady dotyczące ustawiania uprawnień znajdują się w temacie [using Set-AADCloudSyncPermissions](#using-set-aadcloudsyncpermissions) .

## <a name="using-set-aadcloudsyncpermissions"></a>Używanie Set-AADCloudSyncPermissions 
`Set-AADCloudSyncPermissions` Program obsługuje następujące typy uprawnień, które są identyczne z uprawnieniami używanymi przez Azure AD Connect. Obsługiwane są następujące typy uprawnień: 

|Typ uprawnienia|Opis|
|-----|-----|
|BasicRead| Zobacz [BasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) uprawnień dla Azure AD Connect|
|PasswordHashSync|Zobacz [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) uprawnień dla Azure AD Connect|
|PasswordWriteBack|Zobacz [PasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) uprawnień dla Azure AD Connect|
|HybridExchangePermissions|Zobacz [HybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) uprawnień dla Azure AD Connect| 
|ExchangeMailPublicFolderPermissions| Zobacz [ExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders-preview) uprawnień dla Azure AD Connect| 
|CloudHR| Stosuje "pełną kontrolę" w "obiektów podrzędnych użytkownika" i "Utwórz/Usuń obiekty użytkownika" w obiekcie "ten obiekt i wszystkie obiekty zależne"| 
|Wszystko|dodaje wszystkie powyższe uprawnienia.| 



Możesz użyć AADCloudSyncPermissions na jeden z dwóch sposobów:
- [Przyznaj określone uprawnienia wszystkim skonfigurowanym domenom](#grant-a-certain-permission-to-all-configured-domains) 
- [Przyznaj określone uprawnienia do konkretnej domeny](#grant-a-certain-permission-to-a-specific-domain) 
## <a name="grant-a-certain-permission-to-all-configured-domains"></a>Przyznaj określone uprawnienia wszystkim skonfigurowanym domenom 
Przyznanie pewnych uprawnień wszystkim skonfigurowanym domenom wymaga użycia konta administratora przedsiębiorstwa.


 ```PowerShell
Set-AADCloudSyncPermissions -PermissionType “Any mentioned above” -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>Przyznaj określone uprawnienia do konkretnej domeny 
Przyznanie pewnych uprawnień do określonej domeny będzie wymagało użycia programu, przy minimalnym koncie administratora domeny, które próbujesz dodać.


 ```PowerShell
Set-AADCloidSyncPermissions -PermissionType “Any mentioned above” -TargetDomain “FQDN of domain” (has to be already configured through wizard) -TargetDomaincredential $credential(same as above) 
```
 

Uwaga: w przypadku 1. Poświadczenia muszą być co najmniej administratora przedsiębiorstwa. 

Dla 2. Poświadczeniami może być administrator domeny lub administrator przedsiębiorstwa. 

  

