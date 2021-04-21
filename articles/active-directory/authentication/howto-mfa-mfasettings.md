---
title: Konfigurowanie usługi Azure AD Multi-Factor Authentication — Azure Active Directory
description: Dowiedz się, jak skonfigurować ustawienia usługi Azure AD Multi-Factor Authentication w Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: c067dba3a8af87e354019154fad8304fe9edfbbc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829660"
---
# <a name="configure-azure-ad-multi-factor-authentication-settings"></a>Konfigurowanie ustawień usługi Azure AD Multi-Factor Authentication

Aby dostosować środowisko użytkownika końcowego dla usługi Azure AD Multi-Factor Authentication, można skonfigurować opcje ustawień, takich jak progi blokady konta lub alerty i powiadomienia o oszustwie. Niektóre ustawienia znajdują się bezpośrednio w Azure Portal dla usługi Azure Active Directory (Azure AD), a niektóre w oddzielnym portalu usługi Azure AD Multi-Factor Authentication.

Następujące ustawienia usługi Azure AD Multi-Factor Authentication są dostępne w Azure Portal:

| Cecha | Opis |
| ------- | ----------- |
| [Blokada konta](#account-lockout) | Tymczasowo zablokuj kontom dostęp do usługi Azure AD Multi-Factor Authentication, jeśli w wierszu jest zbyt wiele prób uwierzytelniania odrzuconych. Ta funkcja dotyczy tylko użytkowników, którzy wprowadzają numer PIN w celu uwierzytelnienia. (Serwer MFA) |
| [Blokowanie/odblokowywanie użytkowników](#block-and-unblock-users) | Blokuj określonym użytkownikom możliwość otrzymywania żądań usługi Azure AD Multi-Factor Authentication. Wszystkie próby uwierzytelnienia dla zablokowanych użytkowników są automatycznie odrzucane. Użytkownicy pozostaną zablokowani przez 90 dni od chwili zablokowania lub ręcznego odblokowania. |
| [Alert dotyczący wykrycia oszustwa](#fraud-alert) | Konfigurowanie ustawień, które umożliwiają użytkownikom zgłaszanie fałszywych żądań weryfikacji. |
| [Powiadomienia](#notifications) | Włącz powiadomienia o zdarzeniach z serwera MFA. |
| [Tokeny OATH](concept-authentication-oath-tokens.md) | Używany w środowiskach usługi Azure AD MFA opartych na chmurze do zarządzania tokenami OATH dla użytkowników. |
| [Ustawienia połączeń telefonicznych](#phone-call-settings) | Konfigurowanie ustawień związanych z połączeniami telefonicznymi i powitaniami dla środowisk w chmurze i lokalnych. |
| Dostawcy | Spowoduje to pokazanie wszystkich istniejących dostawców uwierzytelniania, którzy być może skojarzyli Cię z Twoim kontem. Nowi dostawcy uwierzytelniania mogą nie zostać utworzeni od 1 września 2018 r. |

![Azure Portal — ustawienia usługi Azure AD Multi-Factor Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Blokada konta

Aby zapobiec wielokrotnym próbom uwierzytelniania wieloskładnikowego w ramach ataku, ustawienia blokady konta umożliwiają określenie, ile nieudanych prób można zezwolić, zanim konto zostanie zablokowane przez określony czas. Ustawienia blokady konta są stosowane tylko wtedy, gdy w wierszu polecenia usługi MFA zostanie wprowadzony kod PIN.

Dostępne są następujące ustawienia:

* Liczba odmów usługi MFA w celu wyzwolenia blokady konta
* Minuty do momentu zresetowania licznika blokady konta
* Minuty do automatycznego odblokowania konta

Aby skonfigurować ustawienia blokady konta, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
1. Przejdź do **Azure Active Directory**  >    >  **uwierzytelniania wieloskładnikowego**  >  **zabezpieczeń.**
1. Wprowadź wymagane wartości dla środowiska, a następnie wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający ustawienia blokady konta w Azure Portal](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Blokowanie i odblokowywanie użytkowników

Jeśli urządzenie użytkownika zostanie utracone lub skradzione, możesz zablokować próby uwierzytelniania wieloskładnikowego usługi Azure AD dla skojarzonego konta. Wszystkie próby uwierzytelniania wieloskładnikowego usługi Azure AD dla zablokowanych użytkowników są automatycznie blokowane. Użytkownicy pozostają zablokowani przez 90 dni od momentu zablokowania. Opublikowaliśmy film wideo na temat [blokowania](https://www.youtube.com/watch?v=WdeE1On4S1o) i odblokowywania użytkowników w dzierżawie, aby pokazać, jak to zrobić.

### <a name="block-a-user"></a>Blokuj użytkownika

Aby zablokować użytkownika, wykonaj następujące czynności:

1. Przejdź do **Azure Active Directory**  >    >  **uwierzytelniania wieloskładnikowego**  >  **zabezpieczeń Blokowanie/odblokowywanie użytkowników.**
1. Wybierz **pozycję Dodaj,** aby zablokować użytkownika.
1. Wprowadź nazwę użytkownika zablokowanego użytkownika jako `username@domain.com` , a następnie podaj komentarz w polu *Przyczyna.*
1. Gdy wszystko będzie gotowe, **wybierz przycisk OK,** aby zablokować użytkownika.

### <a name="unblock-a-user"></a>Odblokowywanie użytkownika

Aby odblokować użytkownika, wykonaj następujące czynności:

1. Przejdź do **Azure Active Directory**  >  **zabezpieczeń**  >  **usługi MFA**  >  **Blokuj/odblokuj użytkowników.**
1. W kolumnie *Akcja* obok żądanego użytkownika wybierz pozycję **Odblokuj**.
1. Wprowadź komentarz w *polu Przyczyna odblokowania.*
1. Gdy wszystko będzie gotowe, **wybierz przycisk OK,** aby odblokować użytkownika.

## <a name="fraud-alert"></a>Alert dotyczący wykrycia oszustwa

Funkcja alertu o oszustwie umożliwia użytkownikom zgłaszanie fałszywych prób uzyskania dostępu do zasobów. Po otrzymaniu nieznanego i podejrzanego monitu usługi MFA użytkownicy mogą zgłosić próbę oszustwa przy użyciu aplikacji Microsoft Authenticator lub telefonu.

Dostępne są następujące opcje konfiguracji alertu o oszustwie:

* **Automatycznie** blokuj użytkowników, którzy zgłaszają oszustwa: jeśli użytkownik zgłasza oszustwo, próby uwierzytelnienia usługi Azure AD MFA dla konta użytkownika są blokowane przez 90 dni lub do momentu odblokowania konta przez administratora. Administrator może przeglądać logowania przy użyciu raportu logowania i podjąć odpowiednie działania, aby zapobiec przyszłym oszustwom. Administrator może następnie [odblokować](#unblock-a-user) konto użytkownika.
* **Kod do zgłaszania oszustw podczas** początkowego powitania: gdy użytkownicy otrzymują telefon w celu przeprowadzenia uwierzytelniania wieloskładnikowego, zwykle naciskają klawisz w celu potwierdzenia **#** logowania. Aby zgłosić oszustwo, użytkownik wprowadza kod przed naciśnięciem klawisza **#** . Ten kod ma **domyślnie wartość 0,** ale można go dostosować.

   > [!NOTE]
   > Domyślne powitania głosowe od firmy Microsoft instruują użytkowników, aby nacisli **0#** w celu przesłania alertu o oszustwie. Jeśli chcesz użyć kodu innego niż **0,** nagraj i przekaż własne niestandardowe powitania głosowe z odpowiednimi instrukcjami dla użytkowników.

Aby włączyć i skonfigurować alerty dotyczące oszustw, wykonaj następujące czynności:

1. Przejdź **do** Azure Active Directory  >  **o**  >  **oszustwie usługi MFA**  >  **zabezpieczeń.**
1. Ustaw ustawienie *Zezwalaj użytkownikom na przesyłanie alertów o oszustwie* na **wartość Wł.**.
1. Skonfiguruj ustawienie *Automatycznie blokuj użytkowników,* którzy zgłaszają oszustwo, lub Kod, aby zgłaszać oszustwa podczas początkowego powitania zgodnie z potrzebami. 
1. Gdy wszystko będzie gotowe, wybierz **pozycję Zapisz.**

### <a name="view-fraud-reports"></a>Wyświetlanie raportów o oszustwie

Wybierz **Azure Active Directory**  >  **szczegóły uwierzytelniania**  >  **logowania.** Raport o oszustwie jest teraz częścią standardowego raportu logowania usługi Azure AD i będzie pokazywany w **szczegółach** wyników jako odmowa uwierzytelniania MFA, wprowadzono kod oszustwa.
 
## <a name="notifications"></a>Powiadomienia

Powiadomienia e-mail można skonfigurować, gdy użytkownicy będą zgłaszać alerty o oszustwie. Te powiadomienia są zwykle wysyłane do administratorów tożsamości, ponieważ poświadczenia konta użytkownika prawdopodobnie zostały naruszone. W poniższym przykładzie pokazano, jak wygląda wiadomość e-mail z powiadomieniem o alercie o oszustwie:

![Przykład wiadomości e-mail z powiadomieniem o alercie o oszustwie](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

Aby skonfigurować powiadomienia o alertach o oszustwie, wykonaj następujące ustawienia:

1. Przejdź do **Azure Active Directory**  >  **usługi Security**  >  **Multi-Factor Authentication.**  >  
1. Wprowadź adres e-mail do dodania w następnym polu.
1. Aby usunąć istniejący adres e-mail, wybierz opcję **...** obok żądanego adresu e-mail, a następnie wybierz pozycję **Usuń.**
1. Gdy wszystko będzie gotowe, wybierz **pozycję Zapisz.**

## <a name="oath-tokens"></a>Tokeny OATH

Usługa Azure AD obsługuje korzystanie z tokenów SHA-1 OATH-TOTP, które odświeżają kody co 30 lub 60 sekund. Klienci mogą zakupić te tokeny od wybranego dostawcy.

Tokeny sprzętowe OATH TOTP są zwykle wyposażone w klucz tajny lub iniekcję wstępnie zaprogramowane w tokenie. Te klucze muszą być wprowadzane do usługi Azure AD zgodnie z opisem w poniższych krokach. Klucze tajne są ograniczone do 128 znaków, które mogą nie być zgodne ze wszystkimi tokenami. Klucz tajny może zawierać tylko znaki *a-z* lub *A-Z* i *cyfry od 1 do 7* i musi być zakodowany w *formacie Base32.*

Programowalne tokeny sprzętowe OATH TOTP, które można ponownie edytować, można również skonfigurować za pomocą usługi Azure AD w przepływie konfiguracji tokenu oprogramowania.

Tokeny sprzętowe OATH są obsługiwane w ramach publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz Dodatkowe warunki użytkowania dotyczące [wersji Microsoft Azure zapoznawczych](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![Przekazywanie tokenów OATH do bloku tokenów uwierzytelniania wieloskładnikowego OATH](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Pozyskane tokeny należy przekazać w formacie pliku wartości rozdzielanych przecinkami (CSV), w tym nazwę UPN, numer seryjny, klucz tajny, interwał czasu, producenta i model, jak pokazano w poniższym przykładzie:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Upewnij się, że w pliku CSV znajduje się wiersz nagłówka.

Po prawidłowym sformatowaniu jako plik CSV administrator może zalogować się do usługi Azure Portal, przejść do usługi **Azure Active Directory > Security > MFA > tokenów OATH** i przekazać wynikowy plik CSV.

W zależności od rozmiaru pliku CSV przetwarzanie może potrwać kilka minut. Wybierz przycisk **Odśwież,** aby uzyskać bieżący stan. Jeśli w pliku występują błędy, możesz pobrać plik CSV zawierający listę błędów do rozwiązania. Nazwy pól w pobranym pliku CSV różnią się od przekazanej wersji.

Po wprowadzeniu jakichkolwiek błędów administrator może aktywować  każdy klucz, wybierając pozycję Aktywuj dla tokenu i wprowadzając uwierzytelnianie OTP wyświetlane w tokenie.

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji wystawcy uwierzytelnień, takich jak Microsoft Authenticator aplikacji, skonfigurowanych do użycia w dowolnym momencie.

## <a name="phone-call-settings"></a>Ustawienia połączeń telefonicznych

Jeśli użytkownicy otrzymają połączenia telefoniczne z monitami uwierzytelniania wieloskładnikowego, możesz skonfigurować ich środowisko, takie jak identyfikator rozmówcy lub powitanie głosowe.

Jeśli w Stany Zjednoczone nie skonfigurowano identyfikatora wywołującego mfa, połączenia głosowe firmy Microsoft pochodzą z następujących numerów. Jeśli używasz filtrów spamu, pamiętaj, aby wykluczyć następujące liczby:

* *+1 (866) 539 4191*
* *+1 (855) 330 8653*
* *+1 (877) 668 6536*

> [!NOTE]
> Gdy wywołania usługi Azure AD Multi-Factor Authentication są umieszczane za pośrednictwem publicznej sieci telefonicznej, połączenia są czasami kierowane przez operatora, który nie obsługuje identyfikatora wywołującego. W związku z tym identyfikator wywołujący nie jest gwarantowany, mimo że usługa Azure AD Multi-Factor Authentication zawsze wysyła go. Dotyczy to zarówno połączeń telefonicznych, jak i wiadomości SMS dostarczanych przez usługę Azure AD Multi-Factor Authentication. Jeśli musisz sprawdzić, czy wiadomość SMS pochodzi z usługi Azure AD Multi-Factor Authentication, zobacz Jakie krótkie kody SMS są używane [do wysyłania wiadomości?](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

Aby skonfigurować własny numer identyfikatora wywołującego, wykonaj następujące kroki:

1. Przejdź do **Azure Active Directory** security MFA Phone call settings (Ustawienia połączeń  >    >  **telefonicznych uwierzytelniania**  >  **wieloskładnikowego zabezpieczeń).**
1. Ustaw numer **identyfikatora rozmówcy usługi MFA** na numer, który ma być wyświetlony użytkownikom na telefonie. Dozwolone są tylko numery w USA.
1. Gdy wszystko będzie gotowe, wybierz **pozycję Zapisz.**

### <a name="custom-voice-messages"></a>Niestandardowe komunikaty głosowe

Możesz użyć własnych nagrań lub powitań dla usługi Azure AD Multi-Factor Authentication z funkcją niestandardowych wiadomości głosowych. Tych komunikatów można używać oprócz lub w celu zastąpienia domyślnych nagrań firmy Microsoft.

Przed rozpoczęciem należy pamiętać o następujących ograniczeniach:

* Obsługiwane formaty plików to *wav* i *mp3.*
* Limit rozmiaru pliku wynosi 1 MB.
* Komunikaty uwierzytelniania powinny być krótsze niż 20 sekund. Komunikaty dłuższe niż 20 sekund mogą spowodować niepowodzenie weryfikacji. Użytkownik może nie odpowiedzieć przed zakończeniem komunikatu i przeoczy czas weryfikacji.

### <a name="custom-message-language-behavior"></a>Zachowanie niestandardowego języka komunikatów

Gdy niestandardowy komunikat głosowy jest odtwarzany dla użytkownika, język komunikatu zależy od następujących czynników:

* Język bieżącego użytkownika.
  * Język wykryty przez przeglądarkę użytkownika.
  * Inne scenariusze uwierzytelniania mogą zachowywać się inaczej.
* Język wszystkich dostępnych komunikatów niestandardowych.
  * Ten język jest wybierany przez administratora po dodaniu niestandardowego komunikatu.

Jeśli na przykład istnieje tylko jeden komunikat niestandardowy w języku niemieckim:

* Użytkownik uwierzytelniony w języku niemieckim usłyszy niestandardową wiadomość w języku niemieckim.
* Użytkownik uwierzytelniony w języku angielskim usłyszy standardową wiadomość w języku angielskim.

### <a name="custom-voice-message-defaults"></a>Niestandardowe ustawienia domyślne komunikatów głosowych

Następujące przykładowe skrypty mogą służyć do tworzenia własnych komunikatów niestandardowych. Te frazy są wartościami domyślnymi, jeśli nie skonfigurujesz własnych komunikatów niestandardowych:

| Nazwa komunikatu | Skrypt |
| --- | --- |
| Uwierzytelnianie powiodło się | Logowanie zostało pomyślnie zweryfikowane. Goodbye. |
| Monit o rozszerzenie | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta, aby kontynuować. |
| Potwierdzenie oszustwa | Przesłano alert o oszustwie. Aby odblokować konto, skontaktuj się z działem pomocy technicznej IT Twojej firmy. |
| Powitanie przed oszustwem (standardowa) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta, aby zakończyć weryfikację. Jeśli ta weryfikacja nie zostanie zainicjowana, może się okazać, że ktoś próbuje uzyskać dostęp do Twojego konta. Naciśnij zero funta, aby przesłać alert o oszustwie. Spowoduje to powiadomienie zespołu IT Twojej firmy i zablokowanie dalszych prób weryfikacji. |
| Zgłoszone oszustwo Przesłano alert o oszustwie. | Aby odblokować konto, skontaktuj się z działem pomocy technicznej IT Twojej firmy. |
| Uaktywnienie | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta, aby zakończyć weryfikację. |
| Ponawianie próby odmówienia uwierzytelnienia | Odmowa weryfikacji. |
| Ponów próbę (standardowa) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta , aby zakończyć weryfikację. |
| Greeting (Standard) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta , aby zakończyć weryfikację. |
| Greeting (PIN) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Wprowadź numer PIN, a następnie klucz funta, aby zakończyć weryfikację. |
| Powitanie przed oszustwem (PIN) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft.  Wprowadź numer PIN, a następnie klucz funta, aby zakończyć weryfikację. Jeśli nie zainicjowaliśmy tej weryfikacji, być może ktoś próbuje uzyskać dostęp do Twojego konta. Naciśnij zero funta, aby przesłać alert o oszustwie. Spowoduje to powiadomienie zespołu IT Twojej firmy i zablokowanie dalszych prób weryfikacji. |
| Ponawianie próby (numer PIN) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Wprowadź numer PIN, a następnie klucz funta, aby zakończyć weryfikację. |
| Monit rozszerzenia po cyfrach | Jeśli masz już to rozszerzenie, naciśnij klawisz funta, aby kontynuować. |
| Odmowa uwierzytelniania | Niestety, obecnie nie możemy Cię zalogować. Spróbuj ponownie później. |
| Powitanie aktywacji (standardowa) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta, aby zakończyć weryfikację. |
| Ponawianie aktywacji (standardowa) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Naciśnij klawisz funta, aby zakończyć weryfikację. |
| Powitanie aktywacji (PIN) | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Wprowadź numer PIN, a następnie klucz funta, aby zakończyć weryfikację. |
| Monit o rozszerzenie przed cyframi | Dziękujemy za korzystanie z systemu weryfikacji logowania firmy Microsoft. Przenieś to wywołanie do rozszerzenia ... |

### <a name="set-up-a-custom-message"></a>Konfigurowanie komunikatu niestandardowego

Aby użyć własnych komunikatów niestandardowych, wykonaj następujące kroki:

1. Przejdź do **Azure Active Directory**  >  **połączenia**  >  **telefonicznego uwierzytelniania**  >  **wieloskładnikowego zabezpieczeń.**
1. Wybierz **pozycję Dodaj powitanie**.
1. Wybierz typ **powitania,** taki jak *Greeting (standard) lub* Authentication successful (Uwierzytelnianie *powiodło się).*
1. Wybierz **język**, na podstawie poprzedniej sekcji dotyczącej [niestandardowego zachowania języka komunikatów.](#custom-message-language-behavior)
1. Wyszukaj i wybierz plik dźwiękowy *mp3* *lub wav* do przekazania.
1. Gdy wszystko będzie gotowe, wybierz **pozycję Dodaj**, a następnie **pozycję Zapisz.**

## <a name="mfa-service-settings"></a>Ustawienia usługi MFA

Ustawienia haseł aplikacji, zaufanych ip i opcji weryfikacji oraz uwierzytelniania wieloskładnikowego usługi Azure AD Multi-Factor Authentication można znaleźć w ustawieniach usługi. Jest to bardziej starszy portal, który nie jest częścią zwykłego portalu usługi Azure AD.

Dostęp do ustawień usługi można uzyskać z witryny Azure Portal przez Azure Active Directory Security MFA Getting started Configure Additional  >    >    >    >    >  **cloud-based MFA settings**(Wprowadzenie do konfigurowania dodatkowych ustawień usługi MFA opartych na chmurze). Zostanie otwarte nowe okno lub karta z *dodatkowymi opcjami ustawień* usługi.

## <a name="trusted-ips"></a>Zaufane adresy IP

Funkcja _zaufanego adresu IP_ usługi Azure AD Multi-Factor Authentication pomija monity o uwierzytelnianie wieloskładnikowe dla użytkowników, którzy logują się ze zdefiniowanego zakresu adresów IP. Zaufane zakresy adresów IP można ustawić dla środowisk lokalnych, dzięki czemu gdy użytkownicy znajdują się w jednej z tych lokalizacji, nie będzie wyświetlany monit uwierzytelniania wieloskładnikowego usługi Azure AD. Funkcja _zaufanych ip w_ usłudze Azure AD Multi-Factor Authentication wymaga Azure AD — wersja Premium wersji P1. 

> [!NOTE]
> Zaufane adresy IP mogą zawierać prywatne zakresy adresów IP tylko w przypadku korzystania z serwera MFA. W przypadku usługi Azure AD Multi-Factor Authentication opartej na chmurze można używać tylko zakresów publicznych adresów IP.
>
> Zakresy adresów IPv6 są obsługiwane tylko w [interfejsie Nazwana lokalizacja (wersja zapoznawcza).](../conditional-access/location-condition.md)

Jeśli Twoja organizacja wdraża rozszerzenie serwera NPS w celu zapewnienia uwierzytelniania wieloskładnikowego dla aplikacji lokalnych, źródłowy adres IP będzie zawsze wyświetlany jako serwer NPS, za pośrednictwem których próba uwierzytelnienia przepływa.

| Typ dzierżawy usługi Azure AD | Opcje funkcji zaufanego adresu IP |
|:--- |:--- |
| Zarządzanie |**Określony zakres adresów IP:** administratorzy określają zakres adresów IP, które mogą pominąć uwierzytelnianie wieloskładnikowe dla użytkowników logowych się z intranetu firmy. Można skonfigurować maksymalnie 50 zaufanych zakresów adresów IP.|
| Federacyjni |**Wszyscy użytkownicy federowani:** wszyscy użytkownicy federowani logowani wewnątrz organizacji mogą pominąć uwierzytelnianie wieloskładnikowe. Użytkownicy pomijają weryfikację przy użyciu oświadczenia wystawionego przez Active Directory Federation Services (AD FS).<br/>**Określony zakres adresów IP:** administratorzy określają zakres adresów IP, które mogą pominąć uwierzytelnianie wieloskładnikowe dla użytkowników logują się z intranetu firmy. |

Obejście zaufanego adresu IP działa tylko z wewnątrz firmowego intranetu. Jeśli wybierzesz opcję **Wszyscy użytkownicy federacyjni** i użytkownik będzie się spoza firmowego intranetu, użytkownik musi uwierzytelnić się przy użyciu uwierzytelniania wieloskładnikowego. Proces jest taki sam, nawet jeśli użytkownik przedstawia AD FS oświadczenia.

### <a name="end-user-experience-inside-of-corpnet"></a>Środowisko użytkownika końcowego w sieci firmowej

Gdy funkcja zaufanych ip jest wyłączona, dla przepływów przeglądarki jest wymagane uwierzytelnianie wieloskładnikowe. Hasła aplikacji są wymagane w przypadku starszych rozbudowanych aplikacji klienckich.

W przypadku korzystania z zaufanych ip nie jest wymagane uwierzytelnianie wieloskładnikowe dla przepływów przeglądarki. Hasła aplikacji nie są wymagane w przypadku starszych rozbudowanych aplikacji klienckich, pod warunkiem, że użytkownik nie utworzył hasła aplikacji. Po użyciu hasła aplikacji hasło pozostaje wymagane.

### <a name="end-user-experience-outside-corpnet"></a>Środowisko użytkownika końcowego poza siecią firmową

Niezależnie od tego, czy są zdefiniowane zaufane adresy IP, dla przepływów przeglądarki jest wymagane uwierzytelnianie wieloskładnikowe. Hasła aplikacji są wymagane w przypadku starszych rozbudowanych aplikacji klienckich.

### <a name="enable-named-locations-by-using-conditional-access"></a>Włączanie nazwanych lokalizacji przy użyciu dostępu warunkowego

Za pomocą reguł dostępu warunkowego można definiować nazwane lokalizacje, korzystając z następujących kroków:

1. Na stronie Azure Portal wyszukaj i wybierz pozycję Azure Active Directory , **a** następnie przejdź do pozycji Security   >  **Conditional Access**  >  **Named locations (Nazwane lokalizacje dostępu warunkowego zabezpieczeń).**
1. Wybierz **pozycję Nowa lokalizacja.**
1. Wprowadź nazwę lokalizacji.
1. Wybierz **pozycję Oznacz jako zaufaną lokalizację**.
1. Wprowadź zakres adresów IP w notacji CIDR dla środowiska, na przykład *40.77.182.32/27.*
1. Wybierz przycisk **Utwórz**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Włączanie funkcji zaufanych ip przy użyciu dostępu warunkowego

Aby włączyć zaufane ip przy użyciu zasad dostępu warunkowego, wykonaj następujące kroki:

1. W Azure Portal wyszukaj i wybierz pozycję Azure Active Directory **,** a następnie przejdź do pozycji Security Conditional Access Named locations (Nazwane  >     >  **lokalizacje dostępu warunkowego zabezpieczeń).**
1. Wybierz **pozycję Skonfiguruj zaufane ip usługi MFA.**
1. Na stronie **Ustawienia usługi** w obszarze **Zaufane ip** wybierz jedną z następujących dwóch opcji:

   * **W przypadku żądań od użytkowników federowanych pochodzących z intranetu:** aby wybrać tę opcję, zaznacz pole wyboru. Wszyscy użytkownicy federacyjni logowani z sieci firmowej pomijają uwierzytelnianie wieloskładnikowe przy użyciu oświadczenia wystawionego przez AD FS. Upewnij się AD FS że ma regułę dodawania oświadczenia intranetowego do odpowiedniego ruchu. Jeśli reguła nie istnieje, utwórz następującą regułę w AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **W przypadku żądań z określonego zakresu** publicznych adresów IP: aby wybrać tę opcję, wprowadź adresy IP w polu tekstowym przy użyciu notacji CIDR.
      * W przypadku adresów IP z zakresu od xxx.xxx.xxx.1 do xxx.xxx.xxx.254 użyj notacji, na przykład **xxx.xxx.xxx.0/24.**
      * W przypadku pojedynczego adresu IP należy użyć notacji, na **przykład xxx.xxx.xxx.xxx/32**.
      * Wprowadź maksymalnie 50 zakresów adresów IP. Użytkownicy logni z tych adresów IP pomijają uwierzytelnianie wieloskładnikowe.

1. Wybierz pozycję **Zapisz**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Włączanie funkcji zaufanych ip przy użyciu ustawień usługi

Jeśli nie chcesz używać zasad dostępu warunkowego do włączania zaufanych ip, możesz skonfigurować ustawienia usługi Azure AD Multi-Factor Authentication, korzystając z następujących kroków: 

1. W Azure Portal wyszukaj i wybierz pozycję Azure Active Directory , **a** następnie wybierz pozycję **Użytkownicy.**
1. Wybierz pozycję **Multi-Factor Authentication**.
1. W obszarze Multi-Factor Authentication wybierz **pozycję Ustawienia usługi.**
1. Na stronie **Ustawienia usługi** w obszarze **Zaufane ip** wybierz jedną (lub obie) z następujących dwóch opcji:

   * **W przypadku żądań od użytkowników federowanych w moim intranecie:** aby wybrać tę opcję, zaznacz pole wyboru. Wszyscy użytkownicy federowani logowani z sieci firmowej pomijają uwierzytelnianie wieloskładnikowe przy użyciu oświadczenia wystawionego przez AD FS. Upewnij się AD FS że ma regułę dodawania oświadczenia intranetowego do odpowiedniego ruchu. Jeśli reguła nie istnieje, utwórz następującą regułę w AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **W przypadku żądań z określonego** zakresu podsieci adresów IP: aby wybrać tę opcję, wprowadź adresy IP w polu tekstowym przy użyciu notacji CIDR.
      * W przypadku adresów IP z zakresu od xxx.xxx.xxx.1 do xxx.xxx.xxx.254 należy użyć notacji, na przykład **xxx.xxx.xxx.0/24.**
      * Dla pojedynczego adresu IP użyj notacji, na przykład **xxx.xxx.xxx.xxx/32**.
      * Wprowadź maksymalnie 50 zakresów adresów IP. Użytkownicy logni z tych adresów IP pomijają uwierzytelnianie wieloskładnikowe.

1. Wybierz pozycję **Zapisz**.

## <a name="verification-methods"></a>Metody weryfikacji

Metody weryfikacji dostępne dla użytkowników można wybrać w portalu ustawień usługi. Gdy użytkownicy rejestrują swoje konta w usłudze Azure AD Multi-Factor Authentication, wybierają preferowaną metodę weryfikacji spośród włączonych opcji. Wskazówki dotyczące procesu rejestracji użytkownika można uzyskać w te [tematze Konfigurowanie konta do uwierzytelniania wieloskładnikowego.](../user-help/multi-factor-authentication-end-user-first-time.md)

Dostępne są następujące metody weryfikacji:

| Metoda | Opis |
|:--- |:--- |
| Połączenie telefoniczne |Umieszcza automatyczne połączenie głosowe. Użytkownik odbiera połączenie i naciska klawisz # na klawiaturze telefonu w celu uwierzytelnienia. Numer telefonu nie jest synchronizowany z lokalna usługa Active Directory. |
| Wiadomość SMS na telefon |Wysyła wiadomość SMS zawierającą kod weryfikacyjny. Użytkownik jest monitowany o wprowadzenie kodu weryfikacyjnego w interfejsie logowania. Ten proces jest nazywany jednokierunkową wiadomością SMS. Dwukierunkowa wiadomość SMS oznacza, że użytkownik musi wysłać wiadomość SMS z powrotem do określonego kodu. Dwukierunkowa wiadomość SMS jest przestarzała i nie jest obsługiwana po 14 listopada 2018 r. Administratorzy powinni włączyć inną metodę dla użytkowników, którzy wcześniej używali jednokierunkowych wiadomości SMS.|
| Powiadomienie za pośrednictwem aplikacji mobilnej |Wysyła powiadomienie wypychane na telefon lub zarejestrowane urządzenie. Użytkownik wyświetly powiadomienie i  wybierze pozycję Weryfikuj, aby ukończyć weryfikację. Aplikacja Microsoft Authenticator jest dostępna dla systemów [Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072) [i iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |
| Kod weryfikacyjny z aplikacji mobilnej lub tokenu sprzętowego |Aplikacja Microsoft Authenticator generuje nowy kod weryfikacyjny OATH co 30 sekund. Użytkownik wprowadza kod weryfikacyjny w interfejsie logowania. Aplikacja Microsoft Authenticator jest dostępna dla systemów [Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072) [i iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |

Aby uzyskać więcej informacji, zobacz [Jakie metody uwierzytelniania i weryfikacji są dostępne w usłudze Azure AD?](concept-authentication-methods.md)

### <a name="enable-and-disable-verification-methods"></a>Włączanie i wyłączanie metod weryfikacji

Aby włączyć lub wyłączyć metody weryfikacji, wykonaj następujące kroki:

1. W Azure Portal wyszukaj i wybierz pozycję Azure Active Directory , **a** następnie wybierz pozycję **Użytkownicy.**
1. Wybierz pozycję **Multi-Factor Authentication**.
1. W obszarze Multi-Factor Authentication wybierz **pozycję Ustawienia usługi.**
1. Na stronie **Ustawienia usługi** w obszarze **opcji** weryfikacji wybierz/usuń zaznaczenie metod do podania użytkownikom.
1. Kliknij pozycję **Zapisz**.

## <a name="remember-multi-factor-authentication"></a>Zapamiętaj uwierzytelnianie wieloskładnikowe

Pamiętaj, _że funkcja Multi-Factor Authentication_ umożliwia użytkownikom pomijanie kolejnych weryfikacji przez określoną liczbę dni po pomyślnym zalogowaniu się do urządzenia przy użyciu usługi Multi-Factor Authentication. Aby zwiększyć użyteczność i zminimalizować liczbę operacji uwierzytelniania wieloskładnikowego na tym samym urządzeniu, wybierz czas trwania co najmniej 90 dni.

> [!IMPORTANT]
> W przypadku naruszenia zabezpieczeń konta lub urządzenia zapamiętanie usługi Multi-Factor Authentication dla zaufanych urządzeń może mieć wpływ na bezpieczeństwo. Jeśli bezpieczeństwo konta firmowego zostanie naruszone lub zaufane urządzenie zostanie utracone lub skradzione, należy [odwołać sesje usługi MFA.](howto-mfa-userdevicesettings.md)
>
> Akcja przywracania odwołuje stan zaufany ze wszystkich urządzeń, a użytkownik musi ponownie przeprowadzić uwierzytelnianie wieloskładnikowe. Możesz również poinstruować użytkowników, aby przywrócili uwierzytelnianie wieloskładnikowe na ich własnych urządzeniach, jak opisano w tece Zarządzanie ustawieniami [uwierzytelniania wieloskładnikowego.](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

### <a name="how-the-feature-works"></a>Jak działa funkcja

Pamiętaj, że funkcja Multi-Factor Authentication ustawia trwały plik cookie w przeglądarce, gdy użytkownik wybierze opcję Nie pytaj ponownie przez **X** dni podczas logowania. Użytkownik nie będzie monitowany o uwierzytelnianie wieloskładnikowe z tej samej przeglądarki, dopóki plik cookie nie wygaśnie. Jeśli użytkownik otworzy inną przeglądarkę na tym samym urządzeniu lub wyczyści pliki cookie, zostanie ponownie wyświetlony monit o zweryfikowanie.

Opcja **Nie pytaj ponownie przez X** dni nie jest wyświetlana w aplikacjach innych niż przeglądarki, niezależnie od tego, czy aplikacja obsługuje nowoczesne uwierzytelnianie. Te aplikacje używają _tokenów odświeżania,_ które zapewniają nowe tokeny dostępu co godzinę. Po weryfikacji tokenu odświeżania usługa Azure AD sprawdza, czy ostatnie uwierzytelnianie wieloskładnikowe wystąpiło w ciągu określonej liczby dni.

Ta funkcja zmniejsza liczbę uwierzytelnień w aplikacjach internetowych, które zwykle monitują za każdym razem. Ta funkcja może zwiększyć liczbę uwierzytelnień dla nowoczesnych klientów uwierzytelniania, które zwykle są monitowane co 180 dni, jeśli skonfigurowano mniejszy czas trwania. Może również zwiększyć liczbę uwierzytelnień w połączeniu z zasadami dostępu warunkowego.

> [!IMPORTANT]
> Pamiętaj, że funkcja **Multi-Factor Authentication**  nie jest zgodna z funkcją nie wykonuj dla mnie zalogowania w usłudze AD FS, gdy użytkownicy wykonują uwierzytelnianie wieloskładnikowe dla usługi AD FS za pośrednictwem usługi Azure Serwer Multi-Factor Authentication lub rozwiązania do uwierzytelniania wieloskładnikowego innej firmy.
>
> Jeśli użytkownicy  wybierzą opcję Nie wyetapiuj mnie na platformie AD FS i oznaczą swoje  urządzenie jako zaufane w przypadku uwierzytelniania wieloskładnikowego, użytkownik nie zostanie automatycznie zweryfikowany po upływie liczby dni ważności uwierzytelniania wieloskładnikowego. Usługa Azure AD żąda nowego uwierzytelniania wieloskładnikowego, ale usługa AD FS zwraca token z oryginalnym oświadczeniem i datą usługi Multi-Factor Authentication, zamiast ponownie wykonywać uwierzytelnianie wieloskładnikowe. **Ta reakcja ustawia pętlę weryfikacji między usługą Azure AD i AD FS.**
>
> Pamiętaj, **że funkcja Multi-Factor Authentication** nie jest zgodna z użytkownikami B2B i nie będzie widoczna dla użytkowników B2B podczas logowania się do zaproszonych dzierżaw.
>

### <a name="enable-remember-multi-factor-authentication"></a>Włącz zapamiętaj uwierzytelnianie wieloskładnikowe

Aby włączyć i skonfigurować opcję, aby użytkownicy pamiętali stan usługi MFA i pomijali monity, wykonaj następujące kroki:

1. W Azure Portal wyszukaj i wybierz pozycję Azure Active Directory , **a** następnie wybierz pozycję **Użytkownicy.**
1. Wybierz pozycję **Multi-Factor Authentication**.
1. W obszarze Multi-Factor Authentication wybierz **pozycję Ustawienia usługi.**
1. Na stronie **Ustawienia usługi w** obszarze Pamiętaj uwierzytelnianie wieloskładnikowe wybierz opcję Zezwalaj użytkownikom na zapamiętanie uwierzytelniania wieloskładnikowego **na zaufanych urządzeniach.** 
1. Ustaw liczbę dni, aby umożliwić zaufanym urządzeniem pomijanie uwierzytelniania wieloskładnikowego. Aby uzyskać optymalne środowisko użytkownika, wydłuż czas trwania do *90* lub więcej dni.
1. Wybierz pozycję **Zapisz**.

### <a name="mark-a-device-as-trusted"></a>Oznaczanie urządzenia jako zaufanego

Po włączeniu funkcji Multi-Factor Authentication użytkownicy mogą oznaczyć urządzenie jako zaufane podczas logowania, wybierając opcję Nie **pytaj ponownie.**

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o dostępnych metodach użycia w usłudze Azure AD Multi-Factor Authentication, zobacz Jakie metody uwierzytelniania i weryfikacji są dostępne w [usłudze Azure Active Directory?](concept-authentication-methods.md)
