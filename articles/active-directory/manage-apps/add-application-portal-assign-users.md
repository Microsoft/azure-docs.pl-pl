---
title: 'Szybki Start: przypisywanie użytkowników do aplikacji, która używa Azure Active Directory jako dostawcy tożsamości'
description: Ten przewodnik Szybki Start przeprowadzi Cię przez proces, dzięki któremu użytkownicy mogą korzystać z aplikacji, która została skonfigurowana do korzystania z usługi Azure AD jako dostawcy tożsamości.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: kenwith
ms.openlocfilehash: 0e31852b6cf40cec9161c6904ce99798f1ee4996
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89303897"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>Szybki Start: przypisywanie użytkowników do aplikacji używającej usługi Azure AD jako dostawcy tożsamości

W poprzednim przewodniku szybki start skonfigurowano właściwości aplikacji. Podczas ustawiania właściwości skonfigurowanych dla użytkowników przypisanych i nieprzypisanych. Ten przewodnik Szybki Start przeprowadzi Cię przez proces przypisywania użytkowników do aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby przypisać użytkowników do aplikacji dodanej do dzierżawy usługi Azure AD, potrzebne są:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z następujących ról: Administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- Opcjonalne: kończenie [wyświetlania aplikacji](view-applications-portal.md).
- Opcjonalne: kończenie [dodawania aplikacji](add-application-portal.md).
- Opcjonalne: kończenie [konfigurowania aplikacji](add-application-portal-configure.md).

>[!IMPORTANT]
>Przetestuj kroki opisane w tym przewodniku Szybki Start przy użyciu środowiska nieprodukcyjnego.

## <a name="assign-users-to-an-app"></a>Przypisywanie użytkowników do aplikacji
1. W portalu usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw**. Następnie Znajdź i wybierz aplikację, którą chcesz skonfigurować.
2. W menu nawigacji po lewej stronie wybierz pozycję **Użytkownicy i grupy**.
   > [!NOTE]
   > Jeśli chcesz przypisać użytkowników do aplikacji firmy Microsoft, takich jak aplikacje pakietu Office 365, niektóre z tych aplikacji używają programu PowerShell. 
3. Wybierz przycisk **Dodaj użytkownika** .
4. W okienku **Dodaj przypisanie** wybierz pozycję **Użytkownicy i grupy**.
5. Wybierz użytkownika lub grupę, która ma zostać przypisana do aplikacji. Możesz również rozpocząć wpisywanie nazwy użytkownika lub grupy w polu wyszukiwania. Możesz wybrać wielu użytkowników i grupy, a wybrane opcje będą widoczne w obszarze **wybrane elementy**.
    > [!IMPORTANT]
    > Po przypisaniu grupy do aplikacji tylko użytkownicy w grupie będą mieli dostęp. Przypisanie nie jest kaskadowe do grup zagnieżdżonych.

    > [!NOTE]
    > Przypisanie oparte na grupach wymaga Azure Active Directory — wersja Premium wersji P1 lub P2. Przypisanie oparte na grupach jest obsługiwane tylko dla grup zabezpieczeń. Członkostwa w grupach zagnieżdżonych i grupy Office 365 nie są obecnie obsługiwane. Aby uzyskać więcej informacji o wymaganiach dotyczących licencjonowania funkcji omówionych w tym artykule, zobacz [stronę z cennikiem Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 
6. Po zakończeniu wybierz **pozycję Wybierz**.
   ![Przypisywanie użytkownika lub grupy do aplikacji](./media/assign-user-or-group-access-portal/assign-users.png)
7. W okienku **Użytkownicy i grupy** wybierz co najmniej jednego użytkownika lub grupę z listy, a następnie wybierz przycisk **Wybierz** w dolnej części okienka.
8. Jeśli aplikacja obsługuje tę funkcję, można przypisać rolę do użytkownika lub grupy. W okienku **Dodaj przypisanie** wybierz **pozycję Wybierz rolę**. Następnie w okienku **Wybierz rolę** wybierz rolę, która ma zostać zastosowana do wybranych użytkowników lub grup, a następnie wybierz pozycję **OK** w dolnej części okienka. 
    > [!NOTE]
    > Jeśli aplikacja nie obsługuje wyboru roli, zostanie przypisana domyślna rola dostępu. W takim przypadku aplikacja zarządza poziomem dostępu dostępnym dla użytkowników.
9. W okienku **Dodaj przypisanie** wybierz przycisk **Przypisz** w dolnej części okienka.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu korzystania z przewodnika Szybki Start Rozważ usunięcie aplikacji. Dzięki temu możesz zachować czyste dzierżawy testowe. Usuwanie aplikacji jest omówione w ostatnim przewodniku szybki start w tej serii, zobacz [usuwanie aplikacji](delete-application-portal.md).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak skonfigurować Logowanie jednokrotne dla aplikacji.
> [!div class="nextstepaction"]
> [Konfigurowanie logowania jednokrotnego](add-application-portal-setup-sso.md)
