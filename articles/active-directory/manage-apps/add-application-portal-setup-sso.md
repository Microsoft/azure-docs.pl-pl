---
title: 'Szybki start: konfigurowanie logowania jednokrotnego opartego na saml dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)'
description: Ten przewodnik Szybki start przechodzi przez proces konfigurowania logowania jednokrotnego (SSO) opartego na saml dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: iangithinji
ms.openlocfilehash: 802cc744063ba2bf1110915ae9b22c9c00e09cfa
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378911"
---
# <a name="quickstart-set-up-saml-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Szybki start: konfigurowanie logowania jednokrotnego opartego na saml dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)

Rozpoczynanie pracy z uproszczonymi logowaniami użytkowników przez skonfigurowanie logowania jednokrotnego dla aplikacji dodanej do dzierżawy usługi Azure Active Directory (Azure AD). Po skonfigurowaniu logowania jednokrotnego użytkownicy mogą zalogować się do aplikacji przy użyciu poświadczeń usługi Azure AD. Logowanie jednokrotne jest dostępne w bezpłatnej wersji usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować logowanie jednokrotne dla aplikacji dodanej do dzierżawy usługi Azure AD, potrzebne są:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z następujących ról: administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- Aplikacja obsługująca logowanie jednokrotne, która została już wstępnie skonfigurowana i dodana do galerii usługi Azure AD. Większość aplikacji może używać usługi Azure AD do logowania jednokrotnego. Aplikacje w galerii usługi Azure AD są wstępnie skonfigurowane. Jeśli twojej aplikacji nie ma na liście lub jest ona opracowana niestandardowo, nadal możesz używać jej z usługą Azure AD. Zapoznaj się z samouczkami i inną dokumentacją w spisie treści. Ten przewodnik Szybki start koncentruje się na aplikacjach, które zostały wstępnie skonfigurowane do logowania jednokrotnego i dodane do galerii usługi Azure AD przez deweloperów aplikacji.
- Opcjonalnie: ukończenie [wyświetlania aplikacji.](view-applications-portal.md)
- Opcjonalnie: ukończenie [dodawania aplikacji.](add-application-portal.md)
- Opcjonalnie: [ukończenie konfigurowania aplikacji.](add-application-portal-configure.md)
- Opcjonalnie: ukończenie [przypisywania użytkowników do aplikacji.](add-application-portal-assign-users.md)


>[!IMPORTANT]
>Użyj środowiska nieprodukcyjną, aby przetestować kroki opisane w tym przewodniku Szybki start.

## <a name="enable-single-sign-on-for-an-app"></a>Włączanie logowania pojedynczego dla aplikacji

Po zakończeniu dodawania aplikacji do dzierżawy usługi Azure AD zostanie wyświetlona strona przeglądu. Jeśli konfigurujesz już dodaną aplikację, zobacz pierwszy przewodnik Szybki start. Zawiera on omówienie aplikacji dodanych do dzierżawy.

Aby skonfigurować logowanie pojedyncze dla aplikacji:

1. W portalu usługi Azure AD wybierz pozycję **Aplikacje dla przedsiębiorstw.** Następnie znajdź i wybierz aplikację, którą chcesz skonfigurować do logowania pojedynczego.
1. W sekcji **Zarządzanie** wybierz **pozycję Logowanie pojedyncze,** aby otworzyć okienko Logowanie **pojedyncze do** edycji.

    > [!IMPORTANT]
    > Jeśli aplikacja używa standardu OpenID Connect (OIDC) dla logowania jednokrotnego, opcja logowania jednokrotnego nie będzie dostępna w nawigacji. Zapoznaj się z przewodnikem Szybki start na temat logowania jednokrotnego opartego na OIDC, aby dowiedzieć się, jak je skonfigurować.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Zrzut ekranu przedstawia stronę konfiguracji logowania pojedynczego w portalu usługi Azure AD.":::

1. Wybierz **pozycję SAML,** aby otworzyć stronę konfiguracji logowania jednokrotnego. W tym przykładzie aplikacja, dla których konfigurujemy logowanie jednokrotne, to GitHub. Po skonfigurowaniu usługi GitHub użytkownicy mogą zalogować się do usługi GitHub przy użyciu poświadczeń z dzierżawy usługi Azure AD.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Zrzut ekranu przedstawia stronę konfiguracji logowania pojedynczego w usłudze GitHub.":::

1. Proces konfigurowania aplikacji do korzystania z usługi Azure AD na użytek logowania jednokrotnego opartego na saml różni się w zależności od aplikacji. Istnieje link do wskazówek dotyczących usługi GitHub. Aby znaleźć przewodniki dotyczące innych aplikacji, zobacz [Tutorials for integrating SaaS applications with Azure Active Directory](/azure/active-directory/saas-apps/)(Samouczki dotyczące integrowania aplikacji SaaS z Azure Active Directory .
1. Postępuj zgodnie z przewodnikiem, aby skonfigurować logowanie jednokrotne dla aplikacji. Wiele aplikacji ma określone wymagania dotyczące subskrypcji dla funkcji logowania jednokrotnego. Na przykład usługa GitHub wymaga subskrypcji Enterprise.
    > [!TIP]
    > Aby dowiedzieć się więcej na temat opcji konfiguracji języka SAML, zobacz Konfigurowanie logowania pojedynczego opartego na [saml.](configure-saml-single-sign-on.md)

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Zrzut ekranu przedstawia opcję Logowanie pojedyncze w subskrypcji Enterprise na stronie cennika usługi GitHub.":::

> [!TIP]
> Zarządzanie aplikacją można zautomatyzować przy użyciu interfejsu interfejs Graph API, zobacz [Automate app management with Microsoft Graph API](/graph/application-saml-sso-configure-api)(Automatyzacja zarządzania aplikacją za pomocą Microsoft Graph API).


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po pracy z tą serią przewodników Szybki start rozważ usunięcie aplikacji w celu oczyszczenia dzierżawy testowej. Usuwanie aplikacji jest uwzględnione w ostatnim przewodniku Szybki start z tej serii. [Zobacz Usuwanie aplikacji.](delete-application-portal.md)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak usunąć aplikację, należy przejść do następnego artykułu.
> [!div class="nextstepaction"]
> [Usuwanie aplikacji](delete-application-portal.md)
