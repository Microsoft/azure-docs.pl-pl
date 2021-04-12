---
title: Diagnozowanie problemów z pulpitem wirtualnym systemu Windows (klasyczny) — Azure
description: Jak zdiagnozować problemy przy użyciu funkcji diagnostyki pulpitu wirtualnego systemu Windows (klasycznego).
author: Heidilohr
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e23a1e9a2a0118402df0d9b8869f170762a52284
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444959"
---
# <a name="identify-and-diagnose-issues-in-windows-virtual-desktop-classic"></a>Identyfikowanie i diagnozowanie problemów z pulpitem wirtualnym systemu Windows (klasyczny)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../diagnostics-role-service.md).

Pulpit wirtualny systemu Windows oferuje funkcję diagnostyki, która umożliwia administratorowi identyfikowanie problemów za pomocą jednego interfejsu. Role pulpitu wirtualnego systemu Windows rejestrują aktywność diagnostyczną za każdym razem, gdy użytkownik współdziała z systemem. Każdy dziennik zawiera istotne informacje, takie jak role pulpitu wirtualnego systemu Windows związane z transakcjami, komunikatami o błędach, informacjami o dzierżawie i informacjami o użytkowniku. Działania diagnostyczne są tworzone przez działania wykonywane zarówno przez użytkownika końcowego, jak i administracyjne. można je podzielić na trzy główne zasobniki:

* Działania dotyczące subskrypcji kanału informacyjnego: użytkownik końcowy wyzwala te działania przy każdej próbie połączenia się ze źródłem danych za pomocą aplikacji Pulpit zdalny Microsoft.
* Działania połączenia: użytkownik końcowy wyzwala te działania przy każdej próbie połączenia się z pulpitem lub funkcją RemoteApp za pomocą aplikacji Pulpit zdalny Microsoft.
* Działania związane z zarządzaniem: Administrator wyzwala te działania za każdym razem, gdy wykonują operacje zarządzania w systemie, takie jak tworzenie pul hostów, przypisywanie użytkowników do grup aplikacji i tworzenie przypisań ról.

Połączenia, które nie docierają do pulpitu wirtualnego systemu Windows, nie będą wyświetlane w wynikach diagnostyki, ponieważ sama usługa roli diagnostyki jest częścią pulpitu wirtualnego systemu Windows. Problemy z połączeniem pulpitu wirtualnego systemu Windows mogą wystąpić, gdy użytkownik końcowy napotyka problemy z łącznością sieciową.

