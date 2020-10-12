---
title: 'Azure AD Connect: ADConnectivityTools programu PowerShell | Microsoft Docs'
description: Ten dokument zawiera informacje referencyjne dotyczące modułu programu PowerShell ADConnectivityTools. PSM1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d5e06d0a6f7693ca9cc69f05f6b2318d6b60b5e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89070806"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect: ADConnectivityTools programu PowerShell

Poniższa dokumentacja zawiera informacje dotyczące modułu PowerShell ADConnectivityTools. PSM1, który jest dołączony do Azure AD Connect.

## <a name="confirm-dnsconnectivity"></a>Confirm-DnsConnectivity

### <a name="synopsis"></a>STRESZCZENIE

Wykrywa lokalne problemy z usługą DNS.

### <a name="syntax"></a>SKŁADNIA

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Uruchamia lokalne testy łączności DNS.
Aby skonfigurować łącznik Active Directory, użytkownik musi mieć zarówno nazwę resolutionthe dla lasu, do którego próbuje nawiązać połączenie, jak i kontrolery domeny skojarzone z tym lasem.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Las

Określa nazwę lasu, dla którego ma zostać przetestowany.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DC

Określ kontrolery domeny do przetestowania.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Zwraca wynik tej diagnostyki w postaci PSObject.
Niepotrzebne podczas ręcznej interakcji z tym narzędziem.

```yml
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

## <a name="confirm-forestexists"></a>Confirm-ForestExists

### <a name="synopsis"></a>STRESZCZENIE

Określa, czy istnieje określony las.

### <a name="syntax"></a>SKŁADNIA

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>OPIS

Wysyła zapytanie do serwera DNS o adresy IP skojarzone z lasem.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Las

Określa nazwę lasu, dla którego ma zostać przetestowany.

```yml
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

## <a name="confirm-functionallevel"></a>Confirm-FunctionalLevel

### <a name="synopsis"></a>STRESZCZENIE

Weryfikuje poziom funkcjonalności lasu usługi AD.

### <a name="syntax"></a>SKŁADNIA

#### <a name="samaccount"></a>SamAccount

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Sprawdza, czy poziom funkcjonalności lasu usługi AD jest równy lub większy niż określony MinAdForestVersion (WindowsServer2003).
Można zażądać konta (domena \ nazwa_użytkownika) i hasła.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>PRZYKŁAD 3

```powershell
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Las

Las docelowy.
Wartość domyślna to Las aktualnie zalogowanego użytkownika.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

Docelowy obiekt ForestFQDN.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, zamiast żądać poświadczeń niestandardowych od użytkownika.

```yml
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

## <a name="confirm-networkconnectivity"></a>Confirm-NetworkConnectivity

### <a name="synopsis"></a>STRESZCZENIE

Wykrywa problemy z łącznością sieciową.

### <a name="syntax"></a>SKŁADNIA

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Uruchamia testy łączności z siecią lokalną.

W przypadku testów sieci lokalnych usługa AAD Connect musi mieć możliwość komunikowania się z nazwanymi kontrolerami domeny w portach 53 (DNS), 88 (Kerberos) i 389 (LDAP) Większość organizacji uruchamia system DNS w swoich kontrolerach domeny, co oznacza, że ten test jest obecnie zintegrowany.
Jeśli określono inny serwer DNS, należy pominąć port 53.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-dcs"></a>-DC

Określ kontrolery domeny do przetestowania.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort

Jeśli użytkownik nie korzysta z usług DNS udostępnianych przez witrynę usługi AD/kontroler domeny logowania, może chcieć pominąć sprawdzanie portu 53.
Użytkownik musi nadal być w stanie rozwiązać _.ldap._tcp.\<forestfqdn\>
Aby konfiguracja łącznika Active Directory powiodła się.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Zwraca wynik tej diagnostyki w postaci PSObject.
Niepotrzebne podczas ręcznej interakcji z tym narzędziem.

```yml
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

## <a name="confirm-targetsarereachable"></a>Confirm-TargetsAreReachable

### <a name="synopsis"></a>STRESZCZENIE

Określa, czy określony las i skojarzone z nim kontrolery domeny są osiągalne.

### <a name="syntax"></a>SKŁADNIA

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>OPIS

Uruchamia testy "ping" (czy komputer może nawiązać połączenie z komputerem docelowym za pomocą sieci i/lub Internetu)

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Las

Określa nazwę lasu, dla którego ma zostać przetestowany.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DC

Określ kontrolery domeny do przetestowania.

```yml
Type: Array
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

