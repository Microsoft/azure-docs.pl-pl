---
title: 'Szybki Start: Wyświetlanie listy aplikacji korzystających z dzierżawy usługi Azure Active Directory (Azure AD) na potrzeby zarządzania tożsamościami'
description: W tym przewodniku szybki start Użyj Azure Portal, aby wyświetlić listę aplikacji, które są zarejestrowane do korzystania z dzierżawy Azure Active Directory (Azure AD) do zarządzania tożsamościami.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d04953789dc3fc864dd2809783b0d214a91738f
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983123"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>Szybki Start: Wyświetlanie listy aplikacji korzystających z dzierżawy usługi Azure Active Directory (Azure AD) na potrzeby zarządzania tożsamościami

Rozpocznij korzystanie z usługi Azure AD jako systemu zarządzania tożsamościami i autoryzacją dla aplikacji używanych przez organizację. W tym przewodniku szybki start zobaczysz aplikacje, znane także jako aplikacje, które zostały już skonfigurowane do korzystania z dzierżawy usługi Azure AD jako dostawcy tożsamości.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wyświetlić aplikacje, które zostały zarejestrowane w dzierżawie usługi Azure AD, potrzebne są:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

>[!IMPORTANT]
>Zalecamy użycie środowiska nieprodukcyjnego w celu przetestowania kroków opisanych w tym przewodniku Szybki Start.

## <a name="find-the-list-of-applications-in-your-tenant"></a>Znajdź listę aplikacji w swojej dzierżawie

Aplikacje, które są zarejestrowane w dzierżawie usługi Azure AD, są widoczne w sekcji **aplikacje dla przedsiębiorstw** w Azure Portal.

Aby wyświetlić aplikacje zarejestrowane w dzierżawie:

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. W lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.
3. W okienku **Azure Active Directory** wybierz pozycję **aplikacje dla przedsiębiorstw**.
4. Z menu rozwijanego **Typ aplikacji** wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Zastosuj**. Zostanie wyświetlona losowa próbka aplikacji dzierżawy.
5. Aby wyświetlić więcej aplikacji, wybierz pozycję **Załaduj więcej** w dolnej części listy. Jeśli dzierżawa zawiera wiele aplikacji, można łatwiej wyszukiwać konkretną aplikację zamiast przewijać listę. Wyszukiwanie określonej aplikacji jest omówione w dalszej części tego przewodnika Szybki Start.

## <a name="select-viewing-options"></a>Wybieranie opcje wyświetlania

Wybierz opcje według tego, czego szukasz.

1. Możesz wyświetlić aplikacje według **typu aplikacji**, **stanu aplikacji**i **widoczności aplikacji**.
2. W obszarze **Typ aplikacji** wybierz jedną z następujących opcji:
    - Opcja **Aplikacje dla przedsiębiorstw** wyświetla aplikacje firm innych niż Microsoft.
    - Opcja **Aplikacje firmy Microsoft** wyświetla aplikacji firmy Microsoft.
    - Opcja **Wszystkie aplikacje** wyświetla zarówno aplikacje firm innych niż Microsoft, jak i aplikacje firmy Microsoft.
3. W obszarze **Stan aplikacji** wybierz pozycję **Dowolne**, **Wyłączono** lub **Włączono**. Opcja **Dowolne** obejmuje zarówno aplikacje wyłączone, jak i włączone.
4. W obszarze **Widoczność aplikacji** wybierz pozycję **Dowolne** lub **Ukryte**. Opcja **Hidden** wyświetla aplikacje, które znajdują się w dzierżawie, ale nie są widoczne dla użytkowników.
5. Po wybraniu opcji wybierz pozycję **Zastosuj**.

## <a name="search-for-an-application"></a>Wyszukiwanie aplikacji

Aby wyszukać określoną aplikację:

1. W menu **Typ aplikacji** wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Zastosuj**.
2. Wprowadź nazwę aplikacji, którą chcesz znaleźć. Jeśli aplikacja została dodana do dzierżawy usługi Azure AD, zostanie wyświetlona w wynikach wyszukiwania. Ten przykład pokazuje, że GitHub nie został dodany do aplikacji dzierżawców.
    ![Przykład pokazuje, że aplikacja nie została dodana do dzierżawy](media/view-applications-portal/search-for-tenant-application.png)
3. Spróbuj wprowadzić kilka pierwszych liter nazwy aplikacji. W tym przykładzie pokazano wszystkie aplikacje, których nazwy zaczynają się od **Sales**.
    ![Przykład pokazuje wszystkie aplikacje, które zaczynają się od sprzedaży](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób wyświetlania aplikacji w dzierżawie usługi Azure AD. Dowiesz się, jak filtrować listę aplikacji według typu aplikacji, stanu i widoczności. Przedstawiono również sposób wyszukiwania określonej aplikacji.

- [Dodawanie aplikacji](add-application-portal.md)
- [Konfigurowanie aplikacji](add-application-portal-configure.md)
- [Konfigurowanie logowania jednokrotnego](add-application-portal-setup-sso.md)
- [Usuwanie aplikacji](delete-application-portal.md)
