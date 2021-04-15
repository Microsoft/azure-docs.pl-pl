---
title: 'Szybki start: konfigurowanie logowania jednokrotnego opartego na OIDC dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)'
description: Ten przewodnik Szybki start przechodzi przez proces konfigurowania logowania jednokrotnego (SSO) opartego na OIDC dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: iangithinji
ms.openlocfilehash: 2e0b49a73422a2f71af37e103e9d4fd6a18a1f61
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378945"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Szybki start: konfigurowanie logowania jednokrotnego opartego na OIDC dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)

Rozpoczynanie pracy z uproszczonymi logowaniami użytkowników przez skonfigurowanie logowania jednokrotnego dla aplikacji dodanej do dzierżawy usługi Azure Active Directory (Azure AD). Po skonfigurowaniu logowania jednokrotnego użytkownicy mogą zalogować się do aplikacji przy użyciu poświadczeń usługi Azure AD. Logowanie jednokrotne jest dostępne w bezpłatnej wersji usługi Azure AD.

Aby dowiedzieć się więcej na temat logowania jednokrotnego opartego na OIDC, zobacz [Understand OIDC-based single sign-on](configure-oidc-single-sign-on.md)(Informacje na temat logowania jednokrotnego opartego na OIDC).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować logowanie jednokrotne dla aplikacji dodanej do dzierżawy usługi Azure AD, potrzebne są:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Jedna z następujących ról: administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- Aplikacja, która obsługuje logowanie jednokrotne, która została już wstępnie skonfigurowana i dodana do galerii usługi Azure AD. Większość aplikacji może używać usługi Azure AD do logowania jednokrotnego. Aplikacje w galerii usługi Azure AD są wstępnie skonfigurowane. Jeśli Twoja aplikacja nie znajduje się na liście lub jest aplikacją niestandardową, nadal możesz jej używać z usługą Azure AD. Zapoznaj się z samouczkami i inną dokumentacją w spisie treści. Ten przewodnik Szybki start koncentruje się na aplikacjach, które zostały wstępnie skonfigurowane do logowania jednokrotnego i dodane do galerii usługi Azure AD przez deweloperów aplikacji.
- Opcjonalnie: ukończenie [wyświetlania aplikacji.](view-applications-portal.md)
- Opcjonalnie: ukończenie [dodawania aplikacji.](add-application-portal.md)
- Opcjonalnie: ukończenie [konfigurowania aplikacji.](add-application-portal-configure.md)
- Opcjonalnie: ukończenie [przypisywania użytkowników do aplikacji.](add-application-portal-assign-users.md)

>[!IMPORTANT]
>Użyj środowiska nieprodukcyjną, aby przetestować kroki opisane w tym przewodniku Szybki start.

## <a name="enable-single-sign-on-for-an-app"></a>Włączanie logowania pojedynczego dla aplikacji

Po dodaniu aplikacji korzystającej ze standardu OIDC dla logowania jednokrotnego masz przycisk konfiguracji. Po wybraniu przycisku przejdź do witryny aplikacji i ukończ proces rejestracji aplikacji. Proces dodawania aplikacji został ominięte w przewodniku Szybki start Dodawanie aplikacji we wcześniejszej części tej serii. Jeśli konfigurujesz już dodaną aplikację, zobacz pierwszy przewodnik Szybki start. Zawiera on omówienie aplikacji już w dzierżawie. 

Aby skonfigurować logowanie pojedyncze dla aplikacji:

1. W przewodniku Szybki start wcześniej w tej serii opisano sposób dodawania aplikacji, która będzie używać dzierżawy usługi Azure AD do zarządzania tożsamościami. Jeśli deweloper aplikacji użył standardu OIDC do zaimplementowania logowania jednokrotnego, podczas dodawania aplikacji zostanie przedstawiony przycisk rejestracji. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="Zrzut ekranu przedstawiający opcję logowania pojedynczego i przycisk rejestracji." lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. Wybierz **pozycję Zarejestruj się.** Zostaniesz zabrany do strony logowania deweloperów aplikacji. Zaloguj się przy Azure Active Directory logowania. 

   > [!IMPORTANT]
    > Jeśli masz już subskrypcję aplikacji, nastąpi walidacja szczegółów użytkownika i informacji o dzierżawie/katalogu. Jeśli aplikacja nie może zweryfikować użytkownika, nastąpi przekierowanie do rejestracji w usłudze aplikacji lub na stronę błędu.

3. Po pomyślnym uwierzytelnieniu zostanie wyświetlone okno dialogowe z prośbą o zgodę administratora. Wybierz **pozycję Zgoda w imieniu organizacji, a** następnie wybierz pozycję **Zaakceptuj**. Aby dowiedzieć się więcej na temat zgody użytkownika i administratora, zobacz [Understand user and admin consent (Opis zgody użytkownika i administratora).](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="Zrzut ekranu przedstawia ekran wyrażania zgody dla aplikacji." lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. Aplikacja zostanie dodana do dzierżawy i zostanie wyświetlona strona główna aplikacji.


> [!TIP]
> Zarządzanie aplikacją można zautomatyzować przy użyciu interfejs Graph API, zobacz [Automatyzowanie](/graph/application-saml-sso-configure-api)zarządzania aplikacją za pomocą Microsoft Graph API.

Oto wideo z dodatkowymi szczegółami dodawania aplikacji opartej na OIDC do usługi Azure AD.

Dodawanie aplikacji opartej na OIDC w Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po pracy z tą serią przewodników Szybki start rozważ usunięcie aplikacji w celu oczyszczenia dzierżawy testowej. Usuwanie aplikacji jest uwzględnione w ostatnim przewodniku Szybki start z tej serii. Zobacz [Usuwanie aplikacji.](delete-application-portal.md)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak usunąć aplikację, należy przejść do następnego artykułu.
> [!div class="nextstepaction"]
> [Usuwanie aplikacji](delete-application-portal.md)
