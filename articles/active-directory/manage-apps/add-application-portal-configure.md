---
title: 'Szybki Start: Konfigurowanie właściwości aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)'
description: Ten przewodnik Szybki Start używa Azure Portal do konfigurowania aplikacji, która została zarejestrowana w dzierżawie Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: d2a538cac1e8bcf9c67c88b206cacd928bfb73c8
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448235"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Szybki Start: Konfigurowanie właściwości aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)

W poprzednim przewodniku szybki start dodano aplikację do dzierżawy usługi Azure Active Directory (Azure AD). Po dodaniu aplikacji będziesz mieć pewność, że dzierżawa usługi Azure AD jest dostawcą tożsamości dla aplikacji. Teraz skonfigurujesz niektóre właściwości aplikacji.
 
## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować właściwości aplikacji w dzierżawie usługi Azure AD, należy wykonać następujące działania:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z następujących ról: Administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- Opcjonalne: kończenie [wyświetlania aplikacji](view-applications-portal.md).
- Opcjonalne: kończenie [dodawania aplikacji](add-application-portal.md).

>[!IMPORTANT]
>Przetestuj kroki opisane w tym przewodniku Szybki Start przy użyciu środowiska nieprodukcyjnego.

## <a name="configure-app-properties"></a>Konfigurowanie właściwości aplikacji

Po zakończeniu dodawania aplikacji do dzierżawy usługi Azure AD zostanie wyświetlona strona przegląd. Jeśli konfigurujesz aplikację, która została już dodana, zapoznaj się z pierwszym przewodnikiem Szybki Start. Przeprowadzi Cię przez proces wyświetlania aplikacji dodanych do dzierżawy. 

Aby edytować właściwości aplikacji:

1. W portalu usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw**. Następnie Znajdź i wybierz aplikację, którą chcesz skonfigurować.
2. W sekcji **Zarządzanie** wybierz pozycję **Właściwości** , aby otworzyć okienko **Właściwości** do edycji.

    ![Zrzut ekranu przedstawiający ekran właściwości, który pokazuje edytowalne właściwości aplikacji.](media/add-application-portal/edit-properties.png)

3. Poświęć chwilę na zapoznanie się z opcjami dostępnymi do konfiguracji:
    - **Włączono, aby użytkownicy mogli się zalogować?** Określa, czy użytkownicy przypisani do aplikacji mogą się zalogować.
    - **Czy przypisanie użytkownika jest wymagane?** Określa, czy użytkownicy, którzy nie są przypisani do aplikacji, mogą się zalogować.
    - **Widoczne dla użytkowników?** Określa, czy użytkownicy przypisani do aplikacji mogą ją zobaczyć w obszarze [Moje aplikacje](https://myapps.microsoft.com) i Microsoft 365 uruchamiania aplikacji. (Zobacz menu Gofr w lewym górnym rogu witryny sieci Web Microsoft 365).
    
    > [!TIP]
    > Przypisywanie użytkowników odbywa się w sekcji **Użytkownicy i grupy** nawigacji.

    Trzy opcje można przełączać niezależnie od siebie, a wynikowe zachowanie nie zawsze jest oczywiste. Oto tabela, która może pomóc:
    
    | Włączono, aby użytkownicy mogli się zalogować? | Wymagane przypisanie użytkownika? | Widoczne dla użytkowników? | Zachowanie dla użytkowników, którzy zostali przypisani do aplikacji. |
    |---|---|---|---|
    | Tak | Tak | Tak | Przypisani użytkownicy mogą zobaczyć aplikację i zalogować się.<br>Nieprzypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować. |
    | Tak | Tak | Nie  | Przypisane zastosowania nie mogą zobaczyć aplikacji, ale mogą się zalogować.<br>Nieprzypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować. |
    | Tak | Nie  | Tak | Przypisani użytkownicy mogą zobaczyć aplikację i zalogować się.<br>Nieprzypisani użytkownicy nie mogą zobaczyć aplikacji, ale mogą się zalogować. |
    | Tak | Nie  | Nie  | Przypisani użytkownicy nie widzą aplikacji, ale mogą się zalogować.<br>Nieprzypisani użytkownicy nie mogą zobaczyć aplikacji, ale mogą się zalogować. |
    | Nie  | Tak | Tak | Przypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować.<br>Nieprzypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować. |
    | Nie  | Tak | Nie  | Przypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować.<br>Nieprzypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować. |
    | Nie  | Nie  | Tak | Przypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować.<br>Nieprzypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować. |
    | Nie  | Nie  | Nie  | Przypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować.<br>Nieprzypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować. |

4. Gdy skończysz, wybierz pozycję **Zapisz**.

## <a name="use-a-custom-logo"></a>Używanie logo niestandardowego

Aby użyć logo niestandardowego:

1. Utwórz logo o 215 do 215 pikseli i Zapisz je w formacie PNG.
2. W portalu usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw**. Następnie Znajdź i wybierz aplikację, którą chcesz skonfigurować.
3. W sekcji **Zarządzanie** wybierz pozycję **Właściwości** , aby otworzyć okienko **Właściwości** do edycji. 
4. Wybierz ikonę, aby przekazać logo.
5. Gdy skończysz, wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający ekran właściwości, który pokazuje, jak zmienić logo.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > Miniatura wyświetlana w tym okienku **Właściwości** nie jest natychmiast aktualizowana. Możesz zamknąć i ponownie otworzyć okienko **Właściwości** , aby wyświetlić zaktualizowaną ikonę.


> [!TIP]
> Zarządzanie aplikacjami można zautomatyzować za pomocą interfejs API programu Graph, zobacz [Automatyzowanie zarządzania aplikacjami za pomocą Microsoft Graph interfejsu API](https://docs.microsoft.com/graph/application-saml-sso-configure-api).

## <a name="add-notes"></a>Dodawanie notatek

Za pomocą pola uwagi możesz dodać wszelkie informacje istotne dla zarządzania aplikacją w usłudze Azure AD. Uwagi to bezpłatne pole tekstowe o maksymalnym rozmiarze 1024 znaków.

1. W portalu usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw**. Następnie Znajdź i wybierz aplikację, którą chcesz skonfigurować.
2. W sekcji **Zarządzanie** wybierz pozycję **Właściwości** , aby otworzyć okienko **Właściwości** do edycji.
3. Zaktualizuj pole uwagi, a następnie wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający ekran właściwości, który pokazuje, jak zmienić uwagi](media/add-application-portal/notes-application.png)

    
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz kontynuować serii szybkiego startu, Rozważ usunięcie aplikacji w celu oczyszczenia dzierżawy testowej. Usuwanie aplikacji jest omówione w ostatnim przewodniku szybki start w tej serii, zobacz [usuwanie aplikacji](delete-application-portal.md).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak przypisać użytkowników do aplikacji.
> [!div class="nextstepaction"]
> [Przypisywanie użytkowników do aplikacji](add-application-portal-assign-users.md)
