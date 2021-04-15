---
title: 'Szybki start: usuwanie aplikacji z dzierżawy Azure Active Directory (Azure AD)'
description: W tym przewodniku Szybki start Azure Portal aplikacji z dzierżawy usługi Azure Active Directory (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 1/5/2021
ms.author: iangithinji
ms.openlocfilehash: 55607d520affe704cecd2e16fb64b3a65c04e167
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374305"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Szybki start: usuwanie aplikacji z dzierżawy Azure Active Directory (Azure AD)

W tym przewodniku Szybki start Azure Portal aplikacji, która została dodana do dzierżawy usługi Azure Active Directory (Azure AD).

Aby dowiedzieć się więcej na temat logowania jednokrotnego i platformy Azure, zobacz [What is Single Sign-On (SSO) (Co to jest logowanie jednokrotne).](what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby usunąć aplikację z dzierżawy usługi Azure AD, potrzebne są:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Jedna z następujących ról: administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- Opcjonalnie: ukończenie [wyświetlania aplikacji.](view-applications-portal.md)
- Opcjonalnie: ukończenie [dodawania aplikacji.](add-application-portal.md)
- Opcjonalnie: ukończenie [konfigurowania aplikacji.](add-application-portal-configure.md)
- Opcjonalnie: ukończenie [przypisywania użytkowników do aplikacji.](add-application-portal-assign-users.md)
- Opcjonalnie: [ukończenie procesu konfigurowanie logowania pojedynczego.](add-application-portal-setup-sso.md)

>[!IMPORTANT]
>Użyj środowiska nieprodukcyjną, aby przetestować kroki opisane w tym przewodniku Szybki start.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Usuwanie aplikacji z dzierżawy usługi Azure AD

Aby usunąć aplikację z dzierżawy usługi Azure AD:

1. W portalu usługi Azure AD wybierz pozycję **Aplikacje dla przedsiębiorstw.** Następnie znajdź i wybierz aplikację, którą chcesz usunąć. W tym przypadku usunięto aplikację **GitHub_test** dodaną w poprzednim przewodniku Szybki start.
1. W sekcji **Zarządzanie** w okienku po lewej stronie wybierz pozycję **Właściwości**.
1. Wybierz **pozycję** Usuń, a następnie wybierz pozycję **Tak,** aby potwierdzić, że chcesz usunąć aplikację z dzierżawy usługi Azure AD.

> [!TIP]
> Możesz zautomatyzować zarządzanie aplikacją przy użyciu interfejsu interfejs Graph API, zobacz [Automatyzowanie](/graph/application-saml-sso-configure-api)zarządzania aplikacją za pomocą Microsoft Graph API.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po pracy z tą serią przewodników Szybki start rozważ usunięcie aplikacji w celu oczyszczenia dzierżawy testowej. Usuwanie aplikacji zostało uwzględnione w tym przewodniku Szybki start.

## <a name="next-steps"></a>Następne kroki

Seria przewodników Szybki start została ukończona. Następnie zapoznaj się z tematem Single Sign-On (SSO) (Co [to jest logowanie jednokrotne?)](what-is-single-sign-on.md) Możesz też zapoznać się z najlepszymi rozwiązaniami w zakresie zarządzania aplikacją.
> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące zarządzania aplikacją](application-management-fundamentals.md)
