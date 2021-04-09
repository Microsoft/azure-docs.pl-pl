---
title: Monitoruj lokalną ochronę hasłem usługi Azure AD
description: Dowiedz się, jak monitorować i przeglądać dzienniki dla ochrony hasłem w usłudze Azure AD dla środowiska lokalnego Active Directory Domain Services
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: edc246a414401c4c1c0248787eda0381fcd63037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741766"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>Monitorowanie i przeglądanie dzienników lokalnych środowisk ochrony haseł usługi Azure AD

Po wdrożeniu ochrony hasłem usługi Azure AD monitorowanie i raportowanie są istotnymi zadaniami. Ten artykuł zawiera szczegółowe informacje ułatwiające zapoznanie się z różnymi technikami monitorowania, w tym o tym, gdzie każda usługa rejestruje dane i jak raportować użycie ochrony hasłem usługi Azure AD.

Monitorowanie i raportowanie odbywa się przy użyciu komunikatów dziennika zdarzeń lub poleceń cmdlet programu PowerShell. Zarówno Agent DC, jak i usługi proxy rejestrują komunikaty dziennika zdarzeń. Wszystkie polecenia cmdlet programu PowerShell opisane poniżej są dostępne tylko na serwerze proxy (Zobacz moduł AzureADPasswordProtection PowerShell). Oprogramowanie agenta kontrolera domeny nie instaluje modułu programu PowerShell.

## <a name="dc-agent-event-logging"></a>Rejestrowanie zdarzeń agenta DC

Na każdym kontrolerze domeny oprogramowanie usługi Agent DC zapisuje wyniki każdej operacji walidacji hasła indywidualnego (i innego stanu) do lokalnego dziennika zdarzeń:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Dziennik administratora agenta kontrolera domeny to podstawowe źródło informacji na temat sposobu, w jaki działa oprogramowanie.

Należy pamiętać, że dziennik śledzenia jest domyślnie wyłączony.

Zdarzenia zarejestrowane przez różne składniki agenta kontrolera domeny mieszczą się w następujących zakresach:

|Składnik |Zakres identyfikatorów zdarzeń|
| --- | --- |
|Biblioteka DLL filtru haseł agenta DC| 10000-19999|
|Proces hostingu usługi agenta kontrolera domeny| 20000-29999|
|Logika walidacji zasad usługi agenta DC| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Dziennik zdarzeń administratora agenta DC

### <a name="password-validation-outcome-events"></a>Zdarzenia wyniku weryfikacji hasła

Na każdym kontrolerze domeny oprogramowanie usługi Agent DC zapisuje wyniki poszczególnych indywidualnych walidacji hasła do dziennika zdarzeń administratora agenta kontrolera domeny.

W przypadku pomyślnej operacji weryfikacji hasła istnieje zazwyczaj jedno zdarzenie rejestrowane z biblioteki DLL filtru haseł agenta kontrolera domeny. W przypadku niepowodzenia operacji weryfikacji hasła zwykle rejestrowane są dwa zdarzenia, jeden z usługi Agent DC i jeden z biblioteki DLL filtru haseł agenta kontrolera domeny.

Zdarzenia dyskretne umożliwiające przechwycenie tych sytuacji są rejestrowane na podstawie następujących czynników:

* Określa, czy hasło jest ustawiane czy zmieniane.
* Czy sprawdzanie poprawności danego hasła zakończyło się pomyślnie lub nie powiodło się.
* Czy Walidacja nie powiodła się z powodu zasad globalnych firmy Microsoft, zasad organizacyjnych lub kombinacji.
* Czy tryb tylko inspekcja jest obecnie włączony, czy wyłączony dla bieżących zasad haseł.

Zdarzenia związane z walidacją hasła klucza są następujące:

