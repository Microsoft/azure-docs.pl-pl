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
ms.openlocfilehash: 256da169761da486d8ac064a2f58a59be43bb5df
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754723"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą aplikacji mobilnej Workday

W tym samouczku dowiesz się, jak zintegrować Azure Active Directory (Azure AD), dostęp warunkowy i usługę Intune z Mobile Appsem produktu Workday. Po zintegrowaniu Mobile Apps produktu Workday z firmą Microsoft można:

* Upewnij się, że urządzenia są zgodne z zasadami przed zalogowaniem się.
* Dodaj formanty do aplikacji w usłudze Workday, aby zapewnić użytkownikom bezpieczny dostęp do danych firmowych. 
* Kontrolka w usłudze Azure AD, która ma dostęp do produktu Workday.
* Zezwól użytkownikom na automatyczne logowanie do produktu Workday przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Integrowanie produktu Workday z usługą Azure AD
* Samouczek: [Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu produktu Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz zasady dostępu warunkowego firmy Microsoft oraz usługi Intune z aplikacjami mobilnymi w programie Workday.

* Aplikacje federacyjne w programie Workday można teraz skonfigurować za pomocą usługi Azure AD w celu włączenia logowania jednokrotnego. Aby uzyskać więcej informacji na temat sposobu konfigurowania, użyj [tego](workday-tutorial.md) linku.

> [!NOTE] 
> Usługa Workday nie obsługuje zasad ochrony aplikacji usługi Intune. Aby korzystać z dostępu warunkowego, należy użyć zarządzania urządzeniami przenośnymi.


## <a name="ensure-users-have-access-to-the-workday-mobile-app"></a>Upewnij się, że użytkownicy mają dostęp do aplikacji mobilnej Workday:

Skonfiguruj dzień roboczy, aby zezwolić na dostęp do ofert aplikacji mobilnych. Należy skonfigurować następujące zasady dla urządzeń przenośnych:

Można je skonfigurować, wykonując następujące instrukcje:

1. Uzyskaj dostęp do zasad zabezpieczeń domeny dla raportu obszaru funkcjonalnego.
2. Wybierz zasady zabezpieczeń.
    * Użycie urządzenia przenośnego — Android
    * Użycie urządzenia przenośnego — iPad
    * Użycie urządzenia przenośnego — iPhone
3. Kliknij pozycję Edytuj uprawnienia.
4. Zaznacz pole wyboru Wyświetl lub Modyfikuj, aby przyznać grupom zabezpieczeń dostęp do raportów lub elementów zabezpieczanych zadaniami.
5. Zaznacz pole wyboru get lub Put, aby przyznać grupom zabezpieczeń dostęp do integracji i raportów lub zadań zabezpieczanych przy użyciu zadania.

Aktywuj oczekujące zmiany zasad zabezpieczeń, uruchamiając zadanie **Aktywuj oczekujące zmiany zasad zabezpieczeń** .

## <a name="open-workday-login-page-in-mobile-browser"></a>Otwórz stronę logowania produktu Workday w przeglądarce mobilnej:

Aby zastosować dostęp warunkowy do aplikacji mobilnej produktu Workday, jest ona wymagana do otwarcia aplikacji w zewnętrznej przeglądarce. Można to zrobić, zaznaczając pole wyboru **Włącz logowanie jednokrotne w przeglądarce mobilnej dla natywnych aplikacji** w obszarze **Edytowanie konfiguracji dzierżawy — zabezpieczenia.** Będzie to wymagało zainstalowania zatwierdzonej przeglądarki usługi Intune na urządzeniu dla systemu iOS i w profilu służbowym dla systemu Android

![Logowanie do przeglądarki mobilnej](./media/workday-tutorial/mobile-browser.png)

## <a name="setup-conditional-access-policy"></a>Skonfiguruj zasady dostępu warunkowego:

Te zasady będą miały wpływ tylko na logowanie na urządzeniu z systemem iOS lub Android. Jeśli chcesz ją rozłożyć na wszystkie platformy, po prostu wybierz **dowolne urządzenie.** Te zasady będą wymagały zgodności urządzenia z zasadami i weryfikują je za pośrednictwem Microsoft Intune. Ze względu na profile służbowe systemu Android należy zablokować użytkownikom możliwość logowania się do usługi Workday (Web lub App), chyba że loguje się za pośrednictwem ich profilu służbowego i zainstalował aplikację za pośrednictwem Intune — Portal firmy. Istnieje jeden dodatkowy krok dla systemu iOS, aby upewnić się, że ta sama sytuacja będzie miała zastosowanie. Poniżej przedstawiono zrzuty ekranu konfiguracji dostępu warunkowego.

**W programie Workday są obsługiwane następujące mechanizmy kontroli dostępu:**
* Wymagaj uwierzytelniania wieloskładnikowego
* Wymagaj, aby urządzenie było oznaczone jako zgodne

**Aplikacja Workday nie obsługuje następujących funkcji:**
* Wymaganie zatwierdzonej aplikacji klienckiej
* Wymagaj zasad ochrony aplikacji (wersja zapoznawcza)

Aby skonfigurować **dzień roboczy** jako **urządzenie zarządzane** , wykonaj następujące czynności:

![Konfigurowanie zasad dostępu warunkowego](./media/workday-tutorial/managed-devices-only.png)

1. Kliknij **> Home Microsoft Intune > conditional Access-Policies > tylko urządzenia zarządzane** 

1. Na stronie **tylko urządzenia zarządzane** podaj wartość pola **Nazwa** , `Managed Devices Only` a następnie kliknij pozycję **aplikacje lub akcje w chmurze** .

1. Wykonaj następujące kroki w **aplikacjach lub akcjach w chmurze** .

    a. Przełącz **Wybierz, do czego służy ta zasada** jako **aplikacje w chmurze** .

    b. W obszarze Dołączanie kliknij pozycję **aplikacje** .

    c. Wybierz pozycję **Workday** z listy wyboru.

    d. Kliknij pozycję **Gotowe** .

1. Włącz **zasady włączania** .

1. Kliknij pozycję **Zapisz** .

Aby **udzielić** dostępu, wykonaj następujące czynności:

![Zasady dostępu warunkowego konfiguracji produktu Workday](./media/workday-tutorial/managed-devices-only-2.png)

1. Kliknij **> Home Microsoft Intune > conditional Access-Policies > tylko urządzenia zarządzane** 

1. Na stronie **tylko urządzenia zarządzane** podaj wartość pola **Nazwa** , `Managed Devices Only` a następnie kliknij pozycję **Kontrola dostępu > Udziel** .

1. Wykonaj następujące czynności na stronie **Udziel** .

    a. Wybierz kontrolki, które mają zostać wymuszone przez **przyznanie dostępu** .

    b. Zaznacz pole **Wymagaj, aby urządzenie było oznaczone jako zgodne** .

    c. Wybierz opcję **Wymagaj jednej z wybranych kontrolek** .

    d. Kliknij pozycję **Wybierz** .

1. Włącz **zasady włączania** .

1. Kliknij pozycję **Zapisz**

## <a name="set-up-device-compliance-policy"></a>Skonfiguruj zasady zgodności urządzeń:

Aby mieć pewność, że urządzenia z systemem iOS będą mogły zalogować się tylko za pośrednictwem aplikacji platformy Workday zarządzanej przez rozwiązanie MDM, należy zablokować aplikację ze sklepu App Store, dodając **model com. Workday. workdayapp** do listy aplikacji z ograniczeniami. Dzięki temu będzie można uzyskać dostęp do produktu Workday tylko na urządzeniach, na których zainstalowano aplikację Workday w portalu firmy. W przypadku przeglądarki użytkownicy będą mogli uzyskać dostęp do produktu Workday tylko wtedy, gdy urządzenie jest zarządzane przez usługę Intune i używa programu Managed Browser.

![Konfiguracja produktu Workday — zasady zgodności urządzeń](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-microsoft-intune-app-configuration-policies"></a>Skonfiguruj zasady konfiguracji aplikacji Microsoft Intune:

| Scenariusz | Pary klucz-wartość |
|----------------------------------------------------------------------------------------   |-----------|
| Automatycznie Wypełnij pola dzierżawców i adresów sieci Web dla:<br>● Workday w systemie Android po włączeniu profilów programu Android for Work.<br>● Produktu Workday na urządzeniu iPad i telefonie iPhone.     | Użyj tych wartości, aby skonfigurować dzierżawcę: <br>Klucz konfiguracji ● = UserGroupCode<br>Typ wartości ● = ciąg <br>● Wartość konfiguracji = nazwa dzierżawy. Przykład: GMS<br>Użyj tych wartości, aby skonfigurować adres sieci Web:<br>Klucz konfiguracji ● = AppServiceHost<br>Typ wartości ● = ciąg<br>● Wartość konfiguracji = podstawowy adres URL dla dzierżawy. Przykład: https://www.myworkday.com                              |   |
| Wyłącz te akcje dla produktu Workday na urządzeniu iPad i telefonie iPhone:<br>● Wycinanie, kopiowanie i wklejanie<br>● Druku                       | Dla tych kluczy ustaw wartość (wartość logiczna) na false, aby wyłączyć funkcję:<br>● AllowCutCopyPaste<br>● AllowPrint  |
| Wyłącz zrzuty ekranu dla produktu Workday w systemie Android. |Ustaw wartość (wartość logiczna) na false w kluczu AllowScreenshots, aby wyłączyć funkcję.|
| Wyłącz sugerowane aktualizacje dla użytkowników.|Ustaw wartość (wartość logiczna) na false w kluczu AllowSuggestedUpdates, aby wyłączyć funkcję.|
|Dostosuj adres URL sklepu z aplikacjami, aby kierować użytkowników mobilnych do wybranego sklepu z aplikacjami.|Użyj tych wartości, aby zmienić adres URL sklepu z aplikacjami:<br>Klucz konfiguracji ● = AppUpdateURL<br>Typ wartości ● = ciąg<br> ● — Wartość konfiguracji = adres URL sklepu App Store|
|       |


## <a name="ios-configuration-policies"></a>Zasady konfiguracji systemu iOS:

1. Przejdź do https://portal.azure.com/ i zaloguj się
2. Wyszukaj **usługę Intune** lub kliknij widżet z listy.
3. Przejdź do pozycji **aplikacje klienckie — > aplikacje — > zasady konfiguracji aplikacji — > + Dodaj zarządzane urządzenia >**
4. Wprowadź nazwę.
5. W obszarze **platforma** wybierz pozycję **iOS/iPadOS**
6. W obszarze **skojarzona aplikacja** Wybierz dodaną aplikację Workday dla systemu iOS
7. Kliknij pozycję **Ustawienia konfiguracji** i w obszarze **Format ustawień konfiguracji** wybierz pozycję **Wprowadź dane XML** .
8. Oto przykładowy plik XML. Dodaj konfiguracje, które chcesz zastosować. Zastąp **STRING_VALUE** ciągiem, którego chcesz użyć. Zamień `<true />` lub `<false />` na `<true />` lub  `<false />` . Jeśli konfiguracja nie zostanie dodana, będzie działać tak, jak zostałaby ustawiona na true (prawda).

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
9. Kliknij pozycję Dodaj.
10. Odśwież stronę i kliknij nowo utworzone zasady.
11. Kliknij pozycję przypisania i wybierz, do kogo chcesz zastosować aplikację.
12. Kliknij pozycję Zapisz.

## <a name="android-configuration-policies"></a>Zasady konfiguracji systemu Android:

1. Przejdź do witryny `https://portal.azure.com/` i zaloguj się.
2. Wyszukaj **usługę Intune** lub kliknij widżet z listy.
3. Przejdź do pozycji **aplikacje klienckie — > aplikacje — > zasady konfiguracji aplikacji — > + Dodaj zarządzane urządzenia >**
5. Wprowadź nazwę. 
6. W obszarze **platforma** wybierz pozycję **Android**
7. W obszarze **skojarzona aplikacja** Wybierz dodaną aplikację Workday dla systemu Android
8. Kliknij pozycję **Ustawienia konfiguracji** i w obszarze **Format ustawień konfiguracji** wybierz pozycję **Wprowadź dane JSON** .

