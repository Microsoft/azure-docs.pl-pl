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
ms.topic: end-user-help
ms.date: 08/20/2020
ms.author: curtand
ms.reviewer: kexia
metadata ms.custom: contperfq1
ms.openlocfilehash: b21b2a9f2d2bd483ed48a4c30b504908bb54a101
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661441"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Typowe problemy z weryfikacją dwuskładnikową i kontem służbowym

Organizacja usługi Azure Active Directory (Azure AD) może włączyć weryfikację dwuskładnikową (2FV). Istnieją pewne typowe problemy 2FV, które często zdarzają się częściej niż w przypadku dowolnych z nas. W tym artykule opisano poprawki dotyczące najczęstszych problemów.

Gdy 2FV jest włączona, logowanie do konta wymaga połączenia następujących danych:

- Twoja nazwa użytkownika
- Twoje hasło
- Urządzenie przenośne lub telefon

2FV jest bezpieczniejszy niż tylko hasło, ponieważ 2FV wymaga czegoś _znanego_ i _czegoś._ Żaden haker nie ma fizycznego telefonu.

>[!Important]
>Jeśli jesteś administratorem, możesz znaleźć więcej informacji na temat sposobu konfigurowania środowiska usługi Azure AD i zarządzania nim w [dokumentacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory).

