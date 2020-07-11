---
title: 'Szybki Start: Konfigurowanie logowania jednokrotnego (SSO) dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)'
description: Ten przewodnik Szybki Start przeprowadzi Cię przez proces konfigurowania logowania jednokrotnego (SSO) dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: b19427070d982918584c13c25518cffe55497000
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223333"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Szybki Start: Konfigurowanie logowania jednokrotnego (SSO) dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)

Zacznij od uproszczonych logowań użytkowników, konfigurując Logowanie jednokrotne (SSO) dla aplikacji dodanej do dzierżawy usługi Azure Active Directory (Azure AD). Po skonfigurowaniu logowania jednokrotnego użytkownicy mogą logować się do aplikacji przy użyciu swoich poświadczeń usługi Azure AD. Logowanie jednokrotne jest zawarte w bezpłatnej wersji usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować Logowanie jednokrotne dla aplikacji, która została dodana do dzierżawy usługi Azure AD, musisz:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z następujących ról: Administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- Aplikacja obsługująca Logowanie jednokrotne, która została już wstępnie skonfigurowana i dodana do galerii usługi Azure AD. Większość aplikacji może używać usługi Azure AD do logowania jednokrotnego. Aplikacje w galerii usługi Azure AD są wstępnie skonfigurowane. Jeśli aplikacja nie jest wyświetlana na liście lub jest aplikacją niestandardową, można nadal z niej korzystać w usłudze Azure AD. Zapoznaj się z samouczkami i inną dokumentacją w spisie treści. Ten przewodnik Szybki Start koncentruje się na aplikacjach, które zostały wstępnie skonfigurowane na potrzeby logowania jednokrotnego i dodane do galerii usługi Azure AD przez deweloperów aplikacji.
- Opcjonalne: kończenie [wyświetlania aplikacji](view-applications-portal.md).
- Opcjonalne: kończenie [dodawania aplikacji](add-application-portal.md).
- Opcjonalne: kończenie [konfigurowania aplikacji](add-application-portal-configure.md).


>[!IMPORTANT]
>W celu przetestowania kroków w tym przewodniku szybki start Użyj środowiska nieprodukcyjnego.


## <a name="enable-single-sign-on-for-an-app"></a>Włączanie logowania jednokrotnego dla aplikacji

Po dodaniu aplikacji do dzierżawy usługi Azure AD zostanie wyświetlona strona przegląd. Jeśli konfigurujesz aplikację, która została już dodana, zapoznaj się z pierwszym przewodnikiem Szybki Start. Przeprowadzi Cię przez proces wyświetlania aplikacji dodanych do dzierżawy. 

Aby skonfigurować Logowanie jednokrotne dla aplikacji:

1. W portalu usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw**. Następnie Znajdź i wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.
1. W sekcji **Zarządzanie** wybierz pozycję **Logowanie** jednokrotne, aby otworzyć okienko **Logowanie** jednokrotne do edycji.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Zrzut ekranu przedstawia stronę konfiguracyjną Logowanie jednokrotne w portalu usługi Azure AD.":::

1. Wybierz pozycję **SAML** , aby otworzyć stronę konfiguracja logowania jednokrotnego. W tym przykładzie aplikacja konfigurowana na potrzeby logowania jednokrotnego jest w serwisie GitHub. Po skonfigurowaniu usługi GitHub użytkownicy mogą logować się do usługi GitHub przy użyciu swoich poświadczeń z dzierżawy usługi Azure AD.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Zrzut ekranu przedstawia stronę konfiguracyjną logowania jednokrotnego w witrynie GitHub.":::

1. Proces konfigurowania aplikacji do korzystania z usługi Azure AD na potrzeby logowania jednokrotnego opartego na protokole SAML różni się w zależności od aplikacji. Istnieje link do wskazówek dotyczących usługi GitHub. Aby znaleźć przewodniki dla innych aplikacji, zobacz [samouczki dotyczące integrowania aplikacji SaaS z Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/).
1. Postępuj zgodnie z przewodnikiem, aby skonfigurować Logowanie jednokrotne dla aplikacji. Wiele aplikacji ma określone wymagania dotyczące subskrypcji dla funkcji logowania jednokrotnego. Na przykład serwis GitHub wymaga subskrypcji przedsiębiorstwa.

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Zrzut ekranu przedstawia opcję logowania jednokrotnego w subskrypcji przedsiębiorstwa na stronie cennika usługi GitHub.":::


## <a name="next-step"></a>Następny krok

- [Usuwanie aplikacji](delete-application-portal.md)
