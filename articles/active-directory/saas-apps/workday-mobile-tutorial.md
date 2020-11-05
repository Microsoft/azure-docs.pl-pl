---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą aplikacji mobilnej Workday | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i aplikacji mobilnej Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: e706649957bf427cd577d7995fb9ce104c687f4b
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378996"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą aplikacji mobilnej Workday

W tym samouczku dowiesz się, jak zintegrować Azure Active Directory (Azure AD), dostęp warunkowy i usługę Intune za pomocą aplikacji mobilnej Workday. Po zintegrowaniu aplikacji mobilnej Workday z firmą Microsoft można:

* Przed zalogowaniem się upewnij się, że urządzenia są zgodne z zasadami.
* Dodaj formanty do aplikacji mobilnej programu Workday, aby upewnić się, że użytkownicy będą bezpiecznie uzyskiwać dostęp do danych firmowych. 
* Kontrolka w usłudze Azure AD, która ma dostęp do produktu Workday.
* Zezwól użytkownikom na automatyczne logowanie do produktu Workday przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć:

* Integruj platformę Workday z usługą Azure AD.
* Przeczytaj [Azure Active Directory integrację logowania jednokrotnego (SSO) z dniem roboczym](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz zasady dostępu warunkowego usługi Azure AD oraz usługę Intune za pomocą aplikacji mobilnej Workday.

Aby włączyć logowanie jednokrotne (SSO), można skonfigurować aplikację federacyjną w dniu Workday przy użyciu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial).

> [!NOTE] 
> Usługa Workday nie obsługuje zasad ochrony aplikacji usługi Intune. Aby korzystać z dostępu warunkowego, należy użyć zarządzania urządzeniami przenośnymi.


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>Upewnij się, że użytkownicy mają dostęp do aplikacji mobilnej Workday

Skonfiguruj dzień roboczy, aby zezwolić na dostęp do swoich aplikacji mobilnych. Należy skonfigurować następujące zasady dla aplikacji Workday Mobile:

1. Uzyskaj dostęp do zasad zabezpieczeń domeny dla raportu obszaru funkcjonalnego.
1. Wybierz odpowiednie zasady zabezpieczeń:
    * Użycie urządzenia przenośnego — Android
    * Użycie urządzenia przenośnego — iPad
    * Użycie urządzenia przenośnego — iPhone
1. Wybierz pozycję **Edytuj uprawnienia**.
1. Zaznacz pole wyboru **Wyświetl lub Modyfikuj** , aby przyznać grupom zabezpieczeń dostęp do raportów lub elementów zabezpieczanych zadaniami.
1. Zaznacz pole wyboru **Get lub Put** , aby przyznać grupom zabezpieczeń dostęp do integracji i raportów lub zadań zabezpieczanych przy użyciu zadania.

Aktywuj oczekujące zmiany zasad zabezpieczeń przez uruchomienie **opcji Aktywuj oczekujące zmiany zasad zabezpieczeń**.

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>Otwórz stronę logowania produktu Workday w przeglądarce dla urządzeń z systemem Workday

Aby zastosować dostęp warunkowy do aplikacji mobilnej Workday, należy otworzyć aplikację w zewnętrznej przeglądarce. W obszarze **Edytowanie ustawień dzierżawy — zabezpieczenia** wybierz pozycję **Włącz logowanie jednokrotne w przeglądarce mobilnej dla natywnych aplikacji**. Wymaga to zainstalowania przeglądarki zatwierdzonej przez usługę Intune na urządzeniu dla systemu iOS oraz w profilu służbowym dla systemu Android.

![Zrzut ekranu przedstawiający logowanie w programie Workday Mobile Browser.](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>Konfigurowanie zasad dostępu warunkowego

Te zasady mają wpływ tylko na logowanie na urządzeniu z systemem iOS lub Android. Jeśli chcesz ją rozłożyć na wszystkie platformy, wybierz **dowolne urządzenie**. Te zasady wymagają, aby urządzenie było zgodne z zasadami i sprawdzane za pośrednictwem usługi Intune. Ponieważ system Android ma profile służbowe, uniemożliwiają one użytkownikom logowanie się w usłudze Workday, chyba że logują się za pośrednictwem ich profilu służbowego i zainstalowano aplikację za pośrednictwem portalu firmy w usłudze Intune. Istnieje jeden dodatkowy krok dla systemu iOS, aby upewnić się, że jest stosowana taka sama sytuacja.

W programie Workday są obsługiwane następujące mechanizmy kontroli dostępu:
* Wymagaj uwierzytelniania wieloskładnikowego
* Wymagaj, aby urządzenie było oznaczone jako zgodne

Aplikacja Workday nie obsługuje następujących funkcji:
* Wymaganie zatwierdzonej aplikacji klienckiej
* Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)

Aby skonfigurować program Workday jako urządzenie zarządzane, wykonaj następujące czynności:

![Zrzut ekranu tylko dla zarządzanych urządzeń i aplikacji w chmurze lub akcji.](./media/workday-tutorial/managed-devices-only.png)

1. Wybierz pozycję **Strona główna**  >  **Microsoft Intune**  >  **dostęp warunkowy — zasady**. Następnie wybierz pozycję **tylko urządzenia zarządzane**. 

1. W obszarze **tylko urządzenia zarządzane** , w obszarze **Nazwa** wybierz pozycję **tylko urządzenia zarządzane** , a następnie wybierz pozycję **aplikacje w chmurze lub akcje**.

1. W **aplikacjach lub akcjach w chmurze** :

    a. Przełącz **Wybierz, do czego dotyczy** aplikacja w **chmurze**.

    b. W obszarze **dołączanie** wybierz pozycję **Wybierz aplikacje**.

    c. Z listy **Wybierz** wybierz pozycję **Workday**.

    d. Wybierz pozycję **Gotowe**.

1. Przełącz **opcję Włącz zasady** na **włączone**.

1. Wybierz pozycję **Zapisz**.

Aby **udzielić** dostępu, wykonaj następujące czynności:

![Zrzut ekranu przedstawiający tylko zarządzane urządzenia i Udziel go.](./media/workday-tutorial/managed-devices-only-2.png)

1. Wybierz pozycję **Strona główna**  >  **Microsoft Intune**  >  **dostęp warunkowy — zasady**. Następnie wybierz pozycję **tylko urządzenia zarządzane**. 

1. Tylko w obszarze **urządzenia zarządzane** , w obszarze **Nazwa** wybierz pozycję **tylko urządzenia zarządzane**. W obszarze **Kontrole dostępu** wybierz pozycję **Udziel**.

1. W **Udziel** :

    a. Wybierz kontrolki, które mają zostać wymuszone przez **przyznanie dostępu**.

    b. Wybierz pozycję **Wymagaj, aby urządzenie było oznaczone jako zgodne**.

    c. Wybierz opcję **Wymagaj jednej z wybranych kontrolek**.

    d. Wybierz pozycję **Wybierz**.

1. Przełącz **opcję Włącz zasady** na **włączone**.

1. Wybierz pozycję **Zapisz**.

## <a name="set-up-device-compliance-policy"></a>Konfigurowanie zasad zgodności urządzeń

Aby umożliwić urządzeniom z systemem iOS logowanie się tylko za pomocą platformy Workday zarządzanej przez zarządzanie urządzeniami przenośnymi, musisz zablokować aplikację ze sklepu App Store, dodając **model com. Workday. workdayapp** do listy aplikacji z ograniczeniami. Zapewnia to, że dostęp do produktu Workday mają tylko urządzenia z zainstalowanym systemem Workday w portalu firmy. W przypadku przeglądarki urządzenia mogą uzyskać dostęp do produktu Workday tylko wtedy, gdy urządzenie jest zarządzane przez usługę Intune i używa programu Managed Browser.

![Zrzut ekranu zasad zgodności urządzeń z systemem iOS.](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>Konfigurowanie zasad konfiguracji aplikacji usługi Intune

| Scenariusz | Pary klucz-wartość |
|----------------------------------------------------------------------------------------   |-----------|
| Automatycznie Wypełnij pola dzierżawców i adresów sieci Web dla:<br>● Workday w systemie Android po włączeniu profilów programu Android for Work.<br>● Produktu Workday na urządzeniu iPad i telefonie iPhone.     | Użyj tych wartości, aby skonfigurować dzierżawcę: <br>Klucz konfiguracji ● = `UserGroupCode`<br>Typ wartości ● = ciąg <br>● Wartość konfiguracji = nazwa dzierżawy. Przykład: `gms`<br>Użyj tych wartości, aby skonfigurować adres sieci Web:<br>Klucz konfiguracji ● = `AppServiceHost`<br>Typ wartości ● = ciąg<br>● Wartość konfiguracji = podstawowy adres URL dla dzierżawy. Przykład: `https://www.myworkday.com`                                |   |
| Wyłącz te akcje dla produktu Workday na urządzeniu iPad i telefonie iPhone:<br>● Wycinanie, kopiowanie i wklejanie<br>● Druku                       | Ustaw wartość (wartość logiczna) na `False` następujące klucze, aby wyłączyć funkcję:<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| Wyłącz zrzuty ekranu dla produktu Workday w systemie Android. |Ustaw wartość (wartość logiczna) na `False` klucz, `AllowScreenshots` Aby wyłączyć funkcję.|
| Wyłącz sugerowane aktualizacje dla użytkowników.|Ustaw wartość (wartość logiczna) na `False` klucz, `AllowSuggestedUpdates` Aby wyłączyć funkcję.|
|Dostosuj adres URL sklepu z aplikacjami, aby kierować użytkowników mobilnych do wybranego sklepu z aplikacjami.|Użyj tych wartości, aby zmienić adres URL sklepu z aplikacjami:<br>Klucz konfiguracji ● = `AppUpdateURL`<br>Typ wartości ● = ciąg<br> ● — Wartość konfiguracji = adres URL sklepu App Store|
|       |


## <a name="ios-configuration-policies"></a>Zasady konfiguracji systemu iOS

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/) i zaloguj się.
1. Wyszukaj **usługę Intune** lub Wybierz widżet z listy.
1. Przejdź do pozycji **aplikacje klienckie**  >  **aplikacje**  >  **zasady konfiguracji aplikacji**. Następnie wybierz pozycję **+ Dodaj**  >  **urządzenia zarządzane**.
1. Wprowadź nazwę.
1. W obszarze **platforma** wybierz pozycję **iOS/iPadOS**.
1. W obszarze **skojarzona aplikacja** Wybierz dodaną aplikację Workday dla systemu iOS.
1. Wybierz pozycję **Ustawienia konfiguracji**. W obszarze **Format ustawień konfiguracji** wybierz pozycję **Wprowadź dane XML**.
1. Oto przykładowy plik XML. Dodaj konfiguracje, które chcesz zastosować. Zamień na `STRING_VALUE` ciąg, który ma być używany. Zamień `<true /> or <false />` na `<true />` lub  `<false />` . Jeśli nie dodasz konfiguracji, ten przykład działa tak, jak jest ustawiony na `True` .

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
1. Wybierz pozycję **Dodaj**.
1. Odśwież stronę i wybierz nowo utworzone zasady.
1. Wybierz pozycję **przypisania** , a następnie wybierz, do kogo ma być stosowana aplikacja.
1. Wybierz pozycję **Zapisz**.

## <a name="android-configuration-policies"></a>Zasady konfiguracji systemu Android

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/) i zaloguj się.
2. Wyszukaj **usługę Intune** lub Wybierz widżet z listy.
3. Przejdź do pozycji **aplikacje klienckie**  >  **aplikacje**  >  **zasady konfiguracji aplikacji**. Następnie wybierz pozycję **+ Dodaj**  >  **urządzenia zarządzane**.
5. Wprowadź nazwę. 
6. W obszarze **platforma** wybierz pozycję **Android**.
7. W obszarze **skojarzona aplikacja** Wybierz dodaną aplikację Workday dla systemu Android.
8. Wybierz pozycję **Ustawienia konfiguracji**. W obszarze **Format ustawień konfiguracji** wybierz pozycję **Wprowadź dane JSON**.

