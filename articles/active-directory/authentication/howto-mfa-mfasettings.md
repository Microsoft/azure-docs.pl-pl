---
title: Konfigurowanie usługi Azure AD Multi-Factor Authentication — Azure Active Directory
description: Dowiedz się, jak skonfigurować ustawienia Multi-Factor Authentication usługi Azure AD w Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 4625b0a750c2b3ff63879bb9ea306bc69b1bb64e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471645"
---
# <a name="configure-azure-ad-multi-factor-authentication-settings"></a>Konfigurowanie ustawień Multi-Factor Authentication usługi Azure AD

Aby dostosować środowisko użytkownika końcowego Multi-Factor Authentication usługi Azure AD, można skonfigurować opcje dla ustawień takich jak progi blokady konta lub alerty oszustwa i powiadomienia. Niektóre ustawienia są bezpośrednio w Azure Portal dla Azure Active Directory (Azure AD), a niektóre w osobnym portalu usługi Azure AD Multi-Factor Authentication.

Poniższe ustawienia Multi-Factor Authentication usługi Azure AD są dostępne w Azure Portal:

| Cecha | Opis |
| ------- | ----------- |
| [Blokada konta](#account-lockout) | Tymczasowo Blokuj konta z używania usługi Azure AD Multi-Factor Authentication, jeśli w wierszu występuje zbyt wiele nieudanych prób uwierzytelnienia. Ta funkcja ma zastosowanie tylko do użytkowników, którzy wprowadzają kod PIN do uwierzytelnienia. (Serwer MFA) |
| [Blokuj/Odblokuj użytkowników](#block-and-unblock-users) | Zablokuj określonym użytkownikom możliwość otrzymywania żądań Multi-Factor Authentication usługi Azure AD. Wszystkie próby uwierzytelnienia dla zablokowanych użytkowników są automatycznie odrzucane. Użytkownicy pozostają Zablokowani przez 90 dni od momentu ich zablokowania lub ręcznie odblokowany. |
| [Alert dotyczący wykrycia oszustwa](#fraud-alert) | Skonfiguruj ustawienia, które umożliwiają użytkownikom zgłaszanie fałszywych żądań weryfikacji. |
| [Powiadomienia](#notifications) | Włącz powiadomienia zdarzeń z serwera MFA. |
| [Tokeny OATH](concept-authentication-oath-tokens.md) | Używany w środowiskach usługi Azure AD MFA opartych na chmurze do zarządzania tokenami OATH dla użytkowników. |
| [Ustawienia połączenia telefonicznego](#phone-call-settings) | Konfigurowanie ustawień związanych z połączeniami telefonicznymi i powitami w środowiskach chmurowych i lokalnych. |
| Dostawcy | Spowoduje to wyświetlenie wszystkich istniejących dostawców uwierzytelniania, które mogły zostać skojarzone z Twoim kontem. Nie można utworzyć nowych dostawców uwierzytelniania od 1 września 2018 |

![Azure Portal — ustawienia Multi-Factor Authentication usługi Azure AD](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Blokada konta

Aby zapobiec powtarzanym próbom MFA w ramach ataku, ustawienia blokady konta pozwalają określić liczbę nieudanych prób zezwolenia, zanim konto zostanie zablokowane w danym okresie czasu. Ustawienia blokady konta są stosowane tylko wtedy, gdy w wierszu polecenia MFA wprowadzono kod PIN.

Dostępne są następujące ustawienia:

* Liczba Odmów MFA do wyzwolenia blokady konta
* Liczba minut, po których licznik blokady konta zostanie zresetowany
* Liczba minut, po których konto zostanie automatycznie odblokowane

Aby skonfigurować ustawienia blokady konta, wykonaj następujące ustawienia:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
1. Przejdź do **Azure Active Directory**  >    >    >  **Blokada konta** usługi MFA.
1. Wprowadź wartości Wymagaj dla danego środowiska, a następnie wybierz pozycję **Zapisz**.

    ![Zrzut ekranu ustawień blokady konta w Azure Portal](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Blokowanie i odblokowywanie użytkowników

Jeśli urządzenie użytkownika zostało zgubione lub skradzione, można zablokować usługi Azure AD Multi-Factor Authentication prób dla skojarzonego konta. Wszystkie próby Multi-Factor Authentication usługi Azure AD dla zablokowanych użytkowników są automatycznie odrzucane. Użytkownicy pozostają zablokowani przez 90 dni od momentu zablokowania. Opublikowano film wideo dotyczący [sposobu blokowania i odblokowywania użytkowników w Twojej dzierżawie](https://www.youtube.com/watch?v=WdeE1On4S1o) , aby pokazać, jak to zrobić.

### <a name="block-a-user"></a>Blokuj użytkownika

Aby zablokować użytkownika, wykonaj następujące czynności:

1. Przejdź do usługi **Azure Active Directory**  >  **Security**  >  **MFA**  >  **Blokuj/Odblokuj użytkowników**.
1. Wybierz pozycję **Dodaj** , aby zablokować użytkownika.
1. Wprowadź nazwę użytkownika dla zablokowanego użytkownika jako `username@domain.com` , a następnie podaj komentarz w polu *Przyczyna* .
1. Gdy wszystko będzie gotowe, wybierz **przycisk OK** , aby zablokować użytkownika.

### <a name="unblock-a-user"></a>Odblokuj użytkownika

Aby odblokować użytkownika, wykonaj następujące czynności:

1. Przejdź do usługi **Azure Active Directory**  >  **Security**  >  **MFA**  >  **Blokuj/Odblokuj użytkowników**.
1. W kolumnie *Akcja* obok żądanego użytkownika wybierz opcję **Odblokuj**.
1. Wprowadź komentarz w *przyczynie odblokowania* pola.
1. Gdy wszystko będzie gotowe, wybierz **przycisk OK** , aby odblokować użytkownika.

## <a name="fraud-alert"></a>Alert dotyczący wykrycia oszustwa

Funkcja alertu oszustwa umożliwia użytkownikom zgłaszanie fałszywych prób uzyskania dostępu do zasobów. Po otrzymaniu nieznanego i podejrzanego monitu MFA użytkownicy mogą zgłosić próbę oszustwa przy użyciu aplikacji Microsoft Authenticator lub za pośrednictwem telefonu.

Dostępne są następujące opcje konfiguracji alertów oszustw:

* **Automatycznie blokuj użytkowników, którzy zgłaszają oszustwo**: w przypadku zgłaszania oszustw przez użytkownika próby uwierzytelnienia usługi Azure AD MFA dla konta użytkownika są blokowane przez 90 dni lub do momentu odblokowania konta przez administratora. Administrator może przejrzeć logowania przy użyciu raportu logowania i podjąć odpowiednie działania, aby zapobiec przyszłym oszustwom. Administrator może następnie [odblokować](#unblock-a-user) konto użytkownika.
* **Kod służący do zgłaszania oszustw podczas początkowego powitania**: gdy użytkownicy otrzymają połączenie telefoniczne w celu przeprowadzenia uwierzytelniania wieloskładnikowego, zazwyczaj naciskają **#** się w celu potwierdzenia ich zalogowania. Aby zgłosić oszustwo, użytkownik wprowadza kod przed naciśnięciem klawisza **#** . Ten kod jest domyślnie **0** , ale można go dostosować.

   > [!NOTE]
   > Domyślne powitanie głosu firmy Microsoft instruuje użytkowników, aby nacisnąć **0 #** w celu przesłania alertu oszustwa. Jeśli chcesz użyć kodu innego niż **0**, zarejestruj i przekaż własne niestandardowe pozdrowienia głosowe z odpowiednimi instrukcjami dla użytkowników.

Aby włączyć i skonfigurować alerty oszustwa, wykonaj następujące czynności:

1. Przejdź do   >  alertu dotyczącego oszustwa Azure Active Directory **Security**  >  **MFA**  >  .
1. Dla opcji *Zezwalaj użytkownikom na przesyłanie alertów oszustw* ustaw wartość **włączone**.
1. Skonfiguruj *Automatyczne blokowanie użytkowników, którzy zgłaszają oszustwo* lub *kod w celu zgłaszania oszustw podczas początkowego ustawienia powitania* zgodnie z potrzebami.
1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

### <a name="view-fraud-reports"></a>Wyświetlanie raportów o oszustwie

Wybierz pozycję **Azure Active Directory**  >    >  **szczegóły uwierzytelniania** logowania. Raport oszustwa jest teraz częścią standardowego raportu logowania do usługi Azure AD, który będzie wyświetlany w **"szczegółowe dane wynikowe"** jako odmowa MFA, wprowadzony kod oszustwa.
 
## <a name="notifications"></a>Powiadomienia

Powiadomienia e-mail można skonfigurować, gdy użytkownicy zgłaszają alerty oszustwa. Te powiadomienia są zwykle wysyłane do administratorów tożsamości, ponieważ mogą zostać naruszone poświadczenia konta użytkownika. W poniższym przykładzie przedstawiono sposób, w jaki wygląda wiadomość e-mail z powiadomieniem o alercie oszustwa:

![Przykładowa wiadomość e-mail z powiadomieniem o alercie oszustwa](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

Aby skonfigurować powiadomienia o alertach dotyczących oszustw, należy wykonać następujące czynności:

1. Przejdź do **Azure Active Directory**  >    >    >  **powiadomień** Multi-Factor Authentication zabezpieczeń.
1. Wprowadź adres e-mail, który ma zostać dodany do następnego pola.
1. Aby usunąć istniejący adres e-mail, wybierz opcję **...** obok żądanego adresu e-mail, a następnie wybierz pozycję **Usuń**.
1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

## <a name="oath-tokens"></a>Tokeny OATH

Usługa Azure AD obsługuje użycie tokenów TOTP SHA-1, które odświeżają kody co 30 lub 60 sekund. Klienci mogą zakupić te tokeny od wybranego przez siebie dostawcy.

Tokeny sprzętowe TOTP OATH zazwyczaj pochodzą z kluczem tajnym lub inicjatorem, który jest wstępnie zaprogramowany w tokenie. Te klucze muszą być danymi wejściowymi do usługi Azure AD, zgodnie z opisem w poniższych krokach. Klucze tajne są ograniczone do 128 znaków, które mogą nie być zgodne ze wszystkimi tokenami. Klucz tajny może zawierać tylko znaki *a-z* lub *a-z* i cyfry *1-7*, a także musi być zakodowany w *Base32*.

Programowalne tokeny sprzętowe TOTP OATH można również skonfigurować za pomocą usługi Azure AD w przepływie instalacji tokenu oprogramowania.

Tokeny sprzętowe OATH są obsługiwane w ramach publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure

![Przekazywanie tokenów OATH do bloku tokenów OATH usługi MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Po uzyskaniu tokenów należy je przekazać w formacie pliku wartości rozdzielanych przecinkami (CSV), w tym nazwy UPN, numeru seryjnego, klucza tajnego, interwału czasu, producenta i modelu, jak pokazano w następującym przykładzie:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Upewnij się, że dołączysz wiersz nagłówka do pliku CSV.

Po poprawnym sformatowaniu pliku CSV administrator może następnie zalogować się do Azure Portal, przejść do **Azure Active Directory > zabezpieczenia > MFA > tokeny Oath** i przekazać plik CSV.

Proces może potrwać kilka minut, w zależności od rozmiaru pliku CSV. Wybierz przycisk **Odśwież** , aby uzyskać bieżący stan. W przypadku wystąpienia błędów w pliku można pobrać plik CSV, który zawiera listę błędów, które należy rozwiązać. Nazwy pól w pobranym pliku CSV różnią się od przekazanej wersji.

Po rozwiązaniu jakichkolwiek błędów administrator może aktywować każdy klucz, wybierając pozycję **Aktywuj** dla tokenu i wprowadzając uwierzytelnianie OTP wyświetlane na tokenie.

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji uwierzytelniania, takich jak Microsoft Authenticator aplikacji skonfigurowanych do użycia w dowolnym momencie.

## <a name="phone-call-settings"></a>Ustawienia połączenia telefonicznego

Jeśli użytkownicy odbierają połączenia telefoniczne z instrukcjami uwierzytelniania wieloskładnikowego, można skonfigurować ich środowisko, takie jak identyfikator wywołującego lub powitanie głosu.

W Stany Zjednoczone, jeśli nie skonfigurowano identyfikatora obiektu wywołującego usługi MFA, połączenia głosowe od firmy Microsoft pochodzą od następujących liczb. W przypadku używania filtrów spamu upewnij się, że te numery zostały wykluczone:

* *+ 1 (866) 539 4191*
* *+ 1 (855) 330 8653*
* *+ 1 (877) 668 6536*

> [!NOTE]
> Gdy wywołania usługi Azure AD Multi-Factor Authentication są umieszczane za pośrednictwem publicznej sieci telefonicznej, czasami wywołania są kierowane przez operatora, który nie obsługuje identyfikatora rozmówcy. W związku z tym identyfikator wywołującego nie jest gwarantowany, mimo że usługa Azure AD Multi-Factor Authentication zawsze ją wysyła. Dotyczy to zarówno połączeń telefonicznych, jak i komunikatów tekstowych udostępnianych przez usługę Azure AD Multi-Factor Authentication. Jeśli musisz sprawdzić, czy wiadomość tekstowa pochodzi z usługi Azure AD Multi-Factor Authentication, zobacz, [jakie krótkie kody programu SMS są używane do wysyłania wiadomości?](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

Aby skonfigurować własny numer ID procesu wywołującego, wykonaj następujące czynności:

1. Przejdź do **Azure Active Directory**  >    >    >  **ustawień połączenia telefonicznego** usługi MFA.
1. Ustaw **numer identyfikatora rozmówcy usługi MFA** na numer, który użytkownicy będą widzieć na telefonie. Dozwolone są tylko numery w Stanach Zjednoczonych.
1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

### <a name="custom-voice-messages"></a>Niestandardowe wiadomości głosowe

Możesz użyć własnych nagrań lub powitania w usłudze Azure AD Multi-Factor Authentication za pomocą funkcji niestandardowych wiadomości głosowych. Te komunikaty mogą służyć jako uzupełnienie lub zastępowanie domyślnych nagrań firmy Microsoft.

Przed rozpoczęciem należy pamiętać o następujących ograniczeniach:

* Obsługiwane formaty plików to *WAV* i *MP3*.
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

### <a name="custom-voice-message-defaults"></a>Niestandardowe wartości domyślne wiadomości głosowych

Następujące przykładowe skrypty mogą służyć do tworzenia własnych niestandardowych komunikatów. Te wyrażenia są wartościami domyślnymi, jeśli nie skonfigurowano własnych niestandardowych komunikatów:

| Nazwa komunikatu | Skrypt |
| --- | --- |
| Uwierzytelnianie powiodło się | Logowanie zostało pomyślnie zweryfikowane. Koniec. |
| Monit o rozszerzenie | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz krzyżyk, aby kontynuować. |
| Potwierdzenie oszustwa | Przesłano alert oszustwa. Aby odblokować konto, skontaktuj się z działem pomocy technicznej IT w Twojej firmie. |
| Pozdrowienie oszustwa (standard) | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. Jeśli weryfikacja nie została zainicjowana, ktoś może próbować uzyskać dostęp do Twojego konta. Naciśnij zero funta, aby przesłać alert oszustwa. Spowoduje to powiadomienie zespołu IT firmy i zablokowanie dalszych prób weryfikacji. |
| Zgłoszono oszustwo informujące o przesłaniu alertu oszustwa. | Aby odblokować konto, skontaktuj się z działem pomocy technicznej IT w Twojej firmie. |
| Uaktywnienie | Dziękujemy za skorzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij krzyżyk, aby zakończyć weryfikację. |
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

### <a name="set-up-a-custom-message"></a>Konfigurowanie wiadomości niestandardowej

Aby użyć własnych wiadomości niestandardowych, wykonaj następujące czynności:

1. Przejdź do **Azure Active Directory**  >    >    >  **ustawień połączenia telefonicznego** usługi MFA.
1. Wybierz pozycję **Dodaj powitanie**.
1. Wybierz **Typ** powitania, takie jak *Greeting (standard)* lub  *uwierzytelnianie pomyślne*.
1. Wybierz **Język** na podstawie poprzedniej sekcji w temacie [niestandardowe zachowanie języka komunikatów](#custom-message-language-behavior).
1. Wyszukaj i wybierz plik dźwiękowy *MP3* lub *WAV* , który ma zostać przekazany.
1. Gdy wszystko będzie gotowe, wybierz pozycję **Dodaj**, a następnie **Zapisz**.

## <a name="mfa-service-settings"></a>Ustawienia usługi MFA

Ustawienia dotyczące haseł aplikacji, zaufanych adresów IP, opcji weryfikacji i zapamiętania uwierzytelniania wieloskładnikowego w usłudze Azure AD Multi-Factor Authentication można znaleźć w ustawieniach usługi. Jest to większa część starszego portalu i nie jest częścią zwykłego portalu usługi Azure AD.

Ustawienia usługi są dostępne z poziomu Azure Portal, przechodząc do **Azure Active Directory**  >  **zabezpieczenia**  >  **MFA**  >  **wprowadzenie**  >  **Konfigurowanie**  >  **dodatkowych ustawień usługi MFA opartych na chmurze**. Zostanie otwarte nowe okno lub karta z dodatkowymi opcjami *ustawień usługi* .

## <a name="trusted-ips"></a>Zaufane adresy IP

Funkcja _Zaufane adresy IP_ w usłudze Azure AD Multi-Factor Authentication pomija w przypadku użytkowników logujących się ze zdefiniowanego zakresu adresów IP. Możesz ustawić zaufane zakresy adresów IP dla środowisk lokalnych, gdy użytkownicy znajdują się w jednej z tych lokalizacji, nie ma monitu o Multi-Factor Authentication usługi Azure AD.

> [!NOTE]
> W przypadku korzystania z serwera MFA Zaufane adresy IP mogą zawierać prywatne zakresy adresów IPv4. W przypadku usługi Azure AD Multi-Factor Authentication opartej na chmurze można używać tylko zakresów publicznych adresów IP.
>
> Zakresy adresów IPv6 są obsługiwane tylko w interfejsie [nazwanych lokalizacji (wersja zapoznawcza)](../conditional-access/location-condition.md#preview-features) .

Jeśli w organizacji wdrożono rozszerzenie serwera NPS w celu zapewnienia uwierzytelniania MFA w aplikacjach lokalnych, należy zauważyć, że źródłowy adres IP zawsze będzie serwerem NPS, za pomocą którego nastąpi próba uwierzytelnienia.

| Typ dzierżawy usługi Azure AD | Opcje funkcji zaufanego adresu IP |
|:--- |:--- |
| Zarządzanie |**Określony zakres adresów IP**: Administratorzy określają zakres adresów IP, które mogą ominąć uwierzytelnianie wieloskładnikowe dla użytkowników logujących się z firmowej sieci intranet. Można skonfigurować maksymalnie 50 zaufanych zakresów adresów IP.|
| Federacyjni |**Wszyscy użytkownicy federacyjne**: Wszyscy użytkownicy zarejestrowani z wewnątrz organizacji mogą ominąć uwierzytelnianie wieloskładnikowe. Użytkownicy pomijają weryfikację przy użyciu roszczeń wydawanych przez Active Directory Federation Services (AD FS).<br/>**Określony zakres adresów IP**: Administratorzy określają zakres adresów IP, które mogą ominąć uwierzytelnianie wieloskładnikowe dla użytkowników logujących się z firmowej sieci intranet. |

Obejście zaufanego adresu IP działa tylko w intranecie firmy. W przypadku wybrania opcji **Wszyscy użytkownicy Federacji** , a użytkownik loguje się spoza firmowej sieci intranet, użytkownik musi uwierzytelnić się przy użyciu uwierzytelniania wieloskładnikowego. Ten proces jest taki sam, nawet jeśli użytkownik prezentuje AD FS.

### <a name="end-user-experience-inside-of-corpnet"></a>Środowisko użytkownika końcowego w sieci firmowej

Gdy funkcja Zaufane adresy IP jest wyłączona, dla przepływów przeglądarki wymagane jest uwierzytelnianie wieloskładnikowe. Hasła aplikacji są wymagane w przypadku starszych aplikacji klienckich.

W przypadku używania zaufanych adresów IP uwierzytelnianie wieloskładnikowe nie jest wymagane w przypadku przepływów przeglądarki. Hasła aplikacji nie są wymagane w przypadku starszych aplikacji klienckich, pod warunkiem, że użytkownik nie utworzył hasła aplikacji. Gdy hasło aplikacji jest w użyciu, hasło pozostaje wymagane.

### <a name="end-user-experience-outside-corpnet"></a>Środowisko użytkownika końcowego poza siecią Corpnet

Niezależnie od tego, czy jest zdefiniowany zaufany adres IP, dla przepływów przeglądarki wymagane jest uwierzytelnianie wieloskładnikowe. Hasła aplikacji są wymagane w przypadku starszych aplikacji klienckich.

### <a name="enable-named-locations-by-using-conditional-access"></a>Włączanie nazwanych lokalizacji przy użyciu dostępu warunkowego

Za pomocą reguł dostępu warunkowego można definiować nazwane lokalizacje, wykonując następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie przejdź do lokalizacji **zabezpieczenia**  >  **dostęp warunkowy**  >  **o nazwie lokalizacje**.
1. Wybierz pozycję **Nowa lokalizacja**.
1. Wprowadź nazwę lokalizacji.
1. Wybierz pozycję **Oznacz jako zaufaną lokalizację**.
1. Wprowadź zakres adresów IP w notacji CIDR dla danego środowiska, na przykład *40.77.182.32/27*.
1. Wybierz przycisk **Utwórz**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Włączanie funkcji Zaufane adresy IP przy użyciu dostępu warunkowego

Aby włączyć Zaufane adresy IP przy użyciu zasad dostępu warunkowego, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie przejdź do lokalizacji **zabezpieczenia**  >   **dostęp warunkowy**  >  **o nazwie lokalizacje**.
1. Wybierz pozycję **Konfiguruj Zaufane adresy IP usługi MFA**.
1. Na stronie **Ustawienia usługi** w obszarze **Zaufane adresy IP** wybierz jedną z następujących dwóch opcji:

   * **W przypadku żądań od użytkowników federacyjnych pochodzących z mojego intranetu**: aby wybrać tę opcję, zaznacz to pole wyboru. Wszyscy użytkownicy federacyjne, którzy logują się z sieci firmowej, pomijają uwierzytelnianie wieloskładnikowe przy użyciu roszczeń wystawionych przez AD FS. Upewnij się, że AD FS ma regułę umożliwiającą dodanie do odpowiedniego ruchu w intranecie. Jeśli reguła nie istnieje, Utwórz następującą regułę w AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **W przypadku żądań z określonego zakresu publicznych adresów IP**: aby wybrać tę opcję, wprowadź adresy IP w polu tekstowym przy użyciu notacji CIDR.
      * W przypadku adresów IP, które znajdują się w zakresie od xxx. xxx. xxx. 1 do xxx. xxx. xxx. 254, użyj notacji, takiej jak **xxx. xxx. xxx. 0/24**.
      * Dla pojedynczego adresu IP należy użyć notacji, takiej jak **xxx.xxx.xxx.xxx/32**.
      * Wprowadź maksymalnie 50 zakresów adresów IP. Użytkownicy logujący się z tych adresów IP pomijają uwierzytelnianie wieloskładnikowe.

1. Wybierz pozycję **Zapisz**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Włączanie funkcji Zaufane adresy IP przy użyciu ustawień usługi

Jeśli nie chcesz używać zasad dostępu warunkowego do włączania zaufanych adresów IP, możesz skonfigurować *Ustawienia usługi* Azure AD Multi-Factor Authentication, wykonując następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Użytkownicy**.
1. Wybierz pozycję **Multi-Factor Authentication**.
1. W obszarze Multi-Factor Authentication wybierz pozycję **Ustawienia usługi**.
1. Na stronie **Ustawienia usługi** w obszarze **Zaufane adresy IP** wybierz jedną z następujących opcji:

   * **W przypadku żądań od użytkowników federacyjnych w moim intranecie**: aby wybrać tę opcję, zaznacz pole wyboru. Wszyscy użytkownicy federacyjne, którzy logują się z sieci firmowej, pomijają uwierzytelnianie wieloskładnikowe przy użyciu roszczeń wystawionych przez AD FS. Upewnij się, że AD FS ma regułę umożliwiającą dodanie do odpowiedniego ruchu w intranecie. Jeśli reguła nie istnieje, Utwórz następującą regułę w AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **W przypadku żądań z określonego zakresu podsieci adresów IP**: aby wybrać tę opcję, wprowadź adresy IP w polu tekstowym przy użyciu notacji CIDR.
      * W przypadku adresów IP, które znajdują się w zakresie od xxx. xxx. xxx. 1 do xxx. xxx. xxx. 254, użyj notacji, takiej jak **xxx. xxx. xxx. 0/24**.
      * Dla pojedynczego adresu IP należy użyć notacji, takiej jak **xxx.xxx.xxx.xxx/32**.
      * Wprowadź maksymalnie 50 zakresów adresów IP. Użytkownicy logujący się z tych adresów IP pomijają uwierzytelnianie wieloskładnikowe.

1. Wybierz pozycję **Zapisz**.

## <a name="verification-methods"></a>Metody weryfikacji

Możesz wybrać metody weryfikacji, które są dostępne dla użytkowników w portalu ustawień usługi. Gdy użytkownicy rejestrują swoje konta dla Multi-Factor Authentication usługi Azure AD, wybierają preferowaną metodę weryfikacji z opcji, które zostały włączone. Wskazówki dotyczące procesu rejestracji użytkownika znajdują się w [sekcji Konfigurowanie mojego konta do uwierzytelniania wieloskładnikowego](../user-help/multi-factor-authentication-end-user-first-time.md).

Dostępne są następujące metody weryfikacji:

| Metoda | Opis |
|:--- |:--- |
| Wywołanie telefonu |Umieszcza automatyczne połączenie głosowe. Użytkownik odbiera połączenie i naciska klawisz # na klawiaturze telefonu w celu uwierzytelnienia. Numer telefonu nie jest zsynchronizowany z Active Directoryami lokalnymi. |
| Wiadomość SMS na telefon |Wysyła wiadomość tekstową zawierającą kod weryfikacyjny. Użytkownik otrzymuje monit o wprowadzenie kodu weryfikacyjnego w interfejsie logowania. Ten proces jest nazywany jednokierunkową wiadomością SMS. Dwukierunkowa wiadomość SMS oznacza, że użytkownik musi wykonać tekst ponownie w określonym kodzie. Dwukierunkowa wiadomość SMS jest przestarzała i nie jest obsługiwana po 14 listopada 2018. Administratorzy powinni włączyć kolejną metodę dla użytkowników, którzy wcześniej korzystali z dwukierunkowej wiadomości SMS.|
| Powiadomienie za poorednictwem aplikacji mobilnej |Wysyła powiadomienie wypychane na telefon lub zarejestrowane urządzenie. Użytkownik wyświetli powiadomienie i wybierze opcję **Weryfikuj** , aby ukończyć weryfikację. Aplikacja Microsoft Authenticator jest dostępna dla [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)i [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Kod weryfikacyjny z aplikacji mobilnej lub tokenu sprzętowego |Aplikacja Microsoft Authenticator generuje nowy kod weryfikacyjny OATH co 30 sekund. Użytkownik wprowadza kod weryfikacyjny do interfejsu logowania. Aplikacja Microsoft Authenticator jest dostępna dla [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)i [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

Aby uzyskać więcej informacji, zobacz [jakie metody uwierzytelniania i weryfikacji są dostępne w usłudze Azure AD?](concept-authentication-methods.md)

### <a name="enable-and-disable-verification-methods"></a>Włączanie i wyłączanie metod weryfikacji

Aby włączyć lub wyłączyć metody weryfikacyjne, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Użytkownicy**.
1. Wybierz pozycję **Multi-Factor Authentication**.
1. W obszarze Multi-Factor Authentication wybierz pozycję **Ustawienia usługi**.
1. Na stronie **Ustawienia usługi** w obszarze **Opcje weryfikacji** wybierz/Usuń zaznaczenie metod, które mają być dostępne dla użytkowników.
1. Kliknij pozycję **Zapisz**.

## <a name="remember-multi-factor-authentication"></a>Pamiętaj Multi-Factor Authentication

Funkcja _pamiętaj Multi-Factor Authentication_ umożliwia użytkownikom ominięcie kolejnych weryfikacji przez określoną liczbę dni po pomyślnym zalogowaniu się na urządzeniu przy użyciu Multi-Factor Authentication. Aby zwiększyć użyteczność i zminimalizować liczbę przypadków, w których użytkownik musi wykonywać uwierzytelnianie wieloskładnikowe na tym samym urządzeniu, wybierz czas trwania wynoszący 90 dni lub więcej.

> [!IMPORTANT]
> W przypadku naruszenia zabezpieczeń konta lub urządzenia zapamiętanie Multi-Factor Authentication dla zaufanych urządzeń może wpłynąć na bezpieczeństwo. Jeśli konto firmowe zostanie złamane lub zostanie utracone lub skradzione, należy [odwołać sesje MFA](howto-mfa-userdevicesettings.md).
>
> Akcja Przywróć odwołuje stan zaufany ze wszystkich urządzeń, a użytkownik musi ponownie wykonać uwierzytelnianie wieloskładnikowe. Możesz również poinstruować użytkowników, aby mogli przywrócić Multi-Factor Authentication na swoich urządzeniach, jak to zanotowano w temacie [Zarządzanie ustawieniami uwierzytelniania wieloskładnikowego](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

### <a name="how-the-feature-works"></a>Jak działa funkcja

Funkcja Pamiętaj Multi-Factor Authentication ustawia trwały plik cookie w przeglądarce, gdy użytkownik wybierze opcję **nie Monituj ponownie dla X dni** podczas logowania. Użytkownik nie będzie ponownie monitowany o Multi-Factor Authentication z tej samej przeglądarki, dopóki plik cookie nie wygaśnie. Jeśli użytkownik otworzy inną przeglądarkę na tym samym urządzeniu lub wyczyści pliki cookie, zostanie ponownie wyświetlony monit o zweryfikowanie.

Opcja **nie Monituj ponownie dla X dni** nie jest wyświetlana w aplikacjach bez przeglądarki, niezależnie od tego, czy aplikacja obsługuje nowoczesne uwierzytelnianie. Te aplikacje używają _tokenów odświeżania_ , które udostępniają nowe tokeny dostępu co godzinę. Po sprawdzeniu poprawności tokenu odświeżania usługa Azure AD sprawdza, czy ostatnie uwierzytelnianie wieloskładnikowe wystąpiło w ciągu określonej liczby dni.

Funkcja zmniejsza liczbę uwierzytelnień w aplikacjach sieci Web, które zwykle monitują się za każdym razem. Funkcja ta może zwiększyć liczbę uwierzytelnień dla nowoczesnych klientów uwierzytelniania, które zwykle monitują co 90 dni, w przypadku skonfigurowania mniejszego czasu trwania. Może również zwiększyć liczbę uwierzytelnień w połączeniu z zasadami dostępu warunkowego.

> [!IMPORTANT]
> Funkcja **pamiętaj Multi-Factor Authentication** nie jest zgodna z funkcją **Keep ja in in** AD FS, gdy użytkownicy wykonują uwierzytelnianie wieloskładnikowe w przypadku AD FS za pomocą usługi Azure serwer Multi-Factor Authentication lub rozwiązania do uwierzytelniania wieloskładnikowego innej firmy.
>
> Jeśli użytkownik wybierze opcję Nie wylogowuj **mnie** na AD FS, a także oznaczy urządzenie jako zaufane dla Multi-Factor Authentication, użytkownik nie zostanie automatycznie zweryfikowany po upływie następującej liczby dni dla opcji **Zapamiętaj uwierzytelnianie wieloskładnikowe** . Usługa Azure AD żąda od nowego uwierzytelniania wieloskładnikowego, ale AD FS zwraca token z pierwotnym Multi-Factor Authenticationm i datą, zamiast przeprowadzać ponowne uwierzytelnianie wieloskładnikowe. **Ta reakcja ustawia pętlę weryfikacji między usługą Azure AD a AD FS.**
>
> Funkcja **pamiętaj Multi-Factor Authentication** nie jest zgodna z użytkownikami B2B i nie będzie widoczna dla użytkowników B2B podczas logowania się do zaproszonych dzierżawców.
>

### <a name="enable-remember-multi-factor-authentication"></a>Włącz Multi-Factor Authentication Pamiętaj

Aby włączyć i skonfigurować opcję dla użytkowników, którzy będą pamiętać o stanie usługi MFA i pominięciu, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Użytkownicy**.
1. Wybierz pozycję **Multi-Factor Authentication**.
1. W obszarze Multi-Factor Authentication wybierz pozycję **Ustawienia usługi**.
1. Na stronie **Ustawienia usługi** w obszarze **Zapamiętaj uwierzytelnianie wieloskładnikowe** wybierz opcję **Zezwalaj użytkownikom na zapamiętywanie uwierzytelniania wieloskładnikowego na urządzeniach, które ufają** .
1. Ustaw liczbę dni, przez które zaufane urządzenia mają obejść uwierzytelnianie wieloskładnikowe. Aby zapewnić optymalne środowisko użytkownika, należy zwiększyć czas trwania do *90* lub więcej dni.
1. Wybierz pozycję **Zapisz**.

### <a name="mark-a-device-as-trusted"></a>Oznacz urządzenie jako zaufane

Po włączeniu funkcji Pamiętaj Multi-Factor Authentication użytkownicy mogą oznaczyć urządzenie jako zaufane podczas logowania, wybierając opcję **nie Monituj ponownie**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat dostępnych metod korzystania z usługi Azure AD Multi-Factor Authentication, zobacz [jakie metody uwierzytelniania i weryfikacji są dostępne w Azure Active Directory?](concept-authentication-methods.md)
