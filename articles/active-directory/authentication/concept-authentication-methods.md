---
title: Metody uwierzytelniania — Azure Active Directory
description: Metody uwierzytelniania dostępne w usłudze Azure AD dla usług MFA i SSPR
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a82c69575e82a7cf397955f08c3f114e449ba6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78968770"
---
# <a name="what-are-authentication-methods"></a>Co to są metody uwierzytelniania?

Jako administrator wybieraj metody uwierzytelniania dla usługi Azure Multi-Factor Authentication i samoobsługowego resetowania hasła (SSPR) zaleca się, aby użytkownicy musieli zarejestrować wiele metod uwierzytelniania. Gdy metoda uwierzytelniania nie jest dostępna dla użytkownika, może wybrać opcję uwierzytelnienia przy użyciu innej metody.

Administratorzy mogą definiować w zasadach, które metody uwierzytelniania są dostępne dla użytkowników usługi SSPR i MFA. Niektóre metody uwierzytelniania mogą nie być dostępne dla wszystkich funkcji. Aby uzyskać więcej informacji na temat konfigurowania zasad, zobacz artykuł [jak pomyślnie wdrożyć Samoobsługowe resetowanie haseł](howto-sspr-deployment.md) i [zaplanować Multi-Factor Authentication platformy Azure opartej na chmurze](howto-mfa-getstarted.md)

Firma Microsoft zdecydowanie zaleca administratorów pozwala użytkownikom wybrać więcej niż minimalną wymaganą liczbę metod uwierzytelniania na wypadek, gdyby nie mieli dostępu do jednego.

|Metoda uwierzytelniania|Sposób użycia|
| --- | --- |
| Hasło | Uwierzytelnianie MFA i SSPR |
| Pytania zabezpieczające | Tylko SSPR |
| Adres e-mail | Tylko SSPR |
| Aplikacja Microsoft Authenticator | Uwierzytelnianie MFA i SSPR |
| Token sprzętowy OATH | Publiczna wersja zapoznawcza usługi MFA i SSPR |
| SMS | Uwierzytelnianie MFA i SSPR |
| Połączenie głosowe | Uwierzytelnianie MFA i SSPR |
| Hasła aplikacji | MFA tylko w niektórych przypadkach |

