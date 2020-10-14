---
title: Samouczek — Konfigurowanie ról Azure Analysis Services | Microsoft Docs
description: W tym samouczku dowiesz się, jak skonfigurować Azure Analysis Services administratorów i ról użytkowników przy użyciu Azure Portal lub SQL Server Management Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: aea2196671a136145671b977a6d925849b635b73
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018699"
---
# <a name="tutorial-configure-server-administrator-and-user-roles"></a>Samouczek: konfigurowanie ról administratora i użytkowników serwera

 W tym samouczku użyjesz programu SQL Server Management Studio (SSMS), aby połączyć się z serwerem na platformie Azure w celu skonfigurowania ról administratora serwera i modelowej bazy danych. Zostaną w nim również przedstawione informacje na temat [języka TMSL (Tabular Model Scripting Language)](/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200). TMSL to język skryptów oparty na notacji JSON, obsługujący modele tabelaryczne na poziomie zgodności 1200 lub wyższym. Może być używany do automatyzacji wielu zadań związanych z modelowaniem tabelarycznym. Język TMSL jest często używany w programie PowerShell, ale w tym samouczku jest używany w edytorze zapytań XMLA w programie SSMS. Podczas pracy z tym samouczkiem wykonasz następujące zadania: 
  
> [!div class="checklist"]
> * Uzyskiwanie nazwy serwera z portalu
> * Nawiązywanie połączenia z serwerem przy użyciu programu SSMS
> * Dodawanie użytkownika lub grupy do roli administratora serwera 
> * Dodawanie użytkownika lub grupy do roli administratora modelowej bazy danych
> * Dodawanie nowej roli modelowej bazy danych i dodawanie użytkownika lub grupy

