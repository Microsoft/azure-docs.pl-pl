---
title: 'Azure AD Connect: Rozwiązywanie problemów z uwierzytelnianiem przekazywanym | Microsoft Docs'
description: W tym artykule opisano sposób rozwiązywania problemów z uwierzytelnianiem przekazywanym Azure Active Directory (Azure AD).
services: active-directory
keywords: Rozwiązywanie problemów Azure AD Connect uwierzytelnianie przekazywane, instalowanie Active Directory, wymagane składniki usługi Azure AD, logowanie jednokrotne, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a014bd5c8f1edbfb00019b8541cef552271d65b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98762850"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Rozwiązywanie problemów z uwierzytelnianiem przekazywanym usługi Azure Active Directory

Ten artykuł ułatwia znalezienie informacji o rozwiązywaniu problemów dotyczących typowych problemów dotyczących uwierzytelniania przekazywanego za pomocą usługi Azure AD.

>[!IMPORTANT]
>Jeśli występują problemy z logowaniem użytkowników przy użyciu uwierzytelniania przekazywanego, nie należy wyłączać funkcji ani odinstalowywać agentów uwierzytelniania przekazywanego bez posiadania konta administratora globalnego tylko w chmurze do powrotu. Dowiedz się więcej [na temat dodawania konta administratora globalnego tylko w chmurze](../fundamentals/add-users-azure-active-directory.md). Ten krok ma krytyczne znaczenie i gwarantuje, że nie można zablokować dzierżawy.

## <a name="general-issues"></a>Ogólne problemy

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Sprawdź stan agentów funkcji i uwierzytelniania

