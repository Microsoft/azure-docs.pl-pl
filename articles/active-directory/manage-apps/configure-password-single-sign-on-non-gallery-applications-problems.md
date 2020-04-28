---
title: Problemy z konfigurowaniem rejestracji jednokrotnej hasła dla aplikacji spoza galerii
description: Typowe problemy występujące podczas konfigurowania logowania jednokrotnego (SSO) hasła dla aplikacji niestandardowych, które nie znajdują się w galerii aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74274148"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problemy z konfigurowaniem logowania jednokrotnego przy użyciu hasła dla aplikacji spoza galerii

W tym artykule opisano typowe problemy, które mogą wystąpić podczas konfigurowania logowania jednokrotnego (SSO) *hasła* dla aplikacji spoza galerii.

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

    Ta strona jest automatycznie odpadków dla pól wejściowych nazwy użytkownika i hasła. Możesz teraz używać usługi Azure AD do bezpiecznego przesyłania haseł do tej aplikacji przy użyciu rozszerzenia przeglądarki panelu dostępu.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Ręczne przechwytywanie pól logowania dla aplikacji

Aby ręcznie przechwycić pola logowania, musisz mieć zainstalowane rozszerzenie przeglądarki panelu dostępu. Ponadto przeglądarka nie może działać w trybie *InPrivate*, *incognito*lub *prywatnym* .

