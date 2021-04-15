---
title: 'Szybki start: wyświetlanie listy aplikacji, które do zarządzania tożsamościami Azure Active Directory dzierżawy usługi Azure AD'
description: W tym przewodniku Szybki start użyjesz Azure Portal, aby wyświetlić listę aplikacji zarejestrowanych do używania dzierżawy usługi Azure Active Directory (Azure AD) do zarządzania tożsamościami.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: iangithinji
ms.reviewer: arvinh
ms.custom: it-pro
ms.openlocfilehash: c4a03e85798d4406068bd5923dbc753d8e5d31b6
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376447"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>Szybki start: wyświetlanie listy aplikacji, które do zarządzania tożsamościami Azure Active Directory dzierżawy usługi Azure AD

Rozpoczynanie korzystania z usługi Azure AD jako systemu zarządzania tożsamościami i dostępem (IAM) dla aplikacji, z których korzysta organizacja. W tym przewodniku Szybki start zostaną przejrzene aplikacje, znane także jako aplikacje, które zostały już ustawione do używania dzierżawy usługi Azure AD jako dostawcy tożsamości.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wyświetlić aplikacje, które zostały zarejestrowane w dzierżawie usługi Azure AD, potrzebne są:

- Konto platformy Azure. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

>[!IMPORTANT]
>Do testowania kroków w tym przewodniku Szybki start zalecamy użycie środowiska nieprodukcyjną.

## <a name="find-the-list-of-applications-in-your-tenant"></a>Znajdowanie listy aplikacji w dzierżawie

Aplikacje zarejestrowane w dzierżawie usługi Azure AD można  wyświetlać w sekcji Aplikacje dla przedsiębiorstw w Azure Portal.

Aby wyświetlić aplikacje zarejestrowane w dzierżawie:

1. Zaloguj się do [swojego Azure Portal](https://portal.azure.com).
2. W panelu nawigacyjnym po lewej stronie wybierz **pozycję Azure Active Directory**.
3. W **okienku Azure Active Directory** wybierz pozycję **Aplikacje dla przedsiębiorstw.**
4. Z **menu rozwijanego Typ** aplikacji wybierz pozycję **Wszystkie aplikacje,** a następnie wybierz pozycję **Zastosuj.** Zostanie wyświetlona losowa próbka aplikacji dzierżawy.
5. Aby wyświetlić więcej aplikacji, wybierz **pozycję Załaduj** więcej u dołu listy. Jeśli w dzierżawie znajduje się wiele aplikacji, wyszukiwanie konkretnej aplikacji zamiast przewijania listy może być łatwiejsze. Wyszukiwanie określonej aplikacji jest uwzględnione w dalszej części tego przewodnika Szybki start.

## <a name="select-viewing-options"></a>Wybieranie opcje wyświetlania

Wybierz opcje zgodnie z tym, czego szukasz.

1. Aplikacje można wyświetlać według typu **aplikacji,** **stanu aplikacji** i **widoczności aplikacji.**
2. W obszarze **Typ aplikacji** wybierz jedną z następujących opcji:
    - Opcja **Aplikacje dla przedsiębiorstw** wyświetla aplikacje firm innych niż Microsoft.
    - Opcja **Aplikacje firmy Microsoft** wyświetla aplikacji firmy Microsoft.
    - Opcja **Wszystkie aplikacje** wyświetla zarówno aplikacje firm innych niż Microsoft, jak i aplikacje firmy Microsoft.
3. W obszarze **Stan aplikacji** wybierz pozycję **Dowolne**, **Wyłączono** lub **Włączono**. Opcja **Dowolne** obejmuje zarówno aplikacje wyłączone, jak i włączone.
4. W obszarze **Widoczność aplikacji** wybierz pozycję **Dowolne** lub **Ukryte**. Opcja **Ukryte** pokazuje aplikacje, które znajdują się w dzierżawie, ale nie są widoczne dla użytkowników.
5. Po wybraniu wybranych opcji wybierz pozycję **Zastosuj.**

## <a name="search-for-an-application"></a>Wyszukiwanie aplikacji

Aby wyszukać określoną aplikację:

1. W menu **Typ aplikacji** wybierz pozycję **Wszystkie aplikacje,** a następnie wybierz pozycję **Zastosuj.**
2. Wprowadź nazwę aplikacji, którą chcesz znaleźć. Jeśli aplikacja została dodana do dzierżawy usługi Azure AD, zostanie wyświetlona w wynikach wyszukiwania. W tym przykładzie pokazano, że usługa GitHub nie została dodana do aplikacji dzierżawy.
    ![Przykład pokazuje, że aplikacja nie została dodana do dzierżawy](media/view-applications-portal/search-for-tenant-application.png)
3. Spróbuj wprowadzić kilka pierwszych liter nazwy aplikacji. W tym przykładzie pokazano wszystkie aplikacje, których nazwy zaczynają się od **Sales**.
    ![Przykład przedstawia wszystkie aplikacje, które zaczynają się od Sales](media/view-applications-portal/search-by-prefix.png)


> [!TIP]
> Zarządzanie aplikacją można zautomatyzować przy użyciu interfejs Graph API, zobacz [Automatyzowanie](/graph/application-saml-sso-configure-api)zarządzania aplikacją za pomocą Microsoft Graph API.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym przewodniku Szybki start nie utworzyliśmy żadnych nowych zasobów, więc nie ma nic do oczyszczenia.

## <a name="next-steps"></a>Następne kroki

W następnym artykule dowiesz się, jak używać usługi Azure AD jako dostawcy tożsamości dla aplikacji.
> [!div class="nextstepaction"]
> [Dodawanie aplikacji](add-application-portal.md)