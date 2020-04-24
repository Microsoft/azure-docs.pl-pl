---
title: Raporty dotyczące dostępu i użycia usługi Azure MFA — Azure Active Directory
description: W tym artykule opisano, jak używać funkcji raportów usługi Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df562d65ad064efb1be337e0b68cb8638536981
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82112766"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Raporty w usłudze Azure Multi-Factor Authentication

Usługa Azure Multi-Factor Authentication udostępnia kilka raportów, które mogą być używane przez Ciebie i Twoją organizację za pomocą Azure Portal. W poniższej tabeli wymieniono dostępne raporty:

| Raport | Lokalizacja | Opis |
|:--- |:--- |:--- |
| Historia zablokowanego użytkownika | Usługa Azure AD > Security > MFA > blokowanie/odblokowywanie użytkowników | Pokazuje historię żądań zablokowania lub odblokowania użytkowników. |
| Alerty użycia i oszustw | Logowanie za pomocą usługi Azure AD > | Zawiera informacje dotyczące ogólnego użycia, podsumowania użytkowników i szczegółów użytkownika; a także historia alertów o oszustwie przesłanych w określonym zakresie dat. |
| Użycie dla składników lokalnych | Raport aktywności > działania usługi Azure AD > Security > MFA | Zawiera informacje o ogólnym użyciu usługi MFA za pomocą rozszerzenia serwera NPS, usług AD FS i serwera MFA. |
| Historia pominiętych użytkowników | Usługa Azure AD > Security > MFA > jednorazowe obejście | Przedstawia historię żądań w celu obejścia Multi-Factor Authentication dla użytkownika. |
| Stan serwera | Stan serwera usługi Azure AD > Security > MFA > | Przedstawia stan Multi-Factor Authentication serwerów skojarzonych z Twoim kontem. |