Aby dowiedzieć się więcej na temat bezpieczeństwa użytkownika w usłudze Azure Analysis Services, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkowników)](../analysis-services-manage-users.md). 

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure Active Directory w ramach subskrypcji.
- [Serwer usług Azure Analysis Services](../analysis-services-create-server.md) utworzony w ramach subskrypcji.
- Uprawnienia [administratora serwera](../analysis-services-server-admins.md).
- [Przykładowy model adventureworks](../analysis-services-create-sample-model.md) dodany do serwera.
- [Zainstalowana najnowsza wersja programu SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [portalu](https://portal.azure.com/).

## <a name="get-server-name"></a>Uzyskiwanie nazwy serwera
Aby móc nawiązać połączenie z serwerem z poziomu programu SSMS, najpierw należy uzyskać nazwę serwera. Możesz uzyskać nazwę serwera w portalu.

Skopiuj nazwę serwera z **portalu Azure** > serwer > **Omówienie** > **Nazwa serwera**.
   
   ![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-tutorial-roles/aas-copy-server-name.png)

## <a name="connect-in-ssms"></a>Nawiązywanie połączenia w programie SSMS

W pozostałych zadaniach użyjesz programu SSMS, aby połączyć się z serwerem i nim zarządzać.

1. W programie SSMS wybierz pozycję **Eksplorator obiektów**, a następnie kliknij pozycję **Połącz** > **Analysis Services**.

    ![Połącz](./media/analysis-services-tutorial-roles/aas-ssms-connect.png)

2. W oknie dialogowym **Połączenie z serwerem** w polu **Nazwa serwera** skopiuj i wklej nazwę serwera skopiowaną z portalu. W polu **Uwierzytelnianie** wybierz pozycję **Active Directory — Uniwersalne z obsługą uwierzytelniania wieloskładnikowego**, a następnie wprowadź nazwę konta użytkownika i naciśnij przycisk **Połącz**.
   
    ![Nawiązywanie połączenia w programie SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-auth.png)

    > [!TIP]
    > Zalecane jest wybranie opcji Active Directory — Uniwersalne z obsługą uwierzytelniania wieloskładnikowego. Ten typ uwierzytelniania obsługuje [uwierzytelnianie nieinterakcyjne i wieloskładnikowe](../../azure-sql/database/authentication-mfa-ssms-overview.md). 

3. W **Eksploratorze obiektów** rozwiń serwer, aby wyświetlić obiekty serwera. Kliknij prawym przyciskiem myszy, aby wyświetlić właściwości serwera.
   
    ![Eksplorator obiektów w programie SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-objexp.png)

## <a name="add-a-user-account-to-the-server-administrator-role"></a>Dodawanie konta użytkownika do roli administratora serwera

W tym zadaniu dodasz konto użytkownika lub konto grupowe z usługi Azure AD do roli administratora serwera. W przypadku określania grupy zabezpieczeń Użyj `obj:groupid@tenantid` .

1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy nazwę serwera, a następnie kliknij pozycję **Właściwości**. 
2. W oknie **Właściwości serwera programu Analysis Server** kliknij kolejno pozycje **Zabezpieczenia** > **Dodaj**.
3. W oknie **Wybierz użytkownika lub grupę** wprowadź konto użytkownika lub grupy w usłudze Azure AD, a następnie kliknij przycisk **Dodaj**. 
   
     ![Dodawanie administratora serwera](./media/analysis-services-tutorial-roles/aas-add-server-admin.png)

4. Kliknij przycisk **OK**, aby zamknąć okno **Właściwości serwera programu Analysis Server**.

    > [!TIP]
    > Możesz również dodać administratorów serwera za pomocą pozycji **Administratorzy usług Analysis Services** w portalu. 

## <a name="add-a-user-to-the-model-database-administrator-role"></a>Dodawanie użytkownika do roli administratora modelowej bazy danych

W tym zadaniu dodasz konto użytkownika lub konto grupowe do roli administratora sprzedaży internetowej, która już istnieje w modelu. Ta rola posiada uprawnienia pełnej kontroli (administratora) dla przykładowej bazy danych adventureworks. W tym zadaniu używane jest polecenie języka TMSL [CreateOrReplace](/analysis-services/tmsl/createorreplace-command-tmsl), zawarte w gotowym skrypcie.

1. W **Eksploratorze obiektów** rozwiń pozycję **Bazy danych** > **adventureworks** > **Role**. 
2. Kliknij prawym przyciskiem myszy pozycję **Administrator sprzedaży internetowej**, a następnie kliknij pozycję **Rola skryptu jako** > **UTWÓRZ LUB ZAMIEŃ na** > **Nowe okno edytora zapytań**.

    ![Nowe okno edytora zapytań](./media/analysis-services-tutorial-roles/aas-add-db-admin.png)

3. W polu **XMLAQuery**, zmień wartość atrybutu **„memberName”:** na konto użytkownika lub grupy w usłudze Azure AD. Domyślnie dostępne jest również aktualnie zalogowane konto, ale nie musisz dodawać swojego własnego konta, ponieważ jesteś już administratorem serwera.

    ![Skrypt języka TMSL w zapytaniu XMLA](./media/analysis-services-tutorial-roles/aas-add-db-admin-script.png)

4. Naciśnij klawisz **F5**, aby wykonać skrypt.


## <a name="add-a-new-model-database-role-and-add-a-user-or-group"></a>Dodawanie nowej roli modelowej bazy danych i dodawanie użytkownika lub grupy

W tym zadaniu użyjesz polecenia [Create](/analysis-services/tmsl/create-command-tmsl) w skrypcie TMSL, aby utworzyć nową globalną rolę sprzedaży internetowej, wybierzesz uprawnienia do *odczytu* dla tej roli i dodasz do niej konto użytkownika lub grupy z usługi Azure AD.

1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy pozycję **adventureworks**, a następnie kliknij polecenie **Nowe zapytanie** > **XMLA**. 
2. Skopiuj i wklej poniższy skrypt języka TMSL w edytorze zapytań:

    ```JSON
    {
    "create": {
      "parentObject": {
        "database": "adventureworks",
       },
       "role": {
         "name": "Internet Sales Global",
         "description": "All users can query model data",
         "modelPermission": "read",
         "members": [
           {
             "memberName": "globalsales@adventureworks.com",
             "identityProvider": "AzureAD"
           }
         ]
       }
      }
    }
    ```

3. Zmień wartość obiektu `"memberName": "globalsales@adventureworks.com"` na konto użytkownika lub grupy w usłudze Azure AD.
4. Naciśnij klawisz **F5**, aby wykonać skrypt.

## <a name="verify-your-changes"></a>Sprawdzanie zmian

1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy nazwę serwera, a następnie kliknij pozycję **Odśwież** lub naciśnij klawisz **F5**.
2. Rozwiń węzeł **bazy danych**  >  **adventureworks**  >  **role**AdventureWorks. Sprawdź, czy jest widoczne konto użytkownika wraz ze zmianami ról wprowadzonymi w poprzednich zadaniach.   

    ![Sprawdzenie w Eksploratorze obiektów](./media/analysis-services-tutorial-roles/aas-connect-ssms-verify.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy konta i role użytkownika lub grupy nie są już potrzebne, można je usunąć. W tym celu należy użyć **Role Properties**  >  **przynależności** do właściwości roli, aby usunąć konta użytkowników, lub kliknij prawym przyciskiem myszy rolę, a następnie kliknij pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób łączenia się z serwerem Azure AD i pracy z przykładowymi modelowymi bazami danych adventureworks oraz właściwościami w programie SSMS. Przedstawiono również sposób korzystania ze skryptów SSMS i TMSL w celu dodawania użytkowników lub grup do istniejących i nowych ról. Teraz, po skonfigurowaniu uprawnień użytkowników na serwerze i w przykładowej modelowej bazie danych, Ty i inni użytkownicy możecie łączyć się z nimi za pomocą aplikacji klienckich, takich jak Power BI. Aby dowiedzieć się więcej, przejdź do następnego samouczka. 

> [!div class="nextstepaction"]
> [Samouczek: nawiązywanie połączenia za pomocą programu Power BI Desktop](analysis-services-tutorial-pbid.md)