---
title: 'Szybki start: konfigurowanie właściwości aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)'
description: Ten przewodnik Szybki start używa Azure Portal do konfigurowania aplikacji, która została zarejestrowana w dzierżawie usługi Azure Active Directory (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: iangithinji
ms.openlocfilehash: 3b7a5d88aa40422dc46c6ca2c1681447cb030ea4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379523"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Szybki start: konfigurowanie właściwości aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)

W poprzednim przewodniku Szybki start dodano aplikację do dzierżawy usługi Azure Active Directory (Azure AD). Gdy dodajesz aplikację, umożliwiasz dzierżawie usługi Azure AD, że jest ona dostawcą tożsamości dla aplikacji. Teraz skonfigurujesz niektóre właściwości aplikacji.
 
## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować właściwości aplikacji w dzierżawie usługi Azure AD, potrzebne są:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z następujących ról: administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- Opcjonalnie: ukończenie [wyświetlania aplikacji.](view-applications-portal.md)
- Opcjonalnie: ukończenie [dodawania aplikacji.](add-application-portal.md)

>[!IMPORTANT]
>Użyj środowiska nieprodukcyjną, aby przetestować kroki opisane w tym przewodniku Szybki start.

## <a name="configure-app-properties"></a>Konfigurowanie właściwości aplikacji

Po zakończeniu dodawania aplikacji do dzierżawy usługi Azure AD zostanie wyświetlona strona przeglądu. Jeśli konfigurujesz już dodaną aplikację, zobacz pierwszy przewodnik Szybki start. Zawiera on omówienie aplikacji dodanych do dzierżawy. 

Aby edytować właściwości aplikacji:

1. W portalu usługi Azure AD wybierz pozycję **Aplikacje dla przedsiębiorstw.** Następnie znajdź i wybierz aplikację, którą chcesz skonfigurować.
2. W sekcji **Zarządzanie** wybierz pozycję **Właściwości,** aby otworzyć **okienko Właściwości** do edycji.
3. Pomiń chwilę, aby zrozumieć dostępne opcje. Dostępne opcje zależą od sposobu integracji aplikacji z usługą Azure AD. Na przykład aplikacja, która korzysta z logowania pojedynczego opartego na saml, będzie mieć pola, takie jak Adres *URL* dostępu użytkownika, natomiast aplikacja, która korzysta z logowania pojedynczego opartego na OIDC, nie będzie. Należy również zauważyć, że aplikacje dodane za **pośrednictwem Azure Active Directory > Rejestracje aplikacji** są domyślnie aplikacjami opartymi na OIDC. Podczas gdy aplikacje dodane za **pośrednictwem Azure Active Directory > Enterprise** mogą korzystać z dowolnej z wielu standardów logowania pojedynczego. Wszystkie aplikacje pola służące do konfigurowania czasu, w którym aplikacja jest wyświetlana i może być używana. Wysyłane mogą być następujące pola:
    - **Czy włączono logowanie użytkowników?** określa, czy użytkownicy przypisani do aplikacji mogą się logować.
    - **Wymagane jest przypisanie użytkownika?** Określa, czy użytkownicy, którzy nie są przypisani do aplikacji, mogą się logować.
    - **Widoczne dla użytkowników?** Określa, czy użytkownicy przypisani do aplikacji [](https://myapps.microsoft.com) widzą ją w Moje aplikacje i Microsoft 365 uruchamiania aplikacji. (Zobacz menu waffle w lewym górnym rogu witryny Microsoft 365 internetowej).
    
    > [!TIP]
    > Przypisywanie użytkowników odbywa się **w** sekcji Nawigacji Użytkownicy i grupy.

    Te trzy opcje można przełączać niezależnie od siebie, a wynikowe zachowanie nie zawsze jest oczywiste. Oto tabela, która może pomóc:
    
    | Czy włączono logowanie użytkowników? | Wymagane przypisanie użytkownika? | Widoczne dla użytkowników? | Zachowanie użytkowników, którzy są przypisani do aplikacji lub nie. |
    |---|---|---|---|
    | Tak | Tak | Tak | Przypisani użytkownicy mogą zobaczyć aplikację i zalogować się.<br>Nieprzypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować. |
    | Tak | Tak | Nie  | Przypisane zastosowania nie widzą aplikacji, ale mogą się zalogować.<br>Nieprzypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować. |
    | Tak | Nie  | Tak | Przypisani użytkownicy mogą zobaczyć aplikację i zalogować się.<br>Nieprzypisani użytkownicy nie widzą aplikacji, ale mogą się zalogować. |
    | Tak | Nie  | Nie  | Przypisani użytkownicy nie widzą aplikacji, ale mogą się zalogować.<br>Nieprzypisani użytkownicy nie widzą aplikacji, ale mogą się zalogować. |
    | Nie  | Tak | Tak | Przypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować.<br>Nieprzypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować. |
    | Nie  | Tak | Nie  | Przypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować.<br>Nieprzypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować. |
    | Nie  | Nie  | Tak | Przypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować.<br>Nieprzypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować. |
    | Nie  | Nie  | Nie  | Przypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować.<br>Nieprzypisani użytkownicy nie widzą aplikacji i nie mogą się zalogować. |

4. Po zakończeniu wybierz pozycję **Zapisz.**

## <a name="use-a-custom-logo"></a>Używanie logo niestandardowego

Aby użyć logo niestandardowego:

1. Utwórz logo o szerokości 215 na 215 pikseli i zapisz je w formacie PNG.
2. W portalu usługi Azure AD wybierz pozycję **Aplikacje dla przedsiębiorstw.** Następnie znajdź i wybierz aplikację, którą chcesz skonfigurować.
3. W sekcji **Zarządzanie** wybierz pozycję **Właściwości,** aby otworzyć **okienko Właściwości** do edycji. 
4. Wybierz ikonę, aby przekazać logo.
5. Po zakończeniu wybierz pozycję **Zapisz.**

    ![Zrzut ekranu przedstawiający ekran Właściwości, na których pokazano, jak zmienić logo.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > Miniatura wyświetlana w **okienku** Właściwości nie jest od razu aktualizowana. Możesz zamknąć i ponownie otworzyć **okienko Właściwości,** aby wyświetlić zaktualizowaną ikonę.


> [!TIP]
> Zarządzanie aplikacją można zautomatyzować przy użyciu interfejsu interfejs Graph API, zobacz [Automate app management with Microsoft Graph API](/graph/application-saml-sso-configure-api)(Automatyzacja zarządzania aplikacją za pomocą Microsoft Graph API).

## <a name="add-notes"></a>Dodawanie notatek

Za pomocą pola notatki możesz dodać wszelkie informacje istotne dla zarządzania aplikacją w usłudze Azure AD. Uwagi to bezpłatne pole tekstowe o maksymalnym rozmiarze 1024 znaków.

1. W portalu usługi Azure AD wybierz pozycję **Aplikacje dla przedsiębiorstw.** Następnie znajdź i wybierz aplikację, którą chcesz skonfigurować.
2. W sekcji **Zarządzanie** wybierz pozycję **Właściwości,** aby otworzyć **okienko Właściwości** do edycji.
3. Zaktualizuj pole Uwagi i wybierz pozycję **Zapisz.**

    ![Zrzut ekranu właściwości przedstawiający sposób zmiany notatek](media/add-application-portal/notes-application.png)

    
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie zamierzasz kontynuować pracy z serią Szybki start, rozważ usunięcie aplikacji w celu oczyszczenia dzierżawy testowej. Usuwanie aplikacji jest uwzględnione w ostatnim przewodniku Szybki start z tej serii. [Zobacz Usuwanie aplikacji.](delete-application-portal.md)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak przypisać użytkowników do aplikacji, należy przejść do następnego artykułu.
> [!div class="nextstepaction"]
> [Przypisywanie użytkowników do aplikacji](add-application-portal-assign-users.md)