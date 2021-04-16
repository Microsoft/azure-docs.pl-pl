---
title: Rozwiązywanie problemów z logowaniem pojedynczym opartym na hasłach Azure Active Directory
description: Rozwiązywanie problemów z aplikacją usługi Azure AD skonfigurowaną na potrzeby logowania pojedynczego opartego na hasłach.
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.openlocfilehash: 2d9bcbdcb292f33d4b1f9dd1d0f6779d8d6cdd49
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376481"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Rozwiązywanie problemów z logowaniem jednokrotnym opartym na hasłach w usłudze Azure AD

Aby używać logowania jednokrotnego opartego na hasłach w aplikacji Moje aplikacje, należy zainstalować rozszerzenie przeglądarki. Rozszerzenie jest pobierane automatycznie po wybraniu aplikacji skonfigurowanej do logowania jednokrotnego opartego na hasłach. Aby dowiedzieć się więcej Moje aplikacje z perspektywy użytkownika końcowego, zobacz [Moje aplikacje pomocy portalu .](../user-help/my-apps-portal-end-user-access.md)

## <a name="my-apps-browser-extension-not-installed"></a>Moje aplikacje nie zainstalowano rozszerzenia przeglądarki
Upewnij się, że rozszerzenie przeglądarki jest zainstalowane. Aby dowiedzieć się więcej, zobacz [Planowanie Azure Active Directory Moje aplikacje wdrożenia.](my-apps-deployment-plan.md) 

## <a name="single-sign-on-not-configured"></a>Logowanie pojedyncze nie jest skonfigurowane
Upewnij się, że skonfigurowano logowanie pojedyncze oparte na hasłach. Aby dowiedzieć się więcej, [zobacz Configure password-based single sign-on (Konfigurowanie logowania pojedynczego opartego na hasłach).](configure-password-single-sign-on-non-gallery-applications.md)

## <a name="users-not-assigned"></a>Użytkownicy nie przypisani
Upewnij się, że użytkownik jest przypisany do aplikacji. Aby dowiedzieć się więcej, zobacz [Przypisywanie użytkownika lub grupy do aplikacji.](assign-user-or-group-access-portal.md)

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Poświadczenia są wypełniane, ale rozszerzenie ich nie przesyła

Ten problem zwykle występuje, gdy dostawca aplikacji ostatnio zmienił swoją stronę logowania, aby dodać pole, zmienił identyfikator używany do wykrywania pól nazwy użytkownika i hasła lub zmodyfikował sposób działania logowania w aplikacji. Na szczęście w wielu przypadkach firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Firma Microsoft dysponuje technologiami do automatycznego wykrywania przerw w integracji, ale znalezienie problemów od razu może nie być możliwe lub ich rozwiązanie może zająć trochę czasu. W przypadku, gdy jedna z tych integracji nie działa prawidłowo, otwórz przypadek pomocy technicznej, aby można go było naprawić tak szybko, jak to możliwe.

**Jeśli kontaktujesz** się z dostawcą tej aplikacji, wyślij go w nasz sposób, aby firma Microsoft pracowała z nim w celu natywnej integracji aplikacji z Azure Active Directory. Możesz wysłać dostawcę do listy aplikacji w [galerii aplikacji Azure Active Directory,](../develop/v2-howto-app-gallery-listing.md) aby rozpocząć pracę.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Poświadczenia są wypełniane i przesyłane, ale strona wskazuje, że poświadczenia są niepoprawne

Aby rozwiązać ten problem, najpierw spróbuj wykonać następujące czynności:

