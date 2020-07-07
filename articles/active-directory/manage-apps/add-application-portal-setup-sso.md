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
ms.openlocfilehash: 727e51c535eaade4cd229ee10ab92e6306d4cf42
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956122"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Szybki Start: Konfigurowanie logowania jednokrotnego (SSO) dla aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)

Rozpocznij pracę z uproszczonymi logowaniami użytkowników, konfigurując Logowanie jednokrotne dla aplikacji dodanej do dzierżawy usługi Azure AD. Po skonfigurowaniu logowania jednokrotnego użytkownicy będą mogli zalogować się do aplikacji przy użyciu swoich poświadczeń usługi Azure AD. Logowanie jednokrotne jest zawarte w bezpłatnej wersji usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować Logowanie jednokrotne dla aplikacji dodanej do dzierżawy usługi Azure AD, musisz:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z następujących ról: Administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- Aplikacja obsługująca Logowanie jednokrotne, która została już wstępnie skonfigurowana i dodana do galerii usługi Azure AD. Większość aplikacji może używać usługi Azure AD do logowania jednokrotnego. Aplikacje w galerii usługi Azure AD zostały wstępnie skonfigurowane. Jeśli aplikacja nie jest wyświetlana na liście lub jest niestandardową aplikacją, można nadal z niej korzystać w usłudze Azure AD. Zapoznaj się z samouczkami i inną dokumentacją w spisie treści. Ten przewodnik Szybki Start koncentruje się na aplikacjach, które zostały wstępnie skonfigurowane na potrzeby logowania jednokrotnego i dodane do galerii usługi Azure AD przez deweloperów aplikacji.
- (Opcjonalnie: kończenie [wyświetlania aplikacji](view-applications-portal.md)).
- (Opcjonalnie: kończenie [dodawania aplikacji](add-application-portal.md)).
- (Opcjonalnie: zakończenie [konfigurowania aplikacji](add-application-portal-configure.md)).


>[!IMPORTANT]
>Zalecamy użycie środowiska nieprodukcyjnego w celu przetestowania kroków opisanych w tym przewodniku Szybki Start.


## <a name="enable-single-sign-on-for-an-app"></a>Włączanie logowania jednokrotnego dla aplikacji

Po dodaniu aplikacji do dzierżawy usługi Azure AD natychmiast zostanie wyświetlona strona przeglądu. Jeśli konfigurujesz aplikację, która została już dodana, zapoznaj się z pierwszym przewodnikiem Szybki Start i przeprowadzi Cię przez proces wyświetlania aplikacji dodanych do dzierżawy. 

Aby skonfigurować Logowanie jednokrotne dla aplikacji:

1. W portalu usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie Znajdź i wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.
2. W sekcji Zarządzanie wybierz pozycję **Logowanie** jednokrotne, aby otworzyć okienko właściwości do edycji.
    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Pokazuje stronę konfiguracyjną logowania jednokrotnego w portalu usługi Azure AD.":::
3. Wybierz pozycję SAML, aby otworzyć stronę konfiguracja logowania jednokrotnego. W tym przykładzie aplikacja konfigurowana na potrzeby logowania jednokrotnego jest w serwisie GitHub. Po skonfigurowaniu usługi GitHub użytkownicy będą mogli zalogować się w usłudze GitHub przy użyciu swoich poświadczeń z dzierżawy usługi Azure AD.
    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Pokazuje stronę konfiguracyjną logowania jednokrotnego w witrynie GitHub.":::
4. Proces konfigurowania aplikacji do korzystania z usługi Azure AD na potrzeby logowania jednokrotnego opartego na protokole SAML różni się w zależności od aplikacji. Zwróć uwagę na link do wskazówek dotyczących usługi GitHub. Przewodniki dotyczące innych aplikacji można znaleźć pod adresem:https://docs.microsoft.com/azure/active-directory/saas-apps/
5. Postępuj zgodnie z przewodnikiem, aby skonfigurować Logowanie jednokrotne dla aplikacji. Wiele aplikacji ma określone wymagania dotyczące subskrypcji dla funkcji logowania jednokrotnego. Na przykład serwis GitHub wymaga subskrypcji przedsiębiorstwa.
    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Pokazuje opcję logowania jednokrotnego w subskrypcji przedsiębiorstwa na stronie cennika usługi GitHub.":::


## <a name="next-steps"></a>Następne kroki

- [Usuwanie aplikacji](delete-application-portal.md)