Ta zawartość jest przeznaczona do pomocy przy użyciu konta służbowego, które jest kontem udostępnionym przez organizację (na przykład dritan@contoso.com ). Jeśli masz problemy z weryfikacją dwuetapową na konto Microsoft osobistym, czyli kontem skonfigurowanym dla siebie (na przykład danielle@outlook.com ), zobacz [Włączanie lub wyłączanie weryfikacji dwuskładnikowej dla konto Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Nie mam mojego urządzenia przenośnego

Dzieje się tak. Urządzenie przenośne zostało pozostawione w domu, a teraz nie można użyć telefonu do zweryfikowania, kim jesteś. Być może wcześniej dodano alternatywną metodę logowania się do konta, na przykład za poorednictwem telefonu biurowego. Jeśli tak, możesz użyć tej alternatywnej metody teraz. Jeśli nigdy nie dodaliśmy alternatywnej metody weryfikacji, możesz skontaktować się z działem pomocy technicznej Twojej organizacji w celu uzyskania pomocy.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Aby zalogować się do konta służbowego przy użyciu innej metody weryfikacji

1. Zaloguj się do swojego konta, ale wybierz łącze **Zaloguj w innym** miejscu na stronie **weryfikacji dwuetapowej** .

    ![Zmień metodę weryfikacji logowania](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Jeśli nie widzisz **znaku w innym sposobie** linku, oznacza to, że nie zostały skonfigurowane żadne inne metody weryfikacji. Musisz skontaktować się z administratorem, aby uzyskać pomoc w logowaniu się do konta.

2. Wybierz alternatywną metodę weryfikacji i Kontynuuj proces weryfikacji dwuetapowej.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Moje urządzenie przenośne zostało zgubione lub zostało skradzione

W przypadku zgubienia lub kradzieży urządzenia przenośnego można wykonać jedną z następujących czynności:

- Zaloguj się przy użyciu innej metody.
- Skontaktuj się z działem pomocy technicznej Twojej organizacji, aby wyczyścić Twoje ustawienia.

Zdecydowanie zalecamy umożliwienie działowi pomocy technicznej w organizacji, czy Twój telefon został zgubiony lub skradziony. Dział pomocy technicznej może wprowadzać odpowiednie aktualizacje do Twojego konta. Po wyczyszczeniu ustawień zostanie wyświetlony monit o [zarejestrowanie się w celu przeprowadzenia weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-first-time.md) przy następnym logowaniu.

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>Nie otrzymuję kodu weryfikacyjnego wysłanego do urządzenia przenośnego

Nie otrzymasz kodu weryfikacyjnego jest powszechnym problemem. Problem jest zwykle związany z urządzeniem przenośnym i jego ustawieniami. Poniżej przedstawiono niektóre akcje, które można wypróbować.

Wypróbuj ten | Informacje wskazówki
--------- | ------------
Ponowne uruchamianie urządzenia przenośnego | Czasami urządzenie wymaga odświeżenia. Po ponownym uruchomieniu urządzenia zostaną zakończone wszystkie procesy i usługi w tle. Ponowne uruchomienie powoduje także zamknięcie podstawowych składników urządzenia. Wszystkie usługi lub składniki są odświeżane po ponownym uruchomieniu urządzenia.
Sprawdź, czy informacje zabezpieczające są poprawne | Upewnij się, że informacje o metodzie weryfikacji zabezpieczeń są dokładne, a zwłaszcza numery telefonów. Jeśli umieścisz w niewłaściwym numerze telefonu, wszystkie alerty przechodzą do tej niepoprawnej liczby. Na szczęście ten użytkownik nie będzie mógł wykonywać żadnych czynności dotyczących alertów, ale również nie pomoże Ci zalogować się do konta. Aby upewnić się, że informacje są poprawne, zapoznaj się z instrukcjami w artykule [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md) .
Sprawdź, czy powiadomienia są włączone | Upewnij się, że na urządzeniu przenośnym włączono powiadomienia. Upewnij się, że są dozwolone następujące tryby powiadomień: <br/><br/> &bull; Rozmowy telefoniczne <br/> &bull; Aplikacja uwierzytelniania <br/> &bull; Twoja aplikacja do obsługi wiadomości SMS <br/><br/> Upewnij się, że te tryby tworzą alert _widoczny_ na urządzeniu.
Upewnij się, że masz sygnał urządzenia i połączenie internetowe | Upewnij się, że Twoje połączenia telefoniczne i wiadomości SMS są przekazywane do urządzenia przenośnego. Skontaktuj się z ty i Wyślij wiadomość SMS, aby upewnić się, że otrzymujesz oba elementy. Jeśli nie otrzymasz połączenia lub tekstu, najpierw upewnij się, że urządzenie przenośne jest włączone. Jeśli urządzenie jest włączone, ale nadal nie otrzymujesz połączenia lub tekstu, prawdopodobnie wystąpił problem z siecią. Musisz skontaktować się z dostawcą. Jeśli często występują problemy związane z sygnałami, zalecamy zainstalowanie [aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md) i używanie jej na urządzeniu przenośnym. Aplikacja Authenticator może generować losowe kody zabezpieczeń do logowania, nie wymagając żadnego sygnału komórkowego ani połączenia internetowego.
Wyłącz nie przeszkadzać | Upewnij się, że na urządzeniu przenośnym nie została włączona funkcja nie **przeszkadzać** . Gdy ta funkcja jest włączona, powiadomienia nie mogą otrzymywać alertów na urządzeniu przenośnym. Instrukcje dotyczące sposobu wyłączania tej funkcji można znaleźć w podręczniku urządzenia przenośnego.
Odblokuj numery telefonów | W Stany Zjednoczone połączenia głosowe pochodzące od firmy Microsoft pochodzą z następujących liczb: + 1 (866) 539 4191, + 1 (855) 330 8653 i + 1 (877) 668 6536.
Sprawdź ustawienia związane z baterią | Jest to nietypowy bit na powierzchni. Ale jeśli skonfigurowano optymalizację baterii w celu zatrzymywania nieużywanych aplikacji, które nie są aktywne w tle, system powiadomień ma największe ryzyko. Aby spróbować rozwiązać ten problem, wyłącz optymalizację baterii dla aplikacji uwierzytelniania i aplikacji do obsługi komunikatów. Następnie spróbuj ponownie zalogować się do konta.
Wyłącz aplikacje zabezpieczeń innych firm | Niektóre aplikacje zabezpieczeń telefonu blokują wiadomości tekstowe i rozmowy telefoniczne z irytujących nieznanych obiektów wywołujących. Takie aplikacje mogą uniemożliwić otrzymywanie kodu weryfikacyjnego przez telefon. Spróbuj wyłączyć wszystkie aplikacje zabezpieczeń innych firm na telefonie, a następnie poprosić o wysłanie innego kodu weryfikacyjnego.

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>Nie otrzymuję monitu o podanie drugiej informacji weryfikacyjnej

Zaloguj się do konta służbowego przy użyciu nazwy użytkownika i hasła. Następnie powinien zostać wyświetlony monit o podanie dodatkowych informacji weryfikacyjnych o zabezpieczeniach. Jeśli nie zostanie wyświetlony monit, być może jeszcze nie skonfigurowano urządzenia. Urządzenie przenośne musi być skonfigurowane do pracy z konkretną dodatkową metodą weryfikacji zabezpieczeń.

Aby upewnić się, że urządzenie przenośne jest włączone i dostępne, zapoznaj się z artykułem [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md) . Jeśli wiesz, że nie skonfigurowano urządzenia ani Twojego konta, możesz to zrobić teraz, wykonując kroki opisane w artykule [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) .

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>Mam nowy numer telefonu i chcę go dodać

Jeśli masz nowy numer telefonu, musisz zaktualizować Szczegóły metody weryfikacji zabezpieczeń. Dzięki temu można wyświetlić prośby o weryfikację w celu przejścia do odpowiedniej lokalizacji. Aby zaktualizować metodę weryfikacji, wykonaj kroki opisane w sekcji **Dodawanie lub zmiana numeru telefonu** w artykule [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) .

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>Mam nowe urządzenie przenośne i chcę je dodać

Jeśli masz nowe urządzenie przenośne, musisz skonfigurować je do pracy z weryfikacją dwuetapową. To jest rozwiązanie wieloetapowe:

1. Skonfiguruj urządzenie do pracy z Twoim kontem, wykonując kroki opisane w artykule [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md) .

1. Zaktualizuj informacje o koncie i urządzeniu na stronie **dodatkowej weryfikacji zabezpieczeń** . Wykonaj aktualizację, usuwając stare urządzenie i dodając nowe. Aby uzyskać więcej informacji, zobacz artykuł [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md) .

Kroki opcjonalne:

- Zainstaluj i skonfiguruj aplikację Microsoft Authenticator na urządzeniu przenośnym. Zainstaluj program, wykonując czynności opisane w artykule [pobieranie i instalowanie aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md) .

- Włącz weryfikację dwuskładnikową (2FV) dla zaufanych urządzeń. Włącz usługę 2FV, wykonując kroki opisane w sekcji **Włączanie weryfikacji dwuskładnikowej w** artykule [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) .

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Mam problemy z logowaniem się na urządzeniu przenośnym podczas podróży

Może się okazać trudne użycie metody weryfikacji powiązanej z urządzeniami przenośnymi, na przykład wiadomości SMS, podczas gdy jesteś w międzynarodowej lokalizacji. Istnieje również możliwość, że urządzenie przenośne może spowodować naliczenie opłat za roaming. W tej sytuacji zalecamy korzystanie z aplikacji Microsoft Authenticator z opcją nawiązywania połączenia z hotspotem Wi-Fi. Aby uzyskać więcej informacji na temat sposobu konfigurowania aplikacji Microsoft Authenticator na urządzeniu przenośnym, zobacz artykuł [pobieranie i instalowanie aplikacji Microsoft Authenticator](user-help-auth-app-download-install.md) .

## <a name="i-cant-get-my-app-passwords-to-work"></a>Nie mogę pobrać haseł aplikacji

Hasła aplikacji zastępują normalne hasło dla starszych aplikacji klasycznych, które nie obsługują weryfikacji dwuskładnikowej. Najpierw upewnij się, że hasło zostało wpisane prawidłowo. Jeśli to nie rozwiąże problemu, spróbuj utworzyć nowe hasło aplikacji dla aplikacji. Aby to zrobić, wykonaj kroki opisane w sekcji **Tworzenie i usuwanie haseł aplikacji za pomocą portalu Moje aplikacje** w artykule [Zarządzanie hasłami aplikacji w celu weryfikacji dwuetapowej](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="i-cant-turn-off-two-factor-verification"></a>Nie można wyłączyć weryfikacji dwuskładnikowej

Jeśli używasz weryfikacji dwuetapowej przy użyciu konta służbowego (na przykład alain@contoso.com ), najprawdopodobniej oznacza to, że Twoja organizacja zdecydowała się użyć tej dodatkowej funkcji zabezpieczeń. Ponieważ organizacja zdecydowała się na korzystanie z tej funkcji, nie ma możliwości jej samoistnienia. Jeśli jednak korzystasz z weryfikacji dwuetapowej przy użyciu konta osobistego, na przykład alain@outlook.com , możesz włączyć i wyłączyć tę funkcję. Aby uzyskać instrukcje dotyczące kontrolowania weryfikacji dwuskładnikowej dla kont osobistych, zobacz [Włączanie lub wyłączanie weryfikacji dwuskładnikowej dla konto Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Jeśli nie można wyłączyć weryfikacji dwuskładnikowej, może to być spowodowane ustawieniami domyślnymi zabezpieczeń, które zostały zastosowane na poziomie organizacji. Aby uzyskać więcej informacji na temat ustawień domyślnych zabezpieczeń, zobacz [co to są ustawienia domyślne zabezpieczeń?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Nie mogę znaleźć odpowiedzi na mój problem

Jeśli wykonano te kroki, ale nadal występują problemy, skontaktuj się z działem pomocy technicznej Twojej organizacji w celu uzyskania pomocy.

## <a name="related-articles"></a>Pokrewne artykuły:

- [Zarządzanie ustawieniami metody weryfikacji dwuskładnikowej](multi-factor-authentication-end-user-manage-settings.md)

- [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator app FAQ (Aplikacja Microsoft Authenticator — często zadawane pytania)](user-help-auth-app-faq.md)