Upewnij się, że funkcja uwierzytelniania przekazywanego jest nadal **włączona** w dzierżawie, a stan agentów uwierzytelniania wskazuje, że są **aktywne**, a nie **nieaktywne**. Stan można sprawdzić, przechodząc do bloku **Azure AD Connect** w [centrum administracyjnym Azure Active Directory](https://aad.portal.azure.com/).

![Azure Active Directory centrum administracyjnego — Azure AD Connect bloku](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Azure Active Directory centrum administracyjnego — blok uwierzytelniania przekazywanego](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Komunikaty o błędach logowania użytkownika

Jeśli użytkownik nie może zalogować się przy użyciu uwierzytelniania przekazywanego, może zobaczyć jeden z następujących błędów związanych z użytkownikiem na ekranie logowania usługi Azure AD: 

|Błąd|Opis|Rozwiązanie
| --- | --- | ---
|AADSTS80001|Nie można nawiązać połączenia z Active Directory|Upewnij się, że serwery agentów są członkami tego samego lasu usługi AD co użytkownicy, których hasła muszą zostać sprawdzone i mogą łączyć się z Active Directory.  
|AADSTS8002|Przekroczono limit czasu podczas nawiązywania połączenia z Active Directory|Upewnij się, że Active Directory jest dostępna i odpowiada na żądania od agentów.
|AADSTS80004|Nazwa użytkownika przeniesiona do agenta jest nieprawidłowa|Upewnij się, że użytkownik próbuje zalogować się przy użyciu prawidłowej nazwy użytkownika.
|AADSTS80005|Sprawdzanie poprawności napotkało nieprzewidywalny wyjątek WebException|Błąd przejściowy. Ponów żądanie. Jeśli to się nie powiedzie, skontaktuj się z pomocą techniczną firmy Microsoft.
|AADSTS80007|Wystąpił błąd podczas komunikowania się z Active Directory|Sprawdź dzienniki agenta, aby uzyskać więcej informacji i sprawdź, czy Active Directory działa zgodnie z oczekiwaniami.

### <a name="users-get-invalid-usernamepassword-error"></a>Użytkownicy otrzymują błąd nieprawidłowej nazwy użytkownika/hasła 

Może się tak zdarzyć, gdy lokalny element UserPrincipalName (UPN) użytkownika jest różny od nazwy UPN użytkownika w chmurze.

Aby upewnić się, że jest to problem, najpierw Przetestuj, że Agent uwierzytelniania przekazywanego działa prawidłowo:


1. Utwórz konto testowe.  
2. Zaimportuj moduł programu PowerShell na maszynie agenta:

 ```powershell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\PassthroughAuthPSModule\PassthroughAuthPSModule.psd1"
 ```
3. Uruchom polecenie Invoke PowerShell: 

 ```powershell
 Invoke-PassthroughAuthOnPremLogonTroubleshooter 
 ``` 
4. Gdy zostanie wyświetlony monit o wprowadzenie poświadczeń, wprowadź tę samą nazwę użytkownika i hasło, które są używane do logowania się do programu ( https://login.microsoftonline.com) .

Jeśli zostanie wyświetlony ten sam błąd nazwy użytkownika/hasła, oznacza to, że Agent uwierzytelniania przekazywanego działa prawidłowo, a problem może polegać na tym, że lokalna nazwa UPN nie jest w trakcie routingu. Aby dowiedzieć się więcej, zobacz [Konfigurowanie alternatywnego identyfikatora logowania](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

> [!IMPORTANT]
> Jeśli serwer Azure AD Connect nie jest przyłączony do domeny, Azure AD Connect wymagane są [następujące](./how-to-connect-install-prerequisites.md#installation-prerequisites)problemy dotyczące nieprawidłowej nazwy użytkownika i hasła.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Przyczyny niepowodzenia logowania w centrum administracyjnym Azure Active Directory (wymaga licencji Premium)

Jeśli dzierżawa ma skojarzoną licencję Azure AD — wersja Premium, możesz również sprawdzić [raport aktywności logowania](../reports-monitoring/concept-sign-ins.md) w [centrum administracyjnym Azure Active Directory](https://aad.portal.azure.com/).

![Azure Active Directory centrum administracyjnego — raport logowania](./media/tshoot-connect-pass-through-authentication/pta4.png)

Przejdź do **Azure Active Directory**  ->  **logowania** w [centrum administracyjnym Azure Active Directory](https://aad.portal.azure.com/) i kliknij działanie logowania określonego użytkownika. Poszukaj pola **Kod błędu logowania** . Zamapuj wartość tego pola na przyczynę niepowodzenia i rozdzielczość, korzystając z następującej tabeli:

|Kod błędu logowania|Przyczyna niepowodzenia logowania|Rozwiązanie
| --- | --- | ---
| 50144 | Ważność hasła użytkownika usługi Active Directory wygasła. | Zresetuj hasło użytkownika w Active Directory lokalnym.
| 80001 | Brak dostępnych agentów uwierzytelniania. | Zainstaluj i zarejestruj agenta uwierzytelniania.
| 80002 | Upłynął limit czasu żądania weryfikacji hasła agenta uwierzytelniania. | Sprawdź, czy Active Directory jest dostępny z poziomu agenta uwierzytelniania.
| 80003 | Agent uwierzytelniania odebrał nieprawidłową odpowiedź. | Jeśli problem jest spójnie powtarzalny przez wielu użytkowników, sprawdź konfigurację Active Directory.
| 80004 | W żądaniu logowania użyto nieprawidłowej głównej nazwy użytkownika (UPN). | Poproszenie użytkownika o zalogowanie się przy użyciu poprawnej nazwy użytkownika.
| 80005 | Agent uwierzytelniania: wystąpił błąd. | Błąd przejściowy. Spróbuj ponownie później.
| 80007 | Agent uwierzytelniania nie może nawiązać połączenia z usługą Active Directory. | Sprawdź, czy Active Directory jest dostępny z poziomu agenta uwierzytelniania.
| 80010 | Agent uwierzytelniania nie może odszyfrować hasła. | Jeśli problem jest spójnie powtarzalny, zainstaluj i Zarejestruj nowego agenta uwierzytelniania. I Odinstaluj bieżący. 
| 80011 | Agent uwierzytelniania nie może pobrać klucza odszyfrowującego. | Jeśli problem jest spójnie powtarzalny, zainstaluj i Zarejestruj nowego agenta uwierzytelniania. I Odinstaluj bieżący.
| 80014 | Żądanie weryfikacji zostało wysłane po osiągnięciu maksymalnego czasu, który upłynął. | Upłynął limit czasu agenta uwierzytelniania. Otwórz bilet pomocy technicznej z kodem błędu, IDENTYFIKATORem korelacji i sygnaturą czasową, aby uzyskać więcej szczegółów dotyczących tego błędu

>[!IMPORTANT]
>Agenci uwierzytelniania przekazującego uwierzytelniają użytkowników usługi Azure AD, sprawdzając ich nazwy użytkownika i hasła do Active Directory przez wywołanie [interfejsu API Win32 funkcji LogonUser](/windows/win32/api/winbase/nf-winbase-logonusera). W związku z tym jeśli ustawisz ustawienie "Logowanie do" w Active Directory, aby ograniczyć dostęp do logowania do stacji roboczej, musisz dodać serwery obsługujące agentów uwierzytelniania przekazywanego do listy "Logowanie do serwerów". Niewykonanie tej czynności uniemożliwi użytkownikom zalogowanie się do usługi Azure AD.

## <a name="authentication-agent-installation-issues"></a>Problemy z instalacją agenta uwierzytelniania

### <a name="an-unexpected-error-occurred"></a>Wystąpił nieoczekiwany błąd

[Zbierz dzienniki agentów](#collecting-pass-through-authentication-agent-logs) z serwera i skontaktuj się z pomoc techniczna firmy Microsoftą z problemem.

## <a name="authentication-agent-registration-issues"></a>Problemy z rejestracją agenta uwierzytelniania

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Rejestracja agenta uwierzytelniania nie powiodła się z powodu zablokowanych portów

Upewnij się, że serwer, na którym zainstalowano agenta uwierzytelniania, może komunikować się z naszymi adresami URL usług i portami wymienionymi [tutaj](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Rejestracja agenta uwierzytelniania nie powiodła się z powodu błędów autoryzacji tokenu lub konta

Upewnij się, że używasz konta administratora globalnego tylko w chmurze dla wszystkich Azure AD Connect lub instalacji autonomicznego agenta uwierzytelniania oraz operacji rejestracji. Istnieje znany problem z kontami administratora globalnego z obsługą usługi MFA; tymczasowo wyłącz usługę MFA (tylko w celu wykonania operacji) jako obejście.

### <a name="an-unexpected-error-occurred"></a>Wystąpił nieoczekiwany błąd

[Zbierz dzienniki agentów](#collecting-pass-through-authentication-agent-logs) z serwera i skontaktuj się z pomoc techniczna firmy Microsoftą z problemem.

## <a name="authentication-agent-uninstallation-issues"></a>Problemy dotyczące odinstalowywania agenta uwierzytelniania

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Komunikat ostrzegawczy podczas odinstalowywania Azure AD Connect

Jeśli w dzierżawie włączono uwierzytelnianie przekazywane i podjęto próbę odinstalowania Azure AD Connect, zostanie wyświetlony następujący komunikat ostrzegawczy: "użytkownicy nie będą mogli logować się do usługi Azure AD, chyba że zainstalowano innych agentów uwierzytelniania przekazywanego na innych serwerach".

Przed odinstalowaniem Azure AD Connect upewnij się, że Instalator ma [wysoką dostępność](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) , aby uniknąć przerwania logowania użytkownika.

## <a name="issues-with-enabling-the-feature"></a>Problemy z włączaniem funkcji

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Włączanie funkcji nie powiodło się, ponieważ nie ma dostępnych agentów uwierzytelniania

Musisz mieć co najmniej jednego aktywnego agenta uwierzytelniania, aby włączyć uwierzytelnianie przekazywane w dzierżawie. Agenta uwierzytelniania można zainstalować, instalując Azure AD Connect lub autonomiczny Agent uwierzytelniania.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Włączanie funkcji nie powiodło się z powodu zablokowanych portów

Upewnij się, że serwer, na którym zainstalowano Azure AD Connect, może komunikować się z naszymi adresami URL usług i portami wymienionymi [tutaj](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Włączanie funkcji nie powiodło się z powodu błędów autoryzacji tokenu lub konta

Upewnij się, że podczas włączania funkcji używasz konta administratora globalnego tylko w chmurze. Istnieje znany problem z kontami administratora globalnego z obsługą uwierzytelniania wieloskładnikowego (MFA). tymczasowo wyłącz usługę MFA (tylko w celu wykonania operacji) jako obejście.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Zbieranie dzienników agenta uwierzytelniania przekazywanego

W zależności od typu występującego problemu należy wyszukać w różnych miejscach dzienniki agenta uwierzytelniania przekazywanego.

### <a name="azure-ad-connect-logs"></a>Dzienniki Azure AD Connect

Aby uzyskać błędy związane z instalacją, zapoznaj się z dziennikami Azure AD Connect w **%ProgramData%\AADConnect\trace- \* . log**.

### <a name="authentication-agent-event-logs"></a>Dzienniki zdarzeń agenta uwierzytelniania

W przypadku błędów związanych z agentem uwierzytelniania Otwórz aplikację Podgląd zdarzeń na serwerze i sprawdź w obszarze **Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Aby uzyskać szczegółową analizę, Włącz dziennik "Session" (kliknij prawym przyciskiem myszy wewnątrz aplikacji Podgląd zdarzeń, aby znaleźć tę opcję). Nie uruchamiaj agenta uwierzytelniania z włączonym tym dziennikiem podczas normalnych operacji; służy tylko do rozwiązywania problemów. Zawartość dziennika jest widoczna tylko po ponownym wyłączaniu dziennika.



### <a name="detailed-trace-logs"></a>Szczegółowe dzienniki śledzenia

Aby rozwiązać problemy związane z logowaniem użytkownika, poszukaj dzienników śledzenia w programie **%ProgramData%\MICROSOFT\AZURE AD Connect \\ Authentication Agent\Trace**. Te dzienniki zawierają przyczyny niepowodzenia logowania określonego użytkownika przy użyciu funkcji uwierzytelniania przekazywanego. Te błędy są również mapowane na przyczyny niepowodzenia logowania widoczne w poprzedniej tabeli przyczyn niepowodzeń logowania. Poniżej znajduje się przykładowy wpis dziennika:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Aby uzyskać szczegółowe informacje o błędzie ("1328" w poprzednim przykładzie), Otwórz wiersz polecenia i uruchamiając następujące polecenie (Uwaga: Zastąp "1328" rzeczywistym numerem błędu widocznym w dziennikach):

`Net helpmsg 1328`

![Uwierzytelnianie przekazywane](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Dzienniki kontrolera domeny

Jeśli rejestrowanie inspekcji jest włączone, dodatkowe informacje można znaleźć w dziennikach zabezpieczeń kontrolerów domeny. Prosty sposób wykonywania zapytań dotyczących żądań logowania wysyłanych przez agentów uwierzytelniania przekazywanego jest następujący:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Liczniki Monitora wydajności

Innym sposobem monitorowania agentów uwierzytelniania jest śledzenie określonych liczników monitora wydajności na każdym serwerze, na którym jest zainstalowany agent uwierzytelniania. Użyj następujących liczników globalnych (**# PTA uwierzytelniania**, **#PTA nieudanych uwierzytelnień** i **#PTA** uwierzytelnień) i liczników błędów (**# PTA uwierzytelniania**):

![Liczniki Monitora wydajności uwierzytelniania przekazywanego](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Uwierzytelnianie przekazywane zapewnia wysoką dostępność przy użyciu wielu agentów uwierzytelniania i _nie_ równoważenia obciążenia. W zależności od konfiguracji _nie_ Wszyscy agenci uwierzytelniania odbierają w _nierównej_ liczbie żądań. Istnieje możliwość, że określony Agent uwierzytelniania nie odbiera żadnego ruchu.