| Zdarzenie |Zmiana hasła |Ustawiono hasło|
| --- | :---: | :---: |
|Zdane |10014 |10015|
|Niepowodzenie (z powodu zasad haseł klienta)| 10016, 30002| 10017, 30003|
|Niepowodzenie (z powodu zasad haseł firmy Microsoft)| 10016, 30004| 10017, 30005|
|Niepowodzenie (ze względu na połączone zasady haseł firmy Microsoft i klientów)| 10016, 30026| 10017, 30027|
|Niepowodzenie (ze względu na nazwę użytkownika)| 10016, 30021| 10017, 30022|
|Przekazywanie tylko do inspekcji (nie powiodło się zasady haseł klienta)| 10024, 30008| 10025, 30007|
|Zakończyło się tylko Inspekcja (w przypadku niepowodzenia zasad haseł firmy Microsoft)| 10024, 30010| 10025, 30009|
|Zakończyło się tylko Inspekcja (nie udało się połączyć zasad haseł firmy Microsoft i klientów)| 10024, 30028| 10025, 30029|
|Przekazywanie tylko do inspekcji (nie powiodło się z powodu nazwy użytkownika)| 10016, 30024| 10017, 30023|

Przypadki w powyższej tabeli odnoszące się do "połączonych zasad" odnoszą się do sytuacji, w których znaleziono hasło użytkownika zawierające co najmniej jeden token z listy zakazanych haseł Microsoft i listy haseł wykluczonych od klientów.

Przypadki w powyższej tabeli odnoszące się do "Nazwa użytkownika" odnoszą się do sytuacji, w których znaleziono hasło użytkownika zawierające nazwę konta użytkownika i/lub jedną z przyjaznych nazw użytkownika. Każdy scenariusz spowoduje odrzucenie hasła użytkownika, gdy zasady są ustawione na wymuszanie lub przekazanie, jeśli zasady są w trybie inspekcji.

Gdy para zdarzeń jest rejestrowana razem, oba zdarzenia są jawnie skojarzone przez posiadanie tego samego korelacji.

### <a name="password-validation-summary-reporting-via-powershell"></a>Raportowanie podsumowania hasła w programie PowerShell

`Get-AzureADPasswordProtectionSummaryReport`Polecenie cmdlet może służyć do tworzenia widoku podsumowania działania weryfikacji hasła. Przykładowe dane wyjściowe tego polecenia cmdlet są następujące:

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

Zakres raportowania polecenia cmdlet może mieć wpływ na użycie jednego z parametrów – lasu,-Domain lub – kontroler domeny. Nie określono parametru – Las.

`Get-AzureADPasswordProtectionSummaryReport`Polecenie cmdlet działa przez wykonanie zapytania dotyczącego dziennika zdarzeń administratora agenta kontrolera domeny, a następnie zliczanie łącznej liczby zdarzeń, które odpowiadają każdej wyświetlonej kategorii wynik. Poniższa tabela zawiera mapowania między każdym wynikiem i odpowiednim IDENTYFIKATORem zdarzenia:

|Get-AzureADPasswordProtectionSummaryReport Właściwość |Identyfikator odpowiedniego zdarzenia|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Należy pamiętać, że `Get-AzureADPasswordProtectionSummaryReport` polecenie cmdlet jest dostarczane w postaci skryptu programu PowerShell, a jeśli to możliwe, może być przywoływane bezpośrednio w następującej lokalizacji:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> To polecenie cmdlet działa, otwierając sesję programu PowerShell na każdym kontrolerze domeny. Aby pomyślnie zadziałała, należy włączyć obsługę sesji zdalnej programu PowerShell na każdym kontrolerze domeny, a klient musi mieć wystarczające uprawnienia. Aby uzyskać więcej informacji na temat wymagań zdalnej sesji programu PowerShell, uruchom polecenie "Get-Help about_Remote_Troubleshooting" w oknie programu PowerShell.