- Użytkownik musi najpierw spróbować **zalogować się do witryny** internetowej aplikacji bezpośrednio przy użyciu przechowywanych dla nich poświadczeń.

  * Jeśli logowanie działa, użytkownik powinien kliknąć  przycisk Aktualizuj poświadczenia  na kafelku aplikacji w sekcji Aplikacje usługi [Moje aplikacje](https://myapps.microsoft.com/) zaktualizować go do najnowszej znanej roboczej nazwy użytkownika i hasła. 

  * Jeśli ty lub inny administrator przypisał poświadczenia temu użytkownikowi, znajdź przypisanie aplikacji użytkownika lub grupy, przechodząc do karty **Grupy użytkowników &** aplikacji, wybierając przypisanie i klikając przycisk **Aktualizuj** poświadczenia.

- Jeśli użytkownik przypisał własne poświadczenia, użytkownik  powinien sprawdzić, czy jego hasło nie wygasło w **aplikacji,** a jeśli tak, zaktualizować wygasłe hasło, bezpośrednio logując się do aplikacji.

  * Po zaktualizowaniu hasła w aplikacji poproś użytkownika o  kliknięcie przycisku  Aktualizuj poświadczenia  na kafelku aplikacji w sekcji Aplikacje programu [Moje aplikacje w](https://myapps.microsoft.com/) celu zaktualizowania go do najnowszej znanej roboczej nazwy użytkownika i hasła.

  * Jeśli ty lub inny administrator przypisał poświadczenia temu użytkownikowi, znajdź przypisanie aplikacji użytkownika lub grupy, przechodząc do karty **Grupy użytkowników &** aplikacji, wybierając przypisanie i klikając przycisk **Aktualizuj** poświadczenia.

- Upewnij się Moje aplikacje że rozszerzenie przeglądarki jest uruchomione i włączone w przeglądarce użytkownika.

- Upewnij się, że użytkownicy nie próbują zalogować się do aplikacji z Moje aplikacje w trybie **incognito, inPrivate lub prywatnym.** Rozszerzenie Moje aplikacje nie jest obsługiwane w tych trybach.

Jeśli poprzednie sugestie nie działają, może to oznaczać, że po stronie aplikacji nastąpiła zmiana, która tymczasowo zerwała integrację aplikacji z usługą Azure AD. Taka możliwość może wystąpić na przykład wtedy, gdy dostawca aplikacji wprowadza na swojej stronie skrypt, który zachowuje się inaczej w przypadku danych wejściowych ręcznych i zautomatyzowanych, co powoduje, że automatyczna integracja, taka jak nasza, się przerwie. Na szczęście w wielu przypadkach firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Firma Microsoft dysponuje technologiami do automatycznego wykrywania przerw w integracji aplikacji, ale znalezienie problemów od razu może nie być możliwe lub ich rozwiązanie może zająć trochę czasu. Jeśli integracja nie działa prawidłowo, możesz otworzyć przypadek pomocy technicznej, aby rozwiązać problem tak szybko, jak to możliwe. 

Oprócz tego, jeśli kontaktujesz się z dostawcą tej aplikacji, wyślij go w nasz **sposób,**  abyśmy z nim współpracowali w celu natywnej integracji aplikacji z Azure Active Directory. Możesz wysłać dostawcę do listy aplikacji w [galerii aplikacji Azure Active Directory,](../develop/v2-howto-app-gallery-listing.md) aby rozpocząć pracę.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Sprawdź, czy strona logowania aplikacji została ostatnio zmieniona lub wymaga dodatkowego pola

Jeśli strona logowania aplikacji zmieniła się znacząco, czasami powoduje to przerwy w integracji. Na przykład gdy dostawca aplikacji dodaje do swoich doświadczeń pole logowania, certyfikat captcha lub uwierzytelnianie wieloskładnikowe. Na szczęście w wielu przypadkach firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Firma Microsoft dysponuje technologiami do automatycznego wykrywania przerw w integracji aplikacji, ale znalezienie problemów od razu może nie być możliwe lub ich rozwiązanie może zająć trochę czasu. Jeśli integracja nie działa prawidłowo, możesz otworzyć przypadek pomocy technicznej, aby rozwiązać problem tak szybko, jak to możliwe. 

Oprócz tego, jeśli kontaktujesz się z dostawcą tej aplikacji, wyślij go w nasz **sposób,**  abyśmy z nim współpracowali w celu natywnej integracji aplikacji z Azure Active Directory. Możesz wysłać dostawcę do pozycji Listing your application (Wyświetlanie listy aplikacji) w [galerii Azure Active Directory aplikacji,](../develop/v2-howto-app-gallery-listing.md) aby rozpocząć pracę.

## <a name="capture-sign-in-fields-for-an-app"></a>Przechwytywanie pól logowania dla aplikacji

Przechwytywanie pól logowania jest obsługiwane tylko w przypadku stron logowania z obsługą kodu HTML. Nie jest ona obsługiwana w przypadku niestandardowych stron logowania, takich jak te, które korzystają z programu Adobe Flash lub innych technologii, które nie mają włączonego kodu HTML.

Istnieją dwa sposoby przechwytywania pól logowania dla aplikacji niestandardowych:

- **Automatyczne przechwytywanie pól logowania** dobrze sprawdza się w przypadku większości stron logowania z obsługą kodu HTML, jeśli używają dobrze znanych identyfikatorów *DIV* dla pól nazwy użytkownika i hasła. Kod HTML na stronie jest dzielony w celu znalezienia identyfikatorów DIV, które spełniają określone kryteria. Te metadane są zapisywane, aby można je było później odtworzyć w aplikacji.

- **Przechwytywanie pól logowania ręcznego** jest używane, jeśli dostawca aplikacji nie oznaczy pól wejściowych *logowania etykietą*. Przechwytywanie ręczne jest również używane, jeśli dostawca *renderuje wiele pól, których nie można wykryć automatycznie.* Azure Active Directory (Azure AD) można przechowywać dane dla tylu pól, ile znajduje się na stronie logowania, jeśli powiesz, gdzie znajdują się te pola na stronie.

Ogólnie rzecz biorąc, jeśli automatyczne przechwytywanie pola logowania nie działa, spróbuj użyć opcji ręcznej.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Automatyczne przechwytywanie pól logowania dla aplikacji

Aby skonfigurować logowanie jednokrotne oparte na hasłach przy użyciu automatycznego przechwytywania pól logowania, wykonaj następujące kroki:
1. Otwórz witrynę [Azure Portal](https://portal.azure.com/). Zaloguj się jako administrator globalny lub współ administrator.
2. W okienku nawigacji po lewej stronie wybierz pozycję **Wszystkie usługi,** aby otworzyć rozszerzenie usługi Azure AD.
3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz **pozycję Azure Active Directory**.
4. Wybierz **pozycję Aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.
5. Wybierz **pozycję Wszystkie** aplikacje, aby wyświetlić listę aplikacji.
   > [!NOTE]
   > Jeśli nie widzisz chcieć aplikacji, użyj kontrolki **Filtr** w górnej części **listy Wszystkie** aplikacje. Ustaw opcję **Pokaż** na wartość "Wszystkie aplikacje".
6. Wybierz aplikację, którą chcesz skonfigurować dla logowania jednokrotnego.
7. Po zakończeniu ładowania aplikacji wybierz **pozycję Logowanie pojedyncze** w okienku nawigacji po lewej stronie.
8. Wybierz **pozycję Tryb logowania oparty na hasłach.**
9. Wprowadź **adres URL logowania**, który jest adresem URL strony, na której użytkownicy wprowadzają nazwę użytkownika i hasło do zalogowania. *Upewnij się, że pola logowania są widoczne na stronie dla adresu URL, który należy podać.*
10. Wybierz pozycję **Zapisz**.
    Strona zostanie automatycznie zdjętą dla pól wejściowych nazwy użytkownika i hasła. Teraz możesz używać usługi Azure AD do bezpiecznego przesyłania haseł do tej aplikacji przy użyciu Moje aplikacje przeglądarki.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Ręczne przechwytywanie pól logowania dla aplikacji

Aby ręcznie przechwycić pola logowania, musisz mieć zainstalowane Moje aplikacje przeglądarki. Ponadto przeglądarka nie może działać w trybie *inPrivate,* *incognito* ani *prywatnym.*

Aby skonfigurować logowanie jednokrotne oparte na hasłach dla aplikacji przy użyciu funkcji ręcznego przechwytywania pól logowania, wykonaj następujące kroki:
1. Otwórz witrynę [Azure Portal](https://portal.azure.com/). Zaloguj się jako administrator globalny lub współ administrator.
2. W okienku nawigacji po lewej stronie wybierz pozycję **Wszystkie usługi,** aby otworzyć rozszerzenie usługi Azure AD.
3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz **pozycję Azure Active Directory**.
4. Wybierz **pozycję Aplikacje dla przedsiębiorstw** w okienku nawigacji usługi Azure AD.
5. Wybierz **pozycję Wszystkie aplikacje,** aby wyświetlić listę aplikacji.
   > [!NOTE] 
   > Jeśli nie widzisz chętnych aplikacji, użyj kontrolki **Filtr** w górnej **części listy Wszystkie** aplikacje. Ustaw opcję **Pokaż** na wartość "Wszystkie aplikacje".
6. Wybierz aplikację, którą chcesz skonfigurować dla logowania jednokrotnego.
7. Po zakończeniu ładowania aplikacji wybierz **pozycję Logowanie pojedyncze** w okienku nawigacji po lewej stronie.
8. Wybierz **pozycję Tryb logowania oparty na hasłach.**
9. Wprowadź **adres URL logowania**, czyli stronę, na której użytkownicy wprowadźą nazwę użytkownika i hasło, aby się zalogować. *Upewnij się, że pola logowania są widoczne na stronie dla adresu URL, który należy podać.*
10. Wybierz **pozycję Configure *&lt; &gt; appname* Password Single Sign-on Settings**(Konfiguruj ustawienia logowania pojedynczego hasła w aplikacji).
11. Wybierz **pozycję Wykryj ręcznie pola logowania.**
14. Wybierz przycisk **OK**.
15. Wybierz pozycję **Zapisz**.
16. Postępuj zgodnie z instrukcjami, aby użyć Moje aplikacje.


## <a name="troubleshoot-problems"></a>Rozwiązywanie problemów

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Otrzymuję komunikat o błędzie "Nie można odnaleźć żadnych pól logowania pod tym adresem URL"

Ten komunikat o błędzie zostanie wyświetlony, gdy automatyczne wykrywanie pól logowania zakończy się niepowodzeniem. Aby rozwiązać ten problem, spróbuj ręcznie wykrywać pola logowania. Zobacz [sekcję Ręczne przechwytywanie pól logowania dla aplikacji](#manually-capture-sign-in-fields-for-an-app) w tym artykule.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Otrzymuję błąd "Nie można zapisać konfiguracji logowania pojedynczego"

Rzadko aktualizowanie konfiguracji logowania jednokrotnego kończy się niepowodzeniem. Aby rozwiązać ten problem, spróbuj ponownie zaoszczędzić konfigurację.

Jeśli błąd będzie nadal wyświetlany, otwórz przypadek pomocy technicznej. Dołącz informacje opisane w sekcjach Wyświetlanie szczegółów [](#send-notification-details-to-a-support-engineer-to-get-help) powiadomień portalu i Wyślij szczegóły powiadomienia do inżyniera pomocy technicznej, aby uzyskać pomoc w sekcjach tego artykułu. [](#view-portal-notification-details)

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Nie mogę ręcznie wykryć pól logowania dla mojej aplikacji

Jeśli wykrywanie ręczne nie działa, możesz zaobserwować następujące zachowania:
- Proces ręcznego przechwytywania wydawał się działać, ale przechwycone pola nie są poprawne.
- Poprawne pola nie są wyróżniane podczas działania procesu przechwytywania.
- Proces przechwytywania przenosi cię do strony logowania aplikacji zgodnie z oczekiwaniami, ale nic się nie dzieje.
- Ręczne przechwytywanie wydaje się działać, ale logowanie jednokrotne nie występuje, gdy użytkownicy nawigują do aplikacji z Moje aplikacje.

Jeśli wystąpi którykolwiek z tych problemów, wykonaj następujące czynności:
- Upewnij się, że masz zainstalowaną i włączoną najnowszą Moje aplikacje *przeglądarki.*
- Upewnij się, że przeglądarka nie jest w trybie *incognito,* *inPrivate* ani *prywatnym* podczas procesu przechwytywania. Rozszerzenie Moje aplikacje nie jest obsługiwane w tych trybach.
- Upewnij się, że użytkownicy nie próbują zalogować się do aplikacji z usługi Moje aplikacje w trybie *incognito,* *inPrivate* lub *Prywatnym.*
- Spróbuj ponownie wykonać proces ręcznego przechwytywania. Upewnij się, że czerwone znaczniki znajdują się nad prawidłowymi polami.
- Jeśli proces ręcznego przechwytywania wydaje się przestać odpowiadać lub strona logowania nie odpowiada, spróbuj ponownie wykonać proces ręcznego przechwytywania. Tym razem jednak po zakończeniu tego procesu naciśnij klawisz F12, aby otworzyć konsolę dewelopera przeglądarki. Wybierz **kartę konsoli.** Wpisz **window.location=" adres *&lt; URL &gt;*** logowania określony podczas konfigurowania aplikacji " , a następnie naciśnij klawisz Enter. Wymusza to przekierowanie strony, które kończy proces przechwytywania i przechowuje przechwycone pola.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>Nie mogę dodać innego użytkownika do aplikacji logowania jednokrotnego opartej na hasłach

Aplikacja logowania jednokrotnego oparta na hasłach ma limit 48 użytkowników. W związku z tym ma limit 48 kluczy dla par nazwa użytkownika/hasło na aplikację.
Jeśli chcesz dodać kolejnych użytkowników, możesz:
-   Dodawanie dodatkowego wystąpienia aplikacji
-   Najpierw usuń użytkowników, którzy już nie korzystali z aplikacji

## <a name="request-support"></a>Żądanie pomocy technicznej 
Jeśli podczas konfigurowanie logowania jednokrotnego i przypisywanie użytkowników zostanie wyświetlony komunikat o błędzie, otwórz bilet pomocy technicznej. Dołącz jak najwięcej z następujących informacji:

-   Identyfikator błędu korelacji
-   Nazwa UPN (adres e-mail użytkownika)
-   TenantID
-   Typ przeglądarki
-   Strefa czasowa i czas/ramy czasowe wystąpienia błędu
-   Ślady fiddlera

### <a name="view-portal-notification-details"></a>Wyświetlanie szczegółów powiadomień w portalu

Aby wyświetlić szczegóły dowolnego powiadomienia w portalu, wykonaj następujące kroki:
1. Wybierz **ikonę** Powiadomienia (dzwonek) w prawym górnym rogu Azure Portal.
2. Wybierz dowolne powiadomienie, które zawiera *stan Błąd.* (Mają czerwony "!").
   > [!NOTE]
   > Nie można wybrać powiadomień w stanie Powodzenie *lub* W *toku.*
3. Zostanie **otwarte okienko Szczegóły** powiadomienia. Przeczytaj informacje, aby dowiedzieć się więcej o problemie.
5. Jeśli nadal potrzebujesz pomocy, udostępnij informacje inżynierowi pomocy technicznej lub grupie produktów. Wybierz **ikonę kopiowania** po prawej stronie **pola** Błędy kopiowania, aby skopiować szczegóły powiadomienia do udostępnienia.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Wysyłanie szczegółów powiadomienia do inżyniera pomocy technicznej w celu uzyskania pomocy

Ważne jest, aby *udostępnić* wszystkim szczegółom wymienionym w tej sekcji pomoc techniczną, dzięki czemu mogą one pomóc w szybki sposób. Aby go zarejestrować, możesz zrobić zrzut ekranu lub wybrać pozycję **Błąd kopiowania**.

Poniższe informacje wyjaśniają, co oznacza każdy element powiadomienia, i zawierają przykłady.

#### <a name="essential-notification-items"></a>Podstawowe elementy powiadomień

- **Tytuł:** opisowy tytuł powiadomienia.

   Przykład: *Ustawienia serwera proxy aplikacji*

- **Opis:** co wystąpiło w wyniku operacji.

   Przykład: *Wprowadzony wewnętrzny adres URL jest już używany przez inną aplikację.*

- **Identyfikator powiadomienia:** unikatowy identyfikator powiadomienia.

    Przykład: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Identyfikator żądania klienta:** identyfikator określonego żądania wykonanego przez przeglądarkę.

    Przykład: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Sygnatura czasowa UTC:** znacznik czasu, o którym powiadomienie wystąpiło, w czasie UTC.

    Przykład: *2017-03-23T19:50:43.7583681Z*

- **Wewnętrzny identyfikator transakcji:** wewnętrzny identyfikator używany do wyszukiwania błędu w naszych systemach.

    Przykład: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN:** użytkownik, który uruchomił operację.

    Przykład: *tperkins \@ f128.info*

- **Identyfikator dzierżawy:** unikatowy identyfikator dzierżawy, do których należy użytkownik, który uruchomił operację.

    Przykład: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Identyfikator obiektu użytkownika:** unikatowy identyfikator użytkownika, który uruchomił operację.

    Przykład: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>Szczegółowe elementy powiadomień

- **Nazwa wyświetlana:**(może być pusta) bardziej szczegółowa nazwa wyświetlana błędu.

    Przykład: *Ustawienia serwera proxy aplikacji*

- **Stan:** określony stan powiadomienia.

    Przykład: *Niepowodzenie*

- **Identyfikator obiektu:**(może być pusty) identyfikator obiektu, dla którego została uruchomione operacja.

   Przykład: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Szczegóły:** szczegółowy opis tego, co wystąpiło w wyniku operacji.

    Przykład: *Wewnętrzny adres URL " " jest <https://bing.com/> nieprawidłowy, ponieważ jest już w użyciu.*

- **Błąd kopiowania:** umożliwia  wybranie ikony kopiowania  z prawej strony pola tekstowego Błąd kopiowania w celu skopiowania szczegółów powiadomienia w celu pomocy technicznej.

    Przykład:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Następne kroki
* [Seria przewodników Szybki start na temat zarządzania aplikacją](view-applications-portal.md)
* [Planowanie wdrożenia portalu Moje aplikacje](my-apps-deployment-plan.md)