Aby zainstalować rozszerzenie, zobacz sekcję [Instalowanie rozszerzenia przeglądarki panelu dostępu](#install-the-access-panel-browser-extension) w tym artykule.

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

10. Wybierz **pozycję * &lt;&gt; * Konfiguruj ustawienia logowanie**jednokrotne hasła.

11. Wybierz pozycję **ręcznie Wykryj pola logowania**.

14. Wybierz **przycisk OK**.

15. Wybierz pozycję **Zapisz**.

16. Postępuj zgodnie z instrukcjami, aby użyć panelu dostępu.

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

- Ręczne przechwytywanie będzie prawdopodobnie działało, ale Logowanie jednokrotne nie odbywa się, gdy użytkownicy przechodzą do aplikacji z poziomu panelu dostępu.

Jeśli występują jakieś problemy, wykonaj następujące czynności:

- Upewnij się, że masz *zainstalowaną i włączoną*najnowszą wersję rozszerzenia przeglądarki panelu dostępu. Zobacz sekcję [Instalowanie rozszerzenia przeglądarki panelu dostępu](#install-the-access-panel-browser-extension) w tym artykule.

- Upewnij się, że w trakcie procesu przechwytywania przeglądarka nie jest w trybie *incognito*, *InPrivate*lub *Private* . Rozszerzenie panelu dostępu nie jest obsługiwane w tych trybach.

- Upewnij się, że użytkownicy nie próbują zalogować się do aplikacji z poziomu panelu dostępu w trybie *incognito*, *InPrivate*lub *Private*.

- Spróbuj ponownie wykonać ręczną procedurę przechwytywania. Upewnij się, że czerwone znaczniki znajdują się nad poprawnymi polami.

- Jeśli proces przechwytywania ręcznego wydaje się przestać odpowiadać lub strona logowania nie odpowiada, spróbuj ponownie wykonać ręczną procedurę przechwytywania. Ale tym razem po zakończeniu procesu naciśnij klawisz F12, aby otworzyć konsolę dewelopera przeglądarki. Wybierz kartę **konsola** . Wpisz **window. Location = "*&lt;adres URL logowania, który został określony podczas konfigurowania aplikacji&gt;*"**, a następnie naciśnij klawisz ENTER. Powoduje to wymuszenie przekierowania strony kończącego proces przechwytywania i przechowywania przechwyconych pól.

### <a name="contact-support"></a>Kontakt z pomocą techniczną

Jeśli nadal występują problemy, otwórz sprawę z pomoc techniczna firmy Microsoft. Opisz, co się stało. Dołącz szczegóły, które są opisane w [szczegółach powiadomienia portalu](#view-portal-notification-details) i [Wyślij szczegóły powiadomienia do inżyniera pomocy technicznej, aby uzyskać pomoc dotyczącą](#send-notification-details-to-a-support-engineer-to-get-help) sekcji tego artykułu (jeśli dotyczy).

## <a name="install-the-access-panel-browser-extension"></a>Zainstaluj rozszerzenie przeglądarki panelu dostępu

Wykonaj następujące kroki:

1. Otwórz [panel dostępu](https://myapps.microsoft.com) w obsługiwanej przeglądarce. Zaloguj się do usługi Azure AD jako *użytkownik*.

2. Wybierz pozycję **hasło — logowanie JEDNOkrotne** w panelu dostępu.

3. Po wyświetleniu monitu o zainstalowanie oprogramowania wybierz pozycję **Zainstaluj teraz**.

4. Nastąpi przekierowanie do strony pobierania w przeglądarce. Wybierz, aby **dodać** rozszerzenie.

5. Jeśli zostanie wyświetlony monit, wybierz pozycję **Włącz** lub **Zezwalaj**.

6. Po zakończeniu instalacji uruchom ponownie przeglądarkę.

7. Zaloguj się do panelu dostępu. Sprawdź, czy możesz otworzyć aplikacje obsługujące Logowanie jednokrotne.

Możesz również bezpośrednio pobrać rozszerzenie Browser dla przeglądarki Chrome i Firefox, korzystając z następujących linków:

-   [Rozszerzenie panelu dostępu dla programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Wyświetl szczegóły powiadomień portalu

Aby wyświetlić szczegóły powiadomień portalu, wykonaj następujące kroki:

1. Wybierz ikonę **powiadomienia** (dzwonka) w prawym górnym rogu Azure Portal.

2. Wybierz dowolne powiadomienie, które pokazuje stan *błędu* . (Mają czerwoną wartość "!").

   > [!NOTE]
   > Nie można wybrać powiadomień, które są w stanie " *powodzenie* " lub " *w toku* ".

3. Zostanie otwarte okienko **szczegóły powiadomienia** . Przeczytaj informacje, aby dowiedzieć się więcej o problemie.

5. Jeśli nadal potrzebujesz pomocy, Podziel się informacjami z inżynierem pomocy technicznej lub grupą produktów. Wybierz ikonę **kopiowania** z prawej strony pola **błąd kopiowania** , aby skopiować szczegóły powiadomień do udostępnienia.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Wyślij szczegóły powiadomienia do inżyniera pomocy technicznej, aby uzyskać pomoc

Ważne jest, aby udostępnić *wszystkie* szczegóły wymienione w tej sekcji z obsługą, dzięki czemu mogą one szybko pomóc. Aby zarejestrować go, można wykonać zrzut ekranu lub wybrać **błąd kopiowania**.

Poniższe informacje wyjaśniają, co oznacza każdy element powiadomienia i zawiera przykłady.

### <a name="essential-notification-items"></a>Podstawowe elementy powiadomień

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

    Przykład: *tperkins\@f128.info*

- **Identyfikator dzierżawy**: unikatowy identyfikator dzierżawy, do której należy użytkownik, który uruchomił operację.

    Przykład: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Identyfikator obiektu użytkownika**: unikatowy identyfikator użytkownika, który uruchomił operację.

    Przykład: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Szczegółowe elementy powiadomień

- **Nazwa wyświetlana**: (może być pusta) bardziej szczegółową nazwę wyświetlaną dla błędu.

    Przykład: *Ustawienia serwera proxy aplikacji*

- **Stan**: określony stan powiadomienia.

    Przykład: *Niepowodzenie*

- **Identyfikator obiektu**: (może być pusty) identyfikator obiektu, względem którego została uruchomiona operacja.

   Przykład: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Szczegóły**: szczegółowy opis tego, co się stało w wyniku operacji.

    Przykład: *wewnętrzny adres URL<https://bing.com/>"" jest nieprawidłowy, ponieważ jest już używany.*

- **Błąd kopiowania**: umożliwia wybranie **ikony kopiowania** z prawej strony pola tekstowego **błąd kopiowania** , aby skopiować szczegóły powiadomień w celu uzyskania pomocy technicznej.

    Przyklad```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
