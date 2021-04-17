---
title: 'Szybki start: przypisywanie użytkowników do aplikacji, która używa Azure Active Directory jako dostawcy tożsamości'
description: W tym przewodniku Szybki start ominiemy proces zezwalania użytkownikom na używanie aplikacji, która została przez Ciebie tak skonfigurować, aby używać usługi Azure AD jako dostawcy tożsamości.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: iangithinji
ms.openlocfilehash: eb6797fdfb57a503b6dece9f2dfdc3cb0c67feef
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375342"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>Szybki start: przypisywanie użytkowników do aplikacji, która używa usługi Azure AD jako dostawcy tożsamości

W poprzednim przewodniku Szybki start skonfigurowano właściwości aplikacji. Po skonfigurowaniu właściwości środowisko dla przypisanych i nieprzypisanych użytkowników. Ten przewodnik Szybki start zawiera informacje na temat procesu przypisywania użytkowników do aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby przypisać użytkowników do aplikacji dodanej do dzierżawy usługi Azure AD, potrzebne są:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z następujących ról: administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- Opcjonalnie: ukończenie [wyświetlania aplikacji.](view-applications-portal.md)
- Opcjonalnie: ukończenie [dodawania aplikacji.](add-application-portal.md)
- Opcjonalnie: ukończenie [konfigurowania aplikacji.](add-application-portal-configure.md)

>[!IMPORTANT]
>Użyj środowiska nieprodukcyjną, aby przetestować kroki opisane w tym przewodniku Szybki start.

## <a name="assign-users-to-an-app"></a>Przypisywanie użytkowników do aplikacji
1. W portalu usługi Azure AD wybierz pozycję **Aplikacje dla przedsiębiorstw.** Następnie znajdź i wybierz aplikację, którą chcesz skonfigurować.
2. W menu nawigacji po lewej stronie wybierz pozycję **Użytkownicy i grupy.**
   > [!NOTE]
   > Niektóre z Microsoft 365 wymagają użycia programu PowerShell. 
3. Wybierz przycisk **Add user (Dodaj** użytkownika).
4. W **okienku Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy.**
5. Wybierz użytkownika lub grupę, którą chcesz przypisać do aplikacji. Możesz również rozpocząć wpisywanie nazwy użytkownika lub grupy w polu wyszukiwania. Możesz wybrać wielu użytkowników i grupy, a wybrane opcje zostaną wyświetlone w obszarze **Wybrane elementy.**
    > [!IMPORTANT]
    > Po przypisaniu grupy do aplikacji dostęp będą mieli tylko użytkownicy w tej grupie. Przypisanie nie powoduje kaskady dla grup zagnieżdżonych.

    > [!NOTE]
    > Przypisanie oparte na grupach wymaga Azure Active Directory — wersja Premium wersji P1 lub P2. Przypisanie oparte na grupach jest obsługiwane tylko w przypadku grup zabezpieczeń. Członkostwo w grupach zagnieżdżonych Microsoft 365 grupy nie są obecnie obsługiwane. Aby uzyskać więcej wymagań dotyczących licencjonowania funkcji omówiony w tym artykule, zobacz stronę [Azure Active Directory cennika.](https://azure.microsoft.com/pricing/details/active-directory) 
6. Po zakończeniu wybierz pozycję **Wybierz**.
   ![Przypisywanie użytkownika lub grupy do aplikacji](./media/assign-user-or-group-access-portal/assign-users.png)
7. W **okienku Użytkownicy i** grupy wybierz z listy co najmniej jednego użytkownika lub większej liczby grup, a następnie wybierz przycisk **Wybierz** w dolnej części okienka.
8. Jeśli aplikacja go obsługuje, możesz przypisać rolę do użytkownika lub grupy. W **okienku Dodawanie przypisania** wybierz pozycję **Wybierz rolę.** Następnie w **okienku Wybierz** rolę wybierz rolę do zastosowania do wybranych użytkowników lub grup, a następnie wybierz przycisk **OK** w dolnej części okienka. 
    > [!NOTE]
    > Jeśli aplikacja nie obsługuje wyboru roli, zostanie przypisana domyślna rola dostępu. W takim przypadku aplikacja zarządza poziomem dostępu użytkowników.
9. W **okienku Dodawanie** przypisania wybierz **przycisk** Przypisz w dolnej części okienka.

Użytkowników lub grupy można nieprzypisać przy użyciu tej samej procedury. Wybierz użytkownika lub grupę, dla której chcesz usunąć przypisanie, a następnie wybierz pozycję **Usuń**. Niektóre aplikacje Microsoft 365 i Office 365 wymagają użycia programu PowerShell. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki start rozważ usunięcie aplikacji. Dzięki temu dzierżawa testowa będzie czysta. Usuwanie aplikacji jest uwzględnione w ostatnim przewodniku Szybki start z tej serii. [Zobacz Usuwanie aplikacji.](delete-application-portal.md)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak skonfigurować logowanie pojedyncze dla aplikacji, należy przejść do następnego artykułu.
> [!div class="nextstepaction"]
> [Konfigurowanie logowania jednokrotnego opartego na protokole SAML](add-application-portal-setup-sso.md)

LUB

> [!div class="nextstepaction"]
> [Konfigurowanie logowania jednokrotnego opartego na warstwie OIDC](add-application-portal-setup-oidc-sso.md)
