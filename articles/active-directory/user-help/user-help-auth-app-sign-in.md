---
title: Logowanie przy użyciu aplikacji Microsoft Authenticator — usługa Azure AD
description: Użyj aplikacji Microsoft Authenticator, aby zalogować się do konta służbowego lub osobistego konta Microsoft i innych firm, przy użyciu weryfikacji dwuskładnikowej lub logowania za pomocą telefonu.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 03/12/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 0fd0aa19d52adb4bf06c9d21d4182e27497a531b
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471757"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Zaloguj się do swoich kont przy użyciu aplikacji Microsoft Authenticator

Aplikacja Microsoft Authenticator ułatwia zalogowanie się do kont w przypadku korzystania z weryfikacji dwuskładnikowej. Weryfikacja dwuskładnikowa zapewnia bezpieczniejszy dostęp do kont, szczególnie podczas wyświetlania informacji poufnych. Ponieważ hasła mogą zostać zapomniane, skradzione lub ujawnione, weryfikacja dwuskładnikowa zapewnia dodatkowy poziom zabezpieczeń, który pomaga chronić konto, utrudniając włamanie się na nie innym osobom.

Aplikacji Microsoft Authenticator można używać na wiele sposobów, na przykład:

- Udostępnienie monitu dla drugiej metody weryfikacji po zalogowaniu się przy użyciu nazwy użytkownika i hasła.

