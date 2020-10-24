---
title: Pytania & odpowiedzi na temat Microsoft Authenticator App-Azure AD
description: Często zadawane pytania i odpowiedzi dotyczące aplikacji uwierzytelniania firmy Microsoft i weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 10/20/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 017898249f1ad32a37b7ccd6bce17fe3d32843f3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92480313"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>Często zadawane pytania dotyczące aplikacji Microsoft Authenticator

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące aplikacji Microsoft Authenticator. Jeśli nie widzisz odpowiedzi na swoje pytanie, przejdź do [forum aplikacji Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp).

Aplikacja Microsoft Authenticator została zastąpiona aplikacją Azure Authenticator i jest zalecaną aplikacją w przypadku korzystania z usługi Azure Multi-Factor Authentication. Aplikacja Microsoft Authenticator jest dostępna dla systemów [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) i [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="registering-a-device"></a>Rejestrowanie urządzenia

**P**: czy rejestrowanie urządzenia oznacza zgodę na dostęp do firmy lub usługi do mojego urządzenia?

Odp **.:** zarejestrowanie urządzenia zapewnia dostęp urządzenia do usług organizacji i nie zezwala na dostęp organizacji do Twojego urządzenia.

### <a name="legacy-apns-support-deprecated"></a>Starsza obsługa usługi APNs jest przestarzała

**P**: ze względu na to, że starszy interfejs binarny dla usługi Apple Push Notification Service jest przestarzały w listopadzie 2020, jak nadal używać współużytkowania Microsoft Authenticator/telefonu do logowania?

Odp **.:** [Apple ogłosiła wycofanie](https://developer.apple.com/news/?id=11042019a) powiadomień wypychanych, które używają interfejsu binarnego dla urządzeń z systemem iOS, takich jak te używane przez współczynnik telefonu. Aby nadal odbierać powiadomienia wypychane, Zalecamy zaktualizowanie aplikacji uwierzytelniania do najnowszej wersji aplikacji. W międzyczasie można obejść ten element, ręcznie sprawdzając powiadomienia w aplikacji uwierzytelniania.

### <a name="app-lock-feature"></a>Funkcja blokady aplikacji

**P**: co to jest blokada aplikacji i jak można jej użyć, aby zwiększyć bezpieczeństwo?

Odp **.: Blokada**aplikacji pomaga zapewnić bezpieczniejsze kody weryfikacyjne, informacje o aplikacji i ustawienia aplikacji. Gdy blokada aplikacji jest włączona, użytkownik zostanie poproszony o uwierzytelnienie przy użyciu numeru PIN urządzenia lub biometrycznego za każdym razem, gdy zostanie otwarty wystawca uwierzytelnienia. Blokada aplikacji pomaga również upewnić się, że jesteś jedyną osobą, która może zatwierdzać powiadomienia, monitując o kod PIN lub biometryczny podczas zatwierdzania powiadomienia logowania. Blokadę aplikacji można włączać lub wyłączać na stronie ustawień uwierzytelniania. Domyślnie blokada aplikacji jest włączona, gdy konfigurujesz kod PIN lub biometryczny na urządzeniu.<br><br>Niestety, nie ma gwarancji, że blokada aplikacji uniemożliwi dostęp do uwierzytelnienia. Wynika to z faktu, że rejestracja urządzenia może wystąpić w innych lokalizacjach poza wystawcą uwierzytelnienia, na przykład w ustawieniach konta systemu Android lub w aplikacji Portal firmy.

### <a name="windows-mobile-retired"></a>Wycofane z systemu Windows Mobile

**P**: mam urządzenie z systemem Windows Mobile, a Microsoft Authenticator w systemie Windows Mobile jest przestarzały. Czy mogę kontynuować uwierzytelnianie przy użyciu aplikacji?

Odp **.:** wszystkie uwierzytelnienia przy użyciu Microsoft Authenticator w systemie Windows Mobile zostaną wycofane po 15 lipca 2020. Zdecydowanie zalecamy użycie alternatywnej metody uwierzytelniania, aby uniknąć zablokowania konta.<br>Alternatywne opcje dla użytkowników korporacyjnych obejmują:<br><ul><li>Konfigurowanie Microsoft Authenticator dla [systemu Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) lub [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>[Konfigurowanie wiadomości SMS](multi-factor-authentication-setup-phone-number.md) do otrzymywania kodów weryfikacyjnych.</li><li>Konfigurowanie numeru telefonu do odbierania [połączeń telefonicznych w celu zweryfikowania tożsamości](multi-factor-authentication-setup-office-phone.md).</li></ul><br>Alternatywne opcje dla użytkowników indywidualnych konto Microsoft są następujące:<br><ul><li>Konfigurowanie Microsoft Authenticator dla [systemu Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) lub [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>Konfigurowanie alternatywnej metody logowania (SMS lub poczty e-mail), aktualizując informacje zabezpieczające na [stronie zabezpieczenia konta Microsoft](https://account.microsoft.com/security/).</li></ul>

### <a name="android-screenshots"></a>Zrzuty ekranu systemu Android

**P**: Czy można korzystać z zrzutów ekranu dla kodów haseł jednorazowych (OTP) w ramach uwierzytelniania systemu Android?

Odp **.: począwszy**od wersji 6.2003.1704 uwierzytelniania systemu Android, domyślnie wszystkie kody OTP są ukryte w czasie, gdy zostanie pobrany zrzut ekranu wystawcy uwierzytelnienia. Jeśli chcesz zobaczyć swoje kody OTP na zrzutach ekranu lub zezwolić innym aplikacjom na przechwytywanie ekranu uwierzytelniającego, możesz. Po prostu Włącz ustawienie **przechwytywania ekranu** w uwierzytelnianiu i uruchom ponownie aplikację.

### <a name="delete-stored-data"></a>Usuń przechowywane dane

**P**: jakie dane są przechowywane w moim imieniu przez wystawcę uwierzytelniania i jak można je usunąć?

Odp **.: aplikacja**Authenticator zbiera trzy rodzaje informacji:<ul><li>Informacje o koncie, które należy podać po dodaniu konta. Te dane można usunąć, usuwając Twoje konto.</li><li>Dane dziennika diagnostycznego, które pozostają tylko w aplikacji, do momentu wybrania opcji **Wyślij dzienniki** menu **Pomoc** aplikacji, aby wysłać dzienniki do firmy Microsoft. Te dzienniki mogą zawierać dane osobowe, takie jak adresy e-mail, adresy serwerów lub adresy IP. Mogą również zawierać dane urządzenia, takie jak nazwa urządzenia i wersja systemu operacyjnego. Wszystkie zebrane dane osobowe są ograniczone do informacji niezbędnych do rozwiązywania problemów z aplikacjami. Te pliki dzienników można przeglądać w aplikacji w dowolnym momencie, aby zobaczyć zebrane informacje. W przypadku wysłania plików dziennika inżynierowie aplikacji uwierzytelniania będą korzystać z nich tylko w celu rozwiązywania problemów zgłoszonych przez klientów.</li><li>Dane użycia nieidentyfikowalnego przez użytkownika, takie jak "uruchomiono Dodawanie przepływu konta/pomyślnie dodano konto" lub "powiadomienie zostało zatwierdzone". Te dane są integralną częścią naszych decyzji inżynieryjnych. Twoje użycie pomaga nam określić, gdzie możemy ulepszyć aplikacje w sposób, który są dla Ciebie ważne. Gdy aplikacja jest używana po raz pierwszy, zobaczysz powiadomienie o tej kolekcji danych. Informuje o tym, że można ją wyłączyć na stronie **ustawień** aplikacji. To ustawienie można włączyć lub wyłączyć w dowolnym momencie.</li></ul>

### <a name="codes-in-the-app"></a>Kody w aplikacji

**P**: Jakie są kody w aplikacji?

Odp **.: po**otwarciu uwierzytelniania zobaczysz dodane konta jako kafelki. Konta służbowe i osobiste konta Microsoft będą widoczne sześć lub osiem cyfr w widoku pełny ekran konta (dostęp przez naciśnięcie kafelka konta). W przypadku innych kont zobaczysz sześć-lub osiem cyfr na stronie **konta** aplikacji.<br>Te kody są używane jako hasło jednokrotne, aby sprawdzić, czy użytkownik jest zalogowany. Po zalogowaniu się przy użyciu nazwy użytkownika i hasła wpisz kod weryfikacyjny, który jest skojarzony z tym kontem. Na przykład jeśli Katy się na konto contoso, naciśnij kafelek konta, a następnie użyj kodu weryfikacyjnego 895823. W przypadku konta programu Outlook należy wykonać te same czynności.<br>Naciśnij kafelek konto contoso.<br>![Kafelki kont w aplikacji Authenticator](media/user-help-auth-app-faq/katy-signin.png)<br>Po naciśnięciu kafelka konta Contoso kod weryfikacyjny będzie widoczny na pełnym ekranie.<br>![Kod weryfikacyjny na kafelku konta w usłudze Authenticator](media/user-help-auth-app-faq/verification-code.png)

### <a name="countdown-timer"></a>Czasomierz odliczania

**P**: Dlaczego liczba obok kodu jest w trakcie zliczania?

Odp **.: można**zobaczyć 30-sekundowe zliczanie czasomierza obok aktywnego kodu weryfikacyjnego. Ten czasomierz jest tak, aby nigdy nie logować się przy użyciu tego samego kodu dwa razy. W przeciwieństwie do hasła nie chcemy zapamiętać tej liczby. Pomysłem jest to, że tylko ktoś z dostępem do telefonu wie swój kod.

### <a name="grayed-account-tile"></a>Kafelek konta wyszarzonego

**P**: Dlaczego mój kafelek konta jest szary?

Odp.: niektóre organizacje **wymagają, aby**uwierzytelnienie działało z logowaniem jednokrotnym i chronić zasoby organizacji. W tej sytuacji konto nie jest używane na potrzeby weryfikacji dwuetapowej i jest wyświetlane jako szare lub nieaktywne. Ten typ konta jest często nazywany "brokerem".

### <a name="device-registration"></a>Rejestracja urządzenia

**P**: co to jest rejestracja urządzenia?
Odp **.:** organizacja może wymagać zarejestrowania urządzenia w celu śledzenia dostępu do zabezpieczonych zasobów, takich jak pliki i aplikacje. Mogą również włączyć dostęp warunkowy w celu zmniejszenia ryzyka niepożądanego dostępu do tych zasobów. Możesz wyrejestrować urządzenie w **ustawieniach**, ale możesz utracić dostęp do wiadomości e-mail w programie Outlook, w plikach w usłudze OneDrive i utracisz możliwość logowania za pomocą telefonu.

### <a name="verification-codes-when-connected"></a>Kody weryfikacyjne w przypadku połączenia

**P**: Czy muszę mieć połączenie z Internetem lub moją siecią, aby uzyskać i korzystać z kodów weryfikacyjnych?

Odp **.: kody**nie wymagają połączenia z Internetem ani nie są połączone z danymi, więc nie potrzebujesz usługi telefonicznej do logowania. Ponadto, ponieważ aplikacja przestaje działać natychmiast po jej zamknięciu, nie spowoduje to wyczerpania baterii.

### <a name="no-notifications-when-app-is-closed"></a>Brak powiadomień po zamknięciu aplikacji

**P**: Dlaczego otrzymuję powiadomienia tylko wtedy, gdy aplikacja jest otwarta? Gdy aplikacja zostanie ZAMKNIĘTA, nie otrzymuję powiadomień.

Odp **.: w przypadku**otrzymywania powiadomień, ale nie alertu, nawet w przypadku korzystania z dzwonka na, należy sprawdzić ustawienia aplikacji. Upewnij się, że aplikacja jest włączona, aby używać dźwięku lub aby przekonywać wibracji w celu otrzymywania powiadomień. Jeśli w ogóle nie otrzymujesz powiadomień, należy sprawdzić następujące warunki:<ul><li>Czy Twój telefon w trybie nie przeszkadza ani nie jest cichy? Te tryby mogą uniemożliwić aplikacjom wysyłanie powiadomień.</li><li>Czy można otrzymywać powiadomienia z innych aplikacji? Jeśli nie, może to być problem z połączeniami sieciowymi na telefonie lub kanałem powiadomień z systemu Android lub Apple. Możesz spróbować rozwiązać połączenia sieciowe przy użyciu ustawień telefonu. Może być konieczne skontaktowanie się z dostawcą usług w celu uzyskania pomocy dotyczącej kanału powiadomień dla systemu Android lub Apple.</li><li>Czy można otrzymywać powiadomienia dla niektórych kont w aplikacji, ale nie do innych? Jeśli tak, Usuń problematyczne konto z aplikacji, Dodaj je ponownie, zezwalając na powiadomienia, i sprawdź, czy ten problem został rozwiązany.</li></ul>Jeśli podjęto próbę wykonania wszystkich kroków i nadal występują problemy, zalecamy wysłanie plików dziennika do celów diagnostycznych. Otwórz aplikację, przejdź do **pomocy**, a następnie wybierz pozycję **Wyślij dzienniki**. Następnie przejdź do [forum aplikacji Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) i poinformuj nas o problemie, który widzisz, oraz o podjętych krokach.

### <a name="switch-to-push-notifications"></a>Przełącz do powiadomień wypychanych

**P**: używam kodów weryfikacyjnych w aplikacji, ale jak przełączać się do powiadomień wypychanych?

Odp **.:** możesz skonfigurować powiadomienia dla konta służbowego (jeśli jest to dozwolone przez administratora) lub do osobistego konto Microsoft. Powiadomienia nie będą działały dla kont innych firm, takich jak Google lub Facebook.<br>Aby przełączyć konto osobiste do powiadomień, należy ponownie zarejestrować urządzenie przy użyciu konta. Przejdź do pozycji **Dodaj konto**, wybierz pozycję **osobiste konto Microsoft**, a następnie zaloguj się przy użyciu nazwy użytkownika i hasła.<br>W przypadku konta służbowego organizacja decyduje, czy zezwolić na powiadomienia jednego kliknięcia.

### <a name="notifications-for-other-accounts"></a>Powiadomienia dla innych kont

**Pytanie**: czy powiadomienia działają dla kont innych niż Microsoft?

Odp **.: nie**, powiadomienia działają tylko z kontami Microsoft i kontami Azure Active Directory. Jeśli Twoja firma lub szkoła korzysta z kont usługi Azure AD, można wyłączyć tę funkcję.

### <a name="backup-and-recovery"></a>Tworzenie i przywracanie kopii zapasowych

**P**: otrzymałem nowe urządzenie lub przywrócono moje urządzenie z kopii zapasowej. Jak mogę ponownie skonfigurować moje konta w ramach uwierzytelniania?

Odp **.: Jeśli**w starym urządzeniu włączono **Tworzenie kopii zapasowych w chmurze** , możesz użyć starej kopii zapasowej do odzyskania poświadczeń konta na nowym urządzeniu z systemem iOS lub Android. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie kopii zapasowej i odzyskiwanie poświadczeń konta z wystawcą uwierzytelnienia](user-help-auth-app-backup-recovery.md) .

### <a name="lost-device"></a>Zgubione urządzenie

**P**: Utracono moje urządzenie lub przeniesiono je na nowe urządzenie. Jak mogę upewnij się, że powiadomienia nie przejdą dalej na moje stare urządzenie?

Odp **.: dodanie**uwierzytelniania do nowego urządzenia nie powoduje automatycznego usunięcia aplikacji ze starego urządzenia. Nawet usunięcie aplikacji ze starego urządzenia jest niewystarczające. Należy usunąć aplikację ze starego urządzenia i poinformować firmę Microsoft lub organizację, aby zapomnieć i wyrejestrować stare urządzenie.<ul><li>**Aby usunąć aplikację z urządzenia przy użyciu konto Microsoft osobistego.** Przejdź do obszaru weryfikacji dwuetapowej na stronie [zabezpieczenia konta](https://account.microsoft.com/security)   i wybierz wyłączenie weryfikacji dla starego urządzenia.</li><li>**Aby usunąć aplikację z urządzenia przy użyciu konto Microsoft służbowego.** Przejdź do obszaru weryfikacji dwuetapowej [strony Moje aplikacje](https://myapps.microsoft.com/) lub portalu niestandardowego w organizacji, aby wyłączyć weryfikację dla starego urządzenia.</li></ul>

### <a name="remove-account-from-app"></a>Usuń konto z aplikacji

**P**: Jak mogę usunąć konto z aplikacji?

Odp **.: naciśnij**kafelek konta dla konta, które chcesz usunąć z aplikacji, aby wyświetlić pełny ekran konta. Naciśnij pozycję **Usuń konto** , aby usunąć konto z aplikacji.<br>Jeśli masz urządzenie zarejestrowane w organizacji, możesz potrzebować dodatkowego kroku, aby usunąć konto. Na tych urządzeniach wystawca uwierzytelnienia jest automatycznie rejestrowany jako administrator urządzenia. Jeśli chcesz całkowicie odinstalować aplikację, musisz najpierw wyrejestrować aplikację w ustawieniach aplikacji.

### <a name="too-many-permissions"></a>Zbyt wiele uprawnień

**P**: Dlaczego aplikacja żąda tylu uprawnień?

Odp **.:** Oto pełna lista uprawnień, które mogą zostać poproszeni i jak są używane przez aplikację. Określone uprawnienia są zależne od typu telefonu.<ul><li>**Używaj sprzętu biometrycznego.** Niektóre konta służbowe wymagają dodatkowego numeru PIN przy każdej weryfikacji tożsamości. Aplikacja wymaga zgody użytkownika na korzystanie z biometrycznej lub rozpoznawania twarzy zamiast wprowadzania numeru PIN.</li><li>**Cyfrowej.** Służy do skanowania kodów QR po dodaniu pracy, szkoły lub innej konto Microsoft.</li><li>**Kontakty i telefon.** Aplikacja wymaga tego uprawnienia do wyszukiwania służbowych kont Microsoft na telefonie i dodawania ich do aplikacji.</li><li>**Dotycząc.** Służy do upewnienia się, że Twój numer telefonu pasuje do numeru w rekordzie, gdy logujesz się za pomocą osobistego konto Microsoft po raz pierwszy. Wysyłamy wiadomość SMS na telefon, na którym zainstalowano aplikację, która zawiera kod weryfikacyjny 6-8 cyfry. Nie musisz znaleźć tego kodu i wprowadzić go, ponieważ wystawca uwierzytelnienia automatycznie w wiadomości tekstowej.</li><li>**Rysuj na inne aplikacje.** Powiadomienie, które umożliwia zweryfikowanie tożsamości, jest również wyświetlane w dowolnej innej działającej aplikacji.</li><li>**Odbieraj dane z Internetu.** To uprawnienie jest wymagane do wysyłania powiadomień.</li><li>**Zapobiegaj używaniu telefonu do uśpienia.** Jeśli zarejestrujesz swoje urządzenie w organizacji, Twoja organizacja może zmienić te zasady na telefonie.</li><li>**Kontroluj wibracje.** Możesz wybrać, czy chcesz przetworzyć drgania za każdym razem, gdy otrzymasz powiadomienie, aby zweryfikować Twoją tożsamość.</li><li>**Używaj sprzętu odcisków palców.** Niektóre konta służbowe wymagają dodatkowego numeru PIN przy każdej weryfikacji tożsamości. Aby uprościć proces, możemy użyć odcisku palca zamiast wprowadzać kod PIN.</li><li> **Wyświetlanie połączeń sieciowych.** Po dodaniu konto Microsoft aplikacja wymaga połączenia z siecią/Internetem.</li><li>**Odczytaj zawartość magazynu**. To uprawnienie jest używane tylko wtedy, gdy zgłaszasz problem techniczny przy użyciu ustawień aplikacji. Niektóre informacje z magazynu są zbierane w celu zdiagnozowania problemu.</li><li>**Pełny dostęp do sieci.** To uprawnienie jest wymagane do wysyłania powiadomień w celu zweryfikowania tożsamości.</li><li>**Uruchamiany przy uruchamianiu.** Po ponownym uruchomieniu telefonu to uprawnienie umożliwia kontynuowanie otrzymywania powiadomień w celu zweryfikowania tożsamości.</li></ul>

### <a name="approve-requests-without-unlocking"></a>Zatwierdzanie żądań bez odblokowywania

**P**: Dlaczego wystawca uwierzytelnienia pozwala na zatwierdzenie żądania bez odblokowywania urządzenia?

Odp.: nie trzeba odblokowywać **urządzenia, aby**zatwierdzać żądania weryfikacji, ponieważ wszystko, co trzeba udowodnić, ma Twój telefon. Weryfikacja dwuetapowa wymaga udowodnienia dwóch rzeczy — ty wiesz i masz. Jest to hasło użytkownika. Jest to Twój numer telefonu (skonfigurowany przy użyciu uwierzytelniania za pomocą usługi Authenticator i zarejestrowany w ramach weryfikacji usługi MFA). W związku z tym, posiadanie telefonu i zatwierdzenie żądania spełnia kryteria drugiego czynnika uwierzytelniania.

### <a name="activity-notifications"></a>Powiadomienia o działaniach

**P**: Dlaczego otrzymuję powiadomienia o aktywności mojego konta?

Odp **.: powiadomienia o**działaniach są wysyłane do uwierzytelniania natychmiast po wprowadzeniu zmiany do osobistych kont Microsoft, co pomaga zapewnić bezpieczniejsze działanie. Te powiadomienia zostały wcześniej wysłane tylko za pośrednictwem poczty e-mail i wiadomości SMS. Aby uzyskać więcej informacji o tych powiadomieniach dotyczących działań, zobacz [co się stanie w przypadku nietypowego logowania do konta](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in). Aby zmienić miejsce otrzymywania powiadomień, zaloguj się do miejsca, w [którym można się skontaktować z alertami konta niekrytycznego](https://account.live.com/SecurityNotifications/Update) na koncie.

### <a name="one-time-passcodes"></a>Jednorazowe kody dostępu

**P**: moje jednorazowe kody dostępu nie działają. Co mam zrobić?

Odp **.: Upewnij się, że**Data i godzina na urządzeniu są poprawne i są automatycznie synchronizowane. Jeśli data i godzina są błędne lub nie są zsynchronizowane, kod nie będzie działał.

### <a name="windows-10-mobile"></a>Windows 10 Mobile

**P**: system operacyjny Windows 10 Mobile był przestarzały z grudnia 2019. Czy Microsoft Authenticator w systemach operacyjnych Windows Mobile będzie również przestarzała?

Odp **.:** wystawca uwierzytelnienia we wszystkich systemach operacyjnych Windows Mobile nie będzie obsługiwany po 28 lutego 2020. Użytkownicy nie będą uprawnieni do otrzymywania nowych aktualizacji w celu opublikowania w tej dacie wymienionej daty. Po 28 lutego 2020 usługi firmy Microsoft, które aktualnie obsługują uwierzytelnianie przy użyciu Microsoft Authenticator we wszystkich systemach operacyjnych Windows Mobile, rozpoczną korzystanie z pomocy technicznej. Aby można było uwierzytelnić się w usługach firmy Microsoft, zdecydowanie zachęcamy wszystkich naszych użytkowników do przełączania się do alternatywnego mechanizmu uwierzytelniania przed tą datą.

### <a name="default-mail-app"></a>Domyślna aplikacja poczty

**P**: podczas logowania do konta służbowego przy użyciu domyślnej aplikacji poczty dołączonej do systemu iOS otrzymuję monit o uwierzytelnienie w celu uzyskania informacji weryfikacyjnych o zabezpieczeniach. Po wprowadzeniu tych informacji i powrocie do aplikacji poczty zostanie wyświetlony komunikat o błędzie. Co mogę zrobić?

Odp.: to najbardziej prawdopodobną **przyczyną jest to**, że logowanie i Twoja aplikacja poczty odbywają się w dwóch różnych aplikacjach, co powoduje, że proces logowania w tle nie działa i kończy się niepowodzeniem. Aby spróbować rozwiązać ten problem, zalecamy wybranie ikony **Safari** w prawym dolnym rogu ekranu podczas logowania się do aplikacji poczty e-mail. Przechodzenie do przeglądarki Safari powoduje, że cały proces logowania odbywa się w jednej aplikacji, co umożliwia pomyślne zalogowanie się do aplikacji.

### <a name="apple-watch-watchos-7"></a>Apple Watch systemu watchOS 7

**P**: Dlaczego mam problemy z Apple Watch w systemu watchOS 7?

Odp **.: występuje**problem z zatwierdzaniem powiadomień w programie systemu watchOS 7. Firma Microsoft współpracuje z firmą Apple w celu uzyskania tego stałego. W międzyczasie wszystkie powiadomienia, które wymagają aplikacji Microsoft Authenticator systemu watchOS, powinny być zatwierdzone na telefonie.

### <a name="apple-watch-doesnt-show-accounts"></a>Apple Watch nie pokazuje kont

**P**: Dlaczego nie wszystkie moje konta są wyświetlane, gdy otwieram wystawcę uwierzytelnienia na moim Apple Watch?

Odp.: wystawca **jest**obsługiwany tylko na kontach osobistych lub służbowych firmy Microsoft przy użyciu powiadomień wypychanych w aplikacji pomocnika Apple Watch. W przypadku innych kont, takich jak Google lub Facebook, musisz otworzyć aplikację Authenticator na telefonie, aby wyświetlić kody weryfikacyjne.

### <a name="apple-watch-notifications"></a>Powiadomienia Apple Watch

**P**: Dlaczego nie mogę zatwierdzić ani odmówić powiadomienia na moim Apple Watch?

Odp **.: najpierw**upewnij się, że uaktualniono do wersji 6.0.0 lub nowszej na telefonie iPhone. Następnie otwórz Microsoft Authenticator aplikację pomocnika na Apple Watch i odszukaj wszystkie konta z przyciskiem **skonfigurowanym** poniżej. Ukończ proces instalacji, aby zatwierdzić powiadomienia dla tych kont.

### <a name="apple-watch-communication-error"></a>Błąd komunikacji Apple Watch

**P**: otrzymuję błąd komunikacji między Apple Watch i moim telefonem. Co mogę zrobić, aby rozwiązać problem?

Odp.: ten błąd występuje **,** gdy ekran czujki przechodzi w stan uśpienia przed zakończeniem komunikacji z telefonem.<br><b>Jeśli błąd występuje podczas instalacji:</b><br>Spróbuj ponownie uruchomić Instalatora, aby upewnić się, że czujka jest w stanie aktywności, dopóki proces nie zostanie wykonany. W tym samym czasie Otwórz aplikację na telefonie i Odpowiedz na wszystkie wyświetlane komunikaty.<br>Jeśli Twój telefon i czujka nadal nie komunikują się, możesz spróbować wykonać następujące czynności:<ol><li>Wymuś wyjście z aplikacji Microsoft Authenticator Phone i otwórz ją ponownie na telefonie iPhone.</li><li>Wymuś zamknięcie aplikacji pomocnika na Apple Watch.<ol><li> Otwórz aplikację pomocnika Microsoft Authenticator na Twoim ekranie</li><li>Przytrzymaj przycisk boczny do momentu wyświetlenia ekranu **zamykania** .</li><li>Zwolnij przycisk boczny i przytrzymaj Digital Crown, aby wymusić zakończenie aktywnej aplikacji.</li></ol></li><li>Wyłącz zarówno połączenia Bluetooth, jak i Wi-Fi dla telefonu i czujki, a następnie włącz je ponownie.</li><li>Uruchom ponownie telefon iPhone i czujkę.</li></ol><b>Jeśli wystąpi błąd podczas próby zatwierdzenia powiadomienia:</b><br>Przy następnej próbie zatwierdzenia powiadomienia na Apple Watch zachować aktywności ekranu, dopóki żądanie nie zostanie ukończone, i usłyszysz dźwięk wskazujący, że zakończono pomyślnie.

### <a name="apple-watch-companion-app-not-syncing"></a>Nie zsynchronizowano aplikacji pomocnika Apple Watch

**P**: dlaczego nie Microsoft Authenticator aplikacji pomocnika do Apple Watch synchronizacji lub wyświetlania w mojej czujce?

Odp.: Jeśli aplikacja nie jest wyświetlana na Twoim ekranie, spróbuj wykonać następujące **czynności:** <ol><li>Upewnij się, że na Twoim ekranie jest uruchomiony program systemu watchOS 4,0 lub nowszy.</li><li>Ponownie zsynchronizuj czujkę.</li></ol>

### <a name="apple-watch-companion-app-crashed"></a>Apple Watch awaria aplikacji pomocnika

**P**: wystąpiła awaria aplikacji pomocnika my Apple Watch. Czy mogę wysłać do Ciebie dzienniki awaryjne, aby móc zbadać?

Odp **.: najpierw**musisz się upewnić, że wybrano opcję udostępnienia analiz. Jeśli jesteś użytkownikiem usługi TestFlight, nastąpiło już zarejestrowanie. W przeciwnym razie możesz przejść do pozycji **ustawienia > prywatność > Analytics** i wybrać opcje **udostępnij telefon iPhone & Obejrzyj Analytics** i **udostępnić je deweloperom aplikacji** .<br>Po utworzeniu konta możesz spróbować odtworzyć awarię, aby dzienniki awarii były automatycznie wysyłane do firmy Microsoft w celu zbadania go. Jeśli jednak nie możesz odtworzyć awarii, możesz ręcznie skopiować pliki dziennika i wysłać je do nas.<ol><li>Otwórz aplikację Watch na telefonie, przejdź do pozycji **ustawienia > ogólne**, a następnie kliknij pozycję **Kopiuj Obejrzyj analizę**.</li><li>Znajdź odpowiednią awarię w obszarze **ustawienia > prywatność > analiza > dane analityczne**, a następnie ręcznie Skopiuj cały tekst.</li><li>Otwórz program Authenticator na telefonie i wklej skopiowany tekst do pola tekstowego **Udostępnij deweloperom aplikacji** na stronie **wysyłanie dzienników** .</li></ol>

## <a name="next-steps"></a>Następne kroki

- Jeśli masz problemy z uzyskaniem kodu weryfikacyjnego dla konto Microsoft osobistych, zobacz sekcję **Rozwiązywanie problemów z kodem weryfikacyjnym** w artykule [konto Microsoft informacje zabezpieczające & kody weryfikacyjne](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- Jeśli potrzebujesz więcej informacji na temat weryfikacji dwuetapowej, zobacz [Konfigurowanie mojego konta na potrzeby weryfikacji dwuetapowej](multi-factor-authentication-end-user-first-time.md)

- Aby uzyskać więcej informacji na temat informacji zabezpieczających, zobacz [informacje zabezpieczające (wersja zapoznawcza) — Omówienie](./security-info-setup-signin.md)

- Jeśli Twoje pytanie nie zostało odebrane w tym miejscu, chcemy poznać Twoją opinię. Przejdź do [forum aplikacji Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp) , aby opublikować pytanie i uzyskać pomoc od społeczności lub pozostawić komentarz na tej stronie.
