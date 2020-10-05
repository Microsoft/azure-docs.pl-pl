---
title: 'Szybki Start: Konfigurowanie logowania jednokrotnego opartego na usłudze OIDC (SSO) dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)'
description: Ten przewodnik Szybki Start przeprowadzi Cię przez proces konfigurowania logowania jednokrotnego opartego na usłudze OIDC (SSO) dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 0f3073214fb47de006c6d9ebb07f443f3e63a4f3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91349102"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Szybki Start: Konfigurowanie logowania jednokrotnego opartego na usłudze OIDC (SSO) dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)

Zacznij od uproszczonych logowań użytkowników, konfigurując Logowanie jednokrotne (SSO) dla aplikacji dodanej do dzierżawy usługi Azure Active Directory (Azure AD). Po skonfigurowaniu logowania jednokrotnego użytkownicy mogą logować się do aplikacji przy użyciu swoich poświadczeń usługi Azure AD. Logowanie jednokrotne jest zawarte w bezpłatnej wersji usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować Logowanie jednokrotne dla aplikacji, która została dodana do dzierżawy usługi Azure AD, musisz:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z następujących ról: Administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- Aplikacja obsługująca Logowanie jednokrotne, która została już wstępnie skonfigurowana i dodana do galerii usługi Azure AD. Większość aplikacji może używać usługi Azure AD do logowania jednokrotnego. Aplikacje w galerii usługi Azure AD są wstępnie skonfigurowane. Jeśli aplikacja nie jest wyświetlana na liście lub jest aplikacją niestandardową, można nadal z niej korzystać w usłudze Azure AD. Zapoznaj się z samouczkami i inną dokumentacją w spisie treści. Ten przewodnik Szybki Start koncentruje się na aplikacjach, które zostały wstępnie skonfigurowane na potrzeby logowania jednokrotnego i dodane do galerii usługi Azure AD przez deweloperów aplikacji.
- Opcjonalne: kończenie [wyświetlania aplikacji](view-applications-portal.md).
- Opcjonalne: kończenie [dodawania aplikacji](add-application-portal.md).
- Opcjonalne: kończenie [konfigurowania aplikacji](add-application-portal-configure.md).
- Opcjonalne: kończenie [przypisywania użytkowników do aplikacji](add-application-portal-assign-users.md).

>[!IMPORTANT]
>Przetestuj kroki opisane w tym przewodniku Szybki Start przy użyciu środowiska nieprodukcyjnego.

## <a name="enable-single-sign-on-for-an-app"></a>Włączanie logowania jednokrotnego dla aplikacji

Po dodaniu aplikacji korzystającej ze standardu OIDC na potrzeby rejestracji jednokrotnej masz przycisk konfiguracji. Po wybraniu przycisku Przejdź do witryny aplikacje i Ukończ proces rejestracji aplikacji. Proces dodawania aplikacji znajduje się w temacie Dodawanie aplikacji szybkiego startu w tej serii. Jeśli konfigurujesz aplikację, która została już dodana, zapoznaj się z pierwszym przewodnikiem Szybki Start. Przeprowadzi Cię przez proces wyświetlania aplikacji już znajdujących się w Twojej dzierżawie. 

Aby skonfigurować Logowanie jednokrotne dla aplikacji:

1. W tym przewodniku szybki start zawarto informacje o dodawaniu aplikacji, która będzie używać dzierżawy usługi Azure AD do zarządzania tożsamościami. Jeśli deweloper aplikacji użył standardu OIDC w celu zaimplementowania logowania jednokrotnego, po dodaniu aplikacji zostanie wyświetlony przycisk tworzenia konta. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="Zrzut ekranu przedstawia opcję logowania jednokrotnego i przycisk tworzenia konta." lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. Wybierz pozycję **Utwórz konto** , aby przejść do strony logowania deweloperów aplikacji. Zaloguj się przy użyciu poświadczeń logowania Azure Active Directory. 

   > [!IMPORTANT]
    > Jeśli masz już subskrypcję aplikacji, sprawdzanie poprawności szczegółów użytkownika i informacji o dzierżawie/katalogu nastąpi. Jeśli aplikacja nie może zweryfikować użytkownika, zostanie przekierowany do rejestracji w usłudze aplikacji lub na stronie błędu.

3. Po pomyślnym uwierzytelnieniu zostanie wyświetlone okno dialogowe z pytaniem o zgodę administratora. Wybierz pozycję **zgoda w imieniu organizacji** , a następnie wybierz pozycję **Zaakceptuj**. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="Zrzut ekranu przedstawia opcję logowania jednokrotnego i przycisk tworzenia konta." lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. Aplikacja zostanie dodana do dzierżawy, a zostanie wyświetlona strona główna aplikacji.


> [!TIP]
> Zarządzanie aplikacjami można zautomatyzować za pomocą interfejs API programu Graph, zobacz [Automatyzowanie zarządzania aplikacjami za pomocą Microsoft Graph interfejsu API](https://docs.microsoft.com/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tą serią szybki start Rozważ usunięcie aplikacji w celu oczyszczenia dzierżawy testowej. Usuwanie aplikacji jest omówione w ostatnim przewodniku szybki start w tej serii, zobacz [usuwanie aplikacji](delete-application-portal.md).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak usunąć aplikację.
> [!div class="nextstepaction"]
> [Usuwanie aplikacji](delete-application-portal.md)