- Udostępnienie możliwości logowania się bez wymagania hasła, ale za to przy użyciu nazwy użytkownika i jego twarzy, odcisku palca lub numeru PIN dostarczanych przez urządzenie przenośne.

  >[!Important]
  >Ta metoda logowania za pomocą telefonu działa tylko z kontami służbowymi i osobistymi firmy Microsoft. Konta firm innych niż Microsoft wymagają użycia standardowego procesu weryfikacji dwuetapowej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby móc korzystać z aplikacji Microsoft Authenticator, musisz:

 1. Pobierz i zainstaluj aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiono, zobacz [pobieranie i instalowanie aplikacji](user-help-auth-app-download-install.md).

 2. Dodaj do aplikacji Microsoft Authenticator swoje konta służbowe, osobiste i inne firmy. Aby uzyskać szczegółowe instrukcje, zobacz [Dodawanie konta służbowego](user-help-auth-app-add-work-school-account.md), [Dodawanie kont osobistych](user-help-auth-app-add-personal-ms-account.md)i [Dodawanie kont innych niż Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>Włącz i zaloguj się za pomocą telefonu do konta służbowego

Logowanie za pomocą telefonu jest typem weryfikacji dwuetapowej. Nadal musisz zweryfikować swoją tożsamość, podając ty i ty masz pewność, ale logowanie za pomocą telefonu pozwala pominąć wprowadzanie hasła do konta i przeprowadzanie wszystkich weryfikacji tożsamości na urządzeniu przenośnym.

Przed włączeniem logowania za telefonem należy włączyć weryfikację dwuskładnikową. Aby uzyskać więcej informacji na temat sposobu włączania weryfikacji dwuskładnikowej dla konta, zobacz [Dodawanie konta służbowego](user-help-auth-app-add-work-school-account.md) i [Dodawanie kont osobistych](user-help-auth-app-add-personal-ms-account.md).

Logowanie przy użyciu telefonu jest dostępne tylko na urządzeniach z systemami iOS i Android z systemem Android 6,0 lub nowszym.

### <a name="turn-on-phone-sign-in"></a>Włącz logowanie przy użyciu telefonu

Otwórz aplikację Microsoft Authenticator, przejdź do swojego konta służbowego i Włącz logowanie za pomocą telefonu.

- **Po naciśnięciu kafelka konta** zostanie wyświetlony widok pełny ekran konta. Jeśli zobaczysz, że logowanie za pomocą **telefonu jest włączone** , oznacza to, że użytkownik jest w pełni skonfigurowany, aby zalogować się bez hasła. Jeśli zobaczysz opcję **Włącz logowanie przy użyciu telefonu**, naciśnij ją, aby włączyć logowanie za telefonem.
- **Jeśli aplikacja jest już używana do weryfikacji dwuskładnikowej**, możesz nacisnąć kafelek konto, aby wyświetlić pełny ekran konta. Następnie naciśnij pozycję **Włącz logowanie** przy użyciu telefonu, aby włączyć logowanie przy użyciu telefonu.
- **Jeśli nie możesz znaleźć konta służbowego** na ekranie **konta** aplikacji, oznacza to, że nie został jeszcze dodany do aplikacji. Dodaj swoje konto służbowe, wykonując czynności opisane w [pomocy Dodawanie konta służbowego lub szkolnego](user-help-auth-app-add-work-school-account.md).

> [!NOTE]
> Firma Microsoft nie obsługuje kombinacji rejestracji urządzeń i uwierzytelniania opartego na certyfikatach w programie Authenticator w systemie iOS. Zamiast tego użytkownik musi ręcznie zarejestrować urządzenie w ustawieniach uwierzytelniania uwierzytelniającego przed zalogowaniem się.

Po włączeniu logowania za pomocą telefonu możesz zalogować się tylko przy użyciu aplikacji Microsoft Authenticator. Oto kroki tej procedury:

1. Zaloguj się do konta służbowego.

    Po wpisaniu nazwy użytkownika zostanie wyświetlony ekran **Zatwierdzanie logowania z** dwoma cyframi i prośba o zalogowanie się za pomocą aplikacji Microsoft Authenticator. Jeśli nie chcesz używać tej metody logowania, możesz **zamiast tego użyć hasła**, a następnie zalogować się przy użyciu hasła.

    ![Zatwierdź pole logowania na komputerze](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. Otwórz powiadomienie lub Microsoft Authenticator aplikację na urządzeniu, a następnie naciśnij liczbę odpowiadającą liczbie widocznej na ekranie **logowania Zatwierdź** na komputerze.

    ![Zatwierdź pole logowania na urządzeniu](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. Wybierz pozycję **Zatwierdź**, jeśli rozpoznajesz próbę logowania. W przeciwnym razie wybierz pozycję **Odmów**.

4. Aby ukończyć uwierzytelnianie, użyj numeru PIN lub klucza biometrycznego telefonu.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>Włączanie i używanie logowania za pomocą telefonu do osobistych kont Microsoft

Możesz włączyć logowanie za pomocą telefonu do osobistego konto Microsoft, na przykład konto, którego używasz do logowania się w usłudze Outlook.com, Xbox lub Skype.

>[!NOTE]
>Aby chronić Twoje konto, aplikacja Microsoft Authenticator wymaga, aby na urządzeniu zablokowała kod PIN lub zabezpieczenie biometryczne. Jeśli telefon zostanie odblokowany, aplikacja wymaga skonfigurowania blokady zabezpieczeń przed włączeniem logowania za pomocą telefonu.

### <a name="turn-on-phone-sign-in"></a>Włącz logowanie przy użyciu telefonu 

Otwórz aplikację Microsoft Authenticator, przejdź do swojego konta służbowego i Włącz logowanie za pomocą telefonu.

- **Po naciśnięciu kafelka konta** zostanie wyświetlony widok pełny ekran konta. Jeśli zobaczysz, że logowanie za pomocą **telefonu jest włączone** , oznacza to, że użytkownik jest w pełni skonfigurowany, aby zalogować się bez hasła. Jeśli zobaczysz opcję **Włącz logowanie przy użyciu telefonu**, naciśnij ją, aby włączyć logowanie za telefonem.
- **Jeśli aplikacja jest już używana do weryfikacji dwuskładnikowej**, możesz nacisnąć kafelek konto, aby wyświetlić pełny ekran konta. Następnie naciśnij pozycję **Włącz logowanie** przy użyciu telefonu, aby włączyć logowanie przy użyciu telefonu.
- **Jeśli nie możesz znaleźć konta** na ekranie **konta** aplikacji, oznacza to, że nie został jeszcze dodany do aplikacji. Dodaj konto Microsoft osobiste, wykonując czynności opisane w artykule [Dodawanie osobistych kont Microsoft](user-help-auth-app-add-personal-ms-account.md) .

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Zaloguj się do swojego konta przy użyciu logowania za pomocą telefonu

1. Przejdź do osobistej strony logowania konto Microsoft, a następnie zamiast wpisywać hasło, wybierz łącze **Użyj aplikacji Microsoft Authenticator** .

    Firma Microsoft wysyła powiadomienie do telefonu.

2. Zatwierdź powiadomienie.

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Zaloguj się przy użyciu weryfikacji dwuskładnikowej dla swojego konta

Standardowa metoda weryfikacji dwuskładnikowej wymaga wprowadzenia nazwy użytkownika i hasła do urządzenia, do którego się logujesz, a następnie wybrania, czy aplikacja Microsoft Authenticator odbierze powiadomienie, czy chcesz skopiować kod weryfikacyjny z aplikacji Authenticator. Na urządzeniu z systemem Android kody weryfikacyjne można znaleźć na ekranie **konta** . Na urządzeniu z systemem iOS te kody weryfikacyjne można znaleźć na ekranie **konta** lub w widoku pełnego ekranu konta w zależności od typu konta. Po dodaniu konta do aplikacji Microsoft Authenticator należy włączyć weryfikację dwuskładnikową dla Twojego konta.

>[!Note]
>Jeśli nie widzisz konta służbowego lub konta osobistego na ekranie **konta** aplikacji Microsoft Authenticator, oznacza to, że konto nie zostało dodane do aplikacji Microsoft Authenticator. Aby dodać konto, zobacz [Dodawanie konta służbowego](user-help-auth-app-add-work-school-account.md) lub [Dodawanie konta osobistego](user-help-auth-app-add-personal-ms-account.md).

Aby uzyskać instrukcje niezbędne do zalogowania się do konta służbowego lub osobistego przy użyciu różnych metod weryfikacji dwuskładnikowej, zobacz [Logowanie przy użyciu weryfikacji dwuetapowej lub informacji zabezpieczających](user-help-sign-in.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

| Pytanie | Rozwiązanie |
| -------- | -------- |
| Jak jest bezpieczniejsze logowanie za pomocą mojego telefonu niż wpisanie hasła? | Dzisiaj większość osób loguje się w witrynach lub aplikacjach sieci Web przy użyciu nazwy użytkownika i hasła. Niestety, hasła mogą być tracone, skradzione lub odgadnąć przez hakerów.<br><br>Po skonfigurowaniu aplikacji Microsoft Authenticator tworzy ona na telefonie klucz, który umożliwia odblokowanie konta, które jest chronione przez numer PIN lub blokada biometryczna Twojego telefonu. Ten klucz jest następnie używany do potwierdzenia Twojej tożsamości podczas logowania.<br><br>**Ważne**<br>Dane są używane tylko w celu ochrony klucza lokalnie. Nie jest to nigdy wysyłane do ani przechowywane w chmurze. |
| Czy logowanie do telefonu zastąpi weryfikację dwuetapową? Czy należy ją wyłączyć? | Logowanie za pomocą telefonu to dwuetapowa weryfikacja, w której dwa kroki są wykonywane na urządzeniu przenośnym. Należy zachować weryfikację dwóch etapów, aby zapewnić dodatkowe zabezpieczenia dla Twojego konta. |
| Czy po włączeniu weryfikacji dwuetapowej dla mojego konta należy zatwierdzić dwie powiadomienia? | Nie. Zalogowanie się do konto Microsoft przy użyciu telefonu jest również traktowane jako weryfikacja dwuetapowa, więc nie jest wymagane żadne drugie zatwierdzenie. |
| Co zrobić, Jeśli utracisz mój telefon lub nie masz go? Jak mogę uzyskać dostęp do mojego konta? | Zawsze możesz wybrać łącze Użyj zamiast tego hasła na stronie logowania, aby przełączyć się z powrotem do korzystania z hasła. Jeśli jednak korzystasz z weryfikacji dwuetapowej, nadal musisz użyć drugiej metody do zweryfikowania swojej tożsamości.<br><br>**Ważne**<br>Zdecydowanie zachęcamy do upewnienia się, że masz więcej niż jedną, aktualną metodę weryfikacji skojarzoną z Twoim kontem.<br><br>Możesz zarządzać metodami weryfikacji dla kont osobistych na stronie [ustawień zabezpieczeń](https://account.live.com/proofs/manage) . W przypadku kont służbowych możesz przejść na stronę [dodatkowej weryfikacji zabezpieczeń](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) w organizacji lub stronę **Zachowaj bezpieczeństwo konta** , jeśli administrator włączył informacje zabezpieczające. Więcej informacji o zabezpieczeniach znajduje się w temacie Informacje o zabezpieczeniach [(wersja zapoznawcza) — Omówienie](./security-info-setup-signin.md).<br><br>Jeśli nie możesz zarządzać metodami weryfikacji, musisz skontaktować się z administratorem. |
| Jak mogę zatrzymać korzystanie z tej funkcji i wrócić do korzystania z mojego hasła? | W przypadku kont osobistych wybierz link **zamiast tego użyj hasła** podczas logowania. Ostatni wybór jest zapamiętany i oferowany domyślnie przy następnym logowaniu. Jeśli kiedykolwiek zechcesz wrócić do korzystania z logowania za pomocą telefonu, wybierz łącze **Użyj zamiast niego aplikacji** podczas logowania.<br><br>W przypadku kont służbowych należy wyrejestrować urządzenie ze strony **Ustawienia** aplikacji Microsoft Authenticator lub wyłączyć urządzenie z obszaru **& aktywności urządzeń** w Twoim profilu. Aby uzyskać więcej informacji na temat wyłączania urządzenia z profilu, zobacz [Aktualizowanie informacji o profilu i koncie z portalu My Apps](./my-account-portal-devices-page.md#disable-a-device). |
| Dlaczego nie mogę używać więcej niż jednego konta służbowego do logowania za pomocą telefonu? | Telefon musi być zarejestrowany w ramach jednego konta służbowego. Jeśli chcesz włączyć logowanie za pomocą telefonu dla innego konta służbowego, musisz wyrejestrować konto z tego urządzenia za pośrednictwem strony **ustawień** . |
| Czy mogę zalogować się do komputera przy użyciu telefonu? | W przypadku komputera zalecamy zalogowanie się przy użyciu funkcji Windows Hello w systemie Windows 10. Funkcja Windows Hello umożliwia zalogowanie się przy użyciu wyglądu, odcisku palca lub numeru PIN. |

## <a name="next-steps"></a>Następne kroki

- Jeśli masz problemy z uzyskaniem kodu weryfikacyjnego dla konto Microsoft osobistych, zobacz sekcję **Rozwiązywanie problemów z kodem weryfikacyjnym** w artykule [konto Microsoft informacje zabezpieczające & kody weryfikacyjne](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- Jeśli masz więcej ogólnych pytań dotyczących aplikacji, zapoznaj się z tematem [Microsoft Authenticator FAQ](user-help-auth-app-faq.md)

- Jeśli potrzebujesz więcej informacji na temat weryfikacji dwuetapowej, zobacz [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md)

- Aby uzyskać więcej informacji na temat informacji zabezpieczających, zobacz [informacje zabezpieczające (wersja zapoznawcza) — Omówienie](./security-info-setup-signin.md)