---
title: Metody i funkcje uwierzytelniania — Azure Active Directory
description: Poznaj różne metody i funkcje uwierzytelniania dostępne w Azure Active Directory, aby pomóc w ulepszaniu i zabezpieczaniu zdarzeń związanych z logowaniem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 05283c02c2e5f95e22beb6fbeaad7a99e42ee6aa
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87540839"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Jakie metody uwierzytelniania i weryfikacji są dostępne w usłudze Azure Active Directory?

W ramach funkcji logowania dla kont w usłudze Azure Active Directory (Azure AD) istnieją różne sposoby, aby użytkownik mógł się uwierzytelnić. Nazwa użytkownika i hasło są najczęstszym sposobem, w jaki użytkownik mógłby historycznie podawać poświadczenia. Nowoczesne funkcje uwierzytelniania i zabezpieczeń w usłudze Azure AD umożliwiają uzupełnianie lub zamienienie podstawowego hasła przy użyciu dodatkowych metod uwierzytelniania.

Użytkownik w usłudze Azure AD może wybrać uwierzytelnianie przy użyciu jednej z następujących metod uwierzytelniania:

* Tradycyjna nazwa użytkownika i hasło
* Logowanie bezhasło aplikacji Microsoft Authenticator
* Token sprzętowy OATH lub klucz zabezpieczeń FIDO2
* Logowanie przy użyciu hasła opartego na programie SMS

Wiele kont w usłudze Azure AD jest włączonych do samoobsługowego resetowania hasła (SSPR) lub Multi-Factor Authentication platformy Azure. Te funkcje obejmują dodatkowe metody weryfikacji, takie jak rozmowa telefoniczna lub pytania zabezpieczające. Zaleca się, aby użytkownicy musieli zarejestrować wiele metod weryfikacji. Jeśli jedna metoda nie jest dostępna dla użytkownika, może zdecydować się na uwierzytelnienie przy użyciu innej metody.

W poniższej tabeli opisano, jakie metody są dostępne dla uwierzytelniania podstawowego lub pomocniczego:

