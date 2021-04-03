---
title: 'Azure AD Connect: ADSyncTools programu PowerShell | Microsoft Docs'
description: Ten dokument zawiera informacje referencyjne dotyczące modułu programu PowerShell ADSyncTools. PSM1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1652c52dcc6870e396d74a2f38fe63c304d37df5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97672272"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: ADSyncTools programu PowerShell
Poniższa dokumentacja zawiera informacje dotyczące modułu PowerShell ADSyncTools. PSM1, który jest dołączony do Azure AD Connect.

## <a name="install-the-adsynctools-powershell-module"></a>Instalowanie modułu ADSyncTools PowerShell
Aby zainstalować moduł ADSyncTools PowerShell, wykonaj następujące czynności:

1.  Otwórz program Windows PowerShell z naszemu kontuą administracyjną
2.  Wpisz lub skopiuj i wklej następujące elementy: 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Naciśnij klawisz ENTER.
4.  Aby sprawdzić, czy moduł został zainstalowany, wprowadź lub skopiuj i wklej następujący tekst "
    ```powershell
    Get-module AdSyncTools
    ```
5.  Powinny teraz być widoczne informacje o module.


## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>STRESZCZENIE
Czyszczenie mS-ds-ConsistencyGuid z użytkownika usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Wyczyść wartość w temacie mS-ds-ConsistencyGuid dla docelowego użytkownika usługi AD

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Użytkownik docelowy w usłudze AD do ustawienia

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnienie w postaci streszczenia}}

### <a name="syntax"></a>SKŁADNIA

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>OPIS
{{Wypełnienie w opisie}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Dodaj tutaj opis przykładu}}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnienie w postaci streszczenia}}

### <a name="syntax"></a>SKŁADNIA

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
{{Wypełnienie w opisie}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Dodaj tutaj opis przykładu}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-database"></a>-Database
{{Opis wypełnienia bazy danych}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Wystąpienie
{{Opis wystąpienia wypełnienia}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Opis hasła wypełnienia}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{Fill Server Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{Fill-Description UserName}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>STRESZCZENIE
Eksportuj raport ConsistencyGuid

### <a name="syntax"></a>SKŁADNIA

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Generuje raport ConsistencyGuid na podstawie pliku CSV importu z Import-ADSyncToolsImmutableIdMigration

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Użyj alternatywnego identyfikatora logowania (poczta)

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

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
Nazwa pliku wyjściowego dla plików CSV i dzienników

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnienie w postaci streszczenia}}

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>OPIS
{{Wypełnienie w opisie}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Dodaj tutaj opis przykładu}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-hostName
{{Fill opis nazwy hosta}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>STRESZCZENIE
Pobierz użytkownika z usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Zwraca obiekt usługi AD do wykonania: obsługa wiele lasów

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Użytkownik docelowy w usłudze AD do ustawiania ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>STRESZCZENIE
Pobierz mS-ds-ConsistencyGuid z użytkownika usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Zwraca wartość w atrybucie mS-ds-ConsistencyGuid docelowego użytkownika usługi AD w formacie identyfikatora GUID

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Użytkownik docelowy w usłudze AD do ustawienia

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>STRESZCZENIE
Pobieranie ObjectGuid z użytkownika usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Zwraca wartość w atrybucie ObjectGUID docelowego użytkownika usługi AD w formacie identyfikatora GUID

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Użytkownik docelowy w usłudze AD do ustawienia

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>STRESZCZENIE
Pobierz historię przebiegu połączenia usługi AAD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja zwracająca historię uruchomienia połączenia usługi AAD w formacie XML

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-days"></a>— Dni
{{Fill dni Description}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>STRESZCZENIE
Uzyskaj użytkowników z SourceAnchor zmienionymi błędami

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja wysyła zapytanie do historii uruchamiania programu AAD Connect i eksportuje wszystkich użytkowników raportujących błąd: "atrybut SourceAnchor został zmieniony".

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
#Required Parameters
```

$sourcePath = Read-Host-Prompt "wprowadź ścieżkę do pliku dziennika o nazwie pliku" # " \<Source_Path\> " $outputPath = Read-Host-Prompt "wprowadź ścieżkę do pliku wyjściowego z nazwą pliku" # " \<Out_Path\>

 Get-ADSyncToolsUsersSourceAnchorChanged-ścieżka_źródłowa $sourcePath-outputPath $outputPath

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-sourcepath"></a>-ścieżka_źródłowa
{{Fill opis sourcePath}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{Fill outputPath Description}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>STRESZCZENIE
Importuj ImmutableID z usługi AAD

### <a name="syntax"></a>SKŁADNIA

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>OPIS
Generuje plik ze wszystkimi synchronizowanymi użytkownikami usługi Azure AD zawierającymi wartość ImmutableID w wymaganiach w formacie identyfikatora GUID: MSOnline module PowerShell

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-output"></a>-Output
Wyjściowy plik CSV

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

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Pobierz zsynchronizowanych użytkowników z Kosza usługi Azure AD

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

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnienie w postaci streszczenia}}

### <a name="syntax"></a>SKŁADNIA

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
{{Wypełnienie w opisie}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Dodaj tutaj opis przykładu}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-query"></a>-Zapytanie
{{Opis zapytania Fill}}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{Fill — Opis połączenia}}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>STRESZCZENIE
Skrypt służący do usuwania wygasłych certyfikatów z atrybutu UserCertificate

### <a name="syntax"></a>SKŁADNIA

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>OPIS
Ten skrypt przyjmuje wszystkie obiekty z docelowej jednostki organizacyjnej w Active Directory domeny filtrowane według klasy obiektów (użytkownik/komputer) i usuwa wszystkie wygasłe certyfikaty obecne w atrybucie UserCertificate.
Domyślnie (tryb w trybie niezmienionym) będzie tworzyć kopie zapasowe wygasłe certyfikaty tylko do pliku, a nie wszystkie zmiany w usłudze AD.
Jeśli używasz-$false, to każdy wygasły certyfikat obecny w atrybucie UserCertificate dla tych obiektów zostanie usunięty z usługi AD po skopiowaniu do pliku.
Dla każdego certyfikatu zostanie utworzona kopia zapasowa z rozdzieloną nazwą pliku: ObjectClass_ObjectGUID_CertThumprint. cer skrypt utworzy również plik dziennika w formacie CSV pokazujący wszystkich użytkowników z certyfikatami, które są ważne lub wygasłe, w tym rzeczywiste działanie wykonane (pominięte/wyeksportowane/usunięte).

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates-TargetOU "OU = users = użytkownicy, OU = Corp, DC = contoso, DC = com" — Klasa ObjectClass

#### <a name="example-2"></a>PRZYKŁAD 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates-TargetOU "OU = komputery, OU = Corp, DC = contoso, DC = com"-ObjectClass Computer-w $false

### <a name="parameters"></a>PARAMETRY

#### <a name="-targetou"></a>-TargetOU
Docelowa jednostka organizacyjna do wyszukiwania obiektów usługi AD

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

#### <a name="-backuponly"></a>— W dowolnym momencie
Nie spowoduje to usunięcia żadnych certyfikatów z usługi AD

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Filtr klasy obiektów

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>STRESZCZENIE
Krótki opis

### <a name="syntax"></a>SKŁADNIA

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>OPIS
Długi opis

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnienie w postaci streszczenia}}

### <a name="syntax"></a>SKŁADNIA

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>OPIS
{{Wypełnienie w opisie}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Dodaj tutaj opis przykładu}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-hostName
{{Fill opis nazwy hosta}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>STRESZCZENIE
(DO DO) Przywraca atrybut UserCertificate usługi AD z pliku certyfikatu

### <a name="syntax"></a>SKŁADNIA

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>OPIS
Długi opis

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>STRESZCZENIE
Ustaw mS-ds-ConsistencyGuid na użytkownika usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Ustaw wartość w atrybucie mS-ds-ConsistencyGuid dla docelowego użytkownika usługi AD

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Użytkownik docelowy w usłudze AD do ustawiania ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Wartość
ImmutableId (tablica bajtowa, identyfikator GUID, ciąg identyfikatora GUID lub ciąg base64)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnienie w postaci streszczenia}}

### <a name="syntax"></a>SKŁADNIA

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>OPIS
{{Wypełnienie w opisie}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Dodaj tutaj opis przykładu}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-hostName
{{Fill opis nazwy hosta}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
{{Opis portu wypełnienia}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>STRESZCZENIE
Tworzy plik śledzenia z kroku importowania usługi AD i

### <a name="syntax"></a>SKŁADNIA

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Śledzi wszystkie zapytania LDAP programu AAD Connect AD Run z danego punktu kontrolnego usługi AD wodnego (plik cookie partycji). Tworzy plik śledzenia ". \ ADimportTrace_yyyyMMddHHmmss. log" w bieżącym folderze.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Fill ADConnectorXML Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-DC
Plik XML eksportu łącznika usługi AD

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
Docelowy kontroler domeny

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filter
Główna nazwa wyróżniająca lasu

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
Typy obiektów usługi AD do śledzenia \> * = wszystkie typy obiektów

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

#### <a name="-adwatermark"></a>-ADwatermark
Jeśli już działa jako administrator domeny, nie ma potrzeby podania poświadczeń usługi AD.
Ręczne wprowadzanie znaku wodnego zamiast pliku XML, np. $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA (...)"

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>STRESZCZENIE
Krótki opis

### <a name="syntax"></a>SKŁADNIA

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>OPIS
Długi opis

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-context"></a>-Kontekst
Opis pomocy param1

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

#### <a name="-server"></a>-Server
Opis pomocy param2

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Opis pomocy param2

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filter
Opis pomocy param2

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>STRESZCZENIE
Aktualizuje użytkowników przy użyciu nowego ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SKŁADNIA

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Aktualizuje użytkowników przy użyciu nowej wartości ConsistencyGuid (ImmutableId) pobranej z raportu ConsistencyGuid ta funkcja obsługuje parametr WhatIf. Uwaga: należy zaimportować raport ConsistencyGuid z kartą Demiliter

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-distinguishedname"></a>-Odróżnionyname
DistinguishedName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Action
Akcja

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
Nazwa pliku wyjściowego dla plików dziennika

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
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
