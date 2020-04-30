---
title: Konfigurowanie Multi-Factor Authentication platformy Azure — Azure Active Directory
description: W tym artykule opisano sposób konfigurowania ustawień usługi Azure Multi-Factor Authentication w Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9002447df44a1f0c87ec9de03e316d7d12d8a626
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82105662"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurowanie ustawień usługi Azure Multi-Factor Authentication

Ten artykuł ułatwia zarządzanie ustawieniami Multi-Factor Authentication w Azure Portal. Obejmuje ona różne tematy, które ułatwiają maksymalne wykorzystanie Multi-Factor Authentication platformy Azure. Nie wszystkie funkcje są dostępne w każdej wersji platformy Azure Multi-Factor Authentication.

Możesz uzyskać dostęp do ustawień związanych z usługą Azure Multi-Factor Authentication z poziomu Azure Portal, przechodząc do **Azure Active Directory** > **zabezpieczenia** > **MFA**.

![Azure Portal — ustawienia Multi-Factor Authentication usługi Azure AD](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Ustawienia

Niektóre z tych ustawień mają zastosowanie do serwera MFA, usługi Azure MFA lub obu.

| Funkcja | Opis |
| ------- | ----------- |
| Blokada konta | Tymczasowe blokowanie kont w usłudze wieloskładnikowe uwierzytelnianie, jeśli w wierszu występuje zbyt wiele nieudanych prób uwierzytelnienia. Ta funkcja ma zastosowanie tylko do użytkowników, którzy wprowadzają kod PIN do uwierzytelnienia. (Serwer MFA) |
| [Blokuj/Odblokuj użytkowników](#block-and-unblock-users) | Służy do blokowania, aby określeni użytkownicy mogli odbierać Multi-Factor Authentication żądania. Wszystkie próby uwierzytelnienia dla zablokowanych użytkowników są automatycznie odrzucane. Użytkownicy pozostają zablokowani przez 90 dni od momentu zablokowania. |
| [Alert dotyczący wykrycia oszustwa](#fraud-alert) | Konfigurowanie ustawień związanych z możliwościami użytkowników w celu zgłaszania fałszywych żądań weryfikacji |
| [Powiadomienia](#notifications) | Włącz powiadomienia zdarzeń z serwera MFA. |
| [Tokeny OATH](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Używany w środowiskach Azure MFA opartych na chmurze do zarządzania tokenami OATH dla użytkowników. |
| [Ustawienia połączenia telefonicznego](#phone-call-settings) | Konfigurowanie ustawień związanych z połączeniami telefonicznymi i powitami w środowiskach chmurowych i lokalnych. |
| Dostawcy | Spowoduje to wyświetlenie wszystkich istniejących dostawców uwierzytelniania, które mogły zostać skojarzone z Twoim kontem. Nie można utworzyć nowych dostawców uwierzytelniania od 1 września 2018 |

## <a name="manage-mfa-server"></a>Zarządzanie serwerem usługi MFA

Ustawienia w tej sekcji dotyczą tylko serwera MFA.

| Funkcja | Opis |
| ------- | ----------- |
| Ustawienia serwera | Pobierz serwer usługi MFA i Wygeneruj poświadczenia aktywacji w celu zainicjowania środowiska |
| [Jednorazowe obejście](#one-time-bypass) | Zezwalaj użytkownikowi na uwierzytelnianie bez przeprowadzania weryfikacji dwuetapowej przez ograniczony czas. |
| [Reguły buforowania](#caching-rules) |  Buforowanie jest używane głównie w przypadku systemów lokalnych, takich jak sieć VPN, Wyślij wiele żądań weryfikacji, gdy pierwsze żądanie jest nadal w toku. Ta funkcja umożliwia automatyczne pomyślne wykonanie kolejnych żądań po pomyślnym zakończeniu pierwszej weryfikacji w toku. |
| Stan serwera | Zobacz stan lokalnych serwerów usługi MFA, w tym wersji, stanu, adresu IP i daty ostatniej komunikacji. |

## <a name="activity-report"></a>Raport działań

Raporty dostępne tutaj są specyficzne dla serwera MFA (lokalnego). W przypadku raportów usługi Azure MFA (Cloud) zobacz Raport logowania w usłudze Azure AD.

## <a name="block-and-unblock-users"></a>Blokowanie i odblokowywanie użytkowników

Użyj funkcji _Blokuj i Odblokuj użytkowników_ , aby uniemożliwić użytkownikom otrzymywanie żądań uwierzytelniania. Wszystkie próby uwierzytelnienia dla zablokowanych użytkowników są automatycznie odrzucane. Użytkownicy pozostają zablokowani przez 90 dni od momentu zablokowania.

### <a name="block-a-user"></a>Blokuj użytkownika

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do usługi **Azure Active Directory** > **Security** > **MFA** > **Blokuj/Odblokuj użytkowników**.
3. Wybierz pozycję **Dodaj** , aby zablokować użytkownika.
4. Wybierz **grupę replikacji**. Wprowadź nazwę użytkownika dla zablokowanego użytkownika jako **username\@Domain.com**. Wprowadź komentarz w polu **Przyczyna** .
5. Wybierz pozycję **Dodaj** , aby zakończyć blokowanie użytkownika.

### <a name="unblock-a-user"></a>Odblokuj użytkownika

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do usługi **Azure Active Directory** > **Security** > **MFA** > **Blokuj/Odblokuj użytkowników**.
3. Wybierz opcję **Odblokuj** w kolumnie **Akcja** obok użytkownika, aby odblokować.
4. Wprowadź komentarz w **przyczynie odblokowania** pola.
5. Wybierz opcję **Odblokuj** , aby zakończyć Odblokowywanie użytkownika.

## <a name="fraud-alert"></a>Alert dotyczący wykrycia oszustwa

Skonfiguruj funkcję _alertu oszustwa_ , aby umożliwić użytkownikom zgłaszanie fałszywych prób dostępu do zasobów. Użytkownicy mogą raportować próby oszustwa przy użyciu aplikacji mobilnej lub za pośrednictwem telefonu.

### <a name="turn-on-fraud-alerts"></a>Włącz alerty oszustwa

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do**alertu**dotyczącego oszustwa **Azure Active Directory** > **Security** > **MFA** > .
3. Dla opcji **Zezwalaj użytkownikom na przesyłanie alertów oszustw** ustaw wartość **włączone**.
4. Wybierz pozycję **Zapisz**.

### <a name="configuration-options"></a>Opcje konfiguracji

* **Zablokuj użytkownika w przypadku zgłoszenia oszustwa**: w przypadku zgłaszania oszustw przez użytkownika konto jest blokowane przez 90 dni lub do momentu odblokowania konta przez administratora. Administrator może przejrzeć logowania przy użyciu raportu logowania i podjąć odpowiednie działania, aby zapobiec przyszłym oszustwom. Administrator może następnie [odblokować](#unblock-a-user) konto użytkownika.
* **Kod służący do zgłaszania oszustw podczas początkowego powitania**: gdy użytkownicy otrzymają połączenie telefoniczne w celu przeprowadzenia weryfikacji **#** dwuetapowej, zwykle są naciskane w celu potwierdzenia ich zalogowania. Aby zgłosić oszustwo, użytkownik wprowadza kod przed naciśnięciem klawisza **#**. Ten kod jest domyślnie **0** , ale można go dostosować.

   >[!NOTE]
   >Domyślne powitanie głosu firmy Microsoft instruuje użytkowników, aby nacisnąć **0 #** w celu przesłania alertu oszustwa. Jeśli chcesz użyć kodu innego niż **0**, zarejestruj i przekaż własne niestandardowe pozdrowienia głosowe z odpowiednimi instrukcjami dla użytkowników.
   >

### <a name="view-fraud-reports"></a>Wyświetlanie raportów o oszustwie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Azure Active Directory** > **Sign-ins** > **szczegóły uwierzytelniania**logowania. Raport oszustwa jest teraz częścią standardowego raportu logowania do usługi Azure AD, który będzie wyświetlany w **"szczegółowe dane wynikowe"** jako odmowa MFA, wprowadzony kod oszustwa.
 
## <a name="notifications"></a>Powiadomienia

Skonfiguruj tutaj adresy e-mail dla użytkowników, którzy będą otrzymywać wiadomości e-mail z alertami o oszustwie w **Azure Active Directory** > **Security** > **Multi-Factor Authentication** > **powiadomienia**Multi-Factor Authentication zabezpieczeń.

![Przykład wiadomości e-mail z alertem oszustwa powiadomień](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Ustawienia połączenia telefonicznego

### <a name="caller-id"></a>Identyfikator obiektu wywołującego

**Numer identyfikatora rozmówcy usługi MFA** — jest to liczba, którą użytkownicy zobaczą na telefonie. Dozwolone są tylko numery w Stanach Zjednoczonych.

>[!NOTE]
>Gdy Multi-Factor Authentication wywołania są umieszczane za pośrednictwem publicznej sieci telefonicznej, czasami są kierowane przez operatora, który nie obsługuje identyfikatora rozmówcy. Z tego powodu identyfikator wywołującego nie jest gwarantowany, mimo że system Multi-Factor Authentication zawsze go wysyła.

W Stany Zjednoczone, jeśli nie skonfigurowano identyfikatora obiektu wywołującego usługi MFA, połączenia głosowe od firmy Microsoft pochodzą z następujących liczb: + 1 (866) 539 4191, + 1 (855) 330 8653 i + 1 (877) 668 6536. W przypadku używania filtrów spamu upewnij się, że te liczby nie zostały wykluczone.

### <a name="custom-voice-messages"></a>Niestandardowe wiadomości głosowe

Możesz użyć własnych nagrań lub powitań na potrzeby weryfikacji dwuetapowej przy użyciu funkcji _niestandardowych wiadomości głosowych_ . Te komunikaty mogą służyć jako uzupełnienie lub zastępowanie nagrań firmy Microsoft.

Przed rozpoczęciem należy pamiętać o następujących ograniczeniach:

* Obsługiwane formaty plików to WAV i MP3.
* Limit rozmiaru pliku wynosi 1 MB.
* Komunikaty uwierzytelniania powinny być krótsze niż 20 sekund. Komunikaty dłuższe niż 20 sekund mogą spowodować niepowodzenie weryfikacji. Użytkownik może nie odpowiadać, zanim zakończy się komunikat i zostanie przeprowadzony limit czasu weryfikacji.

### <a name="custom-message-language-behavior"></a>Niestandardowe zachowanie języka komunikatów

Gdy do użytkownika zostanie odtworzony niestandardowy komunikat głosowy, język wiadomości zależy od następujących czynników:

* Język bieżącego użytkownika.
  * Język wykryty przez przeglądarkę użytkownika.
  * Inne scenariusze uwierzytelniania mogą zachowywać się inaczej.
* Język wszystkich dostępnych komunikatów niestandardowych.
  * Ten język jest wybierany przez administratora, gdy zostanie dodany niestandardowy komunikat.

Na przykład jeśli istnieje tylko jeden komunikat niestandardowy z językiem niemieckim:

* Użytkownik, który uwierzytelnia się w języku niemieckim, będzie słyszeć niestandardowy komunikat niemiecki.
* Użytkownik, który uwierzytelnia się w języku angielskim, będzie słyszeć standardowy komunikat w języku angielskim.

### <a name="set-up-a-custom-message"></a>Konfigurowanie wiadomości niestandardowej

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
1. Przejdź do **Azure Active Directory** > **Security** > **MFA** > **ustawień połączenia telefonicznego**usługi MFA.
1. Wybierz pozycję **Dodaj powitanie**.
1. Wybierz typ pozdrowienia.
1. Wybierz język.
1. Wybierz plik dźwiękowy MP3 lub WAV, który ma zostać przekazany.
1. Wybierz pozycję **Dodaj**.

### <a name="custom-voice-message-defaults"></a>Niestandardowe wartości domyślne wiadomości głosowych

Przykładowe skrypty do tworzenia komunikatów niestandardowych.

| Nazwa komunikatu | Skrypt |
| --- | --- |
| Uwierzytelnianie powiodło się | Logowanie zostało pomyślnie zweryfikowane. Koniec. |
| Monit o rozszerzenie | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz krzyżyk, aby kontynuować. |
| Potwierdzenie oszustwa | Przesłano alert oszustwa. Aby odblokować konto, skontaktuj się z działem pomocy technicznej IT w Twojej firmie. |
| Pozdrowienie oszustwa (standard) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. Jeśli weryfikacja nie została zainicjowana, ktoś może próbować uzyskać dostęp do Twojego konta. Naciśnij zero funta, aby przesłać alert oszustwa. Spowoduje to powiadomienie zespołu IT firmy i zablokowanie dalszych prób weryfikacji. |
| Zgłoszono oszustwo informujące o przesłaniu alertu oszustwa. | Aby odblokować konto, skontaktuj się z działem pomocy technicznej IT w Twojej firmie. |
| Aktywacja | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. |
| Ponowienie próby uwierzytelnienia | Odmowa weryfikacji. |
| Ponów próbę (standard) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. |
| Greetings (standard) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. |
| Pozdrowienie (PIN) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Wprowadź numer PIN, a następnie wybierz krzyżyk, aby zakończyć weryfikację. |
| Pozdrowienie oszustwa (PIN) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft.  Wprowadź numer PIN, a następnie wybierz krzyżyk, aby zakończyć weryfikację. Jeśli weryfikacja nie została zainicjowana, ktoś może próbować uzyskać dostęp do Twojego konta. Naciśnij zero funta, aby przesłać alert oszustwa. Spowoduje to powiadomienie zespołu IT firmy i zablokowanie dalszych prób weryfikacji. |
| Ponów próbę (PRZYPNIJ) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Wprowadź numer PIN, a następnie wybierz krzyżyk, aby zakończyć weryfikację. |
| Monituj o rozszerzenie po cyfrach | Jeśli jest już w tym rozszerzeniu, naciśnij krzyżyk, aby kontynuować. |
| Odmowa uwierzytelniania | Niestety, nie można teraz zalogować. Spróbuj ponownie później. |
| Pozdrowienie aktywacji (warstwa standardowa) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. |
| Ponowna próba aktywacji (standardowa) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. |
| Pozdrowienie aktywacji (PIN) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Wprowadź numer PIN, a następnie wybierz krzyżyk, aby zakończyć weryfikację. |
| Monituj o rozszerzenie przed cyframi | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Prześlij to wywołanie do rozszerzenia... |

## <a name="one-time-bypass"></a>Jednorazowe obejście

Funkcja _jednorazowe obejście_ umożliwia użytkownikowi uwierzytelnianie pojedynczego czasu bez przeprowadzania weryfikacji dwuetapowej. Obejście jest tymczasowe i wygasa po określonej liczbie sekund. W sytuacjach, gdy aplikacja mobilna lub telefon nie otrzymuje powiadomienia lub połączenia telefonicznego, można zezwolić na jednorazowe obejście, aby użytkownik mógł uzyskać dostęp do żądanego zasobu.

### <a name="create-a-one-time-bypass"></a>Utwórz jednorazowe obejście

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **Azure Active Directory** > **zabezpieczenia** > usługi**MFA** > **jednorazowo**.
3. Wybierz pozycję **Dodaj**.
4. W razie potrzeby wybierz grupę replikacji dla obejścia.
5. Wprowadź nazwę użytkownika jako **nazwę\@użytkownika Domain.com**. Wprowadź liczbę sekund, przez jaką obejście ma być ostatnie. Wprowadź przyczynę obejścia.
6. Wybierz pozycję **Dodaj**. Limit czasu zaczyna obowiązywać natychmiast. Użytkownik musi się zalogować przed upływem jednorazowego obejścia.

### <a name="view-the-one-time-bypass-report"></a>Wyświetl raport jednorazowego obejścia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do **Azure Active Directory** > **zabezpieczenia** > usługi**MFA** > **jednorazowo**.

## <a name="caching-rules"></a>Reguły buforowania

Można ustawić okres, aby zezwolić na próby uwierzytelniania po uwierzytelnieniu użytkownika przy użyciu funkcji _buforowania_ . Kolejne próby uwierzytelniania dla użytkownika w określonym przedziale czasu powiodło się automatycznie. Buforowanie jest używane głównie w przypadku systemów lokalnych, takich jak sieć VPN, Wyślij wiele żądań weryfikacji, gdy pierwsze żądanie jest nadal w toku. Ta funkcja umożliwia automatyczne pomyślne wykonanie kolejnych żądań po pomyślnym zakończeniu pierwszej weryfikacji w toku.

>[!NOTE]
>Funkcja buforowania nie jest przeznaczona do użycia podczas logowania do Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Konfigurowanie buforowania

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **Azure Active Directory** > **Security** > **MFA** > **reguły buforowania**usługi MFA.
3. Wybierz pozycję **Dodaj**.
4. Z listy rozwijanej wybierz **Typ pamięci podręcznej** . Wprowadź maksymalną liczbę sekund w **pamięci podręcznej**.
5. W razie potrzeby wybierz typ uwierzytelniania i Określ aplikację.
6. Wybierz pozycję **Dodaj**.

## <a name="mfa-service-settings"></a>Ustawienia usługi MFA

Ustawienia dotyczące haseł aplikacji, zaufanych adresów IP, opcji weryfikacji i zapamiętania usługi uwierzytelniania wieloskładnikowego dla systemu Azure Multi-Factor Authentication można znaleźć w obszarze Ustawienia usług. Ustawienia usługi są dostępne z poziomu Azure Portal, przechodząc do **Azure Active Directory** > **zabezpieczenia** > **MFA** > **wprowadzenie** > **Konfigurowanie** > **dodatkowych ustawień usługi MFA opartych na chmurze**.

![Ustawienia usługi Azure Multi-Factor Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

Zakresy zaufanych adresów IP mogą być prywatne lub publiczne.

## <a name="app-passwords"></a>Hasła aplikacji

Niektóre aplikacje, takie jak Office 2010 lub wcześniejsza i Apple mail przed iOS 11, nie obsługują weryfikacji dwuetapowej. Aplikacje nie są skonfigurowane do akceptowania drugiej weryfikacji. Aby korzystać z tych aplikacji, Skorzystaj z funkcji _hasła aplikacji_ . Możesz użyć hasła aplikacji zamiast tradycyjnego hasła, aby umożliwić aplikacji pominięcie weryfikacji dwuetapowej i kontynuowanie pracy.

Nowoczesne uwierzytelnianie jest obsługiwane przez klientów Microsoft Office 2013 i nowszych. Klienci pakietu Office 2013, w tym Outlook, obsługują nowoczesne protokoły uwierzytelniania i mogą być włączeni do pracy z weryfikacją dwuetapową. Po włączeniu klienta hasła aplikacji nie są wymagane dla klienta.

>[!NOTE]
>Hasła aplikacji nie działają z zasadami uwierzytelniania wieloskładnikowego opartymi na dostępie warunkowym i nowoczesnego uwierzytelniania.

### <a name="considerations-about-app-passwords"></a>Zagadnienia dotyczące haseł aplikacji

W przypadku korzystania z haseł aplikacji należy wziąć pod uwagę następujące ważne kwestie:

* Hasła aplikacji są wprowadzane tylko raz dla aplikacji. Użytkownicy nie muszą śledzić haseł ani wprowadzać ich za każdym razem.
* Rzeczywista wartość hasła jest generowana automatycznie i nie jest dostarczana przez użytkownika. Automatyczne generowanie hasła jest trudniejsze dla osoby atakującej w celu odgadnięcia i jest bezpieczniejsze.
* Dla każdego użytkownika istnieje limit 40 haseł.
* Aplikacje, które buforują hasła i używają ich w scenariuszach lokalnych, mogą zacząć kończyć się niepowodzeniem, ponieważ hasło aplikacji nie jest znane poza kontem służbowym. Przykładem tego scenariusza są wiadomości e-mail programu Exchange, które są lokalne, ale zarchiwizowana poczta znajduje się w chmurze. W tym scenariuszu to samo hasło nie działa.
* Po włączeniu Multi-Factor Authentication na koncie użytkownika hasła aplikacji mogą być używane z większością klientów nie korzystających z przeglądarki, takich jak Outlook i Microsoft Skype dla firm. Akcje administracyjne nie mogą być wykonywane przy użyciu haseł aplikacji przez aplikacje nie korzystające z przeglądarki, takie jak Windows PowerShell. Akcje nie mogą być wykonywane nawet wtedy, gdy użytkownik ma konto administracyjne. Aby uruchamiać skrypty programu PowerShell, należy utworzyć konto usługi o silnym haśle i nie włączać konta na potrzeby weryfikacji dwuetapowej.

>[!WARNING]
>Hasła aplikacji nie działają w środowiskach hybrydowych, w których klienci komunikują się z punktami końcowymi automatycznego odnajdywania lokalnego i w chmurze. Hasła domeny są wymagane do uwierzytelniania lokalnego. Do uwierzytelniania w chmurze wymagane są hasła aplikacji.

### <a name="guidance-for-app-password-names"></a>Wskazówki dotyczące nazw haseł aplikacji

Nazwy haseł aplikacji powinny odzwierciedlać urządzenie, na którym są używane. Jeśli masz komputer przenośny, który ma aplikacje niekorzystające z przeglądarki, takie jak Outlook, Word i Excel, Utwórz jedno hasło aplikacji o nazwie **laptop** dla tych aplikacji. Utwórz inne hasło aplikacji o nazwie **Desktop** dla tych samych aplikacji, które są uruchamiane na komputerze stacjonarnym.

>[!NOTE]
>Zalecamy utworzenie jednego hasła aplikacji dla każdego urządzenia, a nie jednego hasła aplikacji.

### <a name="federated-or-single-sign-on-app-passwords"></a>Hasła aplikacji federacyjnych lub logowania jednokrotnego

Usługa Azure AD obsługuje Federacji lub Logowanie jednokrotne (SSO) z lokalnym systemem Windows Server Active Directory Domain Services (AD DS). Jeśli Twoja organizacja jest federacyjnym usługą Azure AD i używasz usługi Azure Multi-Factor Authentication, weź pod uwagę następujące kwestie dotyczące haseł aplikacji.

>[!NOTE]
>Poniższe punkty dotyczą tylko klientów federacyjnych (SSO).

* Hasła aplikacji są weryfikowane przez usługę Azure AD i w związku z tym pomijają Federacji. Federacja jest aktywnie używana tylko podczas konfigurowania haseł aplikacji.
* Nie nawiązano kontaktu z dostawcą tożsamości (dostawcy tożsamości) dla użytkowników federacyjnych (SSO), w przeciwieństwie do przepływu pasywnego. Hasła aplikacji są przechowywane na koncie służbowym. Jeśli użytkownik opuści firmę, informacje o użytkowniku są przepływane do konta służbowego przy użyciu narzędzia **DirSync** w czasie rzeczywistym. Synchronizacja z wyłączeniem lub usuwaniem konta może potrwać do 3 godzin, co może opóźnić wyłączenie/usunięcie hasła aplikacji w usłudze Azure AD.
* Lokalne ustawienia Access Control klienta nie są uznawane przez funkcję haseł aplikacji.
* Funkcja rejestrowania/inspekcji lokalnego uwierzytelniania nie jest dostępna do użycia z funkcją haseł aplikacji.
* Niektóre zaawansowane architektury wymagają kombinacji poświadczeń do weryfikacji dwuetapowej z klientami programu. Te poświadczenia mogą zawierać nazwę użytkownika i hasła konta służbowego oraz hasła aplikacji. Wymagania są zależne od tego, jak uwierzytelnianie jest wykonywane. W przypadku klientów, którzy uwierzytelniają się w infrastrukturze lokalnej, wymagana jest nazwa użytkownika i hasło konta służbowego. W przypadku klientów, którzy uwierzytelniają się w usłudze Azure AD, wymagane jest hasło aplikacji.

  Załóżmy na przykład, że masz następującą architekturę:

  * Lokalne wystąpienie Active Directory jest federacyjne z usługą Azure AD.
  * Używasz usługi Exchange Online.
  * Korzystasz z lokalnego programu Skype dla firm.
  * Używasz usługi Azure Multi-Factor Authentication.

  W tym scenariuszu używane są następujące poświadczenia:

  * Aby zalogować się do usługi Skype dla firm, użyj nazwy użytkownika i hasła konta służbowego.
  * Aby uzyskać dostęp do książki adresowej z klienta programu Outlook, który nawiązuje połączenie z usługą Exchange Online, Użyj hasła aplikacji.

### <a name="allow-users-to-create-app-passwords"></a>Zezwól użytkownikom na tworzenie haseł aplikacji

Domyślnie użytkownicy nie mogą tworzyć haseł aplikacji. Funkcja haseł aplikacji musi być włączona. Aby umożliwić użytkownikom tworzenie haseł aplikacji, należy wykonać poniższą procedurę:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy**.
3. Wybierz **Multi-Factor Authentication**.
4. W obszarze Multi-Factor Authentication wybierz pozycję **Ustawienia usługi**.
5. Na stronie **Ustawienia usługi** wybierz opcję **zezwól użytkownikom na tworzenie haseł aplikacji do logowania się do aplikacji niekorzystających z przeglądarki** .

### <a name="create-app-passwords"></a>Tworzenie haseł aplikacji

Użytkownicy mogą tworzyć hasła aplikacji podczas ich wstępnej rejestracji. Użytkownik może utworzyć hasła aplikacji na końcu procesu rejestracji.

Użytkownicy mogą również tworzyć hasła aplikacji po rejestracji. Aby uzyskać więcej informacji i szczegółowe instrukcje dla użytkowników, zobacz [co to są hasła aplikacji w usłudze Azure Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>Zaufane adresy IP

Funkcja _Zaufane adresy IP_ w usłudze Azure Multi-Factor Authentication jest używana przez administratorów dzierżawy zarządzanej lub federacyjnej. Ta funkcja pomija weryfikację dwuetapową dla użytkowników logujących się z firmowej sieci intranet. Ta funkcja jest dostępna w pełnej wersji platformy Azure Multi-Factor Authentication, a nie w wersji bezpłatnej dla administratorów. Aby uzyskać szczegółowe informacje na temat sposobu uzyskania pełnej wersji usługi Azure Multi-Factor Authentication, zobacz [Azure Multi-Factor Authentication](multi-factor-authentication.md).

> [!NOTE]
> Zaufane adresy IP i dostęp warunkowy usługi MFA działają tylko z adresami IPV4.

Jeśli w organizacji wdrożono rozszerzenie serwera NPS w celu zapewnienia uwierzytelniania MFA w aplikacjach lokalnych, należy zauważyć, że źródłowy adres IP zawsze będzie serwerem NPS, za pomocą którego nastąpi próba uwierzytelnienia.

| Typ dzierżawy usługi Azure AD | Opcje funkcji Zaufane adresy IP |
|:--- |:--- |
| Zarządzani |**Określony zakres adresów IP**: Administratorzy określają zakres adresów IP, które mogą pomijać weryfikację dwuetapową dla użytkowników logujących się z firmowej sieci intranet. Można skonfigurować maksymalnie 50 zaufanych zakresów adresów IP.|
| Federacyjni |**Wszyscy użytkownicy zafederacyjnych**: Wszyscy użytkownicy federacyjne, którzy logują się z wewnątrz organizacji, mogą ominąć weryfikację dwuetapową. Użytkownicy pomijają weryfikację przy użyciu roszczeń wydawanych przez Active Directory Federation Services (AD FS).<br/>**Określony zakres adresów IP**: Administratorzy określają zakres adresów IP, które mogą pomijać weryfikację dwuetapową dla użytkowników logujących się z firmowej sieci intranet. |

Obejście zaufanych adresów IP działa tylko z wewnątrz intranetu firmy. W przypadku wybrania opcji **Wszyscy użytkownicy Federacji** , a użytkownik loguje się spoza firmowej sieci intranet, użytkownik musi uwierzytelnić się przy użyciu weryfikacji dwuetapowej. Ten proces jest taki sam, nawet jeśli użytkownik prezentuje AD FS. 

### <a name="end-user-experience-inside-of-corpnet"></a>Środowisko użytkownika końcowego w sieci firmowej

Po wyłączeniu funkcji Zaufane adresy IP w przypadku przepływów przeglądarki wymagana jest weryfikacja dwuetapowa. Hasła aplikacji są wymagane w przypadku starszych aplikacji klienckich.

Po włączeniu funkcji Zaufane adresy IP weryfikacja dwuetapowa *nie* jest wymagana w przypadku przepływów przeglądarki. Hasła aplikacji *nie* są wymagane w przypadku starszych aplikacji klienckich, pod warunkiem, że użytkownik nie utworzył hasła aplikacji. Gdy hasło aplikacji jest w użyciu, hasło pozostaje wymagane. 

### <a name="end-user-experience-outside-corpnet"></a>Środowisko użytkownika końcowego poza siecią Corpnet

Niezależnie od tego, czy funkcja zaufanych adresów IP jest włączona, dla przepływów przeglądarki wymagana jest weryfikacja dwuetapowa. Hasła aplikacji są wymagane w przypadku starszych aplikacji klienckich.

### <a name="enable-named-locations-by-using-conditional-access"></a>Włączanie nazwanych lokalizacji przy użyciu dostępu warunkowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Security** > **dostęp** > warunkowy zabezpieczenia**o nazwie lokalizacje**.
3. Wybierz pozycję **Nowa lokalizacja**.
4. Wprowadź nazwę lokalizacji.
5. Wybierz pozycję **Oznacz jako zaufaną lokalizację**.
6. Wprowadź zakres adresów IP w notacji CIDR, np. **192.168.1.1/24**.
7. Wybierz przycisk **Utwórz**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Włączanie funkcji Zaufane adresy IP przy użyciu dostępu warunkowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Security** >  **dostęp** > warunkowy zabezpieczenia**o nazwie lokalizacje**.
3. Wybierz pozycję **Konfiguruj Zaufane adresy IP usługi MFA**.
4. Na stronie **Ustawienia usługi** w obszarze **Zaufane adresy IP**wybierz jedną z następujących dwóch opcji:

   * **W przypadku żądań od użytkowników federacyjnych pochodzących z mojego intranetu**: aby wybrać tę opcję, zaznacz to pole wyboru. Wszyscy użytkownicy federacyjne, którzy logują się z sieci firmowej, pomijają weryfikację dwuetapową przy użyciu roszczeń wydawanych przez AD FS. Upewnij się, że AD FS ma regułę umożliwiającą dodanie do odpowiedniego ruchu w intranecie. Jeśli reguła nie istnieje, Utwórz następującą regułę w AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **W przypadku żądań z określonego zakresu publicznych adresów IP**: aby wybrać tę opcję, wprowadź adresy IP w polu tekstowym przy użyciu notacji CIDR.
      * W przypadku adresów IP, które znajdują się w zakresie od xxx. xxx. xxx. 1 do xxx. xxx. xxx. 254, użyj notacji, takiej jak **xxx. xxx. xxx. 0/24**.
      * Dla pojedynczego adresu IP należy użyć notacji, takiej jak **xxx.xxx.xxx.xxx/32**.
      * Wprowadź maksymalnie 50 zakresów adresów IP. Użytkownicy logujący się z tych adresów IP pomijają weryfikację dwuetapową.

5. Wybierz pozycję **Zapisz**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Włączanie funkcji Zaufane adresy IP przy użyciu ustawień usługi

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy**.
3. Wybierz **Multi-Factor Authentication**.
4. W obszarze Multi-Factor Authentication wybierz pozycję **Ustawienia usługi**.
5. Na stronie **Ustawienia usługi** w obszarze **Zaufane adresy IP**wybierz jedną z następujących opcji:

   * **W przypadku żądań od użytkowników federacyjnych w moim intranecie**: aby wybrać tę opcję, zaznacz pole wyboru. Wszyscy użytkownicy federacyjne, którzy logują się z sieci firmowej, pomijają weryfikację dwuetapową przy użyciu roszczeń wydawanych przez AD FS. Upewnij się, że AD FS ma regułę umożliwiającą dodanie do odpowiedniego ruchu w intranecie. Jeśli reguła nie istnieje, Utwórz następującą regułę w AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **W przypadku żądań z określonego zakresu podsieci adresów IP**: aby wybrać tę opcję, wprowadź adresy IP w polu tekstowym przy użyciu notacji CIDR.
      * W przypadku adresów IP, które znajdują się w zakresie od xxx. xxx. xxx. 1 do xxx. xxx. xxx. 254, użyj notacji, takiej jak **xxx. xxx. xxx. 0/24**.
      * Dla pojedynczego adresu IP należy użyć notacji, takiej jak **xxx.xxx.xxx.xxx/32**.
      * Wprowadź maksymalnie 50 zakresów adresów IP. Użytkownicy logujący się z tych adresów IP pomijają weryfikację dwuetapową.

6. Wybierz pozycję **Zapisz**.

## <a name="verification-methods"></a>Metody weryfikacji

Możesz wybrać metody weryfikacji, które są dostępne dla użytkowników. Poniższa tabela zawiera krótkie omówienie metod.

Gdy użytkownicy rejestrują swoje konta dla usługi Azure Multi-Factor Authentication, wybierają ich preferowaną metodę weryfikacji z opcji, które zostały włączone. Wskazówki dotyczące procesu rejestracji użytkownika znajdują się w [sekcji Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-first-time.md).

| Metoda | Opis |
|:--- |:--- |
| Wywołanie telefonu |Umieszcza automatyczne połączenie głosowe. Użytkownik odbiera połączenie i naciska klawisz # na klawiaturze telefonu w celu uwierzytelnienia. Numer telefonu nie jest zsynchronizowany z Active Directoryami lokalnymi. |
| Wiadomość SMS na telefon |Wysyła wiadomość tekstową zawierającą kod weryfikacyjny. Użytkownik otrzymuje monit o wprowadzenie kodu weryfikacyjnego w interfejsie logowania. Ten proces jest nazywany jednokierunkową wiadomością SMS. Dwukierunkowa wiadomość SMS oznacza, że użytkownik musi wykonać tekst ponownie w określonym kodzie. Dwukierunkowa wiadomość SMS jest przestarzała i nie jest obsługiwana po 14 listopada 2018. Administratorzy powinni włączyć kolejną metodę dla użytkowników, którzy wcześniej korzystali z dwukierunkowej wiadomości SMS.|
| Powiadomienie za poorednictwem aplikacji mobilnej |Wysyła powiadomienie wypychane na telefon lub zarejestrowane urządzenie. Użytkownik wyświetli powiadomienie i wybierze opcję **Weryfikuj** , aby ukończyć weryfikację. Aplikacja Microsoft Authenticator jest dostępna dla [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)i [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Kod weryfikacyjny z aplikacji mobilnej lub tokenu sprzętowego |Aplikacja Microsoft Authenticator generuje nowy kod weryfikacyjny OATH co 30 sekund. Użytkownik wprowadza kod weryfikacyjny do interfejsu logowania. Aplikacja Microsoft Authenticator jest dostępna dla [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)i [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Włączanie i wyłączanie metod weryfikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy**.
3. Wybierz **Multi-Factor Authentication**.
4. W obszarze Multi-Factor Authentication wybierz pozycję **Ustawienia usługi**.
5. Na stronie **Ustawienia usługi** w obszarze **Opcje weryfikacji**wybierz/Usuń zaznaczenie metod, które mają być dostępne dla użytkowników.
6. Kliknij przycisk **Zapisz**.

Dodatkowe szczegóły dotyczące korzystania z metod uwierzytelniania można znaleźć w artykule [co to są metody uwierzytelniania](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Pamiętaj Multi-Factor Authentication

Funkcja _pamiętaj Multi-Factor Authentication_ dla urządzeń i przeglądarek, które są zaufane przez użytkownika, to bezpłatna funkcja dla wszystkich Multi-Factor Authentication użytkowników. Użytkownicy mogą ominąć kolejne weryfikacje przez określoną liczbę dni po pomyślnym zalogowaniu się na urządzeniu przy użyciu usługi Multi-Factor Authentication. Ta funkcja zwiększa użyteczność przez zminimalizowanie liczby prób przeprowadzenia weryfikacji dwuetapowej na tym samym urządzeniu.

>[!IMPORTANT]
>W przypadku naruszenia zabezpieczeń konta lub urządzenia zapamiętanie Multi-Factor Authentication dla zaufanych urządzeń może wpłynąć na bezpieczeństwo. Jeśli konto firmowe zostanie złamane lub zostanie utracone lub skradzione, należy [odwołać sesje MFA](howto-mfa-userdevicesettings.md).
>
>Akcja Przywróć odwołuje stan zaufany ze wszystkich urządzeń, a użytkownik musi wykonać weryfikację dwuetapową ponownie. Możesz również poinstruować użytkowników, aby mogli przywrócić Multi-Factor Authentication na swoich urządzeniach, korzystając z instrukcji w sekcji [Zarządzanie ustawieniami weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

### <a name="how-the-feature-works"></a>Jak działa funkcja

Funkcja Pamiętaj Multi-Factor Authentication ustawia trwały plik cookie w przeglądarce, gdy użytkownik wybierze opcję **nie Monituj ponownie dla X dni** podczas logowania. Użytkownik nie będzie ponownie monitowany o Multi-Factor Authentication z tej samej przeglądarki, dopóki plik cookie nie wygaśnie. Jeśli użytkownik otworzy inną przeglądarkę na tym samym urządzeniu lub wyczyści pliki cookie, zostanie ponownie wyświetlony monit o zweryfikowanie.

Opcja **nie Monituj ponownie dla X dni** nie jest wyświetlana w aplikacjach bez przeglądarki, niezależnie od tego, czy aplikacja obsługuje nowoczesne uwierzytelnianie. Te aplikacje używają _tokenów odświeżania_ , które udostępniają nowe tokeny dostępu co godzinę. Po sprawdzeniu poprawności tokenu odświeżania usługa Azure AD sprawdza, czy Ostatnia weryfikacja dwuetapowa wystąpiła w ciągu określonej liczby dni.

Funkcja zmniejsza liczbę uwierzytelnień w aplikacjach sieci Web, które zwykle monitują się za każdym razem. Ta funkcja zwiększa liczbę uwierzytelnień dla nowoczesnych klientów uwierzytelniania, które zwykle monitują co 90 dni. Może również zwiększyć liczbę uwierzytelnień w połączeniu z zasadami dostępu warunkowego.

>[!IMPORTANT]
>Funkcja **pamiętaj Multi-Factor Authentication** nie jest zgodna z funkcją **Keep ja in in** AD FS, gdy użytkownicy wykonują weryfikację dwuetapową dla AD FS za pomocą usługi Azure serwer Multi-Factor Authentication lub rozwiązania do uwierzytelniania wieloskładnikowego innej firmy.
>
>Jeśli użytkownik wybierze opcję Nie wylogowuj **mnie** na AD FS, a także oznaczy urządzenie jako zaufane dla Multi-Factor Authentication, użytkownik nie zostanie automatycznie zweryfikowany po upływie następującej liczby dni dla opcji **Zapamiętaj uwierzytelnianie wieloskładnikowe** . Usługa Azure AD żąda nowej weryfikacji dwuetapowej, ale AD FS zwraca token z pierwotnym Multi-Factor Authenticationm i datą, a nie ponownie wykonuje weryfikacji dwuetapowej. **Ta reakcja ustawia pętlę weryfikacji między usługą Azure AD a AD FS.**
>
>Funkcja **pamiętaj Multi-Factor Authentication** nie jest zgodna z użytkownikami B2B i nie będzie widoczna dla użytkowników B2B podczas logowania się do zaproszonych dzierżawców.
>

### <a name="enable-remember-multi-factor-authentication"></a>Włącz Multi-Factor Authentication Pamiętaj

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Użytkownicy**.
3. Wybierz **Multi-Factor Authentication**.
4. W obszarze Multi-Factor Authentication wybierz pozycję **Ustawienia usługi**.
5. Na stronie **Ustawienia usługi** **Zarządzaj Zapamiętaj uwierzytelnianie wieloskładnikowe**, wybierz opcję **Zezwalaj użytkownikom na zapamiętywanie uwierzytelniania wieloskładnikowego na urządzeniach, które ufają** .
6. Ustaw liczbę dni, przez które zaufane urządzenia mogą pomijać weryfikację dwuetapową. Wartość domyślna to 14 dni.
7. Wybierz pozycję **Zapisz**.

### <a name="mark-a-device-as-trusted"></a>Oznacz urządzenie jako zaufane

Po włączeniu funkcji Pamiętaj Multi-Factor Authentication użytkownicy mogą oznaczyć urządzenie jako zaufane podczas logowania, wybierając pozycję **nie Monituj ponownie**.

## <a name="next-steps"></a>Następne kroki

[Modyfikuj znakowanie strony logowania usługi Azure AD](../fundamentals/customize-branding.md)
