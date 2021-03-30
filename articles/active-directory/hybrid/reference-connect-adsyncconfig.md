---
title: 'Azure AD Connect: ADSyncConfig programu PowerShell | Microsoft Docs'
description: Ten dokument zawiera informacje referencyjne dotyczące modułu programu PowerShell ADSyncConfig. PSM1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a2126aceba8724b46de094d14db754d704500c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85850973"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect: ADSyncConfig programu PowerShell
Poniższa dokumentacja zawiera informacje dotyczące modułu PowerShell ADSyncConfig. PSM1, który jest dołączony do Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>STRESZCZENIE
Pobiera nazwę konta i domenę, która jest skonfigurowana w każdym z łącznika usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>OPIS
Ta funkcja korzysta z polecenia cmdlet "Get-ADSyncConnector", które jest obecne w programie AAD Connect, aby pobrać z parametrów łączności tabelę zawierającą konto łączników usługi AD.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>STRESZCZENIE
Pobiera obiekty AD z wyłączonym dziedziczeniem uprawnień

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Wyszukiwanie w usłudze AD rozpoczyna się od parametru SearchBase i zwraca wszystkie obiekty, przefiltrowane według parametru ObjectClass, które są obecnie wyłączone.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
Znajdź obiekty z wyłączonym dziedziczeniem w domenie "contoso" (domyślnie zwracają tylko obiekty "organizationalUnit")
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'
```

#### <a name="example-2"></a>PRZYKŁAD 2
Znajdź obiekty "User" z wyłączonym dziedziczeniem w domenie "contoso"
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'
```

