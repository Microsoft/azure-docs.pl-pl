---
title: Uzyskaj widoczność dla całej dzierżawy dla Azure Security Center | Microsoft Docs
description: W tym artykule wyjaśniono, jak zarządzać stan zabezpieczeń na dużą skalę, stosując zasady do wszystkich subskrypcji połączonych z dzierżawą Azure Active Directory.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 7252a6ccd77212f75f5db54e5f3fcad7aa2df50a
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92339734"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Uzyskaj widoczność dla całej dzierżawy dla Azure Security Center
W tym artykule wyjaśniono, jak zarządzać stan zabezpieczeń organizacji na dużą skalę, stosując zasady zabezpieczeń do wszystkich subskrypcji platformy Azure połączonych z dzierżawą Azure Active Directory.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Grupy zarządzania
Grupy zarządzania systemu Azure umożliwiają wydajne zarządzanie dostępem, zasadami i raportowaniem w grupach subskrypcji, a także efektywnie zarządzać całą infrastrukturą platformy Azure przez wykonywanie akcji w głównej grupie zarządzania. Każda dzierżawa usługi Azure AD ma jedną grupę zarządzania najwyższego poziomu nazywaną główną grupą zarządzania. Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Ta grupa umożliwia stosowanie globalnych zasad i przypisań ról platformy Azure na poziomie katalogu. 