> [!NOTE]
> To polecenie cmdlet działa zdalnie za pomocą zapytania o dziennik zdarzeń administratora usługi agenta kontrolera domeny. Jeśli dzienniki zdarzeń zawierają dużą liczbę zdarzeń, wykonanie polecenia cmdlet może zająć dużo czasu. Ponadto zbiorcze zapytania sieciowe dotyczące dużych zestawów danych mogą mieć wpływ na wydajność kontrolera domeny. W związku z tym to polecenie cmdlet powinno być używane starannie w środowiskach produkcyjnych.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Przykładowy komunikat dziennika zdarzeń dla zdarzenia o IDENTYFIKATORze 10014 (pomyślna zmiana hasła)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Przykładowy komunikat dziennika zdarzeń dla zdarzenia o IDENTYFIKATORze 10017 i 30003 (zestaw niezakończonych haseł)

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Przykładowy komunikat dziennika zdarzeń dla zdarzenia o IDENTYFIKATORze 30001 (hasło zostało zaakceptowane ze względu na brak dostępnych zasad)

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Przykładowy komunikat dziennika zdarzeń dla zdarzenia o IDENTYFIKATORze 30006 (trwa wymuszone nowe zasady)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Przykładowy komunikat dziennika zdarzeń dla zdarzenia o IDENTYFIKATORze 30019 (ochrona hasłem w usłudze Azure AD jest wyłączona)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Dziennik operacyjny agenta kontrolera domeny

Usługa agenta kontrolera domeny będzie również rejestrować zdarzenia związane z działaniem w następującym dzienniku:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Dziennik śledzenia agenta DC

Usługa agenta kontrolera domeny może również rejestrować pełne zdarzenia śledzenia na poziomie debugowania do następującego dziennika:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Rejestrowanie śledzenia jest domyślnie wyłączone.

> [!WARNING]
> Gdy ta funkcja jest włączona, dziennik śledzenia odbiera dużą liczbę zdarzeń i może mieć wpływ na wydajność kontrolera domeny. W związku z tym ten Rozszerzony Dziennik powinien być włączany tylko wtedy, gdy problem wymaga dokładniejszego zbadania, a następnie tylko przez minimalny czas.

## <a name="dc-agent-text-logging"></a>Rejestrowanie tekstu agenta kontrolera domeny

Usługę agenta DC można skonfigurować tak, aby zapisywać dane w dzienniku tekstowym przez ustawienie następującej wartości rejestru:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Rejestrowanie tekstu jest domyślnie wyłączone. Aby zmiany tej wartości zaczęły obowiązywać, wymagane jest ponowne uruchomienie usługi agenta DC. Po włączeniu usługa agenta DC zapisze w pliku dziennika w obszarze:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Dziennik tekstowy odbiera te same wpisy poziomu debugowania, które mogą być rejestrowane w dzienniku śledzenia, ale zazwyczaj jest to łatwiejszy format do przejrzenia i przeanalizowania.

> [!WARNING]
> Gdy ta funkcja jest włączona, ten dziennik otrzymuje dużą ilość zdarzeń i może mieć wpływ na wydajność kontrolera domeny. W związku z tym ten Rozszerzony Dziennik powinien być włączany tylko wtedy, gdy problem wymaga dokładniejszego zbadania, a następnie tylko przez minimalny czas.

## <a name="dc-agent-performance-monitoring"></a>Monitorowanie wydajności agenta DC

Oprogramowanie usługi Agent DC instaluje obiekt licznika wydajności o nazwie **Azure AD Password Protection**. Obecnie dostępne są następujące liczniki wydajności:

