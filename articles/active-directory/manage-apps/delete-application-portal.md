---
title: 'Szybki Start: Usuwanie aplikacji z dzierżawy usługi Azure Active Directory (Azure AD)'
description: Ten przewodnik Szybki Start używa Azure Portal do usuwania aplikacji z dzierżawy Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 659d136695943d846fe57986d4b64a37f0d8f30e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89300123"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Szybki Start: Usuwanie aplikacji z dzierżawy usługi Azure Active Directory (Azure AD)

Ten przewodnik Szybki Start używa Azure Portal do usuwania aplikacji, która została dodana do dzierżawy usługi Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Wymagania wstępne

Aby usunąć aplikację z dzierżawy usługi Azure AD, potrzebne są:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z następujących ról: Administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- Opcjonalne: kończenie [wyświetlania aplikacji](view-applications-portal.md).
- Opcjonalne: kończenie [dodawania aplikacji](add-application-portal.md).
- Opcjonalne: kończenie [konfigurowania aplikacji](add-application-portal-configure.md).
- Opcjonalne: kończenie [przypisywania użytkowników do aplikacji](add-application-portal-assign-users.md).
- Opcjonalne: kończenie [konfigurowania logowania jednokrotnego](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>Przetestuj kroki opisane w tym przewodniku Szybki Start przy użyciu środowiska nieprodukcyjnego.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Usuwanie aplikacji z dzierżawy usługi Azure AD

Aby usunąć aplikację z dzierżawy usługi Azure AD:

1. W portalu usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw**. Następnie Znajdź i wybierz aplikację, którą chcesz usunąć. W takim przypadku usunięto aplikację **GitHub_test** dodaną w poprzednim przewodniku Szybki Start.
1. W sekcji **Zarządzanie** w okienku po lewej stronie wybierz pozycję **Właściwości**.
1. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **tak** , aby potwierdzić, że chcesz usunąć aplikację z dzierżawy usługi Azure AD.

> [!TIP]
> Zarządzanie aplikacjami można zautomatyzować za pomocą interfejs API programu Graph, zobacz [Automatyzowanie zarządzania aplikacjami za pomocą Microsoft Graph interfejsu API](https://docs.microsoft.com/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tą serią szybki start Rozważ usunięcie aplikacji w celu oczyszczenia dzierżawy testowej. Usuwanie aplikacji zostało omówione w tym przewodniku Szybki Start.

## <a name="next-steps"></a>Następne kroki

Ukończono serię szybkiego startu! Następnym krokiem jest zapoznanie się z najlepszymi rozwiązaniami w zakresie zarządzania aplikacjami.
> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące zarządzania aplikacjami](application-management-fundamentals.md)