Główna Grupa zarządzania jest tworzona automatycznie podczas wykonywania jednej z następujących czynności: 
1. Zezwól na korzystanie z grup zarządzania platformy Azure, przechodząc do **grupy zarządzania** w [Azure Portal](https://portal.azure.com).
2. Utwórz grupę zarządzania za pośrednictwem wywołania interfejsu API.
3. Utwórz grupę zarządzania przy użyciu programu PowerShell.

Aby uzyskać szczegółowe omówienie grup zarządzania, zobacz artykuł [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md) .

## <a name="create-a-management-group-in-the-azure-portal"></a>Utwórz grupę zarządzania w Azure Portal
Subskrypcje można organizować w grupy zarządzania i stosować zasady zarządzania do grup zarządzania. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą zasady zastosowane do tej grupy zarządzania. Gdy grupy zarządzania nie są wymagane do dołączania Security Center, zdecydowanie zaleca się utworzenie co najmniej jednej grupy zarządzania w celu utworzenia głównej grupy zarządzania. Po utworzeniu grupy zostaną do niej połączone wszystkie subskrypcje w ramach dzierżawy usługi Azure AD. Aby uzyskać instrukcje dotyczące programu PowerShell i więcej informacji, zobacz [Tworzenie grup zarządzania na potrzeby zarządzania zasobami i organizacjami](../governance/management-groups/create-management-group-portal.md).

 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie**  >  **grupy zarządzania**usług.
3. Na stronie głównej wybierz pozycję **Nowa grupa zarządzania.** 

    ![Grupa główna](./media/security-center-management-groups/main.png) 
4.  Wypełnij pole Identyfikator grupy zarządzania. 
    - **Identyfikator grupy zarządzania** jest unikatowym identyfikatorem katalogu, który jest używany do przesyłania poleceń z tej grupy zarządzania. Tego identyfikatora nie można edytować po utworzeniu, ponieważ jest on używany w całym systemie Azure do identyfikowania tej grupy. 
    - Pole Nazwa wyświetlana to nazwa wyświetlana w Azure Portal. Oddzielna nazwa wyświetlana jest polem opcjonalnym podczas tworzenia grupy zarządzania i można ją zmienić w dowolnym momencie.  

      ![Utwórz](./media/security-center-management-groups/create_context_menu.png)  
5.  Wybierz pozycję **Zapisz**

### <a name="view-management-groups-in-the-azure-portal"></a>Wyświetlanie grup zarządzania w Azure Portal
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Aby wyświetlić grupy zarządzania, wybierz pozycję **wszystkie usługi** w menu głównym platformy Azure.
3. W obszarze **Ogólne**wybierz pozycję **grupy zarządzania**.

    ![Tworzenie grupy zarządzania](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Przyznaj widoczność na poziomie dzierżawy i możliwość przypisywania zasad

Aby uzyskać wgląd w stan zabezpieczeń wszystkich subskrypcji zarejestrowanych w dzierżawie usługi Azure AD, musisz przypisać rolę platformy Azure z wystarczającymi uprawnieniami do odczytu w głównej grupie zarządzania.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Podnieś poziom dostępu dla administratora globalnego w Azure Active Directory
Administrator dzierżawy Azure Active Directory nie ma bezpośredniego dostępu do subskrypcji platformy Azure. Jednak jako administrator katalogu mają prawo do podniesienia uprawnień do roli, która ma dostęp. Administrator dzierżawy usługi Azure AD musi podnieść się do administratora dostępu użytkowników na poziomie głównego grupy zarządzania, aby mogli przypisywać role platformy Azure. Instrukcje programu PowerShell i dodatkowe informacje znajdują się [w temacie Podnieś poziom dostępu dla administratora globalnego w Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Zaloguj się do [Azure Portal](https://portal.azure.com) lub [Azure Active Directory centrum administracyjnego](https://aad.portal.azure.com).

2. Na liście Nawigacja kliknij **Azure Active Directory** a następnie kliknij pozycję **Właściwości**.

   ![Właściwości usługi Azure AD — zrzut ekranu](./media/security-center-management-groups/aad-properties.png)

3. W obszarze **Zarządzanie dostępem do zasobów platformy Azure**Ustaw przełącznik na **wartość tak**.

   ![Zarządzanie dostępem do zasobów platformy Azure — zrzut ekranu](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Po ustawieniu przełącznika na wartość tak przypiszesz rolę administratora dostępu użytkownika w usłudze Azure RBAC w zakresie głównym (/). To przyznaje uprawnienia do przypisywania ról we wszystkich subskrypcjach platformy Azure i grupach zarządzania skojarzonych z tym katalogiem usługi Azure AD. Ten przełącznik jest dostępny tylko dla użytkowników, którym przypisano rolę administratora globalnego w usłudze Azure AD.

   - Po ustawieniu przełącznika na wartość nie rola administratora dostępu użytkowników w usłudze Azure RBAC zostanie usunięta z konta użytkownika. Nie można już przypisywać ról we wszystkich subskrypcjach platformy Azure i grupach zarządzania skojarzonych z tym katalogiem usługi Azure AD. Można wyświetlać i zarządzać tylko subskrypcjami platformy Azure i grupami zarządzania, do których udzielono dostępu.

4. Kliknij przycisk **Zapisz** , aby zapisać ustawienia.

    - To ustawienie nie jest właściwością globalną i ma zastosowanie tylko do aktualnie zalogowanego użytkownika.

5. Wykonaj zadania, które trzeba wykonać w celu uzyskania dostępu z podwyższonym poziomem uprawnień. Gdy skończysz, ustaw przełącznik z powrotem na **nie**.


### <a name="assign-azure-roles-to-users"></a>Przypisywanie ról platformy Azure do użytkowników
Aby uzyskać wgląd w wszystkie subskrypcje, Administratorzy dzierżawy muszą przypisać odpowiednią rolę platformy Azure do wszystkich użytkowników, którzy chcą udzielić widoczności dla całej dzierżawy, w tym samych, na poziomie głównego grupy zarządzania. Zalecane role do przypisania są **administratora zabezpieczeń** lub **czytelnika zabezpieczeń**. Ogólnie rzecz biorąc, rola administratora zabezpieczeń jest wymagana do zastosowania zasad na poziomie głównym, podczas gdy czytelnik zabezpieczeń wystarcza do zapewnienia widoczności na poziomie dzierżawy. Więcej informacji o uprawnieniach przyznanych przez te role można znaleźć w [opisie wbudowanej roli administrator zabezpieczeń](../role-based-access-control/built-in-roles.md#security-admin) lub [Opis wbudowanej roli czytelnik zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Przypisywanie ról platformy Azure do użytkowników za pomocą Azure Portal: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
1. Aby wyświetlić grupy zarządzania, wybierz pozycję **wszystkie usługi** w menu głównym platformy Azure, a następnie wybierz pozycję **grupy zarządzania**.
1.  Wybierz grupę zarządzania i kliknij przycisk **szczegóły**.

    ![Zrzut ekranu szczegółów Grupy zarządzania](./media/security-center-management-groups/management-group-details.PNG)
 
1. Kliknij kolejno pozycje **Kontrola dostępu (IAM)** i **przypisania ról**.

1. Kliknij pozycję **Dodaj przypisanie roli**.

1. Wybierz rolę do przypisania i użytkownika, a następnie kliknij przycisk **Zapisz**.  
   
   ![Dodaj zrzut ekranu roli czytnika zabezpieczeń](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-azure-roles-to-users-with-powershell"></a>Przypisywanie ról platformy Azure użytkownikom przy użyciu programu PowerShell: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps).
2. Uruchom następujące polecenia: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Po wyświetleniu monitu zaloguj się przy użyciu poświadczeń administratora globalnego. 

    ![Zrzut ekranu z monitem logowania](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Udziel uprawnień roli czytelnika, uruchamiając następujące polecenie:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Aby usunąć rolę, użyj następującego polecenia: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Otwórz lub Odśwież Security Center
Po uzyskaniu podwyższonego poziomu uprawnień Otwórz lub Odśwież Azure Security Center, aby sprawdzić, czy masz wgląd we wszystkie subskrypcje w ramach dzierżawy usługi Azure AD. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Upewnij się, że wybrano wszystkie subskrypcje w selektorze subskrypcji, które chcesz wyświetlić w Security Center.

    ![Zrzut ekranu selektora subskrypcji](./media/security-center-management-groups/subscription-selector.png)

1. Wybierz pozycję **wszystkie usługi** w menu głównym platformy Azure, a następnie wybierz pozycję **Security Center**.
2. W **omówieniu**znajduje się wykres pokrycia subskrypcji.

    ![Zrzut ekranu przedstawiający wykres pokrycia subskrypcji](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Kliknij pozycję **pokrycie** , aby wyświetlić listę pokrytych subskrypcji. 

    ![Zrzut ekranu listy pokrycia subskrypcji](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Usuń dostęp z podwyższonym poziomem uprawnień 
Po przypisaniu ról platformy Azure do użytkowników Administrator dzierżawy powinien usunąć siebie z roli administratora dostępu użytkownika.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) lub [Azure Active Directory centrum administracyjnego](https://aad.portal.azure.com).

2. Na liście Nawigacja kliknij **Azure Active Directory** a następnie kliknij pozycję **Właściwości**.

3. W obszarze **Zarządzanie dostępem do zasobów platformy Azure**Ustaw przełącznik na wartość **nie**.

4. Kliknij przycisk **Zapisz** , aby zapisać ustawienia.



## <a name="adding-subscriptions-to-a-management-group"></a>Dodawanie subskrypcji do grupy zarządzania
Możesz dodać subskrypcje do utworzonej grupy zarządzania. Te kroki nie są wymagane do uzyskania widoczności i globalnego zarządzania zasadami i dostępem na poziomie dzierżawy.

1. W obszarze **grupy zarządzania**wybierz grupę zarządzania, do której chcesz dodać subskrypcję.

    ![Wybierz grupę zarządzania, do której chcesz dodać subskrypcję](./media/security-center-management-groups/management-group-subscriptions.png)

2. Wybierz pozycję **Dodaj istniejące**.

    ![Dodaj istniejące](./media/security-center-management-groups/add-existing.png)

3. Wprowadź subskrypcję w obszarze **Dodaj istniejący zasób** , a następnie kliknij przycisk **Zapisz**.

4. Powtórz kroki od 1 do 3 do momentu dodania wszystkich subskrypcji w zakresie.

   > [!NOTE]
   > Grupy zarządzania mogą zawierać zarówno subskrypcje, jak i podrzędne grupy zarządzania. Jeśli użytkownik przypisze rolę platformy Azure do nadrzędnej grupy zarządzania, dostęp jest dziedziczony przez podrzędne subskrypcje grupy zarządzania. Zasady ustawione w nadrzędnej grupie zarządzania są również dziedziczone przez elementy podrzędne. 

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono, jak uzyskać widoczność dla Azure Security Center w całej dzierżawie. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md)