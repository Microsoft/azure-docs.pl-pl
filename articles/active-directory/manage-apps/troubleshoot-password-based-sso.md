---
title: Rozwiązywanie problemów z logowaniem jednokrotnym opartym na haśle w Azure Active Directory
description: Rozwiązywanie problemów z aplikacją usługi Azure AD, która została skonfigurowana pod kątem logowania jednokrotnego opartego na hasłach.
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 0534c85548b1d8b6203aaac4911dc851dd49d81a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460358"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Rozwiązywanie problemów z logowaniem jednokrotnym na podstawie hasła w usłudze Azure AD

Aby można było korzystać z logowania jednokrotnego opartego na hasłach w aplikacjach, należy zainstalować rozszerzenie przeglądarki. Rozszerzenie jest pobierane automatycznie po wybraniu aplikacji skonfigurowanej do logowania jednokrotnego opartego na hasłach. Aby dowiedzieć się więcej o używaniu aplikacji z perspektywy użytkowników końcowych, zobacz [Moje aplikacje — pomoc](../user-help/my-apps-portal-end-user-access.md).

## <a name="my-apps-browser-extension-not-installed"></a>Nie zainstalowano rozszerzenia przeglądarki Moje aplikacje
Upewnij się, że rozszerzenie przeglądarki zostało zainstalowane. Aby dowiedzieć się więcej, zobacz [Planowanie wdrożenia Azure Active Directory moje aplikacje](access-panel-deployment-plan.md). 

## <a name="single-sign-on-not-configured"></a>Logowanie jednokrotne nie jest skonfigurowane
Upewnij się, że skonfigurowano Logowanie jednokrotne oparte na haśle. Aby dowiedzieć się więcej, zobacz [Konfigurowanie logowania jednokrotnego opartego na haśle](configure-password-single-sign-on-non-gallery-applications.md).

## <a name="users-not-assigned"></a>Użytkownicy Nieprzypisani
Upewnij się, że użytkownik jest przypisany do aplikacji. Aby dowiedzieć się więcej, zobacz [Przypisywanie użytkownika lub grupy do aplikacji](assign-user-or-group-access-portal.md).

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Poświadczenia są wypełniane, ale rozszerzenie nie przesyła ich

Ten problem zwykle występuje, gdy dostawca aplikacji zmienił stronę logowania ostatnio, aby dodać pole, zmienić identyfikator używany do wykrywania pól username i Password lub zmodyfikować sposób działania logowania dla swojej aplikacji. Na szczęście firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Chociaż firma Microsoft ma technologie wykrywające przerwy w integracji, mogą nie być możliwe do wyszukania problemów od razu lub problemy są rozwiązywane nieco czasu. W przypadku, gdy jedna z tych integracji nie działa prawidłowo, należy otworzyć sprawę pomocy technicznej, aby można było ją rozwiązać tak szybko, jak to możliwe.

**Jeśli jesteś w kontakcie z dostawcą aplikacji,** Wyślij je, aby firma Microsoft mogła współpracować z nimi w celu natywnej integracji swojej aplikacji z Azure Active Directory. Aby rozpocząć pracę, możesz wysłać dostawcę do [listy aplikacji w galerii aplikacji Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md) .

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Poświadczenia są wypełnione i przesłane, ale strona wskazuje, że poświadczenia są niepoprawne

Aby rozwiązać ten problem, należy najpierw wykonać następujące czynności:

- Użytkownik musi najpierw próbować **zalogować się do witryny sieci Web aplikacji bezpośrednio** przy użyciu poświadczeń przechowywanych dla nich.

  * Jeśli zalogujesz się, kliknij przycisk **Aktualizuj poświadczenia** na **kafelku aplikacji** w sekcji **aplikacje** [Moje aplikacje](https://myapps.microsoft.com/) , aby zaktualizować je do najnowszej znanej, działającej nazwy użytkownika i hasła.

  * Jeśli ty lub inny administrator przypisał poświadczenia dla tego użytkownika, Znajdź przypisanie aplikacji użytkownika lub grupy, przechodząc do karty **użytkownicy & grupy** aplikacji, wybierając przypisanie i klikając przycisk **Aktualizuj poświadczenia** .

- Jeśli użytkownik przypisał własne poświadczenia, użytkownik musi **upewnić się, że hasło nie wygasło w aplikacji** , a jeśli tak, **zaktualizuj swoje wygasłe hasło** , logując się do aplikacji bezpośrednio.

  * Po zaktualizowaniu hasła w aplikacji, poproś użytkownika o kliknięcie przycisku **Aktualizuj poświadczenia** na **kafelku aplikacji** w sekcji **aplikacje** [Moje aplikacje](https://myapps.microsoft.com/) , aby zaktualizować je do najnowszej znanej, działającej nazwy użytkownika i hasła.

  * Jeśli ty lub inny administrator przypisał poświadczenia dla tego użytkownika, Znajdź przypisanie aplikacji użytkownika lub grupy, przechodząc do karty **użytkownicy & grupy** aplikacji, wybierając przypisanie i klikając przycisk **Aktualizuj poświadczenia** .

- Upewnij się, że rozszerzenie przeglądarki Moje aplikacje jest uruchomione i włączone w przeglądarce użytkownika.

- Upewnij się, że użytkownicy nie próbują zalogować się do aplikacji z moich aplikacji w **trybie incognito, InPrivate lub Private**. Rozszerzenie my Apps nie jest obsługiwane w tych trybach.

Na wypadek, gdyby poprzednie sugestie nie zadziałały, może się zdarzyć, że na stronie aplikacji wystąpił zmiana, która tymczasowo przerwał integrację aplikacji z usługą Azure AD. Na przykład może się to zdarzyć, gdy dostawca aplikacji wprowadza skrypt na stronie, która zachowuje się inaczej w przypadku ręcznych danych wejściowych programu vs, które powodują, że automatyczna integracja, taka jak własna, może zostać przerwana. Na szczęście firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Firma Microsoft ma technologie umożliwiające automatyczne wykrywanie w przypadku przerwania integracji aplikacji, jednak może nie być możliwe znalezienie problemów od razu lub problemy mogą zostać naprawione. Jeśli integracja nie działa prawidłowo, można otworzyć przypadek pomocy technicznej w celu jego ustalenia tak szybko, jak to możliwe. 

Oprócz tego, **Jeśli kontaktuje się z dostawcą aplikacji,** **Wysyłaj je do nas** , aby możemy z nich współpracować, aby w sposób natywny zintegrować swoją aplikację z Azure Active Directory. Aby rozpocząć pracę, możesz wysłać dostawcę do [listy aplikacji w galerii aplikacji Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md) .

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Sprawdź, czy strona logowania aplikacji ostatnio się zmieniła lub wymaga dodatkowego pola

Jeśli strona logowania aplikacji została zmieniona drastycznie, czasami może to spowodować przerwanie naszych integracji. Przykładem jest to, że dostawca aplikacji dodaje do swoich środowisk pole logowania, CAPTCHA lub uwierzytelnianie wieloskładnikowe. Na szczęście firma Microsoft może współpracować z dostawcami aplikacji, aby szybko rozwiązać te problemy.

Firma Microsoft ma technologie umożliwiające automatyczne wykrywanie w przypadku przerwania integracji aplikacji, jednak może nie być możliwe znalezienie problemów od razu lub problemy mogą zostać naprawione. Jeśli integracja nie działa prawidłowo, można otworzyć przypadek pomocy technicznej w celu jego ustalenia tak szybko, jak to możliwe. 

Oprócz tego, **Jeśli kontaktuje się z dostawcą aplikacji,** **Wysyłaj je do nas** , aby możemy z nich współpracować, aby w sposób natywny zintegrować swoją aplikację z Azure Active Directory. Aby rozpocząć pracę, możesz wysłać dostawcę do [listy aplikacji w galerii aplikacji Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md) .

## <a name="capture-sign-in-fields-for-an-app"></a>Przechwytywanie pól logowania dla aplikacji

Przechwytywanie pola logowania jest obsługiwane tylko dla stron logowania z obsługą języka HTML. Nie jest to obsługiwane w przypadku stron logowania niestandardowych, takich jak te, które korzystają z programu Adobe Flash lub innych technologii z obsługą języka HTML.

Istnieją dwa sposoby przechwytywania pól logowania dla aplikacji niestandardowych:

- **Automatyczne przechwytywanie pól logowania** działa dobrze z większością stron logowania obsługujących kod HTML, jeśli w polach Nazwa użytkownika i hasło *są używane dobrze znane identyfikatory DIV* . KOD HTML na stronie jest odpadków, aby znaleźć identyfikatory DIV, które pasują do określonych kryteriów. Metadane są zapisywane, dzięki czemu można je później odtworzyć do aplikacji.

- **Ręczne przechwytywanie pól logowania** jest używane, jeśli dostawca aplikacji *nie etykietuje pól danych wejściowych logowania*. Ręczne przechwytywanie jest również używane, jeśli dostawca *renderuje wiele pól, które nie mogą być wykrywane przez Autowykrywanie*. Azure Active Directory (Azure AD) mogą przechowywać dane dla tylu pól, które znajdują się na stronie logowania, Jeśli wiesz, gdzie te pola znajdują się na stronie.

Ogólnie rzecz biorąc, jeśli automatyczne przechwytywanie pól logowania nie działa, wypróbuj opcję ręczną.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Automatycznie Przechwytuj pola logowania dla aplikacji

Aby skonfigurować Logowanie jednokrotne oparte na haśle przy użyciu automatycznego przechwytywania pól logowania, wykonaj następujące czynności:
1. Otwórz witrynę [Azure Portal](https://portal.azure.com/). Zaloguj się jako Administrator globalny lub współadministrator.
2. W okienku nawigacji po lewej stronie wybierz pozycję **wszystkie usługi** , aby otworzyć rozszerzenie usługi Azure AD.
3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję **Azure Active Directory**.
4. W okienku nawigacji usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw** .
5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę aplikacji.
   > [!NOTE]
   > Jeśli nie widzisz potrzebnej aplikacji, użyj kontrolki **filtru** w górnej części listy **wszystkie aplikacje** . Dla opcji **Pokaż** wybierz wartość "wszystkie aplikacje".
6. Wybierz aplikację, którą chcesz skonfigurować dla logowania jednokrotnego.
7. Po załadowaniu aplikacji wybierz pozycję **Logowanie** jednokrotne w okienku nawigacji po lewej stronie.
8. Wybierz tryb **logowania opartego na hasłach** .
9. Wprowadź **adres URL logowania**, czyli adres URL strony, na której użytkownicy wprowadzają nazwę użytkownika i hasło, aby się zalogować. *Upewnij się, że pola logowania są widoczne na stronie dla podania adresu URL*.
10. Wybierz pozycję **Zapisz**.
    Ta strona jest automatycznie odpadków dla pól wejściowych nazwy użytkownika i hasła. Możesz teraz używać usługi Azure AD do bezpiecznego przesyłania haseł do tej aplikacji przy użyciu rozszerzenia przeglądarki Moje aplikacje.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Ręczne przechwytywanie pól logowania dla aplikacji

Aby ręcznie przechwycić pola logowania, musisz mieć zainstalowane rozszerzenie przeglądarki Moje aplikacje. Ponadto przeglądarka nie może działać w trybie *InPrivate*, *incognito*lub *prywatnym* .

Aby skonfigurować Logowanie jednokrotne na podstawie hasła dla aplikacji przy użyciu funkcji ręcznego rejestrowania pól logowania, wykonaj następujące czynności:
1. Otwórz witrynę [Azure Portal](https://portal.azure.com/). Zaloguj się jako Administrator globalny lub współadministrator.
2. W okienku nawigacji po lewej stronie wybierz pozycję **wszystkie usługi** , aby otworzyć rozszerzenie usługi Azure AD.
3. Wpisz **Azure Active Directory** w polu wyszukiwania filtru, a następnie wybierz pozycję **Azure Active Directory**.
4. W okienku nawigacji usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw** .
5. Wybierz pozycję **wszystkie aplikacje** , aby wyświetlić listę aplikacji.
   > [!NOTE] 
   > Jeśli nie widzisz potrzebnej aplikacji, użyj kontrolki **filtru** w górnej części listy **wszystkie aplikacje** . Dla opcji **Pokaż** wybierz wartość "wszystkie aplikacje".
6. Wybierz aplikację, którą chcesz skonfigurować dla logowania jednokrotnego.
7. Po załadowaniu aplikacji wybierz pozycję **Logowanie** jednokrotne w okienku nawigacji po lewej stronie.
8. Wybierz tryb **logowania opartego na hasłach** .
9. Wprowadź **adres URL logowania**, który jest stroną, w której użytkownicy wprowadzają nazwę użytkownika i hasło w celu zalogowania się. *Upewnij się, że pola logowania są widoczne na stronie dla podania adresu URL*.
10. Wybierz **pozycję * &lt; &gt; * Konfiguruj ustawienia logowanie**jednokrotne hasła.
11. Wybierz pozycję **ręcznie Wykryj pola logowania**.
14. Wybierz przycisk **OK**.
15. Wybierz pozycję **Zapisz**.
16. Postępuj zgodnie z instrukcjami, aby użyć moich aplikacji.


## <a name="troubleshoot-problems"></a>Rozwiązywanie problemów

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Otrzymuję komunikat o błędzie "nie można znaleźć pól logowania przy użyciu tego adresu URL"

Ten komunikat o błędzie jest wyświetlany, gdy automatyczne wykrywanie pól logowania zakończy się niepowodzeniem. Aby rozwiązać ten problem, spróbuj ręcznie wykryć pola logowania. Zobacz [Ręczne przechwytywanie pól logowania dla aplikacji w](#manually-capture-sign-in-fields-for-an-app) tym artykule.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Otrzymuję błąd "nie można zapisać konfiguracji logowania jednokrotnego"

Sporadycznie aktualizowanie konfiguracji logowania jednokrotnego kończy się niepowodzeniem. Aby rozwiązać ten problem, spróbuj ponownie zapisać konfigurację.

Jeśli wystąpi błąd, Otwórz zgłoszenie do pomocy technicznej. Zapoznaj się z informacjami opisanymi w sekcji [Wyświetl szczegóły powiadomień portalu](#view-portal-notification-details) i [Wyślij szczegóły powiadomienia do inżyniera pomocy technicznej, aby uzyskać pomoc dotyczącą](#send-notification-details-to-a-support-engineer-to-get-help) części tego artykułu.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Nie mogę ręcznie wykryć pól logowania dla mojej aplikacji

Podczas ręcznego wykrywania nie działa następujące zachowania:
- Proces przechwytywania ręcznego okazał się działał, ale przechwycone pola są nieprawidłowe.
- Poprawne pola nie są wyróżniane podczas uruchamiania procesu przechwytywania.
- Proces przechwytywania przenosi do strony logowania aplikacji zgodnie z oczekiwaniami, ale nic się nie dzieje.
- Ręczne przechwytywanie będzie prawdopodobnie działało, ale Logowanie jednokrotne nie odbywa się, gdy użytkownicy przechodzą do aplikacji z moich aplikacji.

Jeśli występują jakieś problemy, wykonaj następujące czynności:
- Upewnij się, że masz *zainstalowaną i włączoną*najnowszą wersję rozszerzenia przeglądarki Moje aplikacje.
- Upewnij się, że w trakcie procesu przechwytywania przeglądarka nie jest w trybie *incognito*, *InPrivate*lub *Private* . Rozszerzenie my Apps nie jest obsługiwane w tych trybach.
- Upewnij się, że użytkownicy nie próbują zalogować się do aplikacji z aplikacji w trybie *incognito*, *InPrivate*lub *Private*.
- Spróbuj ponownie wykonać ręczną procedurę przechwytywania. Upewnij się, że czerwone znaczniki znajdują się nad poprawnymi polami.
- Jeśli proces przechwytywania ręcznego wydaje się przestać odpowiadać lub strona logowania nie odpowiada, spróbuj ponownie wykonać ręczną procedurę przechwytywania. Ale tym razem po zakończeniu procesu naciśnij klawisz F12, aby otworzyć konsolę dewelopera przeglądarki. Wybierz kartę **konsola** . Wpisz **window. Location = "* &lt; adres URL logowania, który został określony podczas konfigurowania &gt; aplikacji*"**, a następnie naciśnij klawisz ENTER. Powoduje to wymuszenie przekierowania strony kończącego proces przechwytywania i przechowywania przechwyconych pól.

## <a name="request-support"></a>Żądaj obsługi 
Jeśli podczas konfigurowania logowania jednokrotnego i przypisywania użytkowników zostanie wyświetlony komunikat o błędzie, Otwórz bilet pomocy technicznej. Dołącz możliwie najwięcej następujących informacji:

-   Identyfikator błędu korelacji
-   Nazwa UPN (adres e-mail użytkownika)
-   TenantID
-   Typ przeglądarki
-   Strefa czasowa i czas/czas wystąpienia błędu
-   Ślady programu Fiddler

### <a name="view-portal-notification-details"></a>Wyświetl szczegóły powiadomień portalu

Aby wyświetlić szczegóły powiadomień portalu, wykonaj następujące kroki:
1. Wybierz ikonę **powiadomienia** (dzwonka) w prawym górnym rogu Azure Portal.
2. Wybierz dowolne powiadomienie, które pokazuje stan *błędu* . (Mają czerwoną wartość "!").
   > [!NOTE]
   > Nie można wybrać powiadomień, które są w stanie " *powodzenie* " lub " *w toku* ".
3. Zostanie otwarte okienko **szczegóły powiadomienia** . Przeczytaj informacje, aby dowiedzieć się więcej o problemie.
5. Jeśli nadal potrzebujesz pomocy, Podziel się informacjami z inżynierem pomocy technicznej lub grupą produktów. Wybierz ikonę **kopiowania** z prawej strony pola **błąd kopiowania** , aby skopiować szczegóły powiadomień do udostępnienia.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Wyślij szczegóły powiadomienia do inżyniera pomocy technicznej, aby uzyskać pomoc

Ważne jest, aby udostępnić *wszystkie* szczegóły wymienione w tej sekcji z obsługą, dzięki czemu mogą one szybko pomóc. Aby zarejestrować go, można wykonać zrzut ekranu lub wybrać **błąd kopiowania**.

Poniższe informacje wyjaśniają, co oznacza każdy element powiadomienia i zawiera przykłady.

#### <a name="essential-notification-items"></a>Podstawowe elementy powiadomień

- **Title**: opisowy tytuł powiadomienia.

   Przykład: *Ustawienia serwera proxy aplikacji*

- **Opis**: co się stało z wynikiem operacji.

   Przykład: *wprowadzony wewnętrzny adres URL jest już używany przez inną aplikację.*

- **Identyfikator powiadomienia**: unikatowy identyfikator powiadomienia.

    Przykład: *clientNotification-2adbfc06-2073-4678-A69F-7eb78d96b068*

- **Identyfikator żądania klienta**: konkretny identyfikator żądania, który wykonał przeglądarka.

    Przykład: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Sygnatura czasowa UTC**: znacznik czasu, gdy wystąpiło powiadomienie, w formacie UTC.

    Przykład: *2017-03-23T19:50:43.7583681 z*

- **Identyfikator transakcji wewnętrznej**: wewnętrzny identyfikator używany do wyszukiwania błędu w naszych systemach.

    Przykład: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **Nazwa UPN**: użytkownik, który uruchomił operację.

    Przykład: *tperkins \@ f128.info*

- **Identyfikator dzierżawy**: unikatowy identyfikator dzierżawy, do której należy użytkownik, który uruchomił operację.

    Przykład: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Identyfikator obiektu użytkownika**: unikatowy identyfikator użytkownika, który uruchomił operację.

    Przykład: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>Szczegółowe elementy powiadomień

- **Nazwa wyświetlana**: (może być pusta) bardziej szczegółową nazwę wyświetlaną dla błędu.

    Przykład: *Ustawienia serwera proxy aplikacji*

- **Stan**: określony stan powiadomienia.

    Przykład: *Niepowodzenie*

- **Identyfikator obiektu**: (może być pusty) identyfikator obiektu, względem którego została uruchomiona operacja.

   Przykład: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Szczegóły**: szczegółowy opis tego, co się stało w wyniku operacji.

    Przykład: *wewnętrzny adres URL " <https://bing.com/> " jest nieprawidłowy, ponieważ jest już używany.*

- **Błąd kopiowania**: umożliwia wybranie **ikony kopiowania** z prawej strony pola tekstowego **błąd kopiowania** , aby skopiować szczegóły powiadomień w celu uzyskania pomocy technicznej.

    Przyklad   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Następne kroki
* [Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md)
* [Planowanie wdrożenia portalu Moje aplikacje](access-panel-deployment-plan.md)
