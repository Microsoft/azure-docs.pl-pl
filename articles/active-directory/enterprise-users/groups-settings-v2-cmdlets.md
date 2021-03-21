---
title: Przykłady dla programu PowerShell w wersji 2 dotyczące zarządzania grupami — Azure AD | Microsoft Docs
description: Na tej stronie przedstawiono przykłady programu PowerShell ułatwiające zarządzanie grupami w Azure Active Directory
keywords: Azure AD, Azure Active Directory, PowerShell, grupy, zarządzanie grupami
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12613362d9fc67dea14b41ece19a34febb489dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860647"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Polecenia cmdlet Azure Active Directory w wersji 2 dla zarządzania grupami

> [!div class="op_single_selector"]
> - [Witryna Azure Portal](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [Program PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
>
>

Ten artykuł zawiera przykłady użycia programu PowerShell do zarządzania grupami w usłudze Azure Active Directory (Azure AD).  Zawarto również informacje, jak skonfigurować program przy użyciu modułu Azure AD PowerShell. Najpierw należy [pobrać moduł Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Instalowanie modułu Azure AD PowerShell

Aby zainstalować moduł Azure AD PowerShell, użyj następujących poleceń:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Aby sprawdzić, czy moduł jest gotowy do użycia, użyj następującego polecenia:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Teraz możesz zacząć korzystać z poleceń cmdlet w module. Pełny opis poleceń cmdlet w module usługi Azure AD można znaleźć w dokumentacji referencyjnej online dla [programu Azure Active Directory PowerShell w wersji 2](/powershell/azure/active-directory/install-adv2).

> [!NOTE]
> Polecenia cmdlet programu PowerShell usługi Azure AD nie współpracują z nowym programem PowerShell 7, ponieważ jest on oparty na platformie .NET Core. Mamy świadomość, że trwa proces pobierania aktualizacji. Obecnie zalecamy używanie modułu Windows PowerShell 5. x do użycia z operacjami programu PowerShell usługi Azure AD. 


## <a name="connect-to-the-directory"></a>Nawiązywanie połączenia z katalogiem

Przed rozpoczęciem zarządzania grupami przy użyciu poleceń cmdlet programu PowerShell usługi Azure AD należy połączyć sesję programu PowerShell z katalogiem, który ma być zarządzany. Użyj następującego polecenia:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

Polecenie cmdlet poprosi o poświadczenia, których chcesz użyć w celu uzyskania dostępu do katalogu. W tym przykładzie używamy w karen@drumkit.onmicrosoft.com celu uzyskania dostępu do katalogu demonstracyjnego. Polecenie cmdlet zwraca potwierdzenie, aby pokazać, że sesja została pomyślnie podłączona do katalogu:

```powershell
    Account                       Environment Tenant ID
    -------                       ----------- ---------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

Teraz można rozpocząć korzystanie z poleceń cmdlet AzureAD do zarządzania grupami w katalogu.

## <a name="retrieve-groups"></a>Pobieranie grup

Aby pobrać istniejące grupy z katalogu, należy użyć polecenia cmdlet Get-AzureADGroups. 

Aby pobrać wszystkie grupy w katalogu, należy użyć polecenia cmdlet bez parametrów:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

Polecenie cmdlet zwraca wszystkie grupy w połączonym katalogu.

Za pomocą parametru-objectID można pobrać konkretną grupę, dla której zostanie określony identyfikator obiektu grupy:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

Polecenie cmdlet zwraca teraz grupę, której identyfikator objectID pasuje do wartości wprowadzonego parametru:

```powershell
    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Można wyszukać określoną grupę przy użyciu parametru-Filter. Ten parametr przyjmuje klauzulę filtru ODATA i zwraca wszystkie grupy, które pasują do filtru, jak w poniższym przykładzie:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

> [!NOTE]
> Polecenia cmdlet programu PowerShell usługi Azure AD implementują Standard zapytania OData. Aby uzyskać więcej informacji, zobacz **$Filter** w [opcjach zapytania systemowego OData przy użyciu punktu końcowego OData](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)#BKMK_filter).

## <a name="create-groups"></a>Tworzenie grup

Aby utworzyć nową grupę w katalogu, należy użyć polecenia cmdlet New-AzureADGroup. To polecenie cmdlet tworzy nową grupę zabezpieczeń o nazwie "Marketing":

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Grupy aktualizacji

Aby zaktualizować istniejącą grupę, użyj polecenia cmdlet Set-AzureADGroup. W tym przykładzie zmieniamy Właściwość DisplayName grupy "Administratorzy usługi Intune". Najpierw wyszukamy grupę przy użyciu polecenia cmdlet Get-AzureADGroup i filtrowania przy użyciu atrybutu DisplayName:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Następnie zmienimy Właściwość Description na nową wartość "Administratorzy urządzeń usługi Intune":

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Teraz, jeśli ponownie znajdziesz grupę, zobaczymy, że Właściwość Description zostanie zaktualizowana w celu odzwierciedlenia nowej wartości:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

## <a name="delete-groups"></a>Usuwanie grup

Aby usunąć grupy z katalogu, należy użyć polecenia cmdlet Remove-AzureADGroup w następujący sposób:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Zarządzanie członkostwem w grupie

### <a name="add-members"></a>Dodawanie członków

Aby dodać nowych członków do grupy, użyj polecenia cmdlet Add-AzureADGroupMember. To polecenie dodaje członka do grupy administratorów usługi Intune, która została użyta w poprzednim przykładzie:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Parametr-ObjectId jest identyfikatorem ObjectID grupy, do której chcemy dodać element członkowski, a-RefObjectId jest identyfikatorem ObjectID użytkownika, który chcemy dodać jako członka grupy.

### <a name="get-members"></a>Pobierz członków

Aby uzyskać istniejących członków grupy, należy użyć polecenia cmdlet Get-AzureADGroupMember, jak w poniższym przykładzie:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Usuwanie członków

Aby usunąć element członkowski, który został wcześniej dodany do grupy, należy użyć polecenia cmdlet Remove-AzureADGroupMember, jak pokazano poniżej:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Weryfikuj członków

Aby sprawdzić członkostwa w grupie użytkownika, należy użyć polecenia cmdlet Select-AzureADGroupIdsUserIsMemberOf. To polecenie cmdlet przyjmuje jako parametry identyfikator ObjectId użytkownika, dla którego chcesz sprawdzić członkostwa w grupie, oraz listę grup, dla których chcesz sprawdzić członkostwa. Lista grup musi być podana w formie złożonej zmiennej typu "Microsoft. Open. AzureAD. model. GroupIdsForMembershipCheck", więc najpierw należy utworzyć zmienną o tym typie:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

Następnie dostarczamy wartości dla groupIds, aby zaewidencjonować atrybut "GroupIds" tej zmiennej złożonej:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Teraz, jeśli chcemy sprawdzić członkostwa w grupie użytkownika przy użyciu identyfikatora ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea względem grup w $g, należy użyć:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

Zwracana wartość to lista grup, których członkiem jest ten użytkownik. Można również zastosować tę metodę, aby sprawdzić kontakty, grupy lub członkostwo w jednostkach usługi dla danej listy grup przy użyciu polecenia SELECT-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf lub Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Wyłącz tworzenie grupy przez użytkowników

Można uniemożliwić użytkownikom niebędącym administratorami tworzenie grup zabezpieczeń. Domyślnym zachowaniem w usługach Microsoft Online Directory Services (MSODS) jest umożliwienie użytkownikom niebędącym administratorami tworzenia grup, niezależnie od tego, czy jest również włączona funkcja samoobsługowego zarządzania grupami (GRUPAMI). Ustawienie GRUPAMI kontroluje zachowanie tylko w panelu dostępu moje aplikacje.

Aby wyłączyć tworzenie grupy dla użytkowników niebędących administratorami:

1. Sprawdź, czy użytkownicy niebędący administratorami mogą tworzyć grupy:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Jeśli zwróci wartość `UsersPermissionToCreateGroupsEnabled : True` , użytkownicy niebędący administratorami mogą tworzyć grupy. Aby wyłączyć tę funkcję:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Zarządzanie właścicielami grup

Aby dodać właścicieli do grupy, użyj polecenia cmdlet Add-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Parametr-ObjectId jest identyfikatorem ObjectID grupy, do której chcemy dodać właściciela, a-RefObjectId jest identyfikatorem ObjectID użytkownika lub jednostki usługi, który chcemy dodać jako właściciela grupy.

Aby pobrać właścicieli grupy, należy użyć polecenia cmdlet Get-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

Polecenie cmdlet zwraca listę właścicieli (użytkowników i nazw podmiotów usługi) dla określonej grupy:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Jeśli chcesz usunąć właściciela z grupy, użyj Remove-AzureADGroupOwner polecenia cmdlet:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Zarezerwowane aliasy

Po utworzeniu grupy niektóre punkty końcowe umożliwiają użytkownikowi końcowemu określenie mailNickname lub aliasu, który ma być używany jako część adresu e-mail grupy. Grupy o następujących aliasach poczty e-mail z wysokim poziomem uprawnień mogą być tworzone tylko przez administratora globalnego usługi Azure AD. 
  
* stanowi
* administrator
* administrator
* hostmaster
* majordomo
* postmaster
* root
* zapewnienia
* security
* SSL — administrator
* wiadom

## <a name="group-writeback-to-on-premises-preview"></a>Grupowanie zapisu zwrotnego w środowisku lokalnym (wersja zapoznawcza)

Obecnie wiele grup jest nadal zarządzanych w Active Directory lokalnych. Aby odpowiedzieć na żądania synchronizacji grup w chmurze z powrotem z lokalnymi, funkcja zapisywania zwrotnego grup Microsoft 365 w usłudze Azure AD jest teraz dostępna w wersji zapoznawczej.

Grupy Microsoft 365 są tworzone i zarządzane w chmurze. Funkcja zapisywania zwrotnego umożliwia zapisanie grup Microsoft 365 jako grup dystrybucyjnych w lesie Active Directory z zainstalowanym programem Exchange. Użytkownicy z lokalnymi skrzynkami pocztowymi programu Exchange mogą następnie wysyłać i odbierać wiadomości e-mail z tych grup. Funkcja zapisywania zwrotnego grup nie obsługuje grup zabezpieczeń ani grup dystrybucyjnych usługi Azure AD.

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją [usługi synchronizacji Azure AD Connect](../hybrid/how-to-connect-syncservice-features.md).

Funkcja zapisywania zwrotnego grup Microsoft 365 jest publiczną funkcją w wersji zapoznawczej Azure Active Directory (Azure AD) i jest dostępna z dowolnym płatnym planem licencjonowania usługi Azure AD. Aby uzyskać pewne informacje prawne dotyczące wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="next-steps"></a>Następne kroki

Więcej Azure Active Directory dokumentacji programu PowerShell można znaleźć w [Azure Active Directory poleceniach cmdlet](/powershell/azure/active-directory/install-adv2).

* [Zarządzanie dostępem do zasobów za pomocą grup Azure Active Directory](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