## <a name="confirm-validdomains"></a>Confirm-ValidDomains

### <a name="synopsis"></a>STRESZCZENIE

Sprawdź, czy domeny w uzyskanym FQDN lesie są dostępne

### <a name="syntax"></a>SKŁADNIA

#### <a name="samaccount"></a>SamAccount

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Sprawdź, czy wszystkie domeny w uzyskanym FQDN lesie są osiągalne, próbując pobrać DomainGuid i DomainDN.
Można zażądać konta (domena \ nazwa_użytkownika) i hasła.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>PRZYKŁAD 3

```powershell
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Las

Las docelowy.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

Docelowy obiekt ForestFQDN.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, zamiast żądać poświadczeń niestandardowych od użytkownika.

```yml
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

## <a name="confirm-validenterpriseadmincredentials"></a>Confirm-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>STRESZCZENIE

Sprawdza, czy użytkownik ma poświadczenia administratora przedsiębiorstwa.

### <a name="syntax"></a>SKŁADNIA

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Wyszukuje, czy podany użytkownik ma poświadczenia administratora przedsiębiorstwa.
Można zażądać konta (domena \ nazwa_użytkownika) i hasła.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, zamiast żądać poświadczeń niestandardowych od użytkownika.

```yml
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

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>STRESZCZENIE

Pobiera DomainFQDN z kombinacji konta i hasła.

### <a name="syntax"></a>SKŁADNIA

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Próbuje uzyskać obiekt domainFQDN z podanych poświadczeń.
Jeśli domainFQDN jest prawidłowy, zostanie zwrócona wartość DomainFQDNName lub RootDomainName w zależności od wybranej opcji użytkownika.
Można zażądać konta (domena \ nazwa_użytkownika) i hasła.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType

Żądany rodzaj danych, które zostaną pobrane.
Obecnie ograniczona do "DomainFQDNName" lub "RootDomainName".

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, zamiast żądać poświadczeń niestandardowych od użytkownika.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError

Parametr pomocniczy używany przez funkcję Start-NetworkConnectivityDiagnosisTools

```yml
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

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>STRESZCZENIE

Pobiera ForestFQDN z kombinacji konta i hasła.

### <a name="syntax"></a>SKŁADNIA

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Próbuje uzyskać ForestFQDN z podanych poświadczeń.
Można zażądać konta (domena \ nazwa_użytkownika) i hasła.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Las

Las docelowy. Wartość domyślna to domena aktualnie zalogowanego użytkownika.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, zamiast żądać poświadczeń niestandardowych od użytkownika.

```yml
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

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>STRESZCZENIE

Funkcja Main.

### <a name="syntax"></a>SKŁADNIA

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>OPIS

Uruchamia wszystkie dostępne mechanizmy, które weryfikują poświadczenia usługi AD są prawidłowe.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Las

Las docelowy.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount

W przypadku instalacji niestandardowych: Flaga $True, jeśli użytkownik wybrał opcję "Utwórz nowe konto usługi AD" w oknie konto lasu usługi AD kreatora AADConnect.
$False, jeśli użytkownik wybrał opcję "Użyj istniejącego konta usługi AD".
W przypadku instalacji ekspresowych: wartość tej zmiennej musi być $True dla instalacji ekspresowej.

```yml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName

Parametr, który wstępnie wypełnia pole username, gdy wymagane są poświadczenia użytkownika.

```yml
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

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>STRESZCZENIE

Funkcja Main dla testów łączności sieciowej.

### <a name="syntax"></a>SKŁADNIA

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Uruchamia testy łączności z siecią lokalną.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Las

Określa nazwę lasu do przetestowania.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credentials"></a>-Poświadczenia

Nazwa użytkownika i hasło użytkownika, na którym uruchomiono test.
Wymaga tego samego poziomu uprawnień, który jest wymagany do uruchomienia Kreatora Azure AD Connect.

```yml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-logfilelocation"></a>-LogFileLocation

Określa lokalizację pliku dziennika, który będzie zawierać dane wyjściowe tej funkcji.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DC

Określ kontrolery domeny do przetestowania.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-DisplayInformativeMessage

Flaga umożliwiająca wyświetlanie komunikatu o przeznaczeniu tej funkcji.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Zwraca wynik tej diagnostyki w postaci PSObject.
Nie trzeba określać podczas ręcznej interakcji z tym narzędziem.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-validcredentials"></a>-ValidCredentials

Wskazuje, czy poświadczenia wpisane przez użytkownika są prawidłowe.
Nie trzeba określać podczas ręcznej interakcji z tym narzędziem.

```yml
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
