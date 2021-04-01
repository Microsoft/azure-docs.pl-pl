---
title: 'Azure AD Connect: Rozwiązywanie problemów z łącznością z usługą Azure AD | Microsoft Docs'
description: Wyjaśnia, jak rozwiązywać problemy z łącznością z Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 56e9820c5e3a750a35b7271b86750df00eb4784e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677062"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Rozwiązywanie problemów z łącznością z usługą Azure AD
W tym artykule wyjaśniono, jak działa połączenie między Azure AD Connect i usługą Azure AD oraz jak rozwiązywać problemy z łącznością. Te problemy najprawdopodobniej będą widoczne w środowisku z serwerem proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Rozwiązywanie problemów z łącznością w Kreatorze instalacji
Azure AD Connect korzysta z nowoczesnego uwierzytelniania (przy użyciu biblioteki ADAL) do uwierzytelniania. Kreator instalacji i aparat synchronizacji prawidłowo wymagają skonfigurowania machine.config, ponieważ te dwa są aplikacjami .NET.

W tym artykule pokazano, jak firma Fabrikam nawiązuje połączenie z usługą Azure AD za pomocą serwera proxy. Serwer proxy ma nazwę fabrikamproxy i używa portu 8080.

Najpierw należy upewnić się, że [**machine.config**](how-to-connect-install-prerequisites.md#connectivity) jest prawidłowo skonfigurowany, a **usługa synchronizacji Microsoft Azure AD** została uruchomiona ponownie po aktualizacji pliku machine.config.
![Zrzut ekranu przedstawia część pliku konfiguracji Machine dot.](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> W niektórych blogach innych niż firmy Microsoft jest udokumentowane, że zamiast tego należy wprowadzić zmiany w miiserver.exe.config. Jednak ten plik jest zastępowany przy każdym uaktualnieniu, więc nawet jeśli działa podczas instalacji początkowej, system przestanie działać po pierwszym uaktualnieniu. Z tego powodu zaleca się aktualizację machine.config zamiast tego.
>
>

Na serwerze proxy musi być również otwartych wymaganych adresów URL. Oficjalna lista jest udokumentowana w [adresach URL pakietu Office 365 i w zakresach adresów IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

W przypadku tych adresów URL Poniższa tabela stanowi wartość bezwzględną minimum, aby można było połączyć się z usługą Azure AD. Ta lista nie zawiera żadnych opcjonalnych funkcji, takich jak zapisywanie zwrotne haseł czy Azure AD Connect Health. Opisano je tutaj, aby pomóc w rozwiązywaniu problemów z konfiguracją początkową.

| Adres URL | Port | Opis |
| --- | --- | --- |
| mscrl.microsoft.com |PROTOKÓŁ HTTP/80 |Służy do pobierania list list CRL. |
| \*. verisign.com |PROTOKÓŁ HTTP/80 |Służy do pobierania list list CRL. |
| \*. entrust.net |PROTOKÓŁ HTTP/80 |Służy do pobierania list list CRL dla usługi MFA. |
| \*.windows.net |HTTPS/443 |Używane do logowania się do usługi Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Używany na potrzeby usługi MFA. |
| \*.microsoftonline.com |HTTPS/443 |Służy do konfigurowania katalogu usługi Azure AD i importowania/eksportowania danych. |
| \*. crl3.digicert.com |PROTOKÓŁ HTTP/80 |Służy do weryfikowania certyfikatów. |
| \*. crl4.digicert.com |PROTOKÓŁ HTTP/80 |Służy do weryfikowania certyfikatów. |
| \*. ocsp.digicert.com |PROTOKÓŁ HTTP/80 |Służy do weryfikowania certyfikatów. |
| \*. www.d-trust.net |PROTOKÓŁ HTTP/80 |Służy do weryfikowania certyfikatów. |
| \*. root-c3-ca2-2009.ocsp.d-trust.net |PROTOKÓŁ HTTP/80 |Służy do weryfikowania certyfikatów. |
| \*. crl.microsoft.com |PROTOKÓŁ HTTP/80 |Służy do weryfikowania certyfikatów. |
| \*. oneocsp.microsoft.com |PROTOKÓŁ HTTP/80 |Służy do weryfikowania certyfikatów. |
| \*. ocsp.msocsp.com |PROTOKÓŁ HTTP/80 |Służy do weryfikowania certyfikatów. |

## <a name="errors-in-the-wizard"></a>Błędy w Kreatorze
Kreator instalacji korzysta z dwóch różnych kontekstów zabezpieczeń. Na stronie **nawiązywanie połączenia z usługą Azure AD** korzysta obecnie z zalogowanego użytkownika. Na stronie **Konfiguracja** zmienia się na konto, na [którym działa usługa programu dla aparatu synchronizacji](reference-connect-accounts-permissions.md#adsync-service-account). Jeśli wystąpi problem, najprawdopodobniej pojawił się już na stronie **nawiązywanie połączenia z usługą Azure AD** w kreatorze, ponieważ konfiguracja serwera proxy jest globalna.

Poniżej znajdują się najczęstsze błędy występujące w Kreatorze instalacji.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Kreator instalacji nie został poprawnie skonfigurowany
Ten błąd pojawia się, gdy Kreator nie może nawiązać połączenia z serwerem proxy.
![Zrzut ekranu przedstawia błąd: nie można zweryfikować poświadczeń.](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Jeśli widzisz ten błąd, sprawdź, czy [machine.config](how-to-connect-install-prerequisites.md#connectivity) został prawidłowo skonfigurowany.
* Jeśli jest to poprawne, wykonaj kroki opisane w sekcji [Weryfikowanie łączności z serwerem proxy](#verify-proxy-connectivity) , aby sprawdzić, czy problem jest obecny poza kreatorem.

### <a name="a-microsoft-account-is-used"></a>Zostanie użyta konto Microsoft
Jeśli używasz **konto Microsoft** , a nie konta w **organizacji** , zobaczysz błąd ogólny.
![Używane jest konto Microsoft](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Nie można nawiązać połączenia z punktem końcowym usługi MFA
Ten błąd jest wyświetlany, jeśli **https://secure.aadcdn.microsoftonline-p.com** nie można skontaktować się z punktem końcowym, a Administrator globalny ma włączoną usługę MFA.
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Jeśli widzisz ten błąd, sprawdź, czy punkt końcowy **Secure.aadcdn.microsoftonline-p.com** został dodany do serwera proxy.

### <a name="the-password-cannot-be-verified"></a>Nie można zweryfikować hasła
Jeśli Kreator instalacji pomyślnie nawiązuje połączenie z usługą Azure AD, ale nie można zweryfikować hasła, zobaczysz następujący błąd: ![ nieprawidłowe hasło.](./media/tshoot-connect-connectivity/badpassword.png)

* Czy hasło jest hasłem tymczasowym i należy je zmienić? Czy jest to prawidłowe hasło? Spróbuj zalogować się do programu `https://login.microsoftonline.com` (na innym komputerze niż serwer Azure AD Connect) i sprawdź, czy konto jest możliwe do użycia.

### <a name="verify-proxy-connectivity"></a>Sprawdź łączność z serwerem proxy
Aby sprawdzić, czy serwer Azure AD Connect ma rzeczywistą łączność z serwerem proxy i Internetem, użyj polecenia programu PowerShell, aby sprawdzić, czy serwer proxy zezwala na żądania sieci Web. W wierszu polecenia programu PowerShell uruchom polecenie `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc` . (Technicznie to pierwsze wywołanie `https://login.microsoftonline.com` i ten identyfikator URI działa również, ale inny identyfikator URI jest szybszy, aby odpowiedzieć).

Program PowerShell używa konfiguracji w pliku machine.config do kontaktowania się z serwerem proxy. Ustawienia w usłudze winhttp/netsh nie powinny mieć wpływu na te polecenia cmdlet.

Jeśli serwer proxy jest prawidłowo skonfigurowany, powinien zostać wyświetlony stan sukces: ![ zrzut ekranu przedstawiający stan powodzenia, gdy serwer proxy jest prawidłowo skonfigurowany.](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Jeśli zostanie wyświetlony komunikat **nie można nawiązać połączenia z serwerem zdalnym**, program PowerShell próbuje wykonać bezpośrednie wywołanie bez użycia serwera proxy lub usługa DNS nie jest poprawnie skonfigurowana. Upewnij się, że plik **machine.config** jest prawidłowo skonfigurowany.
![unabletoconnect](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Jeśli serwer proxy nie został prawidłowo skonfigurowany, zostanie wyświetlony komunikat o błędzie: ![ proxy200 ](./media/tshoot-connect-connectivity/invokewebrequest403.png)
 ![ proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Błąd | Tekst błędu | Komentarz |
| --- | --- | --- |
| 403 |Forbidden |Serwer proxy nie został otwarty dla żądanego adresu URL. Ponownie odwiedź konfigurację serwera proxy i upewnij się, że [adresy URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) zostały otwarte. |
| 407 |Wymagane jest uwierzytelnianie serwera proxy |Serwer proxy wymaga logowania, ale nie został podany. Jeśli serwer proxy wymaga uwierzytelnienia, upewnij się, że to ustawienie jest skonfigurowane w machine.config. Upewnij się również, że używasz kont domeny dla użytkownika, który uruchamia kreatora i dla konta usługi. |

### <a name="proxy-idle-timeout-setting"></a>Ustawienie limitu czasu bezczynności serwera proxy
Gdy Azure AD Connect wysyła żądanie eksportu do usługi Azure AD, przetwarzanie żądania przez usługę Azure AD może potrwać do 5 minut przed wygenerowaniem odpowiedzi. Może się to zdarzyć zwłaszcza wtedy, gdy istnieje wiele obiektów grupy z dużymi członkostwami w grupach uwzględnionymi w tym samym żądaniu eksportu. Upewnij się, że limit czasu bezczynności serwera proxy jest skonfigurowany tak, aby był większy niż 5 minut. W przeciwnym razie sporadyczny problem z łącznością z usługą Azure AD może być zaobserwowany na serwerze Azure AD Connect.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Wzorzec komunikacji między Azure AD Connect i usługą Azure AD
Jeśli wykonano wszystkie powyższe kroki i nadal nie można nawiązać połączenia, w tym momencie można rozpocząć wyszukiwanie w dziennikach sieciowych. Ta sekcja zawiera dokument z normalnym i pomyślnym wzorcem łączności. Znajduje się w nim również lista typowych czerwonych śledziów, które mogą zostać zignorowane podczas odczytywania dzienników sieciowych.

* Istnieją wywołania do `https://dc.services.visualstudio.com` . Ten adres URL nie musi być otwarty na serwerze proxy, aby instalacja się powiodła i te wywołania można zignorować.
* Zobaczysz, że rozpoznawanie nazw DNS zawiera listę rzeczywistych hostów, które mają znajdować się w nsatc.net przestrzeni nazwy DNS, a inne przestrzenie nazw nie są w microsoftonline.com. Nie ma jednak żadnych żądań usługi sieci Web na rzeczywistych nazwach serwera i nie trzeba dodawać tych adresów URL do serwera proxy.
* Punkty końcowe adminwebservice i provisioningapi są punktami końcowymi odnajdywania i służą do znajdowania rzeczywistego punktu końcowego do użycia. Te punkty końcowe są różne w zależności od regionu.

### <a name="reference-proxy-logs"></a>Informacje o dziennikach serwera proxy
Oto zrzut z rzeczywistego dziennika proxy i strony Kreatora instalacji, z której został pobrany (zduplikowane wpisy do tego samego punktu końcowego zostały usunięte). Ta sekcja może służyć jako odwołanie do własnego serwera proxy i dzienników sieciowych. Rzeczywiste punkty końcowe mogą być inne w danym środowisku (w szczególności te adresy URL są *kursywą*).

**Nawiązywanie połączenia z usługą Azure AD**

| Godzina | Adres URL |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800 — zakotwiczenie*. microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-Relay*. microsoftonline.com:443 |

**Ustaw opcję**

| Godzina | Adres URL |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800 — zakotwiczenie*. microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900 — zakotwiczenie*. microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800 — zakotwiczenie*. microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-Relay*. microsoftonline.com:443 |

**Synchronizacja początkowa**

| Godzina | Adres URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900 — zakotwiczenie*. microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800 — zakotwiczenie*. microsoftonline.com:443 |

## <a name="authentication-errors"></a>Błędy uwierzytelniania
W tej sekcji omówiono błędy, które można zwrócić z biblioteki ADAL (Biblioteka uwierzytelniania używana przez Azure AD Connect) i program PowerShell. Wyjaśniony błąd powinien ułatwić zapoznanie się z następnymi krokami.

### <a name="invalid-grant"></a>Nieprawidłowy przydział
Nieprawidłowa nazwa użytkownika lub hasło. Aby uzyskać więcej informacji, zobacz [nie można zweryfikować hasła](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Nieznany typ użytkownika
Nie można znaleźć lub rozpoznać katalogu usługi Azure AD. Czy może próbować zalogować się przy użyciu nazwy użytkownika w niezweryfikowanej domenie?

### <a name="user-realm-discovery-failed"></a>Odnajdywanie obszaru użytkownika nie powiodło się
Problemy z konfiguracją sieci lub serwera proxy. Nie można nawiązać połączenia z siecią. Zobacz [Rozwiązywanie problemów z łącznością w Kreatorze instalacji](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Hasło użytkownika wygasło
Twoje poświadczenia wygasły. Zmień hasło.

### <a name="authorization-failure"></a>Niepowodzenie autoryzacji
Nie można autoryzować użytkownika do wykonania akcji w usłudze Azure AD.

### <a name="authentication-canceled"></a>Anulowano uwierzytelnianie
Żądanie uwierzytelniania wieloskładnikowego (MFA) zostało anulowane.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Nawiązywanie połączenia z usługą MS online nie powiodło się
Uwierzytelnianie zakończyło się pomyślnie, ale w programie Azure AD PowerShell występuje problem z uwierzytelnianiem.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Wymagana rola administratora globalnego usługi Azure AD
Użytkownik został pomyślnie uwierzytelniony. Jednak użytkownik nie ma przypisanej roli administratora globalnego. W ten [sposób można przypisywać użytkownikowi rolę administratora globalnego](../roles/permissions-reference.md) .

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Privileged Identity Management włączony
Uwierzytelnianie zakończyło się pomyślnie. Funkcja Privileged Identity Management została włączona i nie jesteś obecnie administratorem globalnym. Aby uzyskać więcej informacji, zobacz [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Informacje o firmie są niedostępne
Uwierzytelnianie zakończyło się pomyślnie. Nie można pobrać informacji o firmie z usługi Azure AD.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Informacje o domenie są niedostępne
Uwierzytelnianie zakończyło się pomyślnie. Nie można pobrać informacji o domenie z usługi Azure AD.

### <a name="unspecified-authentication-failure"></a>Nieokreślony błąd uwierzytelniania
Wyświetlany jako nieoczekiwany błąd w Kreatorze instalacji. Może się zdarzyć, jeśli spróbujesz użyć **konta Microsoft** , a nie **konta w organizacji**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Kroki rozwiązywania problemów z poprzednimi wersjami.
W przypadku wersji zaczynających się od numeru kompilacji 1.1.105.0 (wydanie z lutego 2016) Asystent logowania został wycofany. Ta sekcja i konfiguracja nie powinna już być wymagana, ale jest przechowywana jako odwołanie.

Aby Asystent logowania jednokrotnego działał, należy skonfigurować usługę WinHTTP. Tę konfigurację można wykonać przy użyciu [**polecenia netsh**](how-to-connect-install-prerequisites.md#connectivity).
![Zrzut ekranu przedstawia okno wiersza polecenia z uruchomionym narzędziem netsh w celu ustawienia serwera proxy.](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Asystent logowania nie został poprawnie skonfigurowany
Ten błąd pojawia się, gdy Asystent logowania nie może nawiązać połączenia z serwerem proxy lub serwer proxy nie zezwala na żądanie.
![Zrzut ekranu przedstawia błąd: nie można zweryfikować poświadczeń, sprawdź połączenie sieciowe i ustawienia zapory lub serwera proxy.](./media/tshoot-connect-connectivity/nonetsh.png)

* Jeśli widzisz ten błąd, poszukaj konfiguracji serwera proxy w narzędziu [netsh](how-to-connect-install-prerequisites.md#connectivity) i sprawdź, czy jest ona poprawna.
  ![Zrzut ekranu przedstawia okno wiersza polecenia z uruchomionym narzędziem netsh w celu wyświetlenia konfiguracji serwera proxy.](./media/tshoot-connect-connectivity/netshshow.png)
* Jeśli jest to poprawne, wykonaj kroki opisane w sekcji [Weryfikowanie łączności z serwerem proxy](#verify-proxy-connectivity) , aby sprawdzić, czy problem jest obecny poza kreatorem.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
