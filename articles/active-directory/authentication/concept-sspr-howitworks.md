---
title: Samoobsługowe resetowanie hasła głębokie szczegółowe — Azure Active Directory
description: Jak działa Samoobsługowe resetowanie hasła
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b19c80378aa40a7f791a3eb61130b013217ddee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74848582"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Jak to działa: Samoobsługowe resetowanie haseł w usłudze Azure AD

Jak działa Samoobsługowe resetowanie hasła (SSPR)? Co oznacza ta opcja w interfejsie? Kontynuuj odczytywanie, aby dowiedzieć się więcej o usłudze Azure Active Directory (Azure AD) SSPR.

## <a name="how-does-the-password-reset-portal-work"></a>Jak działa Portal resetowania haseł?

Gdy użytkownik przechodzi do portalu resetowania haseł, przepływ pracy zostaje wyłączony w celu określenia:

   * Jak ma być lokalizowana Strona?
   * Czy konto użytkownika jest prawidłowe?
   * Do jakiej organizacji należy użytkownik?
   * Gdzie jest zarządzane hasło użytkownika?
   * Czy użytkownik ma licencję na korzystanie z tej funkcji?

Przeczytaj poniższe kroki, aby dowiedzieć się więcej na temat logiki za stroną resetowania hasła:

1. Użytkownik wybierze link **nie można uzyskać dostępu do konta** lub przechodzi bezpośrednio do [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) .
   * W zależności od ustawień regionalnych przeglądarki środowisko jest renderowane w odpowiednim języku. Środowisko resetowania hasła jest zlokalizowane w tych samych językach, które obsługuje pakiet Office 365.
   * Aby wyświetlić Portal resetowania haseł w innym zlokalizowanym języku, Dołącz "? MKT =" na końcu adresu URL resetowania hasła, korzystając z przykładu zlokalizowanego na hiszpański [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .
2. Użytkownik wprowadza identyfikator użytkownika i przekazuje CAPTCHA.
3. Usługa Azure AD weryfikuje, czy użytkownik może korzystać z tej funkcji, wykonując następujące sprawdzenia:
   * Sprawdza, czy użytkownik ma włączoną tę funkcję i ma przypisaną licencję usługi Azure AD.
     * Jeśli użytkownik nie ma włączonej tej funkcji lub nie masz przypisanej licencji, użytkownik zostanie poproszony o skontaktowanie się z administratorem w celu zresetowania hasła.
   * Sprawdza, czy użytkownik ma odpowiednie metody uwierzytelniania zdefiniowane na swoim koncie zgodnie z zasadami administratora.
     * Jeśli zasady wymagają tylko jednej metody, to gwarantuje, że użytkownik ma odpowiednie dane zdefiniowane dla co najmniej jednej z metod uwierzytelniania włączonych przez zasady administratora.
       * Jeśli nie skonfigurowano metod uwierzytelniania, użytkownik powinien skontaktować się z administratorem w celu zresetowania hasła.
     * Jeśli zasady wymagają dwóch metod, to gwarantuje, że użytkownik ma odpowiednie dane zdefiniowane dla co najmniej dwóch metod uwierzytelniania włączonych przez zasady administratora.
       * Jeśli nie skonfigurowano metod uwierzytelniania, użytkownik powinien skontaktować się z administratorem w celu zresetowania hasła.
     * Jeśli do użytkownika jest przypisana rola administratora platformy Azure, wymuszane są silne zasady haseł z dwoma bramami. Więcej informacji na temat tych zasad można znaleźć w sekcji [Zasady resetowania różnic zasad](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Sprawdza, czy hasło użytkownika jest zarządzane lokalnie (federacyjne, uwierzytelnianie przekazywane lub skrót hasła).
     * Jeśli zostanie wdrożony Zapis zwrotny, a hasło użytkownika jest zarządzane lokalnie, użytkownik może przeprowadzić uwierzytelnianie i resetowanie hasła.
     * Jeśli zapis zwrotny nie zostanie wdrożony, a hasło użytkownika jest zarządzane lokalnie, użytkownik zostanie poproszony o skontaktowanie się z administratorem w celu zresetowania hasła.
4. Jeśli okaże się, że użytkownik może pomyślnie zresetować swoje hasło, użytkownik zostanie przeprowadzony przez proces resetowania.

## <a name="authentication-methods"></a>Metody uwierzytelniania

Jeśli SSPR jest włączona, należy wybrać co najmniej jedną z następujących opcji dla metod uwierzytelniania. Czasami możesz usłyszeć te opcje określane jako "bramy". Zdecydowanie zalecamy **wybranie co najmniej dwóch metod uwierzytelniania** , aby użytkownicy mieli większą elastyczność w przypadku, gdy ich potrzebują. Dodatkowe szczegóły dotyczące metod wymienionych poniżej można znaleźć w artykule [co to są metody uwierzytelniania?](concept-authentication-methods.md).

* Powiadomienie aplikacji mobilnej
* Kod aplikacji mobilnej
* Poczta e-mail
* Telefon komórkowy
* Telefon służbowy
* Pytania zabezpieczające

Użytkownicy mogą resetować swoje hasła tylko wtedy, gdy mają one dane znajdujące się w metodach uwierzytelniania włączonych przez administratora.

> [!IMPORTANT]
> Począwszy od marca 2019 opcje połączenia telefonicznego nie będą dostępne dla usługi MFA i SSPR użytkowników w bezpłatnych/bezpłatnych dzierżawach Azure AD. Ta zmiana nie ma wpływu na wiadomości SMS. Połączenie telefoniczne będzie nadal dostępne dla użytkowników w płatnych dzierżawach usługi Azure AD. Ta zmiana ma wpływ tylko na dzierżawy usługi Azure AD bezpłatne/próbne.

> [!WARNING]
> Konta przypisane do ról administratora platformy Azure będą wymagane do korzystania z metod zgodnie z definicją w sekcji [administrator resetuje różnice między zasadami](concept-sspr-policy.md#administrator-reset-policy-differences).

![Wybór metod uwierzytelniania w Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Wymagana liczba metod uwierzytelniania

Ta opcja określa minimalną liczbę dostępnych metod uwierzytelniania lub bram, które użytkownik musi wykonać, aby zresetować lub odblokować hasło. Można ustawić jeden lub dwa.

Użytkownicy mogą zdecydować się na dostarczenie dodatkowych metod uwierzytelniania, jeśli administrator włączy tę metodę uwierzytelniania.

Jeśli użytkownik nie ma zarejestrowanej minimalnej wymaganej metody, zobaczy stronę błędu, która kieruje je do żądania resetowania hasła przez administratora.

#### <a name="mobile-app-and-sspr"></a>Aplikacja mobilna i SSPR

W przypadku korzystania z aplikacji mobilnej, takiej jak aplikacja Microsoft Authenticator, jako metody resetowania hasła należy pamiętać o następujących zastrzeżeniach:

* Gdy administratorzy wymagają użycia jednej metody do resetowania hasła, kod weryfikacyjny jest jedyną dostępną opcją.
* Gdy administratorzy wymagają dwóch metod do zresetowania hasła, użytkownicy będą mogli korzystać **z dowolnego z** innych włączonych metod przy użyciu powiadomienia **lub** kodu weryfikacyjnego.

| Liczba metod wymaganych do zresetowania | Jeden | Dwa |
| :---: | :---: | :---: |
| Dostępne funkcje aplikacji mobilnej | Kod | Kod lub powiadomienie |

Użytkownicy nie mają możliwości zarejestrowania swojej aplikacji mobilnej podczas rejestrowania do samoobsługowego resetowania hasła [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Użytkownicy mogą rejestrować swoją aplikację mobilną w usłudze [https://aka.ms/mfasetup](https://aka.ms/mfasetup) lub w nowej wersji zapoznawczej rejestracji informacji o zabezpieczeniach [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!WARNING]
> Aby użytkownicy mogli uzyskać dostęp do nowego środowiska w programie, należy włączyć [zbieżną rejestrację na potrzeby samoobsługowego resetowania haseł i usługi Azure Multi-Factor Authentication (publiczna wersja zapoznawcza)](concept-registration-mfa-sspr-converged.md) [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> Nie można wybrać aplikacji Authenticator jako jedynej metody uwierzytelniania podczas konfigurowania zasad 1 bramy. Podobnie w przypadku konfigurowania zasad 2-bram nie można wybrać aplikacji Authenticator i tylko jednej metody dodatkowej.
> Następnie podczas konfigurowania zasad SSPR, które obejmują aplikację Authenticator jako metodę, należy wybrać co najmniej dodatkową metodę podczas konfigurowania zasad 1 bramy i należy wybrać co najmniej dwie dodatkowe metody podczas konfigurowania zasad dotyczących 2 bram.
> Przyczyną tego wymagania jest fakt, że bieżące środowisko rejestracji SSPR nie obejmuje opcji zarejestrowania aplikacji uwierzytelniającej. Opcja zarejestrowania aplikacji Authenticator jest dołączona do nowej, [zbieżnej rejestracji do samoobsługowego resetowania hasła i Multi-Factor Authentication platformy Azure (publiczna wersja zapoznawcza)](concept-registration-mfa-sspr-converged.md).
> Zezwalanie zasadom, które używają tylko aplikacji Authenticator (dla zasad 1-bramowych) lub aplikacji uwierzytelniania i tylko jednej dodatkowej metody (dla zasad 2-bram), może spowodować, że użytkownicy będą mogli rejestrować się w usłudze SSPR, dopóki nie zostaną skonfigurowani do korzystania z nowego środowiska rejestracji.

### <a name="change-authentication-methods"></a>Zmień metody uwierzytelniania

Jeśli zaczniesz od zasad, które mają tylko jedną wymaganą metodę uwierzytelniania na potrzeby resetowania lub odblokowywania, i zmienisz ją na dwie metody, co się dzieje?

| Liczba zarejestrowanych metod | Wymagana liczba metod | Wynik |
| :---: | :---: | :---: |
| co najmniej 1 | 1 | **Możliwość** resetowania lub odblokowywania |
| 1 | 2 | **Nie można** zresetować lub odblokować |
| 2 lub więcej | 2 | **Możliwość** resetowania lub odblokowywania |

Jeśli zmienisz typy metod uwierzytelniania, których użytkownik może użyć, możesz przypadkowo uniemożliwić użytkownikom korzystanie z usługi SSPR, jeśli nie mają one minimalnej ilości dostępnych danych.

Przykład:
1. W przypadku oryginalnych zasad skonfigurowano dwie wymagane metody uwierzytelniania. Używa on tylko numeru telefonu biurowego i pytań zabezpieczających. 
2. Administrator zmienia zasady, aby nie korzystały już z pytań zabezpieczających, ale umożliwia korzystanie z telefonu komórkowego i alternatywnej poczty e-mail.
3. Użytkownicy bez pola telefonu komórkowego lub alternatywnego adresu e-mail nie mogą zresetować swoich haseł.

## <a name="registration"></a>Rejestracja

### <a name="require-users-to-register-when-they-sign-in"></a>Wymagaj od użytkowników zarejestrowania się podczas logowania

Włączenie tej opcji wymaga, aby użytkownik mógł ukończyć rejestrację resetowania hasła, jeśli zalogują się do aplikacji za pomocą usługi Azure AD. Ten przepływ pracy obejmuje następujące aplikacje:

* Office 365
* Azure Portal
* Panel dostępu
* Aplikacje federacyjne
* Aplikacje niestandardowe korzystające z usługi Azure AD

Gdy wymaganie rejestracji jest wyłączone, użytkownicy mogą rejestrować się ręcznie. Mogą albo odwiedzać [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) , albo wybrać link **zarejestruj do resetowania hasła** na karcie **profil** w panelu dostępu.

> [!NOTE]
> Użytkownicy mogą odrzucić Portal rejestracji resetowania haseł, wybierając pozycję **Anuluj** lub zamykając okno. Ale są monitowani o zarejestrowanie się przy każdym logowaniu do momentu zakończenia rejestracji.
>
> Ten przerwanie nie przerywa połączenia użytkownika, jeśli jest już zalogowany.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Ustaw liczbę dni, po której użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania

Ta opcja określa okres między ustawieniem i ponownym potwierdzeniem informacji uwierzytelniania i jest dostępny tylko po włączeniu opcji Wymagaj, **aby użytkownicy rejestrowali się podczas logowania** .

Prawidłowe wartości to od 0 do 730 dni, a wartość "0" oznacza, że użytkownicy nigdy nie będą monitowani o ponowne potwierdzenie swoich informacji uwierzytelniania.

## <a name="notifications"></a>Powiadomienia

### <a name="notify-users-on-password-resets"></a>Czy powiadamiać użytkowników o resetowaniu hasła?

Jeśli ta opcja jest ustawiona na **tak**, użytkownicy resetowania hasła otrzymają wiadomość e-mail z powiadomieniem o zmianie hasła. Wiadomość e-mail jest wysyłana za pośrednictwem portalu SSPR do swoich głównych i alternatywnych adresów e-mail, które znajdują się w pliku w usłudze Azure AD. Nikt inny nie jest powiadamiany o zdarzeniu resetowania.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Powiadom wszystkich administratorów, gdy inni administratorzy zresetują swoje hasła

Jeśli ta opcja jest ustawiona na **tak**, *Wszyscy administratorzy* otrzymają wiadomość e-mail na swój podstawowy adres E-mail w pliku w usłudze Azure AD. Wiadomość e-mail powiadamia o zmianie hasła przez innego administratora przy użyciu SSPR.

Przykład: w środowisku znajdują się cztery Administratorzy. Administrator A resetuje swoje hasło przy użyciu SSPR. Administratorzy B, C i D otrzymują wiadomość e-mail z alertami dotyczącymi resetowania hasła.

## <a name="on-premises-integration"></a>Integracja lokalna

W przypadku instalowania, konfigurowania i włączania Azure AD Connect dostępne są następujące dodatkowe opcje dla integracji lokalnych. Jeśli te opcje są wyszarzone, to zapisanie zwrotne nie zostało prawidłowo skonfigurowane. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapisywania zwrotnego haseł](howto-sspr-writeback.md).

![Weryfikowanie zapisywania zwrotnego haseł jest włączone i działa][Writeback]

Ta strona zapewnia szybki stan lokalnego klienta zapisywania zwrotnego, w oparciu o bieżącą konfigurację zostanie wyświetlony jeden z następujących komunikatów:

* Lokalny klient zapisywania zwrotnego jest uruchomiony.
* Usługa Azure AD jest w trybie online i jest połączona z lokalnym klientem zapisywania zwrotnego. Jednak wygląda na to, że zainstalowana wersja Azure AD Connect jest nieaktualna. Rozważ [uaktualnienie Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) , aby mieć pewność, że dysponujesz najnowszymi funkcjami łączności i ważnymi poprawkami błędów.
* Niestety, nie możemy sprawdzić stanu lokalnego klienta zapisywania zwrotnego, ponieważ zainstalowana wersja Azure AD Connect jest nieaktualna. [Uaktualnij Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) , aby sprawdzić stan połączenia.
* Niestety, wygląda na to, że nie możemy teraz połączyć się z lokalnym klientem funkcji zapisywania zwrotnego. [Rozwiązywanie problemów z Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) , aby przywrócić połączenie.
* Niestety, nie można nawiązać połączenia z lokalnym klientem zapisywania zwrotnego, ponieważ nie skonfigurowano poprawnie funkcji zapisywania zwrotnego haseł. [Skonfiguruj funkcję zapisywania zwrotnego haseł](howto-sspr-writeback.md) , aby przywrócić połączenie.
* Niestety, wygląda na to, że nie możemy teraz połączyć się z lokalnym klientem funkcji zapisywania zwrotnego. Może to być spowodowane tymczasowymi problemami na naszym końcu. Jeśli problem będzie się powtarzał, [Rozwiązywanie problemów Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) , aby przywrócić połączenie.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Zapisuj hasła wstecz do katalogu lokalnego

Ten formant określa, czy funkcja zapisywania zwrotnego haseł jest włączona dla tego katalogu. Jeśli zapis zwrotny jest włączony, wskazuje stan lokalnej usługi zapisywania zwrotnego. Ten formant jest przydatny, jeśli chcesz tymczasowo wyłączyć funkcję zapisywania zwrotnego haseł bez konieczności ponownego konfigurowania Azure AD Connect.

* Jeśli przełącznik ma wartość **tak**, funkcja zapisywania zwrotnego jest włączona, a federacyjne, uwierzytelnianie przekazywane lub skrót hasła użytkownicy zsynchronizowani mogą resetować swoje hasła.
* Jeśli przełącznik jest ustawiony na wartość **nie**, zapisywanie zwrotne jest wyłączone, a federacyjne, uwierzytelnianie przekazywane lub skrót hasła użytkownicy zsynchronizowani nie mogą resetować haseł.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Zezwalaj użytkownikom na odblokowywanie kont bez resetowania ich hasła

Ta kontrolka określa, czy użytkownicy, którzy odwiedzają Portal resetowania haseł, powinni mieć możliwość odblokowania lokalnych kont Active Directory bez konieczności resetowania hasła. Domyślnie usługa Azure AD odblokowuje konta, gdy wykonuje Resetowanie hasła. To ustawienie służy do rozdzielania tych dwóch operacji.

* W przypadku wybrania opcji **tak**użytkownicy mogą zresetować swoje hasło i odblokować konto lub odblokować konto bez konieczności resetowania hasła.
* Jeśli ustawisz wartość **nie**, użytkownicy będą mogli tylko wykonywać połączone operacje resetowania haseł i odblokowywania kont.

### <a name="on-premises-active-directory-password-filters"></a>Filtry haseł Active Directory lokalnych

Funkcja samoobsługowego resetowania hasła w usłudze Azure AD wykonuje równoważne Resetowanie hasła zainicjowane przez administratora w Active Directory. Jeśli używasz filtru hasła innej firmy w celu wymuszania niestandardowych reguł haseł, a w trakcie samoobsługowego resetowania hasła w usłudze Azure AD jest wymagane sprawdzanie tego filtru haseł, upewnij się, że rozwiązanie filtru haseł innych firm jest skonfigurowane do zastosowania w scenariuszu resetowania hasła administratora. [Ochrona hasłem usługi Azure AD dla systemu Windows Server Active Directory](concept-password-ban-bad-on-premises.md) jest domyślnie obsługiwana.

## <a name="password-reset-for-b2b-users"></a>Resetowanie hasła dla użytkowników B2B

Resetowanie i zmiana hasła są w pełni obsługiwane we wszystkich konfiguracjach między firmami (B2B). Resetowanie hasła użytkownika B2B jest obsługiwane w następujących trzech przypadkach:

* **Użytkownicy z organizacji partnerskiej z istniejącą dzierżawą usługi Azure AD**: Jeśli organizacja, z której korzystasz, ma istniejącą dzierżawę usługi Azure AD, wszystkie *Zasady resetowania hasła są włączone dla tej dzierżawy*. Aby Resetowanie hasła działało, organizacja partnerska musi upewnić się, że usługa Azure AD SSPR jest włączona. Nie ma dodatkowej opłaty dla klientów korzystających z pakietu Office 365 i można ją włączyć, wykonując czynności opisane w przewodniku [wprowadzenie do zarządzania hasłami](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) .
* **Użytkownicy, którzy zarejestrują** się w ramach rejestracji samoobsługowej: Jeśli Twoja organizacja, której używasz, korzysta z funkcji [rejestracji samoobsługowej](../users-groups-roles/directory-self-service-signup.md) w celu uzyskania dzierżawy, zezwolimy im na zresetowanie hasła przy użyciu zarejestrowanej wiadomości e-mail.
* **Użytkownicy B2B**: Wszyscy nowi użytkownicy B2B utworzeni za pomocą nowych [możliwości B2B usługi Azure AD](../active-directory-b2b-what-is-azure-ad-b2b.md) będą mogli zresetować swoje hasła przy użyciu wiadomości e-mail zarejestrowanej podczas procesu zapraszania.

Aby przetestować ten scenariusz, przejdź do https://passwordreset.microsoftonline.com jednego z tych użytkowników partnerskich. Jeśli zdefiniowano alternatywną wiadomość e-mail lub adres e-mail uwierzytelniania, Resetowanie hasła działa zgodnie z oczekiwaniami.

> [!NOTE]
> Konta Microsoft, które otrzymały dostęp gościa do dzierżawy usługi Azure AD, takich jak te z Hotmail.com, Outlook.com lub innych osobistych adresów e-mail, nie mogą korzystać z usługi Azure AD SSPR. Muszą zresetować swoje hasła przy użyciu informacji znajdujących się w artykule, [gdy nie możesz zalogować się do konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.

## <a name="next-steps"></a>Następne kroki

Poniższe artykuły zawierają dodatkowe informacje dotyczące resetowania haseł za pomocą usługi Azure AD:

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmiana hasła](../user-help/active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś jest zerwane. Jak mogę Rozwiązywanie problemów z SSPR?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Dostępne metody uwierzytelniania w usłudze Azure AD i wymagane ilości"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "Konfiguracja zapisywania zwrotnego haseł integracji lokalnej i informacje dotyczące rozwiązywania problemów"
