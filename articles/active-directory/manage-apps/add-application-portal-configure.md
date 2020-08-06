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
ms.openlocfilehash: a1a99e9f02a25f5e1d57ea485930a4f26149b53f
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808409"
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
>W celu przetestowania kroków w tym przewodniku szybki start Użyj środowiska nieprodukcyjnego.

## <a name="configure-app-properties"></a>Konfigurowanie właściwości aplikacji

Po zakończeniu dodawania aplikacji do dzierżawy usługi Azure AD zostanie wyświetlona strona przegląd. Jeśli konfigurujesz aplikację, która została już dodana, zapoznaj się z pierwszym przewodnikiem Szybki Start. Przeprowadzi Cię przez proces wyświetlania aplikacji dodanych do dzierżawy. 

Aby edytować właściwości aplikacji:

1. W portalu usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw**. Następnie Znajdź i wybierz aplikację, którą chcesz skonfigurować.
2. W sekcji **Zarządzanie** wybierz pozycję **Właściwości** , aby otworzyć okienko **Właściwości** do edycji.

    ![Zrzut ekranu przedstawiający ekran właściwości, który pokazuje edytowalne właściwości aplikacji.](media/add-application-portal/edit-properties.png)

3. Poświęć chwilę na zapoznanie się z opcjami dostępnymi do konfiguracji:
    - **Włączono, aby użytkownicy mogli się zalogować?** Określa, czy użytkownicy przypisani do aplikacji mogą się zalogować.
    - **Czy przypisanie użytkownika jest wymagane?** Określa, czy użytkownicy, którzy nie są przypisani do aplikacji, mogą się zalogować.
    - **Widoczne dla użytkowników?** Określa, czy użytkownicy przypisani do aplikacji mogą ją zobaczyć w [panelu dostępu](https://myapps.microsoft.com) i uruchamiania aplikacji pakietu Office 365. (Zobacz menu Gofr w lewym górnym rogu witryny sieci Web pakietu Office 365 lub Microsoft 365).
4. Poniższe tabele ułatwiają wybranie najlepszych opcji dla potrzeb użytkownika.

   - Zachowanie w przypadku użytkowników *przypisanych*:

       | Właściwość aplikacji | Właściwość aplikacji | Właściwość aplikacji | Środowisko użytkowników przypisanych | Środowisko użytkowników przypisanych |
       |---|---|---|---|---|
       | Włączono, aby użytkownicy mogli się zalogować? | Wymagane przypisanie użytkownika? | Widoczne dla użytkowników? | Czy przypisani użytkownicy mogą się logować? | Czy przypisani użytkownicy widzą aplikację?* |
       | Tak | Tak | Tak | Tak | Tak  |
       | Tak | Tak | Nie  | Tak | Nie   |
       | Tak | Nie  | Tak | Tak | Tak  |
       | Tak | Nie  | Nie  | Tak | Nie   |
       | Nie  | Tak | Tak | Nie  | Nie   |
       | Nie  | Tak | Nie  | Nie  | Nie   |
       | Nie  | Nie  | Tak | Nie  | Nie   |
       | Nie  | Nie  | Nie  | Nie  | Nie   |

   - Zachowanie w przypadku użytkowników *nieprzypisanych*:

       | Właściwość aplikacji | Właściwość aplikacji | Właściwość aplikacji | Środowisko użytkowników nieprzypisanych | Środowisko użytkowników nieprzypisanych |
       |---|---|---|---|---|
       | Włączono, aby użytkownicy mogli się zalogować? | Wymagane przypisanie użytkownika? | Widoczne dla użytkowników? | Czy nieprzypisani użytkownicy mogą się logować? | Czy nieprzypisani użytkownicy widzą aplikację?* |
       | Tak | Tak | Tak | Nie  | Nie   |
       | Tak | Tak | Nie  | Nie  | Nie   |
       | Tak | Nie  | Tak | Tak | Nie   |
       | Tak | Nie  | Nie  | Tak | Nie   |
       | Nie  | Tak | Tak | Nie  | Nie   |
       | Nie  | Tak | Nie  | Nie  | Nie   |
       | Nie  | Nie  | Tak | Nie  | Nie   |
       | Nie  | Nie  | Nie  | Nie  | Nie   |

     *Czy użytkownik widzi aplikację w panelu dostępu i narzędziu do uruchamiania aplikacji usługi Office 365?

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


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz kontynuować korzystania z serii szybkiego startu, Rozważ usunięcie aplikacji w celu oczyszczenia dzierżawy testowej. Usuwanie aplikacji jest omówione w ostatnim przewodniku szybki start w tej serii, zobacz [usuwanie aplikacji](delete-application-portal.md).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak skonfigurować Logowanie jednokrotne dla aplikacji.
> [!div class="nextstepaction"]
> [Konfigurowanie logowania jednokrotnego](add-application-portal-setup-sso.md)