## <a name="view-mfa-reports"></a>Wyświetlanie raportów usługi MFA

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **zabezpieczenia** > **MFA**.
3. Wybierz raport, który chcesz wyświetlić.

   ![Raport o stanie serwera usługi MFA w Azure Portal](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Raport dotyczący logowania usługi Azure AD

Za pomocą **raportu aktywność logowania** w [Azure Portal](https://portal.azure.com)można uzyskać informacje potrzebne do określenia sposobu działania środowiska.

Raport logowania zawiera informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników, w tym informacji o użyciu uwierzytelniania wieloskładnikowego (MFA). Dane usługi MFA dają wgląd w sposób działania usługi MFA w Twojej organizacji. Umożliwiają one udzielenie odpowiedzi na takie pytania, jak:

- Czy logowanie zostało zakwestionowane przez usługę MFA?
- Jak użytkownik ukończył uwierzytelnianie MFA?
- Dlaczego użytkownik nie mógł ukończyć uwierzytelniania MFA?
- Ilu użytkowników zostało zakwestionowanych przez usługę MFA?
- Ilu użytkowników nie mogło odpowiedzieć na wezwania usługi MFA?
- Jakie są typowe problemy z usługą MFA, na które natykają się użytkownicy końcowi?

Te dane są dostępne za pomocą [Azure Portal](https://portal.azure.com) i [interfejsu API raportowania](../reports-monitoring/concept-reporting-api.md).

![Raport logowania usługi Azure AD w Azure Portal](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Struktura raportu logowania

Raporty działania logowania dla usługi MFA umożliwiają dostęp do następujących informacji:

**Wymagana usługa MFA:** czy usługa MFA jest wymagana do logowania, czy też nie. Uwierzytelnianie wieloskładnikowe może być wymagane ze względu na użytkownika MFA, dostęp warunkowy lub inne powody. Możliwe wartości to **Yes** lub **no**.

**Wynik usługi MFA:** więcej informacji na temat tego, czy uwierzytelnianie MFA zostało przeprowadzone pomyślnie, czy też nie:

- Jeśli uwierzytelnianie MFA powiodło się, ta kolumna zawiera więcej informacji na temat sposobu przeprowadzenia uwierzytelnienia MFA.
   - Azure Multi-Factor Authentication
      - ukończone w chmurze
      - wygasłe z powodu zasad skonfigurowanych w dzierżawie
      - wyświetlono monit o rejestrację
      - zrealizowane przez oświadczenie w tokenie
      - zrealizowane przez oświadczenie dostarczone przez dostawcę zewnętrznego
      - zrealizowane przez silne uwierzytelnianie
      - pominięte, ponieważ zrealizowany przepływ był przepływem logowania brokera systemu Windows
      - pominięte z powodu hasła aplikacji
      - pominięte z powodu lokalizacji
      - pominięte z powodu zarejestrowanego urządzenia
      - pominięte z powodu zapamiętanego urządzenia
      - pomyślnie ukończono
   - Przekierowane do zewnętrznego dostawcy w celu uwierzytelnienia wieloskładnikowego

- Jeśli uwierzytelnianie MFA nie powiodło się, ta kolumna zawiera przyczynę niepowodzenia.
   - Niepowodzenie uwierzytelniania Azure Multi-Factor Authentication;
      - uwierzytelnianie w toku
      - próba zduplikowanego uwierzytelnienia
      - zbyt wiele razy wprowadzono niepoprawny kod
      - nieprawidłowe uwierzytelnienie
      - nieprawidłowy kod weryfikacyjny aplikacji mobilnej
      - błąd konfiguracji
      - połączenie telefoniczne przekierowane do poczty głosowej
      - numer telefonu ma nieprawidłowy format
      - błąd usługi
      - nie można nawiązać połączenia z telefonem użytkownika
      - nie można wysłać powiadomienia aplikacji mobilnej do urządzenia
      - nie można wysłać powiadomienia aplikacji mobilnej
      - uwierzytelnienie odrzucone przez użytkownika
      - użytkownik nie odpowiedział na powiadomienie aplikacji mobilnej
      - użytkownik nie ma żadnych zarejestrowanych metod weryfikacji
      - użytkownik wprowadził nieprawidłowy kod
      - użytkownik wprowadził nieprawidłowy numer PIN
      - użytkownik rozłączył połączenie telefoniczne bez pomyślnego uwierzytelnienia
      - użytkownik jest zablokowany
      - użytkownik nigdy nie wprowadził kodu weryfikacyjnego
      - nie znaleziono użytkownika
      - kod weryfikacyjny został już raz użyty

**Metoda uwierzytelniania usługi MFA:** metoda uwierzytelniania stosowana przez użytkownika w celu zakończenia uwierzytelniania MFA. Możliwe wartości to:

- Wiadomość SMS
- Powiadomienie aplikacji mobilnej
- Połączenie telefoniczne (numer telefonu uwierzytelniania)
- Kod weryfikacyjny aplikacji mobilnej
- Połączenie telefoniczne (numer telefonu służbowego)
- Połączenie telefoniczne (alternatywny numer telefonu uwierzytelniania)

**Szczegóły uwierzytelniania usługi MFA:** wyczyszczona wersja numeru telefonu, na przykład +X XXXXXXXX64.

**Dostęp warunkowy** Znajdź informacje o zasadach dostępu warunkowego, których dotyczy próba logowania, w tym:

- Nazwa zasady
- Przyznaj kontrolki
- Kontrolki sesji
- Wynik

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Raportowanie programu PowerShell dla użytkowników zarejestrowanych na potrzeby usługi MFA

Najpierw upewnij się, że zainstalowano [moduł PowerShell MSOnline V1](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) .

Zidentyfikuj użytkowników, którzy zostali zarejestrowani na potrzeby uwierzytelniania wieloskładnikowego, korzystając z programu PowerShell w następujący sposób. Ten zestaw poleceń wyklucza wyłączonych użytkowników, ponieważ nie można uwierzytelnić tych kont w usłudze Azure AD.

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Zidentyfikuj użytkowników, którzy nie zarejestrowali usługi MFA przy użyciu poniższego programu PowerShell. Ten zestaw poleceń wyklucza wyłączonych użytkowników, ponieważ nie można uwierzytelnić tych kont w usłudze Azure AD.

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Zidentyfikuj zarejestrowane metody użytkownika i wyjścia. 

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>Możliwe wyniki w raportach aktywności

Poniższa tabela może służyć do rozwiązywania problemów z uwierzytelnianiem wieloskładnikowym za pomocą pobranej wersji raportu aktywność usługi uwierzytelnianie wieloskładnikowe. Nie będą one widoczne bezpośrednio w Azure Portal.

| Wynik wywołania | Opis | Szeroki opis |
| --- | --- | --- |
| SUCCESS_WITH_PIN | Wprowadzono kod PIN | Użytkownik wprowadził kod PIN. Jeśli uwierzytelnianie zakończyło się pomyślnie, wprowadzono poprawny kod PIN. W przypadku odmowy uwierzytelnienia wprowadzono niepoprawny kod PIN lub użytkownik jest ustawiony w trybie standardowym. |
| SUCCESS_NO_PIN | Wprowadzono tylko # | Jeśli użytkownik ma ustawioną opcję Tryb PRZYPINAnia i zostanie odrzucone uwierzytelnianie, oznacza to, że użytkownik nie wprowadził numeru PIN i tylko wprowadzono wartość #.  Jeśli użytkownik jest ustawiony w trybie standardowym, a uwierzytelnianie powiedzie się, oznacza to, że użytkownik wprowadził tylko #, który jest prawidłowym zadaniem w trybie standardowym. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Nie naciśnięto po wprowadzeniu | Użytkownik nie wysłał żadnych cyfr DTMF, ponieważ # nie wprowadzono. Inne wprowadzone cyfry nie są wysyłane, chyba że zostanie wprowadzony znak # wskazujący na ukończenie wpisu. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Brak danych wejściowych telefonu — Przekroczono limit czasu | Odebrano odpowiedź na wywołanie, ale nie ma odpowiedzi. Zazwyczaj oznacza to, że wywołanie zostało pobrane przez pocztę głosową. |
| SUCCESS_PIN_EXPIRED | KOD PIN wygasł i nie został zmieniony | KOD PIN użytkownika wygasł i został wyświetlony monit o jego zmianę, ale zmiana numeru PIN nie została pomyślnie ukończona. |
| SUCCESS_USED_CACHE | Użyta pamięć podręczna | Uwierzytelnianie zakończyło się pomyślnie bez wywołania Multi-Factor Authentication, ponieważ poprzednie pomyślne uwierzytelnienie dla tej samej nazwy użytkownika wystąpił w skonfigurowanym przedziale czasu pamięci podręcznej. |
| SUCCESS_BYPASSED_AUTH | Pominięte uwierzytelnianie | Uwierzytelnianie zakończyło się pomyślnie za pomocą jednorazowego obejścia zainicjowanego dla użytkownika. Aby uzyskać więcej informacji na temat obejścia, zobacz Raport o pominiętych użytkownikach. |
| SUCCESS_USED_IP_BASED_CACHE | Użyta pamięć podręczna oparta na protokole IP | Uwierzytelnianie zakończyło się pomyślnie bez wywołania Multi-Factor Authentication od momentu wcześniejszego pomyślnego uwierzytelnienia dla tej samej nazwy użytkownika, typu uwierzytelniania, nazwy aplikacji i adresu IP w skonfigurowanym przedziale czasu pamięci podręcznej. |
| SUCCESS_USED_APP_BASED_CACHE | Używana pamięć podręczna oparta na aplikacji | Uwierzytelnianie zakończyło się pomyślnie bez wywołania Multi-Factor Authentication, ponieważ poprzednie pomyślne uwierzytelnienie dla tej samej nazwy użytkownika, typu uwierzytelniania i nazwy aplikacji w skonfigurowanym przedziale czasu pamięci podręcznej. |
| SUCCESS_INVALID_INPUT | Nieprawidłowe dane wejściowe telefonu | Odpowiedź wysłana z telefonu jest nieprawidłowa. Może to być z komputera faksowego lub modemu albo użytkownik przeszedł * jako część swojego numeru PIN. |
| SUCCESS_USER_BLOCKED | Użytkownik jest zablokowany | Numer telefonu użytkownika jest zablokowany. Użytkownik może zainicjować zablokowany numer w trakcie wywołania uwierzytelniania lub przez administratora przy użyciu Azure Portal. <br> Uwaga: zablokowany numer jest również ubocznymem alertu oszustwa. |
| SUCCESS_SMS_AUTHENTICATED | Wiadomość SMS uwierzytelniona | W przypadku dwukierunkowego komunikatu testowego użytkownik prawidłowo odpowiedział przy użyciu hasła jednorazowego (OTP) lub OTP + kod PIN. |
| SUCCESS_SMS_SENT | Wiadomość SMS wysłana | W przypadku wiadomości tekstowej wiadomość tekstowa zawierająca jednorazowy kod dostępu (OTP) została pomyślnie wysłana. Użytkownik wprowadzi do aplikacji wartość OTP lub OTP + numer PIN w celu ukończenia uwierzytelniania. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Aplikacja mobilna uwierzytelniona | Użytkownik został pomyślnie uwierzytelniony za pośrednictwem aplikacji mobilnej. |
| SUCCESS_OATH_CODE_PENDING | Oczekiwanie na kod OATH | Użytkownik otrzymał monit o ich kod OATH, ale nie odpowiedział. |
| SUCCESS_OATH_CODE_VERIFIED | Zweryfikowano kod OATH | Po wyświetleniu monitu użytkownik wprowadził prawidłowy kod OATH. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Zweryfikowano rezerwowy kod OATH | Użytkownik odmówił uwierzytelnienia przy użyciu metody podstawowej Multi-Factor Authentication, a następnie podał prawidłowy kod OATH dla powrotu. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Udzielono odpowiedzi na rezerwowe pytania zabezpieczające | Użytkownik odmówił uwierzytelnienia przy użyciu metody podstawowej Multi-Factor Authentication, a następnie poprawnego udzielenia odpowiedzi na pytania zabezpieczające dla powrotu. |
| FAILED_PHONE_BUSY | Uwierzytelnianie jest już w toku | Multi-Factor Authentication już przetwarzał uwierzytelnianie dla tego użytkownika. Jest to często spowodowane przez klientów usługi RADIUS, którzy wysyłają wiele żądań uwierzytelnienia podczas tego samego logowania. |
| CONFIG_ISSUE | Telefon nieosiągalny | Podjęto próbę wywołania, ale nie można jej umieścić lub nie udzielono odpowiedzi. Obejmuje to sygnał zajętości, szybki czas zajętości (odłączony), trzy tony (liczba niedostępnych usług), przekroczenie limitu czasu podczas dzwonienia, itp. |
| FAILED_INVALID_PHONENUMBER | Nieprawidłowy format numeru telefonu | Numer telefonu ma nieprawidłowy format. Numery telefonów muszą być liczbowe i muszą mieć 10 cyfr dla kodu kraju + 1 (Stany Zjednoczone & Kanada). |
| FAILED_USER_HUNGUP_ON_US | Użytkownik zawiesił telefon | Użytkownik odpowiedział na telefon, ale następnie zawiesił się bez naciskania żadnych przycisków. |
| FAILED_INVALID_EXTENSION | Nieprawidłowe rozszerzenie | Rozszerzenie zawiera nieprawidłowe znaki. Dozwolone są tylko cyfry, przecinki, * i #. Można również użyć @ prefix. |
| FAILED_FRAUD_CODE_ENTERED | Wprowadzono kod oszustwa | Użytkownik wybrany do zgłaszania oszustw w trakcie wywołania, co spowodowało odmowę uwierzytelnienia i zablokowany numer telefonu.| 
| FAILED_SERVER_ERROR | Nie można nawiązać połączenia | Usługa Multi-Factor Authentication nie mogła nawiązać połączenia. |
| FAILED_SMS_NOT_SENT | Nie można wysłać wiadomości tekstowej | Nie można wysłać wiadomości tekstowej. Brak uwierzytelniania. |
| FAILED_SMS_OTP_INCORRECT | Nieprawidłowa wiadomość tekstowa OTP | Użytkownik wprowadził nieprawidłowy jednorazowy kod dostępu (OTP) z otrzymanej wiadomości tekstowej. Brak uwierzytelniania. |
| FAILED_SMS_OTP_PIN_INCORRECT | Nieprawidłowa wiadomość tekstowa OTP + numer PIN | Użytkownik wprowadził nieprawidłowy kod dostępu jednorazowego (OTP) i/lub nieprawidłowy numer PIN użytkownika. Brak uwierzytelniania. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Przekroczono maksymalną liczbę prób OTP wiadomości SMS | Użytkownik przekroczył maksymalną liczbę prób jednorazowego kodu dostępu (OTP). |
| FAILED_PHONE_APP_DENIED | Odmowa aplikacji mobilnej | Użytkownik odmówił uwierzytelnienia w aplikacji mobilnej, naciskając przycisk Odmów. |
| FAILED_PHONE_APP_INVALID_PIN | Aplikacja mobilna — nieprawidłowy numer PIN | Użytkownik wprowadził nieprawidłowy numer PIN podczas uwierzytelniania w aplikacji mobilnej. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Nie zmieniono numeru PIN aplikacji mobilnej | Użytkownik nie ukończył pomyślnie wymaganej zmiany numeru PIN w aplikacji mobilnej. |
| FAILED_FRAUD_REPORTED | Zgłoszono oszustwo | Użytkownik zgłosił oszustwo w aplikacji mobilnej. |
| FAILED_PHONE_APP_NO_RESPONSE | Aplikacja mobilna — brak odpowiedzi | Użytkownik nie odpowiedział na żądanie uwierzytelnienia aplikacji mobilnej. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Aplikacja mobilna — wszystkie urządzenia zablokowane | Urządzenia aplikacji mobilnej dla tego użytkownika nie odpowiadają na powiadomienia i zostały zablokowane. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Powiadomienie aplikacji mobilnej nie powiodło się | Wystąpił błąd podczas próby wysłania powiadomienia do aplikacji mobilnej na urządzeniu użytkownika. |
| FAILED_PHONE_APP_INVALID_RESULT | Niewłaściwy wynik aplikacji mobilnej | Aplikacja mobilna zwróciła nieprawidłowy wynik. |
| FAILED_OATH_CODE_INCORRECT | Nieprawidłowy kod OATH | Użytkownik wprowadził nieprawidłowy kod OATH.  Brak uwierzytelniania. |
| FAILED_OATH_CODE_PIN_INCORRECT | Nieprawidłowy kod OATH + numer PIN | Użytkownik wprowadził nieprawidłowy kod OATH i/lub nieprawidłowy numer PIN użytkownika.  Brak uwierzytelniania. |
| FAILED_OATH_CODE_DUPLICATE | Zduplikowany kod OATH | Użytkownik wprowadził wcześniej użyty kod OATH.  Brak uwierzytelniania. |
| FAILED_OATH_CODE_OLD | Nieaktualny kod OATH | Użytkownik wprowadził kod OATH, który poprzedza poprzednio użyty kod OATH.  Brak uwierzytelniania. |
| FAILED_OATH_TOKEN_TIMEOUT | Limit czasu wyniku kodu OATH | Wprowadzenie kodu OATH przez użytkownika zajęło zbyt dużo czasu, a Multi-Factor Authentication próba przekroczenia limitu. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Limit czasu wyniku pytań zabezpieczających | Użytkownik trwał zbyt długo, aby wprowadzić odpowiedź na pytania zabezpieczające, a Multi-Factor Authentication próba przekroczenia limitu czasu. |
| FAILED_AUTH_RESULT_TIMEOUT | Limit czasu wyniku uwierzytelniania | Wykonanie Multi-Factor Authentication przez użytkownika trwało zbyt długo. |
| FAILED_AUTHENTICATION_THROTTLED | Ograniczenie uwierzytelniania | Multi-Factor Authentication próba została ograniczona przez usługę. |

## <a name="next-steps"></a>Następne kroki

* [SSPR i raportowanie informacji dotyczących użycia i usługi MFA](howto-authentication-methods-usage-insights.md)
* [Dla użytkowników](../user-help/multi-factor-authentication-end-user.md)
* [Miejsce wdrożenia](concept-mfa-whichversion.md)