| Metoda | Uwierzytelnianie podstawowe | Uwierzytelnianie pomocnicze |
| --- | --- | --- |
| [Hasło](#password) | Tak | |
| [Aplikacja Microsoft Authenticator](#microsoft-authenticator-app) | Tak (wersja zapoznawcza) | Uwierzytelnianie MFA i SSPR |
| [Klucze zabezpieczeń FIDO2 (wersja zapoznawcza)](#fido2-security-keys) | Tak | Tylko MFA |
| [Tokeny oprogramowania OATH](#oath-software-tokens) | Nie | Funkcja |
| [Tokeny sprzętowe OATH (wersja zapoznawcza)](#oath-hardware-tokens-preview) | Tak | Funkcja |
| [SMS](#phone-options) | Tak (wersja zapoznawcza) | Uwierzytelnianie MFA i SSPR |
| [Połączenie głosowe](#phone-options) | Nie | Uwierzytelnianie MFA i SSPR |
| [Pytania zabezpieczające](#security-questions) | Nie | Tylko SSPR |
| [Adres e-mail](#email-address) | Nie | Tylko SSPR |
| [Hasła aplikacji](#app-passwords) | Nie | MFA tylko w niektórych przypadkach |

W tym artykule opisano różne metody uwierzytelniania i weryfikacji dostępne w usłudze Azure AD oraz określone ograniczenia lub ograniczenia.

![Metody uwierzytelniania używane na ekranie logowania](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>Hasło

Hasło usługi Azure AD jest często jedną z podstawowych metod uwierzytelniania. Nie można wyłączyć metody uwierzytelniania hasła.

Nawet jeśli używasz metody uwierzytelniania, takiej jak [Logowanie oparte na programie SMS](howto-authentication-sms-signin.md) , gdy użytkownik nie używa hasła do podpisywania, hasło pozostaje jako dostępna metoda uwierzytelniania.

## <a name="microsoft-authenticator-app"></a>Aplikacja Microsoft Authenticator

Aplikacja Authenticator zapewnia dodatkowy poziom zabezpieczeń dla konta służbowego usługi Azure AD lub do konto Microsoft i jest dostępny dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594)i [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Za pomocą aplikacji Microsoft Authenticator użytkownicy mogą uwierzytelniać się w trybie bez hasła podczas logowania lub jako dodatkowa opcja weryfikacji podczas samoobsługowego resetowania hasła (SSPR) lub zdarzeń Multi-Factor Authentication platformy Azure.

Użytkownicy mogą otrzymać powiadomienie za pomocą aplikacji mobilnej do zatwierdzenia lub odmowy lub użyć aplikacji Authenticator do wygenerowania kodu weryfikacyjnego OATH, który można wprowadzić w interfejsie logowania. Jeśli włączysz powiadomienie i kod weryfikacyjny, użytkownicy rejestrujący aplikację uwierzytelniania mogą użyć dowolnej metody do zweryfikowania swojej tożsamości.

Aby użyć aplikacji uwierzytelniania przy użyciu monitu logowania zamiast kombinacji nazwy użytkownika i hasła, zobacz [Włączanie logowania bez hasła przy użyciu aplikacji Microsoft Authenticator (wersja zapoznawcza)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Użytkownicy nie mają możliwości zarejestrowania aplikacji mobilnej, gdy włączą SSPR. Zamiast tego użytkownicy mogą rejestrować swoją aplikację mobilną w programie [https://aka.ms/mfasetup](https://aka.ms/mfasetup) lub w ramach rejestracji informacji o zabezpieczeniach w usłudze [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

### <a name="notification-through-mobile-app"></a>Powiadomienie za poorednictwem aplikacji mobilnej

Aplikacja Authenticator może pomóc zapobiec nieautoryzowanemu dostępowi do kont i zatrzymywać fałszywe transakcje przez wypychanie powiadomienia do telefonu lub tabletu. Użytkownicy wyświetlają powiadomienie i jeśli są wiarygodne, wybierz pozycję **Weryfikuj**. W przeciwnym razie można wybrać opcję **Odmów**.

![Zrzut ekranu przedstawiający przykładowy monit przeglądarki sieci Web dla powiadomienia aplikacji uwierzytelniania w celu ukończenia procesu logowania](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Jeśli Twoja organizacja ma pracę pracowników lub podróżujących z Chin, *powiadomienie za pomocą metody aplikacji mobilnej* na urządzeniach z systemem Android nie działa w tym kraju/regionie, ponieważ usługi Google Play (w tym powiadomienia wypychane) są blokowane w regionie. Jednak powiadomienia dla systemu iOS działają. W przypadku urządzeń z systemem Android należy udostępnić alternatywną metodę uwierzytelniania dla tych użytkowników.

### <a name="verification-code-from-mobile-app"></a>Kod weryfikacyjny z aplikacji mobilnej

Aplikacja Authenticator może służyć jako token oprogramowania do wygenerowania kodu weryfikacyjnego OATH. Po wprowadzeniu nazwy użytkownika i hasła wprowadzasz kod dostarczony przez aplikację Authenticator do interfejsu logowania. Kod weryfikacyjny stanowi drugą formę uwierzytelniania.

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji uwierzytelniania, takich jak Microsoft Authenticator aplikacji skonfigurowanych do użycia w dowolnym momencie.

> [!WARNING]
> Aby zapewnić najwyższy poziom zabezpieczeń dla samoobsługowego resetowania hasła, gdy do resetowania jest wymagana tylko jedna metoda, kod weryfikacyjny jest jedyną opcją dostępną dla użytkowników.
>
> Gdy wymagane są dwie metody, użytkownicy mogą resetować przy użyciu powiadomienia lub kodu weryfikacyjnego oprócz innych włączonych metod.

## <a name="fido2-security-keys"></a>FIDO2 klucze zabezpieczeń

FIDO (Fast IDentity online) Alliance pomaga wspierać otwarte standardy uwierzytelniania i zmniejszać użytkowników haseł jako formy uwierzytelniania. FIDO2 to najnowszy standard, który obejmuje Standard Uwierzytelnianie sieci Web (WebAuthn).

Aby użyć kluczy zabezpieczeń FIDO2 w monicie logowania zamiast kombinacji nazwy użytkownika i hasła, zobacz [Włączanie logowania za pomocą klucza zabezpieczeń FIDO2 (wersja zapoznawcza)](howto-authentication-passwordless-security-key.md).

Użytkownicy mogą rejestrować i wybierać klucz zabezpieczeń FIDO2 w interfejsie logowania jako główny sposób uwierzytelniania. Te klucze zabezpieczeń FIDO2 są zazwyczaj urządzeniami USB, ale mogą również korzystać z połączenia Bluetooth lub NFC. W przypadku urządzenia sprzętowego, które obsługuje uwierzytelnianie, zabezpieczenia konta są zwiększane, ponieważ nie ma hasła, które może być ujawnione lub odgadnąć.

Klucze zabezpieczeń FIDO2 w usłudze Azure AD są obecnie dostępne w wersji zapoznawczej. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

## <a name="oath-tokens"></a>Tokeny OATH

TOTP OATH (oparte na czasie, hasło jednorazowe) to otwarty standard, który określa sposób generowania kodów haseł jednorazowych (OTP). TOTP OATH można zaimplementować przy użyciu oprogramowania lub sprzętu w celu wygenerowania kodów. Usługa Azure AD nie obsługuje HOTP OATH, innego standardu generowania kodu.

### <a name="oath-software-tokens"></a>Tokeny oprogramowania OATH

Tokeny OATH oprogramowania są zazwyczaj aplikacjami, takimi jak aplikacja Microsoft Authenticator i inne aplikacje uwierzytelniające. Usługa Azure AD generuje klucz tajny lub inicjator, który jest wprowadzany do aplikacji i użyty do wygenerowania każdego uwierzytelniania OTP.

Aplikacja Authenticator automatycznie generuje kody po skonfigurowaniu do wykonywania powiadomień wypychanych, aby użytkownik miał kopię zapasową nawet wtedy, gdy ich urządzenie nie ma łączności. Można również użyć aplikacji innych firm, które używają TOTP OATH do generowania kodów.

Niektóre tokeny sprzętowe TOTP OATH są programowalne, co oznacza, że nie są one dostarczane z kluczem tajnym lub wstępnie zaprogramowanym inicjatorem. Te programowalne tokeny sprzętowe można skonfigurować przy użyciu klucza tajnego lub inicjatora uzyskanego w ramach przepływu instalacji tokenu oprogramowania. Klienci mogą zakupić te tokeny od wybranego dostawcy i używać klucza tajnego lub inicjatora w procesie instalacji dostawcy.

### <a name="oath-hardware-tokens-preview"></a>Tokeny sprzętowe OATH (wersja zapoznawcza)

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

Po poprawnym sformatowaniu pliku CSV administrator może następnie zalogować się do Azure Portal, przejść do **Azure Active Directory > zabezpieczenia > MFA > tokeny Oath**i przekazać plik CSV.

Proces może potrwać kilka minut, w zależności od rozmiaru pliku CSV. Wybierz przycisk **Odśwież** , aby uzyskać bieżący stan. W przypadku wystąpienia błędów w pliku można pobrać plik CSV, który zawiera listę błędów, które należy rozwiązać. Nazwy pól w pobranym pliku CSV różnią się od przekazanej wersji.

Po rozwiązaniu jakichkolwiek błędów administrator może aktywować każdy klucz, wybierając pozycję **Aktywuj** dla tokenu i wprowadzając uwierzytelnianie OTP wyświetlane na tokenie.

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji uwierzytelniania, takich jak Microsoft Authenticator aplikacji skonfigurowanych do użycia w dowolnym momencie.

## <a name="phone-options"></a>Opcje telefonu

Do bezpośredniego uwierzytelniania przy użyciu wiadomości tekstowej można [skonfigurować i umożliwić użytkownikom uwierzytelnianie SMS (wersja zapoznawcza)](howto-authentication-sms-signin.md). Logowanie oparte na programie SMS jest doskonałe dla pracowników frontonu. Korzystając z logowania opartego na programie SMS, użytkownicy nie muszą znać nazwy użytkownika i hasła w celu uzyskania dostępu do aplikacji i usług. Użytkownik wprowadza swój zarejestrowany numer telefonu komórkowego, odbiera wiadomość SMS z kodem weryfikacyjnym i wprowadza ją w interfejsie logowania.

Użytkownicy mogą również weryfikować się za pomocą telefonu komórkowego lub telefonu biurowego jako dodatkowej formy uwierzytelniania używanej podczas korzystania z usługi Azure Multi-Factor Authentication lub samoobsługowego resetowania hasła (SSPR).

Aby działały prawidłowo, numery telefonów muszą mieć format *+ CountryCode*, na przykład *+ 1 4251234567*.

> [!NOTE]
> Musi być odstęp między kodem kraju/regionu i numerem telefonu.
>
> Resetowanie hasła nie obsługuje rozszerzeń telefonu. Nawet w formacie *4251234567X12345 + 1* rozszerzenia są usuwane przed umieszczeniem wywołania.

### <a name="mobile-phone-verification"></a>Weryfikacja telefonu komórkowego

W przypadku usługi Azure Multi-Factor Authentication lub SSPR użytkownicy mogą zdecydować się na otrzymanie wiadomości SMS z kodem weryfikacyjnym, aby wejść do interfejsu logowania, lub odebrać połączenie telefoniczne z monitem o wprowadzenie zdefiniowanego kodu PIN.

Jeśli użytkownicy nie chcą, aby numer telefonu komórkowego był widoczny w katalogu, ale chcą używać go do resetowania haseł, Administratorzy nie zapełnią numeru telefonu w katalogu. Zamiast tego użytkownicy powinni zapełniać atrybut **telefonu uwierzytelniania** za pośrednictwem rejestracji informacji o zabezpieczeniach w usłudze [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . Administratorzy mogą zobaczyć te informacje w profilu użytkownika, ale nie zostały opublikowane w innym miejscu.

![Zrzut ekranu przedstawiający Azure Portal pokazujący metody uwierzytelniania z wypełnionym numerem telefonu](media/concept-authentication-methods/user-authentication-methods.png)

Firma Microsoft nie gwarantuje spójnego dostarczania wiadomości SMS lub głosowego opartego na usłudze Azure Multi-Factor Authentication na podstawie tej samej liczby. W interesie naszych użytkowników możemy dodawać lub usuwać krótkie kody w dowolnym momencie, gdy wprowadzimy zmiany trasy w celu poprawy możliwości dostarczania wiadomości SMS. Firma Microsoft nie obsługuje krótkich kodów dla krajów/regionów poza Stany Zjednoczone i Kanadę.

#### <a name="text-message-verification"></a>Weryfikacja wiadomości tekstowych

Za pomocą weryfikacji wiadomości tekstowych podczas SSPR lub platformy Azure Multi-Factor Authentication usługa SMS jest wysyłana na numer telefonu komórkowego zawierający kod weryfikacyjny. Aby ukończyć proces logowania, podany kod weryfikacyjny jest wprowadzany do interfejsu logowania.

#### <a name="phone-call-verification"></a>Weryfikacja połączenia telefonicznego

Podczas weryfikacji połączeń telefonicznych podczas SSPR lub platformy Azure Multi-Factor Authentication automatyczne połączenie głosowe jest nawiązywane z numerem telefonu zarejestrowanym przez użytkownika. Aby ukończyć proces logowania, użytkownik otrzymuje monit o wprowadzenie numeru PIN, po którym następuje znak # na klawiaturze.

### <a name="office-phone-verification"></a>Weryfikacja telefonu służbowego

Atrybut telefon biurowy jest zarządzany przez administratora usługi Azure AD i nie może być zarejestrowany przez samego użytkownika.

Podczas weryfikacji połączeń telefonicznych podczas SSPR lub platformy Azure Multi-Factor Authentication automatyczne połączenie głosowe jest nawiązywane z numerem telefonu zarejestrowanym przez użytkownika. Aby ukończyć proces logowania, użytkownik otrzymuje monit o wprowadzenie numeru PIN, po którym następuje znak # na klawiaturze.

### <a name="troubleshooting-phone-options"></a>Rozwiązywanie problemów z opcjami telefonu

Jeśli masz problemy z uwierzytelnianiem za pomocą telefonu w usłudze Azure AD, zapoznaj się z następującymi krokami rozwiązywania problemów:

* Zablokowany identyfikator obiektu wywołującego na pojedynczym urządzeniu.
   * Przejrzyj wszystkie zablokowane numery skonfigurowane na urządzeniu.
* Błędny numer telefonu lub nieprawidłowy kod kraju/regionu lub pomyłek między osobistym numerem telefonu a numerem telefonu służbowego.
   * Rozwiązywanie problemów z obiektem użytkownika i skonfigurowanymi metodami uwierzytelniania. Upewnij się, że zarejestrowano poprawne numery telefonów.
* Wprowadzono nieprawidłowy kod PIN.
   * Upewnij się, że użytkownik użył poprawnego numeru PIN zarejestrowanego dla swojego konta.
* Połączenie przekazane do poczty głosowej.
   * Upewnij się, że użytkownik ma włączony telefon i że usługa jest dostępna w swoim obszarze, lub użyj alternatywnej metody.
* Użytkownik jest zablokowany
   * Skontaktuj się z administratorem usługi Azure AD w Azure Portal.
* Wiadomość SMS nie jest subskrybowana na urządzeniu.
   * Użytkownik zmienia metody lub aktywuje wiadomość SMS na urządzeniu.
* Uszkodzonych dostawców telekomunikacyjnych, takich jak nie wykryto wejścia telefonu, brakujące sygnały DTMF, zablokowany identyfikator rozmówcy na wielu urządzeniach lub zablokowany program SMS na wielu urządzeniach.
   * Firma Microsoft używa wielu dostawców Telecom do przesyłania połączeń telefonicznych i wiadomości SMS w celu uwierzytelnienia. Jeśli zobaczysz którykolwiek z powyższych problemów, poproś użytkownika o użycie metody co najmniej pięć razy w ciągu 5 minut i udostępnienie informacji o tym użytkowniku podczas kontaktowania się z pomocą techniczną firmy Microsoft.

## <a name="security-questions"></a>Pytania zabezpieczające

Pytania zabezpieczające nie są używane jako metoda uwierzytelniania podczas zdarzenia logowania. Zamiast tego pytania zabezpieczające mogą być używane podczas procesu samoobsługowego resetowania hasła (SSPR) w celu potwierdzenia, kim jesteś. Konta administratorów nie mogą używać pytań zabezpieczających jako metody weryfikacji w programie SSPR.

Gdy użytkownicy rejestrują się pod kątem usługi SSPR, są monitowani o wybranie metod uwierzytelniania do użycia. Jeśli zdecydują się na korzystanie z pytań zabezpieczających, wybierają się z zestawu pytań na potrzeby monitowania, a następnie zapewniają własne odpowiedzi.

![Zrzut ekranu przedstawiający Azure Portal pokazujący metody uwierzytelniania i opcje dotyczące pytań zabezpieczających](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Pytania zabezpieczające są przechowywane prywatnie i bezpiecznie w obiekcie użytkownika w katalogu i mogą być udzielane tylko przez użytkowników podczas rejestracji. Nie ma możliwości, aby administrator mógł odczytywać lub modyfikować pytania lub odpowiedzi użytkownika.

Pytania zabezpieczające mogą być mniej bezpieczne niż inne metody, ponieważ niektóre osoby mogą znać odpowiedzi na pytania innego użytkownika. W przypadku używania pytań zabezpieczających z SSPR zaleca się ich użycie w połączeniu z inną metodą. Użytkownik może zostać poproszony o użycie aplikacji Microsoft Authenticator lub uwierzytelniania na telefonie, aby zweryfikować swoją tożsamość podczas procesu SSPR i wybierać pytania zabezpieczające tylko wtedy, gdy nie mają ich telefonu lub zarejestrowanego urządzenia.

### <a name="predefined-questions"></a>Wstępnie zdefiniowane pytania

Poniższe wstępnie zdefiniowane pytania zabezpieczające są dostępne do użycia jako metoda weryfikacji z SSPR. Wszystkie te pytania zabezpieczające są tłumaczone i lokalizowane do pełnego zestawu języków pakietu Office 365 na podstawie ustawień regionalnych przeglądarki użytkownika:

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
* Co to jest miesiąc i rok urodzinowy Twojego elementu równorzędnego? (na przykład listopad 1985)
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

### <a name="custom-security-questions"></a>Niestandardowe pytania zabezpieczające

Aby uzyskać dodatkową elastyczność, możesz zdefiniować własne niestandardowe pytania zabezpieczające. Maksymalna długość niestandardowego pytania zabezpieczającego to 200 znaków.

Niestandardowe pytania zabezpieczające nie są automatycznie lokalizowane, podobnie jak w przypadku domyślnych pytań zabezpieczających. Wszystkie pytania niestandardowe są wyświetlane w tym samym języku, w jakim są wprowadzane w administracyjnym interfejsie użytkownika, nawet jeśli ustawienia regionalne przeglądarki użytkownika są inne. Jeśli potrzebujesz zlokalizowanych pytań, musisz użyć wstępnie zdefiniowanych pytań.

### <a name="security-question-requirements"></a>Wymagania dotyczące zabezpieczeń

W przypadku domyślnych i niestandardowych pytań zabezpieczających obowiązują następujące wymagania i ograniczenia:

* Minimalny limit znaków odpowiedzi to trzy znaki.
* Maksymalny limit znaków odpowiedzi to 40 znaków.
* Użytkownicy nie mogą odpowiedzieć na te same pytania więcej niż jeden raz.
* Użytkownicy nie mogą udzielić odpowiedzi na więcej niż jedno pytanie.
* Dowolny zestaw znaków może służyć do definiowania pytań i odpowiedzi, w tym znaków Unicode.
* Liczba zdefiniowanych pytań musi być większa lub równa liczbie pytań, które były wymagane do zarejestrowania.

## <a name="email-address"></a>Adres e-mail

Nie można użyć adresu e-mail jako metody uwierzytelniania bezpośredniego. Adres e-mail jest dostępny tylko jako opcja weryfikacji dla samoobsługowego resetowania hasła (SSPR). W przypadku wybrania adresu e-mail podczas SSPR do użytkownika zostanie wysłana wiadomość e-mail w celu ukończenia procesu uwierzytelniania/weryfikacji.

Podczas rejestracji w usłudze SSPR użytkownik udostępnia adres e-mail do użycia. Zaleca się, aby użytkownicy korzystali z innego konta e-mail niż konto firmowe, aby upewnić się, że mogą uzyskać do niego dostęp podczas SSPR.

## <a name="app-passwords"></a>Hasła aplikacji

Niektóre starsze aplikacje nie korzystające z przeglądarki nie rozumieją przerw lub przerw w procesie uwierzytelniania. Jeśli użytkownik włączył funkcję uwierzytelniania wieloskładnikowego i próbuje użyć jednej z tych starszych aplikacji niekorzystających z przeglądarki, zazwyczaj nie można uwierzytelnić się. Hasło aplikacji umożliwia użytkownikom dalsze uwierzytelnianie przy użyciu starszych aplikacji niekorzystających z przeglądarki bez przeszkód.

Domyślnie użytkownicy nie mogą tworzyć haseł aplikacji. Jeśli musisz zezwolić użytkownikom na tworzenie haseł aplikacji, wybierz opcję **zezwól użytkownikom na tworzenie haseł aplikacji do logowania się do aplikacji niekorzystających z przeglądarki** w obszarze *Ustawienia usługi* dla właściwości Multi-Factor Authentication platformy Azure dla użytkownika.

![Zrzut ekranu przedstawiający Azure Portal, w którym są wyświetlane ustawienia usługi uwierzytelniania wieloskładnikowego, aby zezwolić użytkownikowi na hasła aplikacji](media/concept-authentication-methods/app-password-authentication-method.png)

Jeśli wymuszasz korzystanie z usługi Azure Multi-Factor Authentication przy użyciu zasad dostępu warunkowego, a nie za pośrednictwem uwierzytelniania wieloskładnikowego dla poszczególnych użytkowników, nie możesz tworzyć haseł aplikacji. Nowoczesne aplikacje, które korzystają z zasad dostępu warunkowego w celu kontrolowania dostępu, nie wymagają haseł aplikacji.

Jeśli Twoja organizacja jest federacyjnym logowaniem jednokrotnym (SSO) w usłudze Azure AD i używasz usługi Azure Multi-Factor Authentication, mają zastosowanie następujące zagadnienia:

* Hasło aplikacji jest weryfikowane przez usługę Azure AD, co spowoduje obejście Federacji. Federacja jest używana tylko podczas konfigurowania haseł aplikacji. W przypadku użytkowników federacyjnych (SSO) hasła są przechowywane w IDENTYFIKATORze organizacji. Jeśli użytkownik opuści firmę, informacja ta musi być przepływa do identyfikatora organizacji przy użyciu narzędzia DirSync. Synchronizacja zdarzeń wyłączenia lub usunięcia konta może potrwać do 3 godzin, co opóźnia wyłączenie/usunięcie haseł aplikacji w usłudze Azure AD.
* Lokalne ustawienia Access Control klienta nie są uznawane przez hasła aplikacji.
* W przypadku haseł aplikacji nie jest dostępna funkcja rejestrowania lokalnego uwierzytelniania ani inspekcji.
* Niektóre zaawansowane projekty architektury mogą wymagać użycia kombinacji nazwy użytkownika i haseł w organizacji oraz haseł aplikacji w przypadku korzystania z uwierzytelniania wieloskładnikowego w zależności od tego, gdzie są uwierzytelniane.
    * W przypadku klientów, którzy uwierzytelniają się w infrastrukturze lokalnej, należy użyć nazwy użytkownika i hasła organizacji.
    * W przypadku klientów, którzy uwierzytelniają się w usłudze Azure AD, Użyj hasła aplikacji.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zobacz Samouczek dotyczący samoobsługowego [resetowania hasła (SSPR)][tutorial-sspr] i [platformy Azure Multi-Factor Authentication][tutorial-azure-mfa].

Aby dowiedzieć się więcej o pojęciach SSPR, zobacz Jak działa funkcja samoobsługowego [resetowania hasła w usłudze Azure AD][concept-sspr].

Aby dowiedzieć się więcej na temat pojęć MFA, zobacz [jak działa usługa Azure Multi-Factor Authentication][concept-mfa].

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