Aby rozpocząć, [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono. Następnie uruchom następujące polecenie cmdlet, aby zalogować się do konta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Diagnozowanie problemów przy użyciu programu PowerShell

Diagnostyka pulpitu wirtualnego systemu Windows używa tylko jednego polecenia cmdlet programu PowerShell, ale zawiera wiele opcjonalnych parametrów, aby pomóc w zawężaniu i izolowaniu problemów. Poniższe sekcje zawierają listę poleceń cmdlet, które można uruchomić w celu zdiagnozowania problemów. Większość filtrów można zastosować razem. Wartości wymienione w nawiasach, takie jak `<tenantName>` , powinny być zastępowane wartościami, które dotyczą danej sytuacji.

>[!IMPORTANT]
>Funkcja diagnostyki służy do rozwiązywania problemów z jednym użytkownikiem. Wszystkie zapytania przy użyciu programu PowerShell muszą zawierać parametry *-username* lub *-ActivityId* . Aby uzyskać możliwości monitorowania, użyj Log Analytics. Aby uzyskać więcej informacji o sposobach wysyłania danych diagnostycznych do obszaru roboczego [, zobacz używanie log Analytics dla funkcji diagnostyki](diagnostics-log-analytics-2019.md) .

### <a name="filter-diagnostic-activities-by-user"></a>Filtruj działania diagnostyczne według użytkownika

Parametr **-username** zwraca listę działań diagnostycznych zainicjowanych przez określonego użytkownika, jak pokazano w poniższym przykładowym poleceniu cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

Parametr **-username** może być również połączony z innymi opcjonalnymi parametrami filtrowania.

### <a name="filter-diagnostic-activities-by-time"></a>Filtrowanie działań diagnostycznych według czasu

Można filtrować listę zwróconych działań diagnostycznych za pomocą parametrów **-StartTime** i **-Endtime** . Parametr **-StartTime** zwróci listę działań diagnostycznych, zaczynając od określonej daty, jak pokazano w poniższym przykładzie.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

Parametr **-Endtime** można dodać do polecenia cmdlet z parametrem **-StartTime** , aby określić określony okres, dla którego mają być odbierane wyniki. Poniższe przykładowe polecenie cmdlet zwróci listę działań diagnostycznych z zakresu od 1 sierpnia do 10 sierpnia.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

Parametry **-StartTime** i **-Endtime** można także łączyć z innymi opcjonalnymi parametrami filtrowania.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Filtruj działania diagnostyczne według typu działania

Działania diagnostyczne można również filtrować według typu działania z parametrem **-ActivityType** . Następujące polecenie cmdlet zwróci listę połączeń użytkowników końcowych:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

Następujące polecenie cmdlet zwróci listę zadań zarządzania administratorami:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

Polecenie cmdlet **Get-RdsDiagnosticActivities** nie obsługuje obecnie określania kanału informacyjnego jako działaniatype.

### <a name="filter-diagnostic-activities-by-outcome"></a>Filtruj działania diagnostyczne według wyniku

Zwracaną listę działań diagnostycznych można filtrować według wyniku z parametrem **wyniku** . Poniższe przykładowe polecenie cmdlet zwróci listę pomyślnych działań diagnostycznych.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

Poniższe przykładowe polecenie cmdlet zwróci listę zakończonych niepowodzeniem działań diagnostycznych.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

Parametr **-wyniku** można także połączyć z innymi opcjonalnymi parametrami filtrowania.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Pobierz określoną aktywność diagnostyczną według identyfikatora działania

Parametr **-ActivityId** zwraca określone działanie diagnostyczne, jeśli istnieje, jak pokazano w poniższym przykładowym poleceniu cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Wyświetl komunikaty o błędach dla działania zakończonego niepowodzeniem według identyfikatora działania

Aby wyświetlić komunikaty o błędach dla działania zakończonego niepowodzeniem, należy uruchomić polecenie cmdlet z parametrem **--szczegółowym** . Listę błędów można wyświetlić, uruchamiając polecenie cmdlet **Select-Object** .

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Pobierz szczegółowe działania diagnostyczne

**-Szczegółowy** parametr zawiera dodatkowe szczegóły dla każdej zwróconego działania diagnostycznego. Format każdego działania różni się w zależności od typu działania. Parametr **-Details** można dodać do dowolnego zapytania **Get-RdsDiagnosticActivities** , jak pokazano w poniższym przykładzie.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Typowe scenariusze błędów

Scenariusze błędów są kategoryzowane w wewnętrznej usłudze i na pulpicie wirtualnym systemu Windows.

* Problem wewnętrzny: określa scenariusze, które nie mogą zostać skorygowane przez administratora dzierżawy i muszą zostać rozwiązane jako problem z pomocą techniczną. Przekazując informacje zwrotne za pomocą [społeczności technicznej pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), należy uwzględnić identyfikator działania i przybliżony przedział czasu, w którym wystąpił problem.
* Problem zewnętrzny: odnosi się do scenariuszy, które mogą zostać skorygowane przez administratora systemu. Są one zewnętrzne dla pulpitu wirtualnego systemu Windows.

W poniższej tabeli wymieniono typowe błędy, w których administratorzy mogą pracować.

>[!NOTE]
>Ta lista zawiera najczęstsze błędy i jest aktualizowana regularnie erze. Aby mieć pewność, że masz najnowsze informacje, pamiętaj o tym, aby ponownie zaewidencjonować ten artykuł co najmniej raz w miesiącu.

### <a name="external-management-error-codes"></a>Kody błędów zarządzania zewnętrznego

|Kod liczbowy|Kod błędu|Sugerowane rozwiązanie|
|---|---|---|
|1322|ConnectionFailedNoMappingOfSIDinAD|Użytkownik nie jest członkiem Azure Active Directory. Postępuj zgodnie z instrukcjami w [Centrum administracyjne usługi Active Directory](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) , aby je dodać.|
|3|UnauthorizedAccess|Użytkownik, który próbował uruchomić administracyjne polecenie cmdlet programu PowerShell, nie ma uprawnień do wykonania tej operacji lub niewpisanej nazwy użytkownika.|
|1000|TenantNotFound|Wprowadzona nazwa dzierżawy nie jest zgodna z żadną z istniejących dzierżawców. Przejrzyj nazwę dzierżawy pod kątem pisowni i spróbuj ponownie.|
|1006|TenantCannotBeRemovedHasSessionHostPools|Dzierżawy nie można usunąć, dopóki nie zawiera ona obiektów. Najpierw usuń pule hostów sesji, a następnie spróbuj ponownie.|
|2000|HostPoolNotFound|Wprowadzona nazwa puli hostów nie jest zgodna z żadną istniejącą pulą hostów. Przejrzyj nazwę puli hostów pod kątem pisowni i spróbuj ponownie.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|Nie można usunąć puli hostów, o ile zawiera ona obiekty. Najpierw usuń wszystkie grupy aplikacji w puli hostów.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Przed usunięciem puli hostów sesji Usuń wszystkie hosty sesji.|
|5001|SessionHostNotFound|Poszukiwany Host sesji może być w trybie offline. Sprawdź stan puli hostów.|
|5008|SessionHostUserSessionsExist |Musisz wylogować wszystkich użytkowników na hoście sesji przed wykonaniem zamierzonego działania zarządzania.|
|6000|AppGroupNotFound|Wprowadzona nazwa grupy aplikacji nie jest zgodna z żadną z istniejących grup aplikacji. Przejrzyj nazwę grupy aplikacji pod kątem pisowni i spróbuj ponownie.|
|6022|RemoteAppNotFound|Wprowadzona nazwa usługi RemoteApp nie jest zgodna z żadną usługą RemoteApp. Przejrzyj nazwę usługi RemoteApp pod kątem literówek i spróbuj ponownie.|
|6010|PublishedItemsExist|Nazwa zasobu, który próbujesz opublikować, jest taka sama jak w przypadku zasobu, który już istnieje. Zmień nazwę zasobu i spróbuj ponownie.|
|7002|NameNotValidWhiteSpace|Nie używaj odstępu w nazwie.|
|8000|InvalidAuthorizationRoleScope|Wprowadzona nazwa roli nie jest zgodna z żadną z istniejących nazw ról. Przejrzyj nazwę roli pod kątem pisowni i spróbuj ponownie. |
|8001|UserNotFound |Wprowadzona nazwa użytkownika nie jest zgodna z żadną z istniejących nazw użytkowników. Sprawdź nazwę literówków i spróbuj ponownie.|
|8005|UserNotFoundInAAD |Wprowadzona nazwa użytkownika nie jest zgodna z żadną z istniejących nazw użytkowników. Sprawdź nazwę literówków i spróbuj ponownie.|
|8008|TenantConsentRequired|Postępuj zgodnie z instrukcjami w [tym miejscu](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) , aby wyrazić zgodę na dzierżawę.|

### <a name="external-connection-error-codes"></a>Kody błędów połączenia zewnętrznego

|Kod liczbowy|Kod błędu|Sugerowane rozwiązanie|
|---|---|---|
|-2147467259|ConnectionFailedAdErrorNoSuchMember|Użytkownik nie jest członkiem Active Directory. Postępuj zgodnie z instrukcjami w [Centrum administracyjne usługi Active Directory](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) , aby je dodać.|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|Host sesji nie został poprawnie przyłączony do Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|Połączenia nie powiodły się, ponieważ Host sesji jest niedostępny. Sprawdź kondycję hosta sesji.|
|-2146233088|ConnectionFailedClientDisconnect|Jeśli ten błąd występuje często, upewnij się, że komputer użytkownika jest połączony z siecią.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|Sesja, z którą użytkownik hosta próbował się połączyć, nie jest w dobrej kondycji. Debuguj maszynę wirtualną.|
|-2146233088|ConnectionFailedUserNotAuthorized|Użytkownik nie ma uprawnień dostępu do opublikowanej aplikacji lub pulpitu. Ten błąd może pojawić się po usunięciu opublikowanych zasobów przez administratora. Poproszenie użytkownika o odświeżenie źródła danych w aplikacji Pulpit zdalny.|
|2|FileNotFound|Aplikacja, do której próbowano uzyskać dostęp, jest nieprawidłowo zainstalowana lub ustawiona na niepoprawną ścieżkę.|
|3|InvalidCredentials|Wprowadzona nazwa użytkownika lub hasło nie są zgodne z istniejącymi nazwami użytkowników ani hasłami. Przejrzyj poświadczenia pod kątem pisowni i spróbuj ponownie.|
|8|ConnectionBroken|Połączenie między klientem a bramą lub serwerem zostało usunięte. Nie trzeba wykonywać żadnych czynności, chyba że wystąpi nieoczekiwany.|
|14|UnexpectedNetworkDisconnect|Połączenie z siecią zostało usunięte. Poproszenie użytkownika o ponowne nawiązanie połączenia.|
|24|ReverseConnectFailed|Maszyna wirtualna hosta nie ma bezpośredniego wglądu w szczegółowe informacje z bramą usług pulpitu zdalnego. Upewnij się, że można rozpoznać adres IP bramy.|
|1322|ConnectionFailedNoMappingOfSIDinAD|Użytkownik nie jest członkiem Active Directory. Postępuj zgodnie z instrukcjami w [Centrum administracyjne usługi Active Directory](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) , aby je dodać.|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o rolach w ramach pulpitu wirtualnego systemu Windows, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup-2019.md).

Aby wyświetlić listę dostępnych poleceń cmdlet programu PowerShell dla pulpitu wirtualnego systemu Windows, zobacz [Dokumentacja programu PowerShell](/powershell/windows-virtual-desktop/overview).