![Metody uwierzytelniania używane na ekranie logowania](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Tokeny sprzętowe OATH dla usługi MFA i SSPR są funkcjami publicznej wersji zapoznawczej programu Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure|
|     |

## <a name="password"></a>Hasło

Hasło usługi Azure AD jest uznawane za metodę uwierzytelniania. Jest to jedna z metod, której **nie można wyłączyć**.

## <a name="security-questions"></a>Pytania zabezpieczające

Pytania zabezpieczające są dostępne tylko w ramach samoobsługowego **resetowania hasła w usłudze Azure AD** do kont innych niż administracyjne.

Jeśli używasz pytań zabezpieczających, zalecamy ich użycie w połączeniu z inną metodą. Pytania zabezpieczające mogą być mniej bezpieczne niż inne metody, ponieważ niektóre osoby mogą znać odpowiedzi na pytania innego użytkownika.

> [!NOTE]
> Pytania zabezpieczające są przechowywane prywatnie i bezpiecznie w obiekcie użytkownika w katalogu i mogą być udzielane tylko przez użytkowników podczas rejestracji. Nie ma możliwości, aby administrator mógł odczytywać lub modyfikować pytania lub odpowiedzi użytkownika.
>

### <a name="predefined-questions"></a>Wstępnie zdefiniowane pytania

* W jakim mieście Twój pierwszy współmałżonek/partner?
* W jakim mieście spotkali się rodzice?
* W jakim mieście znajduje się najbliższy element równorzędny?
* W jakim mieście urodziły się ojciec?
* W jakim mieście było Twoje pierwsze zadanie?
* W jakim mieście urodziła się twoja matka?
* W którym mieście nastąpiło w nowym roku 2000?
* Jaka jest nazwisko Twojego ulubionego nauczyciela w wysokiej szkoły?
* Jaka jest nazwa uczelni, która została zastosowana, ale nie uczestniczyła?
* Jaka jest nazwa miejsca, w którym ma zostać przeprowadzone pierwsze odbiór z ślubu?
* Co to jest drugie imię Twojego ojca?
* Jaka jest Twoja ulubiona żywność?
* Co to jest imię i nazwisko matki babcia?
* Co to jest imię i nazwisko matki?
* Co to jest miesiąc i rok urodzinowy Twojego elementu równorzędnego? (np. listopad 1985)
* Jaka jest nazwa najstarszego elementu równorzędnego?
* Co to jest imię i nazwisko z Paternal dziadka?
* Co to jest drugie imię z najmłodszego elementu równorzędnego?
* Jaką szkołę znasz na szóstej ocenie?
* Jakie było imię i nazwisko najlepszego przyjaciela?
* Jakie było imię i nazwisko Twojego pierwszego znaczenia?
* Jaka była nazwisko Twojego ulubionego nauczyciela szkoły szkolnej?
* Co to jest marka i model Twojego pierwszego samochodu lub motocykla?
* Jaka była nazwa pierwszej szkolnej szkoły?
* Jaka była nazwa szpitala, z którego korzystasz?
* Jaka była nazwa ulicy Twojego pierwszego domu z domem?
* Jaka była nazwa Twojego heroi z domu?
* Jaka była nazwa ulubionego zwierzęcia?
* Jaka była nazwa Twojego pierwszego zwierzęcia domowego?
* Jaki był pseudonim w Twoim domu,?
* Jaki był Twój ulubiony sport w dużej szkole?
* Jakie było Twoje pierwsze zadanie?
* Jakie były cztery ostatnie cyfry Twojego numeru telefonu w Twoim serwisie
* Kiedy jesteś w młodych, co chcesz zrobić po wzrósłe?
* Kto jest najbardziej sławęą osobą?

Wszystkie wstępnie zdefiniowane pytania zabezpieczające są tłumaczone i lokalizowane do pełnego zestawu języków pakietu Office 365 na podstawie ustawień regionalnych przeglądarki użytkownika.

### <a name="custom-security-questions"></a>Niestandardowe pytania zabezpieczające

Niestandardowe pytania zabezpieczające nie są zlokalizowane. Wszystkie pytania niestandardowe są wyświetlane w tym samym języku, w jakim są wprowadzane w administracyjnym interfejsie użytkownika, nawet jeśli ustawienia regionalne przeglądarki użytkownika są inne. Jeśli potrzebujesz zlokalizowanych pytań, musisz użyć wstępnie zdefiniowanych pytań.

Maksymalna długość niestandardowego pytania zabezpieczającego to 200 znaków.

### <a name="security-question-requirements"></a>Wymagania dotyczące zabezpieczeń

* Minimalny limit znaków odpowiedzi to trzy znaki.
* Maksymalny limit znaków odpowiedzi to 40 znaków.
* Użytkownicy nie mogą odpowiedzieć na te same pytania więcej niż jeden raz.
* Użytkownicy nie mogą udzielić odpowiedzi na więcej niż jedno pytanie.
* Dowolny zestaw znaków może służyć do definiowania pytań i odpowiedzi, w tym znaków Unicode.
* Liczba zdefiniowanych pytań musi być większa lub równa liczbie pytań, które były wymagane do zarejestrowania.

## <a name="email-address"></a>Adres e-mail

Adres e-mail jest dostępny **tylko w funkcji samoobsługowego resetowania hasła usługi Azure AD**.

Firma Microsoft zaleca użycie konta e-mail, które nie wymaga hasła usługi Azure AD użytkownika do uzyskania dostępu.

## <a name="microsoft-authenticator-app"></a>Aplikacja Microsoft Authenticator

Aplikacja Microsoft Authenticator zapewnia dodatkowy poziom zabezpieczeń dla konta służbowego lub szkolnego usługi Azure AD lub konto Microsoft.

Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) i [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

> [!NOTE]
> Użytkownicy nie będą mogli zarejestrować swojej aplikacji mobilnej podczas rejestrowania się w celu samoobsługowego resetowania hasła. Zamiast tego użytkownicy mogą rejestrować swoją aplikację [https://aka.ms/mfasetup](https://aka.ms/mfasetup) mobilną w wersji zapoznawczej rejestracji informacji o [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)zabezpieczeniach pod adresem.
>

### <a name="notification-through-mobile-app"></a>Powiadomienie za poorednictwem aplikacji mobilnej

Aplikacja Microsoft Authenticator może pomóc zapobiec nieautoryzowanemu dostępowi do kont i zatrzymywać fałszywe transakcje przez wypychanie powiadomienia do telefonu lub tabletu. Użytkownicy wyświetlają powiadomienie i jeśli są wiarygodne, wybierz pozycję Weryfikuj. W przeciwnym razie można wybrać opcję Odmów.

> [!WARNING]
> Do samoobsługowego resetowania hasła, gdy do resetowania jest wymagana tylko jedna metoda, kod weryfikacyjny jest jedyną opcją dostępną dla użytkowników **w celu zapewnienia najwyższego poziomu zabezpieczeń**.
>
> Gdy wymagane są dwie metody, użytkownicy będą mogli resetować przy **użyciu** powiadomienia **lub** kodu weryfikacyjnego oprócz innych włączonych metod.
>

Jeśli włączysz powiadomienia za pośrednictwem aplikacji mobilnej i kodu weryfikacyjnego z aplikacji mobilnej, użytkownicy rejestrujący aplikację Microsoft Authenticator przy użyciu powiadomienia będą mogli zweryfikować swoją tożsamość przy użyciu zarówno powiadomienia, jak i kodu.

> [!NOTE]
> Jeśli Twoja organizacja ma pracowników pracujących w trakcie lub podróży z Chin, **powiadomienie za pomocą metody aplikacji mobilnej** na **urządzeniach z systemem Android** nie działa w tym kraju. Dla tych użytkowników należy udostępnić alternatywne metody.

### <a name="verification-code-from-mobile-app"></a>Kod weryfikacyjny z aplikacji mobilnej

Aplikacja Microsoft Authenticator lub inne aplikacje innych firm mogą służyć jako token oprogramowania do generowania kodu weryfikacyjnego OATH. Po wprowadzeniu nazwy użytkownika i hasła wprowadzasz kod dostarczony przez aplikację na ekranie logowania. Kod weryfikacyjny stanowi drugą formę uwierzytelniania.

> [!WARNING]
> Do samoobsługowego resetowania hasła, gdy tylko jedna metoda jest wymagana do resetowania kodu weryfikacyjnego jest jedyną opcją dostępną dla użytkowników **w celu zapewnienia najwyższego poziomu zabezpieczeń**.
>

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji uwierzytelniania, takich jak aplikacja Microsoft Authenticator skonfigurowana do użycia w dowolnym momencie.

## <a name="oath-hardware-tokens-public-preview"></a>Tokeny sprzętowe OATH (publiczna wersja zapoznawcza)

OATH to otwarty standard, który określa sposób generowania kodów haseł jednorazowych (OTP). Usługa Azure AD będzie obsługiwać użycie tokenów SHA-TOTP w liczbie 2 sekund lub 60 sekund. Klienci mogą odliczać te tokeny od wybranego przez siebie dostawcy. Klucze tajne są ograniczone do 128 znaków, które mogą nie być zgodne ze wszystkimi tokenami. Klucz tajny może zawierać tylko znaki *a-z* lub *a-z* i cyfry *1-7*, a także musi być zakodowany w Base32.

![Przekazywanie tokenów OATH do bloku tokenów OATH usługi MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Tokeny sprzętowe OATH są obsługiwane w ramach publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure

Po uzyskaniu tokenów należy je przekazać w formacie pliku wartości rozdzielanych przecinkami (CSV), w tym nazwy UPN, numeru seryjnego, klucza tajnego, interwału czasu, producenta i modelu, jak pokazano w następującym przykładzie:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Upewnij się, że dołączysz wiersz nagłówka do pliku CSV.

Po prawidłowym sformatowaniu pliku CSV administrator może następnie zalogować się do Azure Portal, przejść do **Azure Active Directory** > **zabezpieczenia** > **MFA** > **tokeny Oath**usługi MFA i przekazać plik CSV.

Proces może potrwać kilka minut, w zależności od rozmiaru pliku CSV. Kliknij przycisk **Odśwież** , aby uzyskać bieżący stan. W przypadku wystąpienia błędów w pliku można pobrać plik CSV zawierający listę błędów, które należy rozwiązać. Nazwy pól w pobranym pliku CSV różnią się od przekazanej wersji.

Po rozwiązaniu jakichkolwiek błędów administrator może aktywować każdy klucz, klikając **Aktywuj** dla tokenu, który ma zostać aktywowany, i wprowadzając uwierzytelnianie OTP wyświetlane na tokenie.

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji uwierzytelniania, takich jak aplikacja Microsoft Authenticator skonfigurowana do użycia w dowolnym momencie.

## <a name="phone-options"></a>Opcje telefonu

### <a name="mobile-phone"></a>Telefon komórkowy

Dla użytkowników korzystających z telefonów komórkowych są dostępne dwie opcje.

Jeśli użytkownicy nie chcą, aby Twój numer telefonu komórkowego był widoczny w katalogu, ale nadal chcą używać go do resetowania haseł, Administratorzy nie muszą wypełniać go w katalogu. Użytkownicy powinni wypełnić atrybut **telefonu uwierzytelniania** za pośrednictwem [portalu rejestracji resetowania haseł](https://aka.ms/ssprsetup). Administratorzy mogą zobaczyć te informacje w profilu użytkownika, ale nie zostały opublikowane w innym miejscu.

Aby działały prawidłowo, numery telefonów muszą mieć format *+ CountryCode*, na przykład + 1 4255551234.

> [!NOTE]
> Musi być odstęp między kodem kraju i numerem telefonu.
>
> Resetowanie hasła nie obsługuje rozszerzeń telefonu. Nawet w formacie 4255551234X12345 + 1 rozszerzenia są usuwane przed umieszczeniem wywołania.

Firma Microsoft nie gwarantuje spójnego dostarczania wiadomości SMS lub głosowych Multi-Factor Authentication na podstawie tej samej liczby. W interesie naszych użytkowników firma Microsoft może dodawać lub usuwać krótkie kody w dowolnym momencie, gdy wprowadzimy zmiany trasy w celu poprawy możliwości dostarczania wiadomości SMS. Firma Microsoft nie obsługuje krótkich kodów dla krajów/regionów poza Stany Zjednoczone i Kanadę.

#### <a name="text-message"></a>Wiadomość SMS

Wiadomość SMS jest wysyłana na numer telefonu komórkowego zawierający kod weryfikacyjny. Aby kontynuować, wprowadź kod weryfikacyjny podany w interfejsie logowania.

#### <a name="phone-call"></a>Połączenie telefoniczne

Automatyczne połączenie głosowe jest nawiązywane na podanym numerze telefonu. Odpowiedz na połączenie i naciśnij klawisz # na klawiaturze telefonu, aby przeprowadzić uwierzytelnianie

> [!IMPORTANT]
> Począwszy od marca 2019 opcje połączenia telefonicznego nie będą dostępne dla usługi MFA i SSPR użytkowników w bezpłatnych/bezpłatnych dzierżawach Azure AD. Ta zmiana nie ma wpływu na wiadomości SMS. Połączenie telefoniczne będzie nadal dostępne dla użytkowników w płatnych dzierżawach usługi Azure AD. Ta zmiana ma wpływ tylko na dzierżawy usługi Azure AD bezpłatne/próbne.

### <a name="office-phone"></a>Telefon służbowy

Automatyczne połączenie głosowe jest nawiązywane na podanym numerze telefonu. Odpowiedz na wywołanie i naciśnij klawisz # na klawiaturze telefonu w celu uwierzytelnienia.

Aby działały prawidłowo, numery telefonów muszą mieć format *+ CountryCode*, na przykład + 1 4255551234.

Atrybut telefon biurowy jest zarządzany przez administratora.

> [!IMPORTANT]
> Począwszy od marca 2019 opcje połączenia telefonicznego nie będą dostępne dla usługi MFA i SSPR użytkowników w bezpłatnych/bezpłatnych dzierżawach Azure AD. Ta zmiana nie ma wpływu na wiadomości SMS. Połączenie telefoniczne będzie nadal dostępne dla użytkowników w płatnych dzierżawach usługi Azure AD. Ta zmiana ma wpływ tylko na dzierżawy usługi Azure AD bezpłatne/próbne.

> [!NOTE]
> Musi być odstęp między kodem kraju i numerem telefonu.
>
> Resetowanie hasła nie obsługuje rozszerzeń telefonu. Nawet w formacie 4255551234X12345 + 1 rozszerzenia są usuwane przed umieszczeniem wywołania.

### <a name="troubleshooting-phone-options"></a>Rozwiązywanie problemów z opcjami telefonu

Typowe problemy związane z metodami uwierzytelniania przy użyciu numeru telefonu:

* Zablokowany identyfikator obiektu wywołującego na jednym urządzeniu
   * Rozwiązywanie problemów z urządzeniem
* Nieprawidłowy numer telefonu, nieprawidłowy kod kraju, numer telefonu domowego i numer telefonu służbowego
   * Rozwiązywanie problemów z obiektem użytkownika i skonfigurowanymi metodami uwierzytelniania. Upewnij się, że zarejestrowano poprawne numery telefonów.
* Wprowadzono nieprawidłowy kod PIN
   * Upewnij się, że użytkownik użył poprawnego numeru PIN zarejestrowanego na serwerze usługi Azure MFA.
* Połączenie przekazane do poczty głosowej
   * Upewnij się, że użytkownik ma włączony telefon i że usługa jest dostępna w swoim obszarze lub użyj alternatywnej metody.
* Użytkownik jest zablokowany
   * Należy odblokować użytkownika w Azure Portal.
* Wiadomość SMS nie jest subskrybowana na urządzeniu
   * Użytkownik zmienia metody lub aktywuje wiadomość SMS na urządzeniu.
* Błędni dostawcy Telecom (nie wykryto danych wejściowych telefonu, brakujące sygnały DTMF, zablokowany identyfikator rozmówcy na wielu urządzeniach lub zablokowany program SMS na wielu urządzeniach)
   * Firma Microsoft używa wielu dostawców Telecom do przesyłania połączeń telefonicznych i wiadomości SMS w celu uwierzytelnienia. Jeśli widzisz którykolwiek z powyższych problemów, użytkownik próbuje użyć metody co najmniej 5 razy w ciągu 5 minut i dysponować informacjami o tym użytkowniku podczas kontaktowania się z pomocą techniczną firmy Microsoft.

## <a name="app-passwords"></a>Hasła aplikacji

Niektóre aplikacje nie korzystające z przeglądarki nie obsługują uwierzytelniania wieloskładnikowego, jeśli użytkownik włączył uwierzytelnianie wieloskładnikowe i spróbuje korzystać z aplikacji niekorzystających z przeglądarki, nie może się uwierzytelnić. Hasło aplikacji umożliwia użytkownikom dalsze uwierzytelnianie

W przypadku wymuszania Multi-Factor Authentication przy użyciu zasad dostępu warunkowego, a nie za poorednictwem usługi MFA dla poszczególnych użytkowników, nie można tworzyć haseł aplikacji. Aplikacje korzystające z zasad dostępu warunkowego do sterowania dostępem nie potrzebują haseł aplikacji.

Jeśli Twoja organizacja jest federacyjnym logowaniem jednokrotnym w usłudze Azure AD i zamierzasz korzystać z usługi Azure MFA, weź pod uwagę następujące informacje:

* Hasło aplikacji jest weryfikowane przez usługę Azure AD i w związku z tym pomija Federacji. Federacja jest używana tylko podczas konfigurowania haseł aplikacji. W przypadku użytkowników federacyjnych (SSO) hasła są przechowywane w IDENTYFIKATORze organizacji. Jeśli użytkownik opuści firmę, informacja ta musi być przepływa do identyfikatora organizacji przy użyciu narzędzia DirSync. Synchronizacja/usunięcie konta może potrwać do 3 godzin, co opóźnia wyłączenie/usunięcie haseł aplikacji w usłudze Azure AD.
* Ustawienia lokalnej kontroli dostępu klienta nie są uznawane przez hasło aplikacji.
* Nie jest dostępna funkcja rejestrowania/inspekcji lokalnego uwierzytelniania dla haseł aplikacji.
* Niektóre zaawansowane projekty architektury mogą wymagać użycia kombinacji nazwy użytkownika i haseł w organizacji oraz haseł aplikacji podczas przeprowadzania weryfikacji dwuetapowej z klientami, w zależności od tego, gdzie są uwierzytelniane. W przypadku klientów, którzy uwierzytelniają się w infrastrukturze lokalnej, należy użyć nazwy użytkownika i hasła organizacji. W przypadku klientów, którzy uwierzytelniają się w usłudze Azure AD, Użyj hasła aplikacji.
* Domyślnie użytkownicy nie mogą tworzyć haseł aplikacji. Jeśli musisz zezwolić użytkownikom na tworzenie haseł aplikacji, wybierz **opcję Zezwalaj użytkownikom na tworzenie haseł aplikacji do logowania się do aplikacji niekorzystających z przeglądarki** w obszarze Ustawienia usługi.

## <a name="next-steps"></a>Następne kroki

[Włącz Samoobsługowe resetowanie hasła dla organizacji](quickstart-sspr.md)

[Włączanie Multi-Factor Authentication platformy Azure dla Twojej organizacji](howto-mfa-getstarted.md)

[Włącz rejestrację połączoną w dzierżawie](howto-registration-mfa-sspr-combined.md)

[Dokumentacja konfiguracji metody uwierzytelniania użytkownika końcowego](https://aka.ms/securityinfoguide)
