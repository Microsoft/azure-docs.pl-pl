---
title: Rozwiązywanie problemów z rozszerzeniem NPS usługi Azure AD MFA — Azure Active Directory
description: Uzyskaj pomoc w rozwiązywaniu problemów z rozszerzeniem serwera NPS dla usługi Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 03736f468148ee633aff22718dc000220ab7efe4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839033"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-ad-multi-factor-authentication"></a>Rozwiązywanie komunikatów o błędach z rozszerzenia serwera NPS dla usługi Azure AD Multi-Factor Authentication

Jeśli wystąpią błędy rozszerzenia serwera NPS dla usługi Azure AD Multi-Factor Authentication, użyj tego artykułu, aby szybciej uzyskać rozwiązanie. Dzienniki rozszerzenia serwera NPS znajdują się w Podgląd zdarzeń w obszarze **widoki niestandardowe**  >  **role serwera**  >  **usługi zasad sieciowych i dostępu** na serwerze, na którym zainstalowano rozszerzenie serwera NPS.

## <a name="troubleshooting-steps-for-common-errors"></a>Kroki rozwiązywania problemów z typowymi błędami

| Kod błędu | Kroki rozwiązywania problemów |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Skontaktuj się z pomocą techniczną](#contact-microsoft-support)i zanotuj listę kroków związanych z zbieraniem dzienników. Podaj jak najwięcej informacji o tym, co się stało przed błędem, w tym IDENTYFIKATORem dzierżawy oraz główną nazwą użytkownika (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Może wystąpić problem z zainstalowaniem lub skojarzeniem certyfikatu klienta z dzierżawcą. Postępuj zgodnie z instrukcjami w [temacie Rozwiązywanie problemów z serwerem NPS usługi MFA](howto-mfa-nps-extension.md#troubleshooting) w celu zbadania problemów z certyfikatem klienta. |
| **ESTS_TOKEN_ERROR** | Postępuj zgodnie z instrukcjami w [temacie Rozwiązywanie problemów z rozszerzeniem zasad sieciowych usługi MFA,](howto-mfa-nps-extension.md#troubleshooting) aby zbadać certyfikat klienta i tokeny ADAL. |
| **HTTPS_COMMUNICATION_ERROR** | Serwer NPS nie może odbierać odpowiedzi z usługi Azure AD MFA. Sprawdź, czy zapory są otwarte dwukierunkowo dla ruchu do i z https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Na serwerze, na którym działa rozszerzenie serwera zasad sieciowych, sprawdź, czy można nawiązać połączenie  `https://adnotifications.windowsazure.com` i `https://login.microsoftonline.com/` . Jeśli te lokacje nie są ładowane, Rozwiąż problemy z łącznością na tym serwerze. |
| **Rozszerzenie serwera NPS dla usługi Azure AD MFA:** <br> Rozszerzenie serwera NPS dla usługi Azure AD MFA tylko wykonuje uwierzytelnianie pomocnicze dla żądań RADIUS w stanie AccessAccept. Żądanie zostało odebrane dla użytkownika o nazwie "odpowiedź" o stanie odpowiedzi AccessReject, ignorując żądanie. | Ten błąd zwykle odzwierciedla błąd uwierzytelniania w usłudze AD lub że serwer NPS nie może odebrać odpowiedzi z usługi Azure AD. Sprawdź, czy zapory są otwarte dwukierunkowo dla ruchu do i z `https://adnotifications.windowsazure.com` i z `https://login.microsoftonline.com` portów 80 i 443. Ważne jest również, aby sprawdzić, czy na karcie TELEFONowania uprawnień dostępu do sieci jest ustawiona opcja "Kontroluj dostęp za pomocą zasad sieciowych NPS". Ten błąd może również być wyzwalany, jeśli użytkownik nie ma przypisanej licencji. |
| **REGISTRY_CONFIG_ERROR** | Brak klucza w rejestrze dla aplikacji, co może być spowodowane tym, że [skrypt programu PowerShell](howto-mfa-nps-extension.md#install-the-nps-extension) nie został uruchomiony po instalacji. Komunikat o błędzie powinien zawierać brakujący klucz. Upewnij się, że masz klucz w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> W żądaniu usługi RADIUS brakuje obowiązkowego atrybutu userName\Identifier usługi RADIUS. Sprawdź, czy serwer NPS otrzymuje żądania RADIUS | Ten błąd zwykle odzwierciedla problem z instalacją. Rozszerzenie serwera NPS musi być zainstalowane na serwerach NPS, które mogą odbierać żądania RADIUS. Serwery NPS zainstalowane jako zależności dla usług takich jak RDG i RRAS nie odbierają żądań RADIUS. Rozszerzenie serwera NPS nie działa w przypadku instalacji w takich instalacjach i błędów, ponieważ nie może odczytać szczegółów z żądania uwierzytelnienia. |
| **REQUEST_MISSING_CODE** | Upewnij się, że protokół szyfrowania hasła między serwerami NPS i NAS obsługuje pomocniczą metodę uwierzytelniania. **Protokół PAP** obsługuje wszystkie metody uwierzytelniania usługi Azure AD MFA w chmurze: połączenie telefoniczne, jednokierunkowa wiadomość tekstowa, powiadomienie aplikacji mobilnej i kod weryfikacyjny aplikacji mobilnej. **CHAPv2** i **Protokół EAP** obsługują połączenia telefoniczne i powiadomienia aplikacji mobilnej. |
| **USERNAME_CANONICALIZATION_ERROR** | Sprawdź, czy użytkownik jest obecny w lokalnym wystąpieniu Active Directory i czy usługa NPS ma uprawnienia dostępu do katalogu. Jeśli używasz relacji zaufania między lasami, [skontaktuj się z pomocą techniczną](#contact-microsoft-support) w celu uzyskania dalszej pomocy. |

### <a name="alternate-login-id-errors"></a>Alternatywne błędy identyfikatorów logowania

| Kod błędu | Komunikat o błędzie | Kroki rozwiązywania problemów |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Błąd: wyszukiwanie userObjectSid nie powiodło się | Sprawdź, czy użytkownik istnieje w lokalnym wystąpieniu Active Directory. Jeśli używasz relacji zaufania między lasami, [skontaktuj się z pomocą techniczną](#contact-microsoft-support) w celu uzyskania dalszej pomocy. |
| **ALTERNATE_LOGIN_ID_ERROR** | Błąd: alternatywne wyszukiwanie LoginId nie powiodło się | Sprawdź, czy LDAP_ALTERNATE_LOGINID_ATTRIBUTE jest ustawiony [prawidłowy atrybut usługi Active Directory](/windows/win32/adschema/attributes-all). <br><br> Jeśli LDAP_FORCE_GLOBAL_CATALOG jest ustawiona na wartość true lub LDAP_LOOKUP_FORESTS jest skonfigurowana z niepustą wartością, sprawdź, czy skonfigurowano wykaz globalny i czy został do niego dodany atrybut AlternateLoginId. <br><br> Jeśli LDAP_LOOKUP_FORESTS jest skonfigurowany z niepustą wartością, sprawdź, czy wartość jest poprawna. Jeśli istnieje więcej niż jedna nazwa lasu, nazwy muszą być rozdzielone średnikami, a nie spacjami. <br><br> Jeśli te kroki nie rozwiążą problemu, [skontaktuj się z pomocą techniczną](#contact-microsoft-support) , aby uzyskać pomoc. |
| **ALTERNATE_LOGIN_ID_ERROR** | Błąd: alternatywna wartość LoginId jest pusta | Sprawdź, czy atrybut AlternateLoginId jest skonfigurowany dla użytkownika. |

## <a name="errors-your-users-may-encounter"></a>Błędy, które mogą napotkać użytkownicy

| Kod błędu | Komunikat o błędzie | Kroki rozwiązywania problemów |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Dzierżawca wywołujący nie ma uprawnień dostępu do uwierzytelniania dla użytkownika | Sprawdź, czy domena dzierżawy i domena głównej nazwy użytkownika (UPN) są takie same. Na przykład upewnij się, że user@contoso.com próbujesz uwierzytelnić dzierżawcy contoso. Nazwa UPN reprezentuje prawidłowego użytkownika dzierżawy na platformie Azure. |
| **AuthenticationMethodNotConfigured** | Określona metoda uwierzytelniania nie została skonfigurowana dla użytkownika | Użytkownik może dodać lub sprawdzić metody weryfikacyjne zgodnie z instrukcjami w sekcji [Zarządzanie ustawieniami weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Określona metoda uwierzytelniania nie jest obsługiwana. | Zbierz wszystkie dzienniki zawierające ten błąd i [skontaktuj się z pomocą techniczną](#contact-microsoft-support). Podczas kontaktowania się z pomocą techniczną Podaj nazwę użytkownika i metodę weryfikacji pomocniczej, które wywołały błąd. |
| **BecAccessDenied** | Wywołanie Bec MSODS zwróciło odmowę dostępu, prawdopodobnie nie określono nazwy użytkownika w dzierżawie | Użytkownik jest obecny w Active Directory lokalnym, ale nie jest synchronizowany z usługą Azure AD za pomocą usługi AD Connect. Lub użytkownik nie ma dzierżawy. Dodaj użytkownika do usługi Azure AD i Dodaj do nich metody weryfikacji zgodnie z instrukcjami w sekcji [Zarządzanie ustawieniami weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** lub **StrongAuthenticationServiceInvalidParameter** | Numer telefonu jest w nierozpoznawalnym formacie | Użytkownik powinien skorygować numery telefonów weryfikacyjnych. |
| **InvalidSession** | Określona sesja jest nieprawidłowa lub mogła ulec wygaśnięciu | Sesja zajęła więcej niż trzy minuty. Upewnij się, że użytkownik wprowadza kod weryfikacyjny lub odpowiada na powiadomienie aplikacji w ciągu trzech minut od zainicjowania żądania uwierzytelnienia. Jeśli to nie rozwiąże problemu, sprawdź, czy między klientem, serwerem NAS, serwerem NPS i punktem końcowym usługi Azure AD MFA nie ma opóźnień sieciowych.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Nie skonfigurowano domyślnej metody uwierzytelniania dla użytkownika | Użytkownik może dodać lub sprawdzić metody weryfikacyjne zgodnie z instrukcjami w sekcji [Zarządzanie ustawieniami weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-manage-settings.md). Sprawdź, czy użytkownik wybrał domyślną metodę uwierzytelniania i skonfigurował tę metodę dla swojego konta. |
| **OathCodePinIncorrect** | Wprowadzono nieprawidłowy kod i numer PIN. | Ten błąd nie jest oczekiwany w rozszerzeniu serwera NPS. Jeśli użytkownik napotka ten problem, [skontaktuj się z pomocą techniczną](#contact-microsoft-support) , aby uzyskać pomoc dotyczącą rozwiązywania problemów. |
| **ProofDataNotFound** | Nie skonfigurowano danych dowodowych dla określonej metody uwierzytelniania. | Użytkownik próbuje użyć innej metody weryfikacji lub dodać nowe metody weryfikacji zgodnie z instrukcjami w sekcji [Zarządzanie ustawieniami weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-manage-settings.md). Jeśli użytkownik będzie nadal widział ten błąd po potwierdzeniu, że metoda weryfikacji została prawidłowo skonfigurowana, [skontaktuj się z pomocą techniczną](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Wprowadzono nieprawidłowy kod i numer PIN. (OneWaySMS) | Ten błąd nie jest oczekiwany w rozszerzeniu serwera NPS. Jeśli użytkownik napotka ten problem, [skontaktuj się z pomocą techniczną](#contact-microsoft-support) , aby uzyskać pomoc dotyczącą rozwiązywania problemów. |
| **TenantIsBlocked** | Dzierżawca jest zablokowany | [Skontaktuj się z pomocą techniczną](#contact-microsoft-support) , podając *Identyfikator dzierżawy* ze strony właściwości usługi Azure AD w Azure Portal. |
| **UserNotFound** | Nie znaleziono określonego użytkownika | Dzierżawca nie jest już widoczny jako aktywny w usłudze Azure AD. Sprawdź, czy Twoja subskrypcja jest aktywna i czy masz wymagane aplikacje pierwszej firmy. Upewnij się również, że dzierżawa w temacie certyfikatu jest zgodnie z oczekiwaniami, a certyfikat jest nadal ważny i zarejestrowany w jednostce usługi. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Komunikaty, które mogą napotkać użytkownicy, którzy nie są błędami

Czasami użytkownicy mogą odbierać komunikaty z Multi-Factor Authentication, ponieważ ich żądanie uwierzytelnienia nie powiodło się. Nie są to błędy w produkcie konfiguracji, ale są zamierzonymi ostrzeżeniami wyjaśniającymi, dlaczego żądanie uwierzytelnienia zostało odrzucone.

| Kod błędu | Komunikat o błędzie | Zalecane czynności |
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Nieprawidłowy kod entered\OATH kodu | Użytkownik wprowadził nieprawidłowy kod. Spróbuj ponownie, żądając nowego kodu lub Zaloguj się ponownie. |
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Osiągnięto maksymalną dozwoloną liczbę ponownych prób w kodzie | Użytkownik nie zakończył zbyt wiele razy wyzwania weryfikacji. W zależności od ustawień może być konieczne odblokowanie przez administratora.  |
| **SMSAuthFailedWrongCodeEntered** | Wprowadzono błędny kod/nieprawidłowa OTP wiadomości tekstowej | Użytkownik wprowadził nieprawidłowy kod. Spróbuj ponownie, żądając nowego kodu lub Zaloguj się ponownie. |

## <a name="errors-that-require-support"></a>Błędy wymagające pomocy technicznej

W przypadku wystąpienia jednego z tych błędów zalecamy [skontaktowanie się z pomocą techniczną](#contact-microsoft-support) w celu uzyskania pomocy diagnostycznej. Nie istnieje standardowy zestaw kroków, które mogą rozwiązać te błędy. Gdy skontaktujesz się z pomocą techniczną, pamiętaj o dodaniu możliwie największej ilości informacji na temat kroków, które doprowadziły do błędu, oraz informacje o dzierżawie.

| Kod błędu | Komunikat o błędzie |
| ---------- | ------------- |
| **Nieprawidłowy parametr** | Żądanie nie może mieć wartości null |
| **Nieprawidłowy parametr** | Obiekt ObjectId nie może mieć wartości null ani być pusty dla ReplicationScope:{0} |
| **Nieprawidłowy parametr** | Długość elementu NazwaFirmy \{ 0} \ przekracza maksymalną dozwoloną długość {1} |
| **Nieprawidłowy parametr** | Element UserPrincipalName nie może mieć wartości null ani być pusty |
| **Nieprawidłowy parametr** | Podany TenantId nie ma poprawnego formatu |
| **Nieprawidłowy parametr** | Identyfikator sesji nie może mieć wartości null ani być pusty |
| **Nieprawidłowy parametr** | Nie można rozpoznać żadnego ProofData z żądania lub MSODS. ProofData jest nieznany |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Następne kroki

### <a name="troubleshoot-user-accounts"></a>Rozwiązywanie problemów z kontami użytkowników

Jeśli użytkownicy mają [problemy z weryfikacją dwuetapową](../user-help/multi-factor-authentication-end-user-troubleshoot.md), Pomóż im w rozwiązywaniu problemów.

### <a name="health-check-script"></a>Skrypt sprawdzania kondycji

[Skrypt sprawdzania kondycji rozszerzenia serwera NPS usługi Azure AD MFA](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) wykonuje podstawowe Sprawdzanie kondycji podczas rozwiązywania problemów z rozszerzeniem serwera NPS. Uruchom skrypt i wybierz opcję 3.

### <a name="contact-microsoft-support"></a>Kontaktowanie się z pomocą techniczną firmy Microsoft

Jeśli potrzebujesz dodatkowej pomocy, skontaktuj się z specjalistą pomocy technicznej za pomocą [usługi Azure serwer Multi-Factor Authentication support](https://support.microsoft.com/oas/default.aspx?prid=14947). Gdy kontaktuje się z nami, jest to przydatne, jeśli możesz dołączyć tyle informacji o problemie, ile to możliwe. Informacje, które można podać, obejmują stronę, na której wystąpił błąd, konkretny kod błędu, identyfikator konkretnej sesji, identyfikator użytkownika, który obsłużył błąd, oraz Dzienniki debugowania.

Aby zebrać dzienniki debugowania dla diagnostyki obsługi, wykonaj następujące czynności na serwerze NPS:

1. Otwórz Edytor rejestru i przejdź do HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa Ustaw **VERBOSE_LOG** na **wartość true**
2. Otwórz wiersz polecenia administratora i uruchom następujące polecenia:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Odtwórz problem

4. Zatrzymaj śledzenie przy użyciu następujących poleceń:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Otwórz Edytor rejestru i przejdź do HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa Ustaw **VERBOSE_LOG** na **wartość false**
6. Zapisz zawartość folderu C:\NPS i dołącz spakowany plik do zgłoszenia do pomocy technicznej.