---
title: Wyświetlanie listy aplikacji w galerii aplikacji usługi Azure AD | Microsoft Docs
description: Dowiedz się, jak wyświetlić listę aplikacji, która obsługuje logowanie jednokrotne w galerii aplikacji Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/20/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 40d299049a0a1bbff81c412a5beb84f28e64ff46
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706306"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Wyświetlanie listy aplikacji w galerii aplikacji usługi Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

W tym artykule pokazano, jak wyświetlić listę aplikacji w galerii aplikacji Azure Active Directory (Azure AD), zaimplementować Logowanie jednokrotne (SSO) i zarządzać listą.

## <a name="what-is-the-azure-ad-application-gallery"></a>Co to jest Galeria aplikacji usługi Azure AD?

- Klienci szukają najlepszego możliwego środowiska logowania jednokrotnego.
- Konfiguracja aplikacji jest prosta i minimalna.
- Szybkie wyszukiwanie umożliwia znalezienie aplikacji w galerii.
- Wszyscy klienci usługi Azure AD w wersji bezpłatna, podstawowa i Premium mogą korzystać z tej integracji.
- Podzielni klienci mogą uzyskać samouczek konfigurowania krok po kroku.
- Klienci korzystający z systemu do zarządzania tożsamościami w różnych domenach ([Standard scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) mogą korzystać z aprowizacji dla tej samej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- W przypadku aplikacji federacyjnych (otwartych IDENTYFIKATORami i protokołu SAML/WS-The-karmione) aplikacja musi obsługiwać model oprogramowanie jako usługa (SaaS) w celu uzyskania listy w galerii aplikacji usługi Azure AD. Aplikacje w galerii Enterprise Gallery muszą obsługiwać wiele konfiguracji klientów, a nie klientów.
- W przypadku połączenia z programem Open ID aplikacja musi być wielodostępna, a [Struktura zgody na usługę Azure AD](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) musi być poprawnie zaimplementowana dla aplikacji. Użytkownik może wysłać żądanie logowania do wspólnego punktu końcowego, aby klient mógł wyrazić zgodę na dostęp do aplikacji. Możesz kontrolować dostęp użytkowników na podstawie identyfikatora dzierżawy oraz nazwy UPN użytkownika otrzymanej w tokenie.
- W przypadku protokołu SAML 2.0/WS-The---------------------------------- Przed przesłaniem żądania upewnij się, że ta funkcja działa prawidłowo.
- W przypadku logowania jednokrotnego za pomocą hasła upewnij się, że aplikacja obsługuje uwierzytelnianie formularzy, tak aby można było uruchomić Logowanie jednokrotne w oczekiwany sposób.
- Potrzebujesz stałego konta do testowania z co najmniej dwoma zarejestrowanymi użytkownikami.

**Jak uzyskać dostęp do usługi Azure AD dla deweloperów?**

Możesz skorzystać z bezpłatnego konta testowego ze wszystkimi funkcjami usługi Azure AD w warstwie Premium — 90 dni wolnych https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>Prześlij żądanie w portalu

Po przetestowaniu współpracy aplikacji z usługą Azure AD Prześlij żądanie aplikacji w [portalu sieci aplikacji firmy Microsoft](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

Jeśli po zalogowaniu zostanie wyświetlona następująca strona, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Podaj konto e-mail, którego chcesz użyć do przesłania żądania. Służbowy adres e-mail, taki jak [name@yourbusiness.com](mailto:name@yourbusiness.com) jest preferowany. Zespół usługi Azure AD doda konto w portalu sieci aplikacji firmy Microsoft.

![Komunikat żądania dostępu w portalu programu SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Po dodaniu konta możesz zalogować się do portalu sieci aplikacji firmy Microsoft.

Jeśli po zalogowaniu zostanie wyświetlona następująca strona, podaj uzasadnienie biznesowe dla potrzeb dostępu w polu tekstowym. Następnie wybierz pozycję **Żądaj dostępu**.

  ![Pole uzasadnienia biznesowe w portalu programu SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Nasz zespół przegląda szczegóły i zapewnia odpowiednie uzyskanie dostępu. Po zatwierdzeniu żądania możesz zalogować się do portalu i przesłać żądanie, wybierając kafelek **Prześlij żądanie (ISV)** na stronie głównej.

![Kafelek przesyłania żądania (ISV) na stronie głównej](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>Problemy z logowaniem do portalu

Jeśli ten błąd występuje podczas logowania, poniżej przedstawiono szczegółowe informacje o tym problemie, co pozwala na jego rozwiązanie.

* Jeśli logowanie zostało zablokowane, jak pokazano poniżej:

  ![Rozwiązywanie problemów z aplikacją w galerii](./media/howto-app-gallery-listing/blocked.png)

**Co się dzieje:**

Użytkownik-Gość jest federacyjny dla dzierżawy głównej, która jest również usługą Azure AD. Użytkownik-Gość jest w wysokim ryzyku. Firma Microsoft nie zezwala użytkownikom wysokiego ryzyka na dostęp do zasobów. Wszyscy użytkownicy o wysokim ryzyku (pracownicy lub Goście/dostawcy) muszą skorygować/zamknąć ryzyko uzyskania dostępu do zasobów firmy Microsoft. Dla użytkowników-Gości ten czynnik ryzyka pochodzi z dzierżawy głównej, a zasady pochodzą z dzierżawy zasobów (w tym przypadku firmy Microsoft).
 
**Bezpieczne rozwiązania:**

* Zarejestrowani użytkownicy-Goście zarejestrowany przez usługę MFA korygują własne ryzyko użytkownika. Może to zostać zrobione przez użytkownika-gościa wykonującego bezpieczną zmianę lub zresetowanie hasła ( https://aka.ms/sspr) w swojej dzierżawie domowej (wymaga to usługi MFA i SSPR w dzierżawie głównej). Bezpieczne Zmienianie lub Resetowanie hasła musi być inicjowane w usłudze Azure AD, a nie w Premium.

* Użytkownicy-Goście mają swoje administratorów korygują ryzyko. W takim przypadku administrator wykona Resetowanie hasła (tymczasowe generowanie hasła). Nie wymaga to ochrony tożsamości. Administrator użytkownika-gościa może przejść do https://aka.ms/RiskyUsers i kliknąć pozycję "Resetuj hasło".

* Użytkownicy-Goście mają swoich administratorów blisko lub odrzucają ryzyko. Nie wymaga to ochrony tożsamości. Administrator może przejść do https://aka.ms/RiskyUsers i kliknąć pozycję "Odrzuć ryzyko związane z użytkownikiem". Jednak administrator musi wykonać należytą staranność, aby upewnić się, że było to fałszywe pozytywne oceny ryzyka przed zamknięciem ryzyka użytkownika. W przeciwnym razie, narażamy zasoby firmy Microsoft na ryzyko, pomijając ocenę ryzyka bez badania.

> [!NOTE]
> Jeśli masz problemy z dostępem, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Zaimplementuj Logowanie jednokrotne przy użyciu protokołu federacyjnego

Aby wyświetlić listę aplikacji w galerii aplikacji usługi Azure AD, należy najpierw zaimplementować jeden z następujących protokołów federacyjnych obsługiwanych przez usługę Azure AD. Musisz również wyrazić zgodę na warunki i postanowienia galerii aplikacji usługi Azure AD. Przeczytaj warunki i postanowienia galerii aplikacji usługi Azure AD w [tej witrynie sieci Web](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect Connect**: aby zintegrować aplikację z usługą Azure AD przy użyciu protokołu połączenia Open ID Connect, postępuj zgodnie z [instrukcjami dla deweloperów](v1-authentication-scenarios.md).

    ![Wyświetlanie aplikacji OpenID Connect Connect w galerii](./media/howto-app-gallery-listing/openid.png)

    * Jeśli chcesz dodać aplikację do listy w galerii za pomocą OpenID Connect Connect, wybierz pozycję **OpenID Connect connect & OAuth 2,0** jak pokazano.
    * Jeśli masz problemy z dostępem, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **Saml 2,0** lub **WS-karmione**: Jeśli aplikacja obsługuje protokół SAML 2,0, można zintegrować ją bezpośrednio z DZIERŻAWą usługi Azure AD, postępując zgodnie z [instrukcjami, aby dodać aplikację niestandardową](../manage-apps/view-applications-portal.md).

  ![Wyświetlanie listy aplikacji SAML 2,0 lub WS-Fed w galerii](./media/howto-app-gallery-listing/saml.png)

  * Jeśli chcesz dodać aplikację do listy w galerii za pomocą **saml 2,0** lub **WS-karmione**, wybierz pozycję **SAML 2.0/WS-karmione** , jak pokazano.

  * Jeśli masz problemy z dostępem, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Zaimplementuj Logowanie jednokrotne przy użyciu hasła logowania jednokrotnego

Utwórz aplikację sieci Web, która zawiera stronę logowania w formacie HTML, aby skonfigurować Logowanie jednokrotne [oparte na haśle](../manage-apps/what-is-single-sign-on.md). Logowanie jednokrotne oparte na hasłach, nazywane również magazynem haseł, umożliwia zarządzanie dostępem użytkowników i hasłami do aplikacji sieci Web, które nie obsługują federacji tożsamości. Jest to również przydatne w scenariuszach, w których kilku użytkowników musi udostępniać pojedyncze konto, na przykład na kontach aplikacji Media społecznościowych w organizacji.

![Wyświetlanie listy aplikacji Logowanie jednokrotne w galerii](./media/howto-app-gallery-listing/passwordsso.png)

* Jeśli chcesz dodać aplikację do listy w galerii przy użyciu hasła logowania jednokrotnego, wybierz pozycję **hasło Logowanie jednokrotne (UserName & Password)** , jak pokazano.
* Jeśli masz problemy z dostępem, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Żądanie aprowizacji użytkownika

Postępuj zgodnie z procesem przedstawionym na poniższej ilustracji, aby zażądać aprowizacji użytkowników.

   ![Żądanie aprowizacji użytkownika](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Zaktualizuj lub Usuń istniejącą listę

Aby zaktualizować lub usunąć istniejącą aplikację w galerii aplikacji usługi Azure AD, należy najpierw przesłać żądanie w [portalu sieci aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Jeśli masz konto służbowe, użyj go, aby zalogować się do tego portalu. Jeśli tak nie jest, użyj konto Microsoft, takich jak Outlook lub Hotmail, aby się zalogować.

- Wybierz odpowiednią opcję, jak pokazano na poniższej ilustracji.

    ![Wyświetlanie listy aplikacji SAML w galerii](./media/howto-app-gallery-listing/updateorremove.png)

    * Aby zaktualizować istniejącą aplikację, wybierz odpowiednią opcję zgodnie z wymaganiami.
    * Aby usunąć istniejącą aplikację z galerii aplikacji usługi Azure AD, wybierz pozycję **Usuń moją aplikację z galerii**.
    * Jeśli masz problemy z dostępem, skontaktuj się z [zespołem ds. integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Wyświetlanie listy żądań według klientów

Klienci mogą przesłać żądanie, aby wyświetlić listę aplikacji, wybierając pozycję **żądania aplikacji przez klientów**  >  **przesyłając nowe żądanie**.

![Pokazuje kafelek aplikacje żądane przez klienta](./media/howto-app-gallery-listing/customer-submit-request.png)

Oto przepływ aplikacji żądanych przez klienta.

![Pokazuje przepływ aplikacji żądanych przez klienta](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>Osie czasu

Oś czasu procesu tworzenia listy aplikacji SAML 2,0 lub WS-Fed w galerii wynosi od 7 do 10 dni roboczych.

  ![Oś czasu do wyświetlania aplikacji SAML w galerii](./media/howto-app-gallery-listing/timeline.png)

Oś czasu procesu tworzenia listy aplikacji OpenID Connect Connect w galerii to od 2 do 5 dni roboczych.

  ![Oś czasu do wyświetlania aplikacji OpenID Connect Connect w galerii](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Ich eskalacji

W przypadku wszelkich eskalacji Wyślij wiadomość e-mail do [zespołu integracji rejestracji jednokrotnej usługi Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com) w systemie SaaSApplicationIntegrations@service.microsoft.com i będziemy reagować tak szybko, jak to możliwe.