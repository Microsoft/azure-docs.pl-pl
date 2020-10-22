---
title: Samoobsługowe resetowanie hasła głębokie szczegółowe — Azure Active Directory
description: Jak działa Samoobsługowe resetowanie hasła
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa1c2627917bfe386c488470f6a78db4c51f2ec
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92363678"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Jak to działa: Samoobsługowe resetowania hasła usługi Azure AD

Azure Active Directory (usługa Azure AD) funkcja samoobsługowego resetowania haseł (SSPR) umożliwia użytkownikom zmianę lub Resetowanie hasła bez konieczności korzystania z administratora ani skontaktuj się z pomocą techniczną. Jeśli konto użytkownika jest zablokowane lub zapomni swoje hasło, może wykonać monit o odblokowanie siebie i zawracanie do pracy. Ta możliwość zmniejsza liczbę wywołań pomocy technicznej i utratę produktywności, gdy użytkownik nie może zalogować się na urządzeniu ani w aplikacji.

> [!IMPORTANT]
> Ten artykuł koncepcyjny wyjaśnia, jak działa funkcja samoobsługowego resetowania hasła. Jeśli jesteś użytkownikiem końcowym już zarejestrowanym do samoobsługowego resetowania hasła i chcesz wrócić do swojego konta, przejdź do strony [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Jeśli Twój zespół IT nie włączył możliwości resetowania własnego hasła, skontaktuj się z pomocą techniczną, aby uzyskać dodatkową pomoc.

## <a name="how-does-the-password-reset-process-work"></a>Jak działa proces resetowania haseł?

Użytkownik może zresetować lub zmienić swoje hasło przy użyciu [portalu SSPR](https://aka.ms/sspr). Muszą najpierw zarejestrować odpowiednie metody uwierzytelniania. Gdy użytkownik uzyskuje dostęp do portalu SSPR, platforma Azure traktuje następujące czynniki:

* Jak ma być lokalizowana Strona?
* Czy konto użytkownika jest prawidłowe?
* Do jakiej organizacji należy użytkownik?
* Gdzie jest zarządzane hasło użytkownika?
* Czy użytkownik ma licencję na korzystanie z tej funkcji?

Gdy użytkownik wybierze link **nie można uzyskać dostępu do konta** z aplikacji lub strony lub przechodzi bezpośrednio do programu [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) , język używany w portalu SSPR jest oparty na następujących opcjach:

* Domyślnie ustawienia regionalne przeglądarki są używane do wyświetlania SSPR w odpowiednim języku. Środowisko resetowania hasła jest zlokalizowane w tych samych językach, które [Microsoft 365 obsługiwane](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec)przez program.
* Jeśli chcesz połączyć się z SSPR w określonym zlokalizowanym języku, Dołącz `?mkt=` na końcu adresu URL resetowania hasła wraz z wymaganymi ustawieniami regionalnymi.
    * Na przykład aby określić hiszpańskie ustawienia regionalne *-US* , użyj `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .

Po wyświetleniu portalu SSPR w wymaganym języku użytkownik zostanie poproszony o podanie identyfikatora użytkownika i przekazanie elementu CAPTCHA. Usługa Azure AD sprawdzi teraz, czy użytkownik może korzystać z usługi SSPR, wykonując następujące sprawdzenia:

* Sprawdza, czy użytkownik ma SSPR włączony i ma przypisaną licencję usługi Azure AD.
  * Jeśli użytkownik nie jest włączony do usługi SSPR lub nie ma przypisanej licencji, użytkownik zostanie poproszony o skontaktowanie się z administratorem w celu zresetowania hasła.
* Sprawdza, czy użytkownik ma odpowiednie metody uwierzytelniania zdefiniowane na swoim koncie zgodnie z zasadami administratora.
  * Jeśli zasady wymagają tylko jednej metody, należy sprawdzić, czy użytkownik ma odpowiednie dane zdefiniowane dla co najmniej jednej z metod uwierzytelniania włączonych przez zasady administratora.
    * Jeśli nie skonfigurowano metod uwierzytelniania, użytkownik powinien skontaktować się z administratorem w celu zresetowania hasła.
  * Jeśli zasady wymagają dwóch metod, należy sprawdzić, czy użytkownik ma odpowiednie dane zdefiniowane dla co najmniej dwóch metod uwierzytelniania włączonych przez zasady administratora.
    * Jeśli nie skonfigurowano metod uwierzytelniania, użytkownik powinien skontaktować się z administratorem w celu zresetowania hasła.
  * Jeśli do użytkownika jest przypisana rola administratora platformy Azure, wymuszane są silne zasady haseł z dwoma bramami. Aby uzyskać więcej informacji, zobacz temat [różnice w zasadach resetowania ustawień administratora](concept-sspr-policy.md#administrator-reset-policy-differences).
* Sprawdza, czy hasło użytkownika jest zarządzane lokalnie, na przykład wtedy, gdy dzierżawa usługi Azure AD korzysta z federacyjnego, uwierzytelniania przekazywanego lub synchronizacji skrótów haseł:
  * Jeśli zostanie skonfigurowane zapisywanie zwrotne SSPR, a hasło użytkownika jest zarządzane lokalnie, użytkownik może wykonać uwierzytelnianie i resetowanie hasła.
  * Jeśli zapisywanie zwrotne SSPR nie zostanie wdrożone i hasło użytkownika jest zarządzane lokalnie, użytkownik zostanie poproszony o skontaktowanie się z administratorem w celu zresetowania hasła.

Jeśli wszystkie poprzednie testy zostały zakończone pomyślnie, użytkownik jest kierowany przez proces resetowania lub zmiany hasła.

> [!NOTE]
> SSPR może wysyłać powiadomienia e-mail do użytkowników w ramach procesu resetowania hasła. Te wiadomości e-mail są wysyłane przy użyciu usługi przekaźnika SMTP, która działa w trybie aktywny-aktywny w kilku regionach.
>
> Usługi przekazywania SMTP odbierają i przetwarzają treści wiadomości e-mail, ale nie przechowują ich. Treść wiadomości e-mail SSPR, która może potencjalnie zawierać informacje dostarczone przez klienta, nie jest przechowywana w dziennikach usługi przekazywania SMTP. Dzienniki zawierają tylko metadane protokołu.

Aby rozpocząć pracę z usługą SSPR, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Samouczek: Włączanie funkcji samoobsługowego resetowania hasła (SSPR)](tutorial-enable-sspr.md)

## <a name="registration-options"></a>Opcje rejestracji

Aby użytkownicy mogli resetować lub zmieniać swoje hasła przy użyciu usługi SSPR, muszą zarejestrować siebie i metody uwierzytelniania, które mają być używane. Jak wspomniano w poprzedniej sekcji, użytkownik musi być zarejestrowany do SSPR i mieć zastosowaną licencję.

### <a name="require-users-to-register-when-they-sign-in"></a>Wymagaj od użytkowników zarejestrowania się podczas logowania

Możesz włączyć opcję, aby wymagać od użytkownika przeprowadzenia rejestracji SSPR, jeśli zalogują się do aplikacji przy użyciu usługi Azure AD. Ten przepływ pracy obejmuje następujące aplikacje:

* Microsoft 365
* Azure Portal
* Panel dostępu
* Aplikacje federacyjne
* Aplikacje niestandardowe korzystające z usługi Azure AD

Jeśli rejestracja nie jest wymagana, użytkownicy nie będą monitowani podczas logowania, ale mogą zarejestrować się ręcznie. Użytkownicy mogą albo odwiedzać [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) , albo wybrać link **zarejestruj do resetowania hasła** na karcie **profil** w panelu dostępu.

![Opcje rejestracji dla SSPR w Azure Portal][Registration]

> [!NOTE]
> Użytkownicy mogą odrzucić Portal rejestracji SSPR, wybierając pozycję **Anuluj** lub zamykając okno. Są jednak monitowani o zarejestrowanie się przy każdym logowaniu do momentu zakończenia rejestracji.
>
> To przerwanie do rejestracji w usłudze SSPR nie przerywa połączenia użytkownika, jeśli jest już zalogowany.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Ustaw liczbę dni, po której użytkownicy zostaną poproszeni o ponowne potwierdzenie swoich informacji uwierzytelniania

Aby upewnić się, że metody uwierzytelniania są poprawne, gdy są potrzebne do resetowania lub zmiany hasła, można wymagać od użytkowników potwierdzenia ich informacji zarejestrowanych po upływie określonego czasu. Ta opcja jest dostępna tylko po włączeniu opcji **Wymagaj, aby użytkownicy rejestrowali się podczas logowania** .

Prawidłowe wartości, aby monitować użytkownika o potwierdzenie ich zarejestrowanych metod, to od *0* do *730* dni. Ustawienie tej wartości na *0* oznacza, że użytkownicy nigdy nie będą monitowani o potwierdzenie ich informacji uwierzytelniania.

## <a name="authentication-methods"></a>Metody uwierzytelniania

Jeśli użytkownik jest włączony do SSPR, musi zarejestrować co najmniej jedną metodę uwierzytelniania. Zdecydowanie zalecamy wybranie co najmniej dwóch metod uwierzytelniania, aby użytkownicy mieli większą elastyczność w przypadku, gdy ich potrzebują. Aby uzyskać więcej informacji, zobacz [co to są metody uwierzytelniania?](concept-authentication-methods.md).

Dla SSPR są dostępne następujące metody uwierzytelniania:

* Powiadomienie aplikacji mobilnej
* Kod aplikacji mobilnej
* E-mail
* Telefon komórkowy
* Telefon służbowy
* Pytania zabezpieczające

Użytkownicy mogą resetować swoje hasła tylko wtedy, gdy zarejestrowali metodę uwierzytelniania, którą włączył administrator.

> [!WARNING]
> Konta przypisane do ról *administratora* platformy Azure są wymagane do korzystania z metod zgodnie z definicją w sekcji [administrator resetuje różnice między zasadami](concept-sspr-policy.md#administrator-reset-policy-differences).

![Wybór metod uwierzytelniania w Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Wymagana liczba metod uwierzytelniania

Można skonfigurować liczbę dostępnych metod uwierzytelniania, które użytkownik musi podać, aby zresetować lub odblokować hasło. Tę wartość można ustawić na *jedną* lub *dwa*.

Użytkownicy mogą i powinni rejestrować wiele metod uwierzytelniania. Zdecydowanie zaleca się, aby użytkownicy rejestrowali co najmniej dwie metody uwierzytelniania, co zapewnia większą elastyczność w przypadku, gdy ich potrzebują.

Jeśli użytkownik nie ma minimalnej wymaganej liczby wymaganych metod podczas próby użycia SSPR, zobaczy stronę błędu, która kieruje je do żądania zresetowania hasła przez administratora. Należy zachować ostrożność w przypadku zwiększenia liczby metod wymaganych od jednej do dwóch, jeśli masz istniejących użytkowników zarejestrowanych dla usługi SSPR, a następnie nie mogą korzystać z tej funkcji. Aby uzyskać więcej informacji, zapoznaj się z sekcją w poniższej sekcji, aby [zmienić metody uwierzytelniania](#change-authentication-methods).

#### <a name="mobile-app-and-sspr"></a>Aplikacja mobilna i SSPR

W przypadku korzystania z aplikacji mobilnej jako metody resetowania hasła, takiej jak aplikacja Microsoft Authenticator, mają zastosowanie następujące zagadnienia:

* Gdy administratorzy wymagają użycia jednej metody do resetowania hasła, kod weryfikacyjny jest jedyną dostępną opcją.
* Gdy administratorzy wymagają dwóch metod do zresetowania hasła, użytkownicy mogą używać kodu powiadomień **lub** weryfikacji oprócz innych włączonych metod.

| Liczba metod wymaganych do zresetowania | Jeden | Dwa |
| :---: | :---: | :---: |
| Dostępne funkcje aplikacji mobilnej | Kod | Kod lub powiadomienie |

Użytkownicy nie mają możliwości zarejestrowania aplikacji mobilnej podczas rejestrowania do samoobsługowego resetowania hasła [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Użytkownicy mogą rejestrować swoją aplikację mobilną w usłudze [https://aka.ms/mfasetup](https://aka.ms/mfasetup) lub w ramach rejestracji informacji o zabezpieczeniach w usłudze [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> Nie można wybrać aplikacji Authenticator jako jedynej metody uwierzytelniania, gdy wymagana jest tylko Metoda. Podobnie nie można wybrać aplikacji Authenticator i tylko jednej metody dodatkowej, gdy wymagane są dwie metody.
>
> Podczas konfigurowania zasad SSPR, które obejmują aplikację Authenticator jako metodę, należy wybrać co najmniej jedną dodatkową metodę, gdy wymagana jest jedna metoda, i należy wybrać co najmniej dwie dodatkowe metody, gdy wymagane jest skonfigurowanie dwóch metod.
>
> To wymaganie wynika z faktu, że bieżące środowisko rejestracji SSPR nie obejmuje opcji zarejestrowania aplikacji uwierzytelniania. Opcja zarejestrowania aplikacji Authenticator jest dołączona do nowego [środowiska rejestracji połączonej](./concept-registration-mfa-sspr-combined.md).
>
> Zezwalanie na używanie zasad korzystających tylko z aplikacji Authenticator (gdy wymagana jest jedna metoda) lub aplikacji Authenticator i tylko jednej dodatkowej metody (gdy wymagane są dwie metody), może spowodować, że użytkownicy będą mogli rejestrować się w usłudze SSPR, dopóki nie zostaną skonfigurowani do korzystania z nowego, połączonego środowiska rejestracji.

### <a name="change-authentication-methods"></a>Zmień metody uwierzytelniania

Jeśli zaczniesz od zasad, które mają tylko jedną wymaganą metodę uwierzytelniania na potrzeby resetowania lub odblokowywania, i zmienisz ją na dwie metody, co się dzieje?

| Liczba zarejestrowanych metod | Wymagana liczba metod | Wynik |
| :---: | :---: | :---: |
| co najmniej 1 | 1 | **Możliwość** resetowania lub odblokowywania |
| 1 | 2 | **Nie można** zresetować lub odblokować |
| 2 lub więcej | 2 | **Możliwość** resetowania lub odblokowywania |

Zmiana dostępnych metod uwierzytelniania może również spowodować problemy dla użytkowników. Jeśli zmienisz typy metod uwierzytelniania, których użytkownik może użyć, możesz przypadkowo uniemożliwić użytkownikom korzystanie z usługi SSPR, jeśli nie mają one minimalnej ilości dostępnych danych.

Rozważmy następujący przykładowy scenariusz:

1. W przypadku oryginalnych zasad skonfigurowano dwie wymagane metody uwierzytelniania. Używa on tylko numeru telefonu biurowego i pytań zabezpieczających.
1. Administrator zmienia zasady, aby nie korzystały już z pytań zabezpieczających, ale umożliwia korzystanie z telefonu komórkowego i alternatywnej poczty e-mail.
1. Użytkownicy bez pól telefon komórkowy lub alternatywny adres e-mail nie mogą resetować haseł.

## <a name="notifications"></a>Powiadomienia

Aby zwiększyć świadomość zdarzeń związanych z hasłami, SSPR umożliwia skonfigurowanie powiadomień zarówno dla użytkowników, jak i administratorów tożsamości.

### <a name="notify-users-on-password-resets"></a>Czy powiadamiać użytkowników o resetowaniu hasła?

Jeśli ta opcja jest ustawiona na **tak**, użytkownicy resetowania hasła otrzymają wiadomość e-mail z powiadomieniem, że hasło zostało zmienione. Wiadomość e-mail jest wysyłana za pośrednictwem portalu SSPR do swoich głównych i alternatywnych adresów e-mail przechowywanych w usłudze Azure AD. Nikt inny nie jest powiadamiany o zdarzeniu resetowania.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Powiadom wszystkich administratorów, gdy inni administratorzy zresetują swoje hasła

Jeśli ta opcja jest ustawiona na **tak**, wszyscy administratorzy platformy Azure otrzymają wiadomość e-mail na swój podstawowy adres e-mail przechowywany w usłudze Azure AD. Wiadomość e-mail powiadamia o zmianie hasła przez innego administratora przy użyciu SSPR.

Rozważmy następujący przykładowy scenariusz:

* W środowisku znajdują się cztery Administratorzy.
* Administrator *A* resetuje swoje hasło przy użyciu SSPR.
* Administratorzy *B*, *C*i *D* otrzymują wiadomość e-mail z alertami dotyczącymi resetowania hasła.

## <a name="on-premises-integration"></a>Integracja lokalna

Jeśli masz środowisko hybrydowe, możesz skonfigurować Azure AD Connect do zapisywania zdarzeń zmiany hasła z powrotem z usługi Azure AD do katalogu lokalnego.

![Weryfikowanie zapisywania zwrotnego haseł jest włączone i działa][Writeback]

Usługa Azure AD sprawdza bieżącą łączność hybrydową i udostępnia jeden z następujących komunikatów w Azure Portal:

* Lokalny klient zapisywania zwrotnego jest uruchomiony.
* Usługa Azure AD jest w trybie online i jest połączona z lokalnym klientem zapisywania zwrotnego. Jednak wygląda na to, że zainstalowana wersja Azure AD Connect jest nieaktualna. Rozważ [uaktualnienie Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) , aby mieć pewność, że dysponujesz najnowszymi funkcjami łączności i ważnymi poprawkami błędów.
* Niestety, nie możemy sprawdzić stanu lokalnego klienta zapisywania zwrotnego, ponieważ zainstalowana wersja Azure AD Connect jest nieaktualna. [Uaktualnij Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) , aby sprawdzić stan połączenia.
* Niestety, wygląda na to, że nie możemy teraz połączyć się z lokalnym klientem funkcji zapisywania zwrotnego. [Rozwiązywanie problemów z Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) , aby przywrócić połączenie.
* Niestety, nie można nawiązać połączenia z lokalnym klientem zapisywania zwrotnego, ponieważ nie skonfigurowano poprawnie funkcji zapisywania zwrotnego haseł. [Skonfiguruj funkcję zapisywania zwrotnego haseł](./tutorial-enable-sspr-writeback.md) , aby przywrócić połączenie.
* Niestety, wygląda na to, że nie możemy teraz połączyć się z lokalnym klientem funkcji zapisywania zwrotnego. Może to być spowodowane tymczasowymi problemami na naszym końcu. Jeśli problem będzie się powtarzał, [Rozwiązywanie problemów Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) , aby przywrócić połączenie.

Aby rozpocząć pracę z funkcją zapisywania zwrotnego SSPR, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Samouczek: Włączanie zapisywania zwrotnego funkcji samoobsługowego resetowania hasła (SSPR)](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Zapisuj hasła wstecz do katalogu lokalnego

Możesz włączyć funkcję zapisywania zwrotnego haseł przy użyciu Azure Portal. Możesz również tymczasowo wyłączyć funkcję zapisywania zwrotnego haseł bez konieczności ponownego konfigurowania Azure AD Connect.

* Jeśli opcja jest ustawiona na **tak**, funkcja zapisywania zwrotnego jest włączona. Użytkownicy z uwierzytelnianiem federacyjnym, przekazującym lub skrótem hasła mogą resetować swoje hasła.
* Jeśli opcja jest ustawiona na wartość **nie**, zapisywanie zwrotne jest wyłączone. Użytkownicy z uwierzytelnianiem federacyjnym, uwierzytelniania przekazywanego lub skrótu hasła nie mogą resetować haseł.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Zezwalaj użytkownikom na odblokowywanie kont bez resetowania ich hasła

Domyślnie usługa Azure AD odblokowuje konta, gdy wykonuje Resetowanie hasła. Aby zapewnić elastyczność, można zezwolić użytkownikom na odblokowanie kont lokalnych bez konieczności resetowania hasła. Użyj tego ustawienia, aby oddzielić te dwie operacje.

* W przypadku wybrania opcji **tak**użytkownicy mogą zresetować swoje hasło i odblokować konto lub odblokować konto bez konieczności resetowania hasła.
* Jeśli ustawiono wartość **nie**, użytkownicy będą mogli wykonywać operacje resetowania hasła i odblokowywania konta.

### <a name="on-premises-active-directory-password-filters"></a>Filtry haseł Active Directory lokalnych

SSPR wykonuje odpowiednik resetowania hasła zainicjowane przez administratora w Active Directory. Jeśli w celu wymuszania niestandardowych reguł haseł używasz filtru hasła innej firmy, upewnij się, że ten filtr haseł jest sprawdzany podczas samoobsługowego resetowania hasła w usłudze Azure AD, sprawdź, czy rozwiązanie filtru haseł innych firm jest skonfigurowane do zastosowania w scenariuszu resetowania hasła administratora. [Ochrona hasłem usługi Azure AD dla Active Directory Domain Services](concept-password-ban-bad-on-premises.md) jest domyślnie obsługiwana.

## <a name="password-reset-for-b2b-users"></a>Resetowanie hasła dla użytkowników B2B

Resetowanie i zmiana hasła są w pełni obsługiwane we wszystkich konfiguracjach między firmami (B2B). Resetowanie hasła użytkownika B2B jest obsługiwane w następujących trzech przypadkach:

* **Użytkownicy z organizacji partnerskiej z istniejącą dzierżawą usługi Azure AD**: Jeśli organizacja, której jesteś partnerem, ma istniejącą dzierżawę usługi Azure AD, wszystkie zasady resetowania hasła są włączone dla tej dzierżawy. Aby Resetowanie hasła działało, organizacja partnerska musi upewnić się, że usługa Azure AD SSPR jest włączona. Nie ma dodatkowych opłat za Microsoft 365 klientów.
* **Użytkownicy, którzy zarejestrują** się w ramach rejestracji samoobsługowej: Jeśli organizacja, której jesteś partnerem, użyła funkcji [rejestracji samoobsługowej](../enterprise-users/directory-self-service-signup.md) w celu uzyskania dzierżawy, zezwolimy im na zresetowanie hasła przy użyciu zarejestrowanej wiadomości e-mail.
* **Użytkownicy B2B**: Wszyscy nowi użytkownicy B2B utworzeni przy użyciu nowych [możliwości B2B usługi Azure AD](../external-identities/what-is-b2b.md) mogą również resetować swoje hasła przy użyciu wiadomości e-mail zarejestrowanych podczas procesu zapraszania.

Aby przetestować ten scenariusz, przejdź do https://passwordreset.microsoftonline.com jednego z tych użytkowników partnerskich. Jeśli zdefiniowano alternatywną wiadomość e-mail lub adres e-mail uwierzytelniania, Resetowanie hasła działa zgodnie z oczekiwaniami.

> [!NOTE]
> Konta Microsoft, które uzyskały dostęp gościa do dzierżawy usługi Azure AD, takie jak te z Hotmail.com, Outlook.com lub innych osobistych adresów e-mail, nie mogą korzystać z usługi Azure AD SSPR. Muszą zresetować swoje hasła przy użyciu informacji znajdujących się w artykule, [gdy nie możesz zalogować się do konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą SSPR, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Samouczek: Włączanie funkcji samoobsługowego resetowania hasła (SSPR)](tutorial-enable-sspr.md)

Poniższe artykuły zawierają dodatkowe informacje dotyczące resetowania haseł za pomocą usługi Azure AD:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Dostępne metody uwierzytelniania w usłudze Azure AD i wymagane ilości"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Skonfiguruj opcje rejestracji SSPR w Azure Portal"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Integracja lokalna dla SSPR w Azure Portal"