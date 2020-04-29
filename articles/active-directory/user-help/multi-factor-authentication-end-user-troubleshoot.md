---
title: Typowe problemy z uwierzytelnianiem dwuskładnikowym konta — Azure AD
description: Rozwiązania niektórych typowych problemów z weryfikacją dwuskładnikową oraz konta służbowego.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 3df79fb1e103ddfaafeb348641e675cd250f5858
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81271696"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Typowe problemy z weryfikacją dwuskładnikową i kontem służbowym

Gdy organizacja usługi Azure Active Directory (Azure AD) zmieni weryfikację dwuetapową, logowanie do konta służbowego wymaga kombinacji nazwy użytkownika, hasła oraz urządzenia przenośnego lub telefonu. Jest to bezpieczniejsze niż tylko hasło, polegające na dwóch formach uwierzytelniania: coś znanego, a ty ty. Weryfikacja dwuetapowa może pomóc w zaprzestaniu zaniechania złośliwych hakerów, ponieważ nawet jeśli mają swoje hasło, szanse się, że nie mają one jeszcze urządzenia.

<center>

![Obraz metod uwierzytelniania koncepcyjnego](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Istnieją pewne typowe problemy z weryfikacją dwuetapową, które pojawiają się częściej niż dowolna z nas. W tym artykule opisano najczęstsze problemy i niektóre możliwe poprawki.

>[!Important]
>Jeśli jesteś administratorem, możesz znaleźć więcej informacji na temat sposobu konfigurowania środowiska usługi Azure AD i zarządzania nim w [dokumentacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory).
>
>Ta zawartość jest również przeznaczona wyłącznie do użytku z kontem służbowym, które jest kontem udostępnionym przez organizację (na przykład alain@contoso.com). Jeśli masz problemy z weryfikacją dwuetapową i osobistą konto Microsoft, czyli kontem skonfigurowanym dla siebie (na przykład danielle@outlook.com), zobacz [Włączanie lub wyłączanie weryfikacji dwuskładnikowej dla konto Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Nie mam mojego urządzenia przenośnego

Dzieje się tak. Urządzenie przenośne zostało pozostawione w domu, a teraz nie można użyć telefonu do sprawdzenia, kim jesteś. Jeśli wcześniej dodano kolejną metodę logowania się do konta, takiego jak telefon biurowy, powinno być możliwe korzystanie z tej metody teraz. Jeśli nigdy nie dodaliśmy dodatkowej metody weryfikacji, musisz skontaktować się z działem pomocy technicznej Twojej organizacji i pomożemy Ci wrócić do swojego konta.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Aby zalogować się do konta służbowego przy użyciu innej metody weryfikacji

1. Zaloguj się do swojego konta, ale wybierz łącze **Zaloguj w innym** miejscu na stronie **weryfikacji dwuetapowej** .

    ![Zmień metodę weryfikacji logowania](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Jeśli nie widzisz **znaku w innym sposobie** linku, oznacza to, że nie zostały skonfigurowane żadne inne metody weryfikacji. Musisz skontaktować się z administratorem, aby uzyskać pomoc w logowaniu się do konta.

2. Wybierz alternatywną metodę weryfikacji i Kontynuuj proces weryfikacji dwuetapowej.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Moje urządzenie przenośne zostało zgubione lub zostało skradzione

W przypadku zgubienia lub kradzieży urządzenia przenośnego możesz zalogować się przy użyciu innej metody lub poproszeniu działu pomocy technicznej organizacji o wyczyszczenie ustawień. Zdecydowanie zalecamy umożliwienie działowi pomocy technicznej Twojej firmy informacji w przypadku zgubienia lub kradzieży telefonu, dzięki czemu można wykonać odpowiednie aktualizacje na Twoim koncie. Po wyczyszczeniu ustawień zostanie wyświetlony monit o [zarejestrowanie się w celu przeprowadzenia weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-first-time.md) przy następnym logowaniu.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Nie otrzymuję kodu weryfikacyjnego wysłanego do urządzenia przenośnego

Kod weryfikacyjny nie jest typowym problemem i jest zazwyczaj związany z urządzeniem przenośnym i jego ustawieniami. Niektóre możliwe czynności do wypróbowania:

Wypróbuj ten | Informacje wskazówki
--------- | ------------
Ponowne uruchamianie urządzenia przenośnego | Czasami urządzenie wymaga odświeżenia. Ponowne uruchomienie urządzenia kończy wszystkie procesy w tle, które są obecnie uruchomione i mogą powodować problemy, a także odświeża podstawowe składniki urządzenia, uruchamiając je ponownie na wypadek awarii w pewnym momencie.
Sprawdź, czy informacje zabezpieczające są poprawne | Upewnij się, że informacje o metodzie weryfikacji zabezpieczeń są dokładne, a zwłaszcza numery telefonów. Jeśli umieścisz w niewłaściwym numerze telefonu, wszystkie alerty przechodzą do tej niepoprawnej liczby. Na szczęście ten użytkownik nie będzie mógł wykonywać żadnych czynności dotyczących alertów, ale również nie pomoże Ci zalogować się do konta. Aby upewnić się, że informacje są poprawne, zapoznaj się z instrukcjami w artykule [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md) .
Sprawdź, czy powiadomienia są włączone | Upewnij się, że na urządzeniu przenośnym włączono powiadomienia i że została wybrana metoda powiadamiania, która umożliwia nawiązywanie połączeń telefonicznych, aplikacji uwierzytelniania i aplikacji do obsługi wiadomości (w przypadku wiadomości SMS) do wysyłania powiadomień o alertach na urządzeniu przenośnym.
Upewnij się, że masz sygnał urządzenia i połączenie internetowe | Upewnij się, że Twoje połączenia telefoniczne i wiadomości SMS są przekazywane do urządzenia przenośnego. Skontaktuj się z ty i Wyślij wiadomość SMS, aby upewnić się, że otrzymujesz oba elementy. Jeśli tego nie zrobisz, najpierw sprawdź, czy urządzenie przenośne jest włączone. Jeśli urządzenie jest włączone, ale nie masz połączenia lub tekstu, prawdopodobnie wystąpił problem z siecią i musisz skontaktować się z dostawcą. Jeśli często występują problemy związane z sygnałami, zalecamy zainstalowanie [aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md) i używanie jej na urządzeniu przenośnym. Aplikacja Authenticator może generować losowe kody zabezpieczeń do logowania, nie wymagając żadnego sygnału komórkowego ani połączenia internetowego.
Wyłącz nie przeszkadzać | Upewnij się, że na urządzeniu przenośnym nie została włączona funkcja nie **przeszkadzać** . Gdy ta funkcja jest włączona, powiadomienia nie mogą otrzymywać alertów na urządzeniu przenośnym. Instrukcje dotyczące sposobu wyłączania tej funkcji można znaleźć w podręczniku urządzenia przenośnego.
Odblokuj numery telefonów | W Stany Zjednoczone połączenia głosowe pochodzące od firmy Microsoft pochodzą z następujących liczb: + 1 (866) 539 4191, + 1 (855) 330 8653 i + 1 (877) 668 6536.
Sprawdź ustawienia związane z baterią | Jest to nietypowy bit na powierzchni, ale jeśli skonfigurowano optymalizację baterii w celu zatrzymywania nieużywanych aplikacji w tle, system powiadomień ma największe ryzyko. Aby spróbować rozwiązać ten problem, wyłącz optymalizację baterii dla aplikacji uwierzytelniania i aplikacji do obsługi wiadomości, a następnie spróbuj ponownie zalogować się do konta.
Wyłącz aplikacje zabezpieczeń innych firm | Jeśli masz aplikację chroniącą wiadomości tekstowe lub połączenia telefoniczne w celu zminimalizowania nieznanych obiektów wywołujących, mogą one uniemożliwić odebranie kodu weryfikacyjnego. Spróbuj wyłączyć wszystkie aplikacje zabezpieczeń innych firm na telefonie, a następnie poprosić o wysłanie innego kodu weryfikacyjnego.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Nie otrzymuję monitu o podanie drugiej informacji weryfikacyjnej

Jeśli zalogowano się na koncie służbowym przy użyciu nazwy użytkownika i hasła, ale nie został wyświetlony monit o podanie dodatkowych informacji weryfikacyjnych o zabezpieczeniach, być może jeszcze nie skonfigurowano urządzenia. Urządzenie przenośne musi być skonfigurowane do pracy z konkretną dodatkową metodą weryfikacji zabezpieczeń. Aby upewnić się, że urządzenie przenośne jest włączone i dostępne do użycia w ramach metody weryfikacji, zobacz artykuł [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md) . Jeśli wiesz, że nie skonfigurowano urządzenia ani Twojego konta, możesz to zrobić teraz, wykonując kroki opisane w artykule [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) .

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Mam nowy numer telefonu i chcę go dodać

Jeśli masz nowy numer telefonu, musisz zaktualizować Szczegóły metody weryfikacji zabezpieczeń, aby wyświetlić komunikaty weryfikacyjne w odpowiedniej lokalizacji. Aby zaktualizować metodę weryfikacji, wykonaj kroki opisane w sekcji **Dodawanie lub zmiana numeru telefonu** w artykule [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) .

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Mam nowe urządzenie przenośne i chcę je dodać

Jeśli masz nowe urządzenie przenośne, musisz skonfigurować je do pracy z weryfikacją dwuetapową. To jest rozwiązanie wieloetapowe:

1. Skonfiguruj swoje urządzenie do pracy z kontem służbowym, wykonując kroki opisane w artykule [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) .

1. Zaktualizuj swoje informacje o koncie i urządzeniu na stronie **dodatkowej weryfikacji zabezpieczeń** , usuwając stare urządzenie i dodając nowe. Aby uzyskać więcej informacji, zobacz artykuł [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md) .

Kroki opcjonalne:

- Pobierz, zainstaluj i skonfiguruj aplikację Microsoft Authenticator na urządzeniu przenośnym, wykonując czynności opisane w artykule [pobieranie i instalowanie aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md) .

- Włącz weryfikację dwuskładnikową dla zaufanych urządzeń, wykonując czynności opisane w sekcji **Włączanie weryfikacji dwuskładnikowej w** artykule [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) .

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Mam problemy z logowaniem się na urządzeniu przenośnym podczas podróży

Może się okazać trudne użycie metody weryfikacji powiązanej z urządzeniami przenośnymi, na przykład wiadomości SMS, podczas gdy jesteś w międzynarodowej lokalizacji. Istnieje również możliwość, że urządzenie przenośne może spowodować naliczenie opłat za roaming. W tej sytuacji zalecamy korzystanie z aplikacji Microsoft Authenticator z opcją nawiązywania połączenia z hotspotem Wi-Fi. Aby uzyskać więcej informacji o pobieraniu, instalowaniu i konfigurowaniu aplikacji Microsoft Authenticator na urządzeniu przenośnym, zobacz artykuł [pobieranie i instalowanie aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md) .

## <a name="i-cant-get-my-app-passwords-to-work"></a>Nie mogę pobrać haseł aplikacji

Hasła aplikacji zastępują normalne hasło dla starszych aplikacji klasycznych, które nie obsługują weryfikacji dwuskładnikowej. Najpierw upewnij się, że hasło zostało wpisane prawidłowo. Jeśli to nie rozwiąże problemu, spróbuj utworzyć nowe hasło aplikacji dla aplikacji, wykonując czynności opisane w sekcji **Tworzenie i usuwanie haseł aplikacji za pomocą portalu Moje aplikacje** w artykule [Zarządzanie hasłami aplikacji w celu weryfikacji dwuetapowej](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="i-cant-turn-two-factor-verification-off"></a>Nie można wyłączyć weryfikacji dwuskładnikowej

Jeśli używasz weryfikacji dwuetapowej przy użyciu konta służbowego (na przykład alain@contoso.com), najprawdopodobniej oznacza to, że Twoja organizacja zdecydowała się użyć tej dodatkowej funkcji zabezpieczeń. Ponieważ organizacja zdecydowała się na korzystanie z tej funkcji, nie ma możliwości jej samoistnienia. Jeśli jednak korzystasz z weryfikacji dwuetapowej przy użyciu konta osobistego, na przykład alain@outlook.com, możesz włączyć i wyłączyć tę funkcję. Aby uzyskać instrukcje dotyczące kontrolowania weryfikacji dwuskładnikowej dla kont osobistych, zobacz [Włączanie lub wyłączanie weryfikacji dwuskładnikowej dla konto Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Jeśli nie można wyłączyć weryfikacji dwuskładnikowej, może to być spowodowane ustawieniami domyślnymi zabezpieczeń, które zostały zastosowane na poziomie organizacji. Aby uzyskać więcej informacji na temat ustawień domyślnych zabezpieczeń, zobacz [co to jest Security defulta?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nie mogę znaleźć odpowiedzi na mój problem

Jeśli wykonano te kroki, ale nadal występują problemy, skontaktuj się z działem pomocy technicznej Twojej organizacji w celu uzyskania pomocy.

## <a name="related-articles"></a>Pokrewne artykuły:

- [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md)

- [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator app FAQ (Aplikacja Microsoft Authenticator — często zadawane pytania)](user-help-auth-app-faq.md)
