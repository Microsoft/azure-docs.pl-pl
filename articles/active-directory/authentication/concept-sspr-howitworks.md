---
title: Głębokie omówienie samoobsługowego resetowania haseł — Azure Active Directory
description: Jak działa samoobsługowe resetowanie hasła
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94077a1c6329aa1fecf9593f2df41fa77afc8a44
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765921"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Jak to działa: Samoobsługowe resetowania hasła usługi Azure AD

Azure Active Directory samoobsługowego resetowania haseł (SSPR, self-service password reset) usługi Azure AD umożliwia użytkownikom zmianę lub zresetowanie hasła bez angażowania administratora ani pomocy technicznej. Jeśli konto użytkownika jest zablokowane lub zapomni hasło, może on postępować zgodnie z monitami, aby odblokować się i wrócić do pracy. Ta możliwość zmniejsza liczbę telefonów do działu pomocy technicznej i zmniejsza produktywność, gdy użytkownik nie może zalogować się do swojego urządzenia lub aplikacji.

> [!IMPORTANT]
> W tym artykule koncepcyjnym wyjaśniono administratorowi, jak działa samoobsługowe resetowanie hasła. Jeśli jesteś już zarejestrowanym użytkownikiem końcowym na potrzeby samoobsługowego resetowania hasła i chcesz wrócić do swojego konta, przejdź na adres [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Jeśli Twój zespół IT nie włączył możliwości resetowania własnego hasła, należy skontaktować się z działem pomocy technicznej, aby uzyskać dodatkową pomoc.

## <a name="how-does-the-password-reset-process-work"></a>Jak działa proces resetowania hasła?

Użytkownik może zresetować lub zmienić swoje hasło przy użyciu [portalu SSPR.](https://aka.ms/sspr) Najpierw muszą oni zarejestrować żądane metody uwierzytelniania. Gdy użytkownik uzyskuje dostęp do portalu SSPR, platforma Azure uwzględnia następujące czynniki:

* Jak powinna być zlokalizowane strona?
* Czy konto użytkownika jest prawidłowe?
* Do jakiej organizacji należy użytkownik?
* Gdzie jest zarządzane hasło użytkownika?
* Czy użytkownik ma licencję na korzystanie z tej funkcji?

Gdy użytkownik wybierze  link Nie można uzyskać dostępu do konta z aplikacji lub strony albo przejdzie bezpośrednio do witryny , język używany w portalu SSPR jest oparty na [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) następujących opcjach:

* Domyślnie ustawienia lokalne przeglądarki są używane do wyświetlania SSPR w odpowiednim języku. Środowisko resetowania hasła jest zlokalizowane w tych samych językach, [które Microsoft 365 obsługuje .](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec)
* Jeśli chcesz połączyć się z resetowaniem hasła w określonym zlokalizowanym języku, dołącz na końcu adresu URL resetowania hasła wraz z `?mkt=` wymaganymi ustawieniami regionalnymi.
    * Aby na przykład określić ustawienia lokalne języka hiszpańskiego *es-us,* `?mkt=es-us` użyj .  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us)

Po wyświetleniu portalu SSPR w wymaganym języku użytkownik jest monitowany o wprowadzenie identyfikatora użytkownika i podanie captcha. Usługa Azure AD sprawdza teraz, czy użytkownik może korzystać z funkcji SSPR, wykonując następujące testy:

* Sprawdza, czy użytkownik ma włączoną funkcję SSPR i ma przypisaną licencję usługi Azure AD.
  * Jeśli użytkownik nie ma włączonego funkcji SSPR lub nie ma przypisanej licencji, zostanie poproszony o skontaktowanie się z administratorem w celu zresetowania hasła.
* Sprawdza, czy użytkownik ma na swoim koncie zdefiniowane właściwe metody uwierzytelniania zgodnie z zasadami administratora.
  * Jeśli zasady wymagają tylko jednej metody, sprawdź, czy użytkownik ma odpowiednie dane zdefiniowane dla co najmniej jednej z metod uwierzytelniania włączonych przez zasady administratora.
    * Jeśli metody uwierzytelniania nie są skonfigurowane, użytkownikowi zaleca się skontaktowanie się z administratorem w celu zresetowania hasła.
  * Jeśli zasady wymagają dwóch metod, sprawdź, czy użytkownik ma odpowiednie dane zdefiniowane dla co najmniej dwóch metod uwierzytelniania włączonych przez zasady administratora.
    * Jeśli metody uwierzytelniania nie są skonfigurowane, użytkownikowi zaleca się skontaktowanie się z administratorem w celu zresetowania hasła.
  * Jeśli do użytkownika przypisano rolę administratora platformy Azure, wymuszane są silne zasady haseł z dwiema bramami. Aby uzyskać więcej informacji, zobacz [Administrator reset policy differences (Różnice w zasadach resetowania administratora).](concept-sspr-policy.md#administrator-reset-policy-differences)
* Sprawdza, czy hasło użytkownika jest zarządzane lokalnie, na przykład czy dzierżawa usługi Azure AD korzysta z federatora, uwierzytelniania pass-through lub synchronizacji skrótów haseł:
  * Jeśli skonfigurowano zapis zwrotny SSPR i hasło użytkownika jest zarządzane lokalnie, użytkownik może przejść do uwierzytelniania i zresetowania swojego hasła.
  * Jeśli zapis zwrotny SSPR nie został wdrożony, a hasło użytkownika jest zarządzane lokalnie, użytkownik zostanie poproszony o skontaktowanie się z administratorem w celu zresetowania hasła.

Jeśli wszystkie poprzednie testy zostaną zakończone pomyślnie, użytkownik zostanie przeprowadzony przez proces resetowania lub zmieniania hasła.

> [!NOTE]
> SSPR może wysyłać powiadomienia e-mail do użytkowników w ramach procesu resetowania hasła. Te wiadomości e-mail są wysyłane przy użyciu usługi przekazywania SMTP, która działa w trybie aktywny-aktywny w kilku regionach.
>
> Usługi przekazywania SMTP odbierają i przetwarzają treść wiadomości e-mail, ale nie przechowują jej. Treść wiadomości e-mail SSPR, która może potencjalnie zawierać informacje podane przez klienta, nie jest przechowywana w dziennikach usługi przekazywania SMTP. Dzienniki zawierają tylko metadane protokołu.

Aby rozpocząć pracę z SSPR, wykonaj czynności z następującego samouczka:

> [!div class="nextstepaction"]
> [Samouczek: włączanie samoobsługowego resetowania hasła](tutorial-enable-sspr.md)


## <a name="require-users-to-register-when-they-sign-in"></a>Wymaganie od użytkowników zarejestrowania się podczas logowania

Możesz włączyć opcję wymagania od użytkownika ukończenia rejestracji funkcji SSPR, jeśli korzysta z nowoczesnego uwierzytelniania lub przeglądarki internetowej do logowania się do dowolnych aplikacji przy użyciu usługi Azure AD. Ten przepływ pracy obejmuje następujące aplikacje:

* Microsoft 365
* Azure Portal
* Panel dostępu
* Aplikacje federowane
* Aplikacje niestandardowe korzystające z usługi Azure AD

Jeśli nie wymagasz rejestracji, użytkownicy nie są monitowane podczas logowania, ale mogą się zarejestrować ręcznie. Użytkownicy mogą odwiedzić lub [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) wybrać link Zarejestruj się w celu **zresetowania** hasła na karcie **Profil** w Panel dostępu.

![Opcje rejestracji dla SSPR w Azure Portal][Registration]

> [!NOTE]
> Użytkownicy mogą odrzucić portal rejestracji usług SSPR, wybierając pozycję **Anuluj** lub zamykając okno. Jednak po każdym zalogowaniu użytkownik jest monitowany o zarejestrowanie się do momentu ukończenia rejestracji.
>
> To przerwanie rejestrowania w celu rejestracji WSPR nie przerywa połączenia użytkownika, jeśli użytkownik jest już zalogowany.

## <a name="reconfirm-authentication-information"></a>Ponowne potwierdzanie informacji o uwierzytelnianiu

Aby upewnić się, że metody uwierzytelniania są poprawne, gdy są potrzebne do zresetowania lub zmiany hasła, możesz wymagać od użytkowników potwierdzenia ich informacji zarejestrowanych po upływie określonego czasu. Ta opcja jest dostępna tylko w przypadku włączenia opcji **Wymagaj od użytkowników rejestrowania się podczas logowania.**

Prawidłowe wartości monitowania użytkownika o potwierdzenie, że jego zarejestrowane metody to *od 0* do *730* dni. Ustawienie tej wartości na *0 oznacza,* że użytkownicy nigdy nie są proszeni o potwierdzenie swoich informacji uwierzytelniania. W przypadku korzystania z połączonego procesu rejestracji użytkownicy będą wymagać potwierdzenia tożsamości przed ponowną weryfikacją swoich informacji.

## <a name="authentication-methods"></a>Metody uwierzytelniania

Gdy użytkownik jest włączony w funkcji SSPR, musi zarejestrować co najmniej jedną metodę uwierzytelniania. Zdecydowanie zalecamy wybranie co najmniej dwóch metod uwierzytelniania, aby użytkownicy mieli większą elastyczność w przypadku, gdy nie będą mogli uzyskać dostępu do jednej metody, gdy jej potrzebują. Aby uzyskać więcej informacji, [zobacz Co to są metody uwierzytelniania?](concept-authentication-methods.md).

Następujące metody uwierzytelniania są dostępne dla usługi SSPR:

* Powiadomienie aplikacji mobilnej
* Kod aplikacji mobilnej
* E-mail
* Telefon komórkowy
* Telefon służbowy
* Pytania zabezpieczające

Użytkownicy mogą resetować swoje hasło tylko wtedy, gdy zarejestrują metodę uwierzytelniania włączoną przez administratora.

> [!WARNING]
> Aby korzystać z *metod zdefiniowanych* w sekcji Różnice w zasadach resetowania administratora, wymagane są przypisane [role administratora platformy Azure.](concept-sspr-policy.md#administrator-reset-policy-differences)

![Wybór metod uwierzytelniania w Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Wymagana liczba metod uwierzytelniania

Możesz skonfigurować liczbę dostępnych metod uwierzytelniania, które użytkownik musi podać, aby zresetować lub odblokować hasło. Tę wartość można ustawić na *jeden lub* *dwa .*

Użytkownicy mogą i powinni rejestrować wiele metod uwierzytelniania. Zdecydowanie zaleca się również zarejestrowanie co najmniej dwóch metod uwierzytelniania, aby zapewnić większą elastyczność w przypadku, gdy nie będą mogli uzyskać dostępu do jednej metody, gdy jej potrzebują.

Jeśli użytkownik nie ma minimalnej liczby wymaganych metod zarejestrowanych podczas próby użycia funkcji SSPR, zobaczy stronę błędu, na która zostanie skierowany wniosek o zresetowanie hasła przez administratora. Należy zadbać o zwiększenie liczby metod wymaganych z jednej do dwóch, jeśli istniejący użytkownicy są zarejestrowani w funkcji SSPR i nie będą mogli korzystać z tej funkcji. Aby uzyskać więcej informacji, zobacz następującą sekcję, aby [zmienić metody uwierzytelniania.](#change-authentication-methods)

#### <a name="mobile-app-and-sspr"></a>Aplikacja mobilna i SSPR

W przypadku korzystania z aplikacji mobilnej jako metody resetowania hasła, na przykład aplikacji Microsoft Authenticator, należy wziąć pod uwagę następujące kwestie:

* Jeśli administratorzy wymagają jednej metody resetowania hasła, kod weryfikacyjny jest jedyną dostępną opcją.
* Gdy administratorzy wymagają użycia dwóch metod resetowania hasła, użytkownicy mogą używać powiadomienia **LUB** kodu weryfikacyjnego oprócz innych włączonych metod.

| Liczba metod wymaganych do zresetowania | Jeden | Dwa |
| :---: | :---: | :---: |
| Dostępne funkcje aplikacji mobilnych | Kod | Kod lub powiadomienie |

Użytkownicy nie mają możliwości zarejestrowania swojej aplikacji mobilnej podczas rejestrowania w celu samoobsługowego resetowania hasła z usługi [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Użytkownicy mogą zarejestrować swoją aplikację mobilną pod adresem lub w połączonej rejestracji [https://aka.ms/mfasetup](https://aka.ms/mfasetup) informacji zabezpieczających pod adresem [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> Nie można wybrać aplikacji Authenticator jako jedynej metody uwierzytelniania, gdy wymagana jest tylko jedna metoda. Podobnie nie można wybrać aplikacji Authenticator i tylko jednej dodatkowej metody w przypadku wymagania dwóch metod.
>
> Podczas konfigurowania zasad SSPR, które zawierają aplikację Authenticator jako metodę, należy wybrać co najmniej jedną dodatkową metodę, gdy jest wymagana jedna metoda, a podczas konfigurowania dwóch metod należy wybrać co najmniej dwie dodatkowe metody.
>
> To wymaganie jest wymagane, ponieważ bieżące środowisko rejestracji SSPR nie zawiera opcji rejestrowania aplikacji wystawcy uwierzytelnień. Opcja zarejestrowania aplikacji authenticator jest dołączona do nowego [połączonego procesu rejestracji.](./concept-registration-mfa-sspr-combined.md)
>
> Zezwolenie na zasady, które korzystają tylko z aplikacji Authenticator (jeśli jest wymagana jedna metoda) lub aplikacji Authenticator i tylko jednej dodatkowej metody (jeśli są wymagane dwie metody), może prowadzić do zablokowania użytkownikom możliwości rejestracji w funkcji SSPR do momentu skonfigurowania ich do korzystania z nowego połączonego środowiska rejestracji.

### <a name="change-authentication-methods"></a>Zmienianie metod uwierzytelniania

Jeśli zaczniesz od zasad, które mają tylko jedną zarejestrowaną metodę uwierzytelniania wymaganą do resetowania lub odblokowywania i zmienisz tę metodę na dwie, co się stanie?

| Liczba zarejestrowanych metod | Wymagana liczba metod | Wynik |
| :---: | :---: | :---: |
| 1 lub więcej | 1 | **Można zresetować** lub odblokować |
| 1 | 2 | **Nie można** zresetować lub odblokować |
| 2 lub więcej | 2 | **Można zresetować** lub odblokować |

Zmiana dostępnych metod uwierzytelniania może również spowodować problemy dla użytkowników. Jeśli zmienisz typy metod uwierzytelniania, których może używać użytkownik, możesz przypadkowo zatrzymać użytkownikom możliwość korzystania z funkcji SSPR, jeśli nie mają minimalnej ilości dostępnych danych.

Rozważmy następujący przykładowy scenariusz:

1. Oryginalne zasady są konfigurowane przy użyciu dwóch wymaganych metod uwierzytelniania. Używa tylko numeru telefonu biurowego i pytań zabezpieczających.
1. Administrator zmienia zasady, aby nie używać już pytań zabezpieczających, ale zezwala na korzystanie z telefonu komórkowego i alternatywnej poczty e-mail.
1. Użytkownicy bez wypełnionych pól telefonu komórkowego lub alternatywnej poczty e-mail nie mogą teraz resetować swoich haseł.

## <a name="notifications"></a>Powiadomienia

Aby zwiększyć świadomość zdarzeń dotyczących haseł, funkcja SSPR umożliwia konfigurowanie powiadomień zarówno dla użytkowników, jak i administratorów tożsamości.

### <a name="notify-users-on-password-resets"></a>Czy powiadamiać użytkowników o resetowaniu hasła?

Jeśli ta opcja ma wartość **Tak,** użytkownicy resetujący swoje hasło otrzymają wiadomość e-mail z powiadomieniem o zmianie hasła. Wiadomość e-mail jest wysyłana za pośrednictwem portalu SSPR na ich podstawowe i alternatywne adresy e-mail przechowywane w usłudze Azure AD. Nikt inny nie jest powiadamiany o zdarzeniu resetowania.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Powiadamianie wszystkich administratorów, gdy inni administratorzy zresetują swoje hasła

Jeśli ta opcja ma wartość **Tak,** wszyscy inni administratorzy platformy Azure otrzymają wiadomość e-mail na podstawowy adres e-mail przechowywany w usłudze Azure AD. Wiadomość e-mail powiadamia o zmianie hasła przez innego administratora za pomocą funkcji SSPR.

Rozważmy następujący przykładowy scenariusz:

* W środowisku istnieją cztery administratorzy.
* Administrator *A* resetuje swoje hasło przy użyciu funkcji SSPR.
* Administratorzy *B,* *C* i *D* otrzymają wiadomość e-mail z alertem o zresetowaniu hasła.

## <a name="on-premises-integration"></a>Integracja lokalna

Jeśli masz środowisko hybrydowe, możesz skonfigurować Azure AD Connect do zapisu zdarzeń zmiany hasła z usługi Azure AD do katalogu lokalnego.

![Włączono i działa funkcja sprawdzania poprawności funkcji zapisu zwrotnego haseł][Writeback]

Usługa Azure AD sprawdza bieżącą łączność hybrydową i udostępnia jeden z następujących komunikatów w Azure Portal:

* Twój klient zapisu zwrotnego w środowisku lokalnym jest uruchomiony i działa.
* Usługa Azure AD jest w trybie online i jest połączona z lokalnym klientem zapisu zwrotnego. Wygląda jednak na to, że zainstalowana wersja Azure AD Connect jest out-of-date. Rozważ [uaktualnienie Azure AD Connect,](../hybrid/how-to-upgrade-previous-version.md) aby upewnić się, że masz najnowsze funkcje łączności i ważne poprawki błędów.
* Niestety, nie możemy sprawdzić stanu lokalnego klienta zapisu zwrotnego, ponieważ zainstalowana wersja Azure AD Connect jest nowsza. [Uaktualnij Azure AD Connect,](../hybrid/how-to-upgrade-previous-version.md) aby sprawdzić stan połączenia.
* Niestety, wygląda na to, że obecnie nie możemy nawiązać połączenia z lokalnym klientem zapisu zwrotnego. [Rozwiąż Azure AD Connect,](./troubleshoot-sspr-writeback.md) aby przywrócić połączenie.
* Niestety, nie możemy nawiązać połączenia z lokalnym klientem zapisu zwrotnego, ponieważ nie skonfigurowano prawidłowo zapisu zwrotnego haseł. [Skonfiguruj zapis zwrotny haseł,](./tutorial-enable-sspr-writeback.md) aby przywrócić połączenie.
* Niestety, obecnie nie możemy nawiązać połączenia z lokalnym klientem zapisu zwrotnego. Może to być spowodowane tymczasowymi problemami po naszej stronie. Jeśli problem będzie się powtarzać, [rozwiąż Azure AD Connect,](./troubleshoot-sspr-writeback.md) aby przywrócić połączenie.

Aby rozpocząć pracę z zapisem zwrotny SSPR, wykonaj czynności z następującego samouczka:

> [!div class="nextstepaction"]
> [Samouczek: włączanie samoobsługowego resetowania hasła (SSPR) — zapis zwrotny](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Zapis haseł do katalogu lokalnego

Funkcję zapisu zwrotnego haseł można włączyć przy użyciu Azure Portal. Można również tymczasowo wyłączyć funkcję zapisu zwrotnego haseł bez konieczności ponownego konfigurowania Azure AD Connect.

* Jeśli opcja jest ustawiona na **tak,** a następnie zapisu zwrotnego jest włączona. Użytkownicy zsynchronizowani z federacyjną, pass-through lub synchronizacją skrótów haseł mogą resetować swoje hasła.
* Jeśli opcja jest ustawiona na **wartość Nie,** zapis zwrotny jest wyłączony. Użytkownicy federacyjni, uwierzytelniania pass-through lub synchronizacji skrótów haseł nie mogą resetować swoich haseł.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Zezwalaj użytkownikom na odblokowywanie kont bez resetowania hasła

Domyślnie usługa Azure AD odblokowuje konta podczas resetowania hasła. Aby zapewnić elastyczność, możesz zezwolić użytkownikom na odblokowywanie kont lokalnych bez konieczności resetowania hasła. Użyj tego ustawienia, aby oddzielić te dwie operacje.

* W przypadku ustawienia opcji **Tak** użytkownicy mają możliwość zresetowania hasła i odblokowania konta lub odblokowania konta bez konieczności resetowania hasła.
* W przypadku ustawienia **na wartość Nie** użytkownicy będą mogli tylko wykonać połączoną operację resetowania hasła i odblokowywania konta.

### <a name="on-premises-active-directory-password-filters"></a>Filtry haseł w lokalnej usłudze Active Directory

SSPR wykonuje odpowiednik resetowania hasła zainicjowanego przez administratora w usłudze Active Directory. Jeśli używasz filtru haseł innej firmy do wymuszania niestandardowych reguł haseł i wymagasz, aby ten filtr haseł był sprawdzany podczas samoobsługowego resetowania hasła usługi Azure AD, upewnij się, że rozwiązanie filtrowania haseł innych firm jest skonfigurowane do stosowania w scenariuszu resetowania hasła administratora. [Ochrona haseł w usłudze Azure AD Active Directory Domain Services](concept-password-ban-bad-on-premises.md) jest domyślnie obsługiwana.

## <a name="password-reset-for-b2b-users"></a>Resetowanie hasła dla użytkowników B2B

Resetowanie i zmienianie haseł jest w pełni obsługiwane we wszystkich konfiguracjach biznesowych (B2B). Resetowanie hasła użytkownika B2B jest obsługiwane w następujących trzech przypadkach:

* **Użytkownicy z organizacji partnerskiej** z istniejącą dzierżawą usługi Azure AD: jeśli organizacja, z która jest partnerem, ma istniejącą dzierżawę usługi Azure AD, przestrzegamy wszystkich zasad resetowania haseł włączonych w tej dzierżawie. Aby resetowanie haseł działało, organizacja partnerska musi tylko upewnić się, że funkcja resetowania hasła w usłudze Azure AD jest włączona. Nie ma dodatkowych opłat dla Microsoft 365 klientów.
* **Użytkownicy,** którzy rejestrują się za pośrednictwem rejestracji samoobsługowej: [](../enterprise-users/directory-self-service-signup.md) jeśli organizacja, z którym współpracujesz, użyła funkcji rejestracji samoobsługowej w celu uzyskania dostępu do dzierżawy, pozwolimy im zresetować hasło przy użyciu zarejestrowanej przez nich wiadomości e-mail.
* **Użytkownicy B2B:** wszyscy nowi użytkownicy B2B utworzeni przy użyciu nowych funkcji [usługi Azure AD B2B](../external-identities/what-is-b2b.md) mogą również resetować swoje hasła przy użyciu wiadomości e-mail, która została zarejestrowana podczas procesu zapraszania.

Aby przetestować ten scenariusz, przejdź do https://passwordreset.microsoftonline.com strony z jednym z tych użytkowników partnera. Jeśli zdefiniowano alternatywny adres e-mail lub adres e-mail uwierzytelniania, resetowanie hasła działa zgodnie z oczekiwaniami.

> [!NOTE]
> Konta Microsoft, do których udzielono dostępu gościa do dzierżawy usługi Azure AD, takie jak konta z adresów Hotmail.com, Outlook.com lub innych osobistych adresów e-mail, nie mogą używać funkcji SSPR usługi Azure AD. Muszą zresetować swoje hasło, korzystając z informacji w artykule Kiedy nie można zalogować się do [konto Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artykułu.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z SSPR, wykonaj czynności z następującego samouczka:

> [!div class="nextstepaction"]
> [Samouczek: włączanie samoobsługowego resetowania hasła](tutorial-enable-sspr.md)

Poniższe artykuły zawierają dodatkowe informacje dotyczące resetowania haseł za pomocą usługi Azure AD:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Dostępne metody uwierzytelniania w usłudze Azure AD i wymagane ilości"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Konfigurowanie opcji rejestracji SSPR w Azure Portal"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Integracja lokalna z usługą SSPR w Azure Portal"