|Nazwa licznika wydajności | Opis|
| --- | --- |
|Przetworzone hasła |Ten licznik wyświetla łączną liczbę przetworzonych haseł (zaakceptowanych lub odrzuconych) od momentu ostatniego uruchomienia.|
|Hasła zostały zaakceptowane |Ten licznik wyświetla łączną liczbę haseł zaakceptowanych od momentu ostatniego uruchomienia.|
|Odrzucone hasła |Ten licznik wyświetla łączną liczbę haseł odrzuconych od momentu ostatniego uruchomienia.|
|Żądania filtru haseł w toku |Ten licznik wyświetla liczbę żądań filtru haseł, które są obecnie w toku.|
|Szczytowe żądania filtru haseł |Ten licznik wyświetla szczytową liczbę współbieżnych żądań filtru haseł od momentu ostatniego uruchomienia.|
|Błędy żądania filtru hasła |Ten licznik wyświetla łączną liczbę żądań filtru hasła, które nie powiodły się z powodu błędu od momentu ostatniego uruchomienia. Błędy mogą wystąpić, gdy usługa agenta DC ochrony hasła usługi Azure AD nie jest uruchomiona.|
|Żądania filtru haseł/s |Ten licznik przedstawia szybkość, z jaką są przetwarzane hasła.|
|Czas przetwarzania żądania filtru haseł |Ten licznik wyświetla średni czas wymagany do przetworzenia żądania filtru haseł.|
|Czas przetwarzania żądania filtru hasła szczytowego |Ten licznik wyświetla maksymalny czas przetwarzania żądania filtru haseł od momentu ostatniego uruchomienia.|
|Hasła zostały zaakceptowane z powodu trybu inspekcji |Ten licznik przedstawia łączną liczbę haseł, które zostałyby odrzucony, ale zostały zaakceptowane, ponieważ zasady haseł zostały skonfigurowane tak, aby były w trybie inspekcji (od momentu ostatniego ponownego uruchomienia).|

## <a name="dc-agent-discovery"></a>Odnajdywanie agenta kontrolera domeny

`Get-AzureADPasswordProtectionDCAgent`Polecenie cmdlet może służyć do wyświetlania podstawowych informacji o różnych agentach kontrolera domeny działających w domenie lub lesie. Te informacje są pobierane z obiektów serviceConnectionPoint zarejestrowanych przez uruchomione usługi agenta kontrolera domeny.

Przykładowe dane wyjściowe tego polecenia cmdlet są następujące:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

Różne właściwości są aktualizowane przez poszczególne usługi agenta kontrolera domeny co godzinę. Dane nadal podlegają Active Directory opóźnieniu replikacji.

Zakres zapytania polecenia cmdlet może mieć wpływ na parametry — lasu lub — domeny.

Jeśli wartość HeartbeatUTC jest przestarzała, może to być objawem, że Agent DC ochrony hasłem usługi Azure AD na tym kontrolerze domeny nie jest uruchomiony lub został odinstalowany lub że komputer został obniżony i nie jest już kontrolerem domeny.

Jeśli wartość PasswordPolicyDateUTC jest nieaktualna, może to być objawem, że Agent DC ochrony hasłem usługi Azure AD na tym komputerze nie działa prawidłowo.

## <a name="dc-agent-newer-version-available"></a>Dostępna jest nowsza wersja agenta DC

W celu wykrycia, że dostępna jest nowsza wersja oprogramowania Agent DC, usługa agenta kontrolera domeny będzie rejestrować zdarzenia ostrzegawcze 30034 w dzienniku operacyjnym.

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

Powyższe zdarzenie nie określa wersji nowszego oprogramowania. Aby uzyskać te informacje, należy przejść do linku w komunikacie dotyczącym zdarzenia.

> [!NOTE]
> Pomimo odwołań do "autoupgrade" w powyższym komunikacie o zdarzeniu oprogramowanie Agent DC nie obsługuje obecnie tej funkcji.

## <a name="proxy-service-event-logging"></a>Rejestrowanie zdarzeń usługi serwera proxy

Usługa serwera proxy emituje minimalny zestaw zdarzeń do następujących dzienników zdarzeń:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Należy pamiętać, że dziennik śledzenia jest domyślnie wyłączony.

> [!WARNING]
> Gdy ta funkcja jest włączona, dziennik śledzenia otrzymuje dużą liczbę zdarzeń, co może mieć wpływ na wydajność hosta serwera proxy. W związku z tym dziennik ten należy włączyć tylko wtedy, gdy problem wymaga dokładniejszego zbadania, a następnie tylko przez minimalny czas.

Zdarzenia są rejestrowane przez różne składniki serwera proxy przy użyciu następujących zakresów:

|Składnik |Zakres identyfikatorów zdarzeń|
| --- | --- |
|Proces hostingu usługi proxy| 10000-19999|
|Podstawowa logika biznesowa usługi proxy| 20000-29999|
|Polecenia cmdlet programu PowerShell| 30000-39999|

## <a name="proxy-service-text-logging"></a>Rejestrowanie tekstu usługi serwera proxy

Usługę serwera proxy można skonfigurować do zapisu do dziennika tekstowego przez ustawienie następującej wartości rejestru:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (REG_DWORD wartość)

Rejestrowanie tekstu jest domyślnie wyłączone. Aby zmiany tej wartości zaczęły obowiązywać, wymagane jest ponowne uruchomienie usługi serwera proxy. Po włączeniu usługa proxy zapisze w pliku dziennika w lokalizacji:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Dziennik tekstowy odbiera te same wpisy poziomu debugowania, które mogą być rejestrowane w dzienniku śledzenia, ale zazwyczaj jest to łatwiejszy format do przejrzenia i przeanalizowania.

> [!WARNING]
> Gdy ta funkcja jest włączona, ten dziennik otrzymuje dużą ilość zdarzeń i może mieć wpływ na wydajność maszyny. W związku z tym ten Rozszerzony Dziennik powinien być włączany tylko wtedy, gdy problem wymaga dokładniejszego zbadania, a następnie tylko przez minimalny czas.

## <a name="powershell-cmdlet-logging"></a>Rejestrowanie poleceń cmdlet programu PowerShell

Polecenia cmdlet programu PowerShell, które powodują zmianę stanu (na przykład Register-AzureADPasswordProtectionProxy), zwykle rejestrują zdarzenie wynikowe w dzienniku operacyjnym.

Ponadto większość poleceń cmdlet programu PowerShell w usłudze Azure AD Password Protection zapisuje dane w dzienniku tekstowym, który znajduje się w obszarze:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Jeśli wystąpi błąd polecenia cmdlet i rozwiązanie and\or problemu nie jest łatwo widoczne, te dzienniki mogą być również konsultowane.

## <a name="proxy-discovery"></a>Odnajdywanie serwera proxy

`Get-AzureADPasswordProtectionProxy`Polecenie cmdlet może służyć do wyświetlania podstawowych informacji o różnych usługach proxy ochrony haseł usługi Azure AD działających w domenie lub lesie. Te informacje są pobierane z obiektów usługi serviceConnectionPoint zarejestrowanych przez uruchomione usługi serwera proxy.

Przykładowe dane wyjściowe tego polecenia cmdlet są następujące:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

Różne właściwości są aktualizowane przez poszczególne usługi serwera proxy w przybliżeniu co godzinę. Dane nadal podlegają Active Directory opóźnieniu replikacji.

Zakres zapytania polecenia cmdlet może mieć wpływ na parametry — lasu lub — domeny.

Jeśli wartość HeartbeatUTC jest przestarzała, może to być objawem, że serwer proxy ochrony hasłem usługi Azure AD na tym komputerze nie jest uruchomiony lub został odinstalowany.

## <a name="proxy-agent-newer-version-available"></a>Dostępna jest nowsza wersja agenta proxy

Usługa serwera proxy rejestruje zdarzenie ostrzeżenia 20002 w dzienniku operacyjnym po wykryciu, że dostępna jest nowsza wersja oprogramowania serwera proxy, na przykład:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

Powyższe zdarzenie nie określa wersji nowszego oprogramowania. Aby uzyskać te informacje, należy przejść do linku w komunikacie dotyczącym zdarzenia.

To zdarzenie będzie emitowane nawet wtedy, gdy Agent proxy jest skonfigurowany z włączonym autouaktualnianiem.

## <a name="next-steps"></a>Następne kroki

[Rozwiązywanie problemów z ochroną hasłem w usłudze Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)

Aby uzyskać więcej informacji na temat globalnych i niestandardowych list zakazanych haseł, zobacz artykuł Zablokuj [złe hasła](concept-password-ban-bad.md)