#### <a name="example-3"></a>PRZYKŁAD 3
Znajdź wszystkie typy obiektów z wyłączonym dziedziczeniem w jednostce organizacyjnej
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'
```



### <a name="parameters"></a>PARAMETRY

#### <a name="-searchbase"></a>-SearchBase
SearchBase dla kwerendy LDAP, która może być domeną AD Odróżnionyname lub nazwą FQDN

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Klasa obiektów do przeszukania, które mogą mieć wartość "*" (dla dowolnej klasy obiektów), "User", "Group", "Container" itd. Domyślnie ta funkcja będzie szukać klasy obiektów "organizationalUnit".

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zainicjuj Las Active Directory i domenę, aby uzyskać podstawowe uprawnienia do odczytu.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncBasicReadPermissions udzieli wymaganych uprawnień do konta synchronizacji usługi AD, co obejmuje następujące elementy:
1.
Odczytaj dostęp do właściwości dla wszystkich atrybutów dla wszystkich obiektów zależnych komputerów
2.
Odczytaj dostęp do właściwości dla wszystkich atrybutów dla wszystkich obiektów zależnych urządzeń
3.
Odczytaj dostęp do właściwości dla wszystkich atrybutów dla wszystkich obiektów podrzędnych foreignsecurityprincipal
5.
Odczytaj dostęp do właściwości dla wszystkich atrybutów dla wszystkich obiektów podrzędnych użytkownika
6.
Odczytaj dostęp do właściwości dla wszystkich atrybutów dla wszystkich podrzędnych obiektów inetOrgPerson
7.
Odczytaj dostęp do właściwości dla wszystkich atrybutów dla wszystkich obiektów grupy zależnej
8.
Odczytaj dostęp do właściwości dla wszystkich atrybutów dla wszystkich obiektów kontaktów zależnych

Te uprawnienia są stosowane do wszystkich domen w lesie.
Opcjonalnie można podać wartość "Odróżnionyname" w parametrze ADobjectDN, aby ustawić te uprawnienia tylko dla tego obiektu usługi AD (w tym dziedziczenie do obiektów podrzędnych).

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PRZYKŁAD 3
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PRZYKŁAD 4
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta Active Directory, które jest lub będzie używane przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta Active Directory, która jest lub będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Wartość Odróżnionename konta Active Directory, która jest lub będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Wyróżniającname docelowego obiektu usługi AD, aby ustawić uprawnienia (opcjonalnie)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Opcjonalny parametr wskazujący, czy nie należy aktualizować kontenera AdminSDHolder przy użyciu tych uprawnień

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zainicjuj Las Active Directory i domenę dla funkcji hybrydowej programu Exchange.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncExchangeHybridPermissions udzieli wymaganych uprawnień do konta synchronizacji usługi AD, co obejmuje następujące elementy:
1.
Dostęp do właściwości odczytu/zapisu dla wszystkich atrybutów dla wszystkich obiektów podrzędnych użytkownika
2.
Dostęp do odczytu/zapisu dla wszystkich atrybutów dla wszystkich podrzędnych obiektów inetOrgPerson
3.
Dostęp do właściwości odczytu/zapisu dla wszystkich atrybutów dla wszystkich obiektów podrzędnych grup
4.
Dostęp do odczytu/zapisu dla wszystkich atrybutów dla wszystkich obiektów kontaktów zależnych

Te uprawnienia są stosowane do wszystkich domen w lesie.
Opcjonalnie można podać wartość "Odróżnionyname" w parametrze ADobjectDN, aby ustawić te uprawnienia tylko dla tego obiektu usługi AD (w tym dziedziczenie do obiektów podrzędnych).

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PRZYKŁAD 3
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PRZYKŁAD 4
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta Active Directory, które jest lub będzie używane przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta Active Directory, która jest lub będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Wartość Odróżnionename konta Active Directory, która jest lub będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Wyróżniającname docelowego obiektu usługi AD, aby ustawić uprawnienia (opcjonalnie)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Opcjonalny parametr wskazujący, czy nie należy aktualizować kontenera AdminSDHolder przy użyciu tych uprawnień

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zainicjuj Las Active Directory i domenę dla funkcji publicznego folderu poczty programu Exchange.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncExchangeMailPublicFolderPermissions udzieli wymaganych uprawnień do konta synchronizacji usługi AD, co obejmuje następujące elementy:
1.
Odczytaj dostęp do właściwości dla wszystkich atrybutów dla wszystkich obiektów podrzędnych PublicFolder

Te uprawnienia są stosowane do wszystkich domen w lesie.
Opcjonalnie można podać wartość "Odróżnionyname" w parametrze ADobjectDN, aby ustawić te uprawnienia tylko dla tego obiektu usługi AD (w tym dziedziczenie do obiektów podrzędnych).

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PRZYKŁAD 3
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PRZYKŁAD 4
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta Active Directory, które jest lub będzie używane przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta Active Directory, która jest lub będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Wartość Odróżnionename konta Active Directory, która jest lub będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Wyróżniającname docelowego obiektu usługi AD, aby ustawić uprawnienia (opcjonalnie)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Opcjonalny parametr wskazujący, czy nie należy aktualizować kontenera AdminSDHolder przy użyciu tych uprawnień

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zainicjuj Active Directory Las i domenę dla funkcji mS-DS-ConsistencyGuid.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncMsDsConsistencyGuidPermissions udzieli wymaganych uprawnień do konta synchronizacji usługi AD, co obejmuje następujące elementy:
1.
Dostęp do właściwości odczytu/zapisu w atrybucie mS-DS-ConsistencyGuid dla wszystkich obiektów podrzędnych użytkownika

Te uprawnienia są stosowane do wszystkich domen w lesie.
Opcjonalnie można podać wartość "Odróżnionyname" w parametrze ADobjectDN, aby ustawić te uprawnienia tylko dla tego obiektu usługi AD (w tym dziedziczenie do obiektów podrzędnych).

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PRZYKŁAD 3
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PRZYKŁAD 4
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta Active Directory, które jest lub będzie używane przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta Active Directory, która jest lub będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Wartość Odróżnionename konta Active Directory, która jest lub będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Wyróżniającname docelowego obiektu usługi AD, aby ustawić uprawnienia (opcjonalnie)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Opcjonalny parametr wskazujący, czy nie należy aktualizować kontenera AdminSDHolder przy użyciu tych uprawnień

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zainicjuj Las Active Directory i domenę na potrzeby synchronizacji skrótów haseł.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncPasswordHashSyncPermissions udzieli wymaganych uprawnień do konta synchronizacji usługi AD, co obejmuje następujące elementy:
1.
Replikowanie zmian w katalogu
2.
Wszystkie zmiany katalogu replikowanego

Te uprawnienia są przyznawane wszystkim domenom w lesie.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta Active Directory, która będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta Active Directory, która będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Wyróżniająca wartość konta Active Directory, która będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zainicjuj Active Directory Las i domenę na potrzeby zapisywania zwrotnego haseł z usługi Azure AD.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncPasswordWritebackPermissions udzieli wymaganych uprawnień do konta synchronizacji usługi AD, co obejmuje następujące elementy:
1.
Zresetuj hasło dla podrzędnych obiektów użytkownika
2.
Zapisz dostęp do właściwości w atrybucie lockoutTime dla wszystkich obiektów podrzędnych użytkownika
3.
Zapisz dostęp do właściwości w atrybucie pwdLastSet dla wszystkich obiektów podrzędnych użytkownika

Te uprawnienia są stosowane do wszystkich domen w lesie.
Opcjonalnie można podać wartość "Odróżnionyname" w parametrze ADobjectDN, aby ustawić te uprawnienia tylko dla tego obiektu usługi AD (w tym dziedziczenie do obiektów podrzędnych).

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PRZYKŁAD 3
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PRZYKŁAD 4
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta Active Directory, które jest lub będzie używane przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta Active Directory, która jest lub będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Wartość Odróżnionename konta Active Directory, która jest lub będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Wyróżniającname docelowego obiektu usługi AD, aby ustawić uprawnienia (opcjonalnie)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Opcjonalny parametr wskazujący, czy nie należy aktualizować kontenera AdminSDHolder przy użyciu tych uprawnień

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zwiększ uprawnienia do obiektu usługi AD, który nie znajduje się w innej grupie zabezpieczeń chronionej przez usługi AD.
Typowym przykładem jest konto programu AD Connect (MSOL) utworzone automatycznie przez program AAD Connect.
To konto ma uprawnienia replikacji wszystkich domen, jednak można je łatwo złamać, ponieważ nie jest chronione.

### <a name="syntax"></a>SKŁADNIA

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncRestrictedPermissions będzie zacieśniać uprawnienia oo podane konto.
Podwyższenie poziomu uprawnień obejmuje następujące kroki:
1. Wyłącz dziedziczenie dla określonego obiektu
2. Usuń wszystkie wpisy kontroli dostępu do określonego obiektu, z wyjątkiem ACE określonych dla siebie.
Chcemy zachować domyślne uprawnienia, które są niezmienione, gdy nastąpi samodzielne.
3. Przypisz te określone uprawnienia:

   | Typ | Nazwa | Access | Dotyczy: |
   |------|------|--------|------------|
   | Zezwalaj | SYSTEM | Pełna kontrola | Ten obiekt |
   | Zezwalaj | Enterprise Admins | Pełna kontrola | Ten obiekt |
   | Zezwalaj | Administratorzy domeny | Pełna kontrola | Ten obiekt | 
   | Zezwalaj | Administratorzy | Pełna kontrola | Ten obiekt |
   | Zezwalaj | Kontrolery domeny przedsiębiorstwa | Wyświetl zawartość <br> Odczyt wszystkich właściwości <br> Uprawnienia do odczytu | Ten obiekt |
   | Zezwalaj | Uwierzytelnieni użytkownicy | Wyświetl zawartość <br> Odczyt wszystkich właściwości <br> Uprawnienia do odczytu | Ten obiekt |

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Wartość wyróżniająca konta Active Directory którego uprawnienia muszą zostać zaostrzone.
Jest to zazwyczaj konto MSOL_nnnnnnnnnn lub niestandardowe konto domeny, które jest skonfigurowane w łączniku usługi AD.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credential"></a>-Credential
Poświadczenia administratora, które ma niezbędne uprawnienia do ograniczenia uprawnień na koncie ADConnectorAccountDN. Jest to zwykle administrator przedsiębiorstwa lub domeny. Użyj w pełni kwalifikowanej nazwy domeny konta administratora, aby uniknąć niepowodzeń wyszukiwania kont.
Przykład: CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
Gdy DisableCredentialValidation jest używany, funkcja nie sprawdza, czy poświadczenia podane w poświadczeniu są prawidłowe w usłudze AD i czy podane konto ma odpowiednie uprawnienia, aby ograniczyć uprawnienia do konta ADConnectorAccountDN.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zainicjuj Active Directory Las i domenę na potrzeby zapisywania zwrotnego grup z usługi Azure AD.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncUnifiedGroupWritebackPermissions udzieli wymaganych uprawnień do konta synchronizacji usługi AD, co obejmuje następujące elementy:
1.
Ogólne odczyt/zapis, usuwanie, usuwanie drzewa i element podrzędny Create\Delete dla wszystkich typów obiektów grup i podobiektach

Te uprawnienia są stosowane do wszystkich domen w lesie.
Opcjonalnie można podać wartość "Odróżnionyname" w parametrze ADobjectDN, aby ustawić te uprawnienia tylko dla tego obiektu usługi AD (w tym dziedziczenie do obiektów podrzędnych).
W takim przypadku ADobjectDN będzie nazwą wyróżniającą kontenera, który ma zostać połączony z funkcją GroupWriteback.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PRZYKŁAD 3
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PRZYKŁAD 4
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta Active Directory, które jest lub będzie używane przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta Active Directory, która jest lub będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Wartość Odróżnionename konta Active Directory, która jest lub będzie używana przez Azure AD Connect synchronizacji do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
Wyróżniającname docelowego obiektu usługi AD, aby ustawić uprawnienia (opcjonalnie)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Opcjonalny parametr wskazujący, czy nie należy aktualizować kontenera AdminSDHolder przy użyciu tych uprawnień

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>STRESZCZENIE
Pokazuje uprawnienia określonego obiektu usługi AD.

### <a name="syntax"></a>SKŁADNIA

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Ta funkcja zwraca wszystkie uprawnienia usługi AD aktualnie ustawione dla danego obiektu usługi AD podane w parametrze-ADobjectDN.
ADobjectDN musi być podana w formacie wyróżniającym.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Fill ADobjectDN Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .
