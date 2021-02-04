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
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 13cbc2e9451221fef951eb6fac4c6b2772275122
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556435"
---
# <a name="organize-management-groups-subscriptions-and-tenant-wide-visibility"></a>Organizowanie grup zarządzania, subskrypcji i widoczności dla całej dzierżawy

W tym artykule wyjaśniono, jak zarządzać stan zabezpieczeń organizacji na dużą skalę, stosując zasady zabezpieczeń do wszystkich subskrypcji platformy Azure połączonych z dzierżawą Azure Active Directory.

Aby uzyskać wgląd w stan zabezpieczeń wszystkich subskrypcji zarejestrowanych w dzierżawie usługi Azure AD, musisz przypisać rolę platformy Azure z wystarczającymi uprawnieniami do odczytu w głównej grupie zarządzania.


## <a name="organize-your-subscriptions-into-management-groups"></a>Organizowanie subskrypcji w grupy zarządzania

### <a name="introduction-to-management-groups"></a>Wprowadzenie do grup zarządzania

Grupy zarządzania systemu Azure umożliwiają wydajne zarządzanie dostępem, zasadami i raportowaniem w grupach subskrypcji, a także efektywnie zarządzać całą infrastrukturą platformy Azure przez wykonywanie akcji w głównej grupie zarządzania. Subskrypcje można organizować w grupy zarządzania i stosować zasady zarządzania do grup zarządzania. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą zasady zastosowane do tej grupy zarządzania. 

Każda dzierżawa usługi Azure AD ma jedną grupę zarządzania najwyższego poziomu nazywaną **główną grupą zarządzania**. Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Ta grupa umożliwia stosowanie globalnych zasad i przypisań ról platformy Azure na poziomie katalogu. 

Główna Grupa zarządzania jest tworzona automatycznie podczas wykonywania jednej z następujących czynności: 
- Otwórz **grupy zarządzania** w [Azure Portal](https://portal.azure.com).
- Utwórz grupę zarządzania z wywołaniem interfejsu API.
- Utwórz grupę zarządzania przy użyciu programu PowerShell. Aby uzyskać instrukcje dotyczące programu PowerShell, zobacz [Tworzenie grup zarządzania w celu zarządzania zasobami i organizacjami](../governance/management-groups/create-management-group-portal.md).

Grupy zarządzania nie są wymagane do dołączenia Security Center, ale zalecamy utworzenie co najmniej jednej, tak aby główna Grupa zarządzania była utworzona. Po utworzeniu grupy zostaną do niej połączone wszystkie subskrypcje w ramach dzierżawy usługi Azure AD. 


Aby uzyskać szczegółowe omówienie grup zarządzania, zobacz artykuł [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md) .

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Wyświetlanie i tworzenie grup zarządzania w Azure Portal

1. Na [Azure Portal](https://portal.azure.com)Użyj pola wyszukiwania na górnym pasku, aby znaleźć i otworzyć **grupy zarządzania**.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Uzyskiwanie dostępu do grup zarządzania":::

    Zostanie wyświetlona lista grup zarządzania.

1. Aby utworzyć grupę zarządzania, wybierz pozycję **Dodaj grupę zarządzania**, wprowadź odpowiednie szczegóły i wybierz pozycję **Zapisz**.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Dodawanie grupy zarządzania do platformy Azure":::

    - **Identyfikator grupy zarządzania** jest unikatowym identyfikatorem katalogu, który jest używany do przesyłania poleceń z tej grupy zarządzania. Tego identyfikatora nie można edytować po utworzeniu, ponieważ jest on używany w całym systemie Azure do identyfikowania tej grupy. 
    - Pole Nazwa wyświetlana to nazwa wyświetlana w Azure Portal. Oddzielna nazwa wyświetlana jest polem opcjonalnym podczas tworzenia grupy zarządzania i można ją zmienić w dowolnym momencie.  


### <a name="add-subscriptions-to-a-management-group"></a>Dodawanie subskrypcji do grupy zarządzania
Możesz dodać subskrypcje do utworzonej grupy zarządzania.

1. W obszarze **grupy zarządzania** wybierz grupę zarządzania dla subskrypcji.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Wybierz grupę zarządzania dla subskrypcji":::

1. Gdy zostanie otwarta strona grupy, wybierz pozycję **szczegóły**

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="Otwieranie strony szczegółów grupy zarządzania":::

1. Na stronie Szczegóły grupy wybierz pozycję **Dodaj subskrypcję**, a następnie wybierz subskrypcje i wybierz pozycję **Zapisz**. Powtarzaj do momentu dodania wszystkich subskrypcji w zakresie.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Dodawanie subskrypcji do grupy zarządzania":::
   > [!IMPORTANT]
   > Grupy zarządzania mogą zawierać zarówno subskrypcje, jak i podrzędne grupy zarządzania. Jeśli użytkownik przypisze rolę platformy Azure do nadrzędnej grupy zarządzania, dostęp jest dziedziczony przez podrzędne subskrypcje grupy zarządzania. Zasady ustawione w nadrzędnej grupie zarządzania są również dziedziczone przez elementy podrzędne. 


## <a name="grant-tenant-wide-permissions-to-yourself"></a>Przyznaj sobie uprawnienia na poziomie dzierżawy

Użytkownik z rolą Azure Active Directory (AD) **administratora globalnego** może korzystać z obowiązków obejmujących wiele dzierżawców, ale nie ma uprawnień platformy Azure do wyświetlania informacji o całej organizacji w Azure Security Center. Podniesienie uprawnień jest wymagane, ponieważ przypisania ról usługi Azure AD nie zapewniają dostępu do zasobów platformy Azure. 

> [!TIP]
> Dowiedz się więcej na temat podniesienia uprawnień roli administratora globalnego w temacie [Podnieś poziom dostępu do zarządzania wszystkimi subskrypcjami i grupami zarządzania platformy Azure](../role-based-access-control/elevate-access-global-admin.md).

Aby przypisać własne uprawnienia na poziomie dzierżawy:

1. Jeśli organizacja zarządza dostępem do zasobów za pomocą [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)lub dowolnego innego narzędzia PIM, rola administratora globalnego musi być aktywna dla użytkownika zgodnie z poniższą procedurą.

1. Jako Administrator globalny bez przypisywania do głównej grupy zarządzania dzierżawy Otwórz stronę **przegląd** Security Center, a następnie wybierz link **widoczność na poziomie dzierżawy** na banerze. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Włącz uprawnienia na poziomie dzierżawy w Azure Security Center":::

1. Wybierz nową rolę platformy Azure, która ma zostać przypisana. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Formularz służący do definiowania uprawnień na poziomie dzierżawy, które mają być przypisane do użytkownika":::

    > [!TIP]
    > Ogólnie rzecz biorąc, rola administratora zabezpieczeń jest wymagana do zastosowania zasad na poziomie głównym, podczas gdy czytelnik zabezpieczeń wystarcza do zapewnienia widoczności na poziomie dzierżawy. Więcej informacji o uprawnieniach przyznanych przez te role można znaleźć w [opisie wbudowanej roli administrator zabezpieczeń](../role-based-access-control/built-in-roles.md#security-admin) lub [Opis wbudowanej roli czytelnik zabezpieczeń](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Różnice między tymi rolami, które są specyficzne dla Security Center, można znaleźć w tabeli w obszarze [role i akcje dozwolone](security-center-permissions.md#roles-and-allowed-actions).

    Widok całej organizacji jest realizowany przez przyznanie ról na głównym poziomie grupy zarządzania dzierżawy.  

1. Wyloguj się z Azure Portal, a następnie zaloguj się ponownie.

1. Po uzyskaniu podwyższonego poziomu uprawnień Otwórz lub Odśwież Azure Security Center, aby sprawdzić, czy masz wgląd we wszystkie subskrypcje w ramach dzierżawy usługi Azure AD. 


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Żądaj uprawnień na poziomie dzierżawy, gdy są niewystarczające

Jeśli zalogujesz się do Security Center i zobaczysz transparent informujący o tym, że Twój Widok jest ograniczony, możesz kliknąć pozycję przez, aby wysłać żądanie do administratora globalnego organizacji. W żądaniu możesz dołączyć rolę, która ma zostać przypisana, a Administrator globalny podejmie decyzję o tym, która rola ma zostać przyznana. 

Jest to decyzja administratora globalnego, czy należy akceptować lub odrzucać te żądania. 

> [!IMPORTANT]
> Jedno żądanie można przesłać co siedem dni.

Aby zażądać podniesionych uprawnień od administratora globalnego:

1. W Azure Portal Otwórz Azure Security Center.

1. Jeśli zobaczysz transparent "widzisz ograniczoną liczbę informacji". Wybierz go.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Transparent informujący użytkownika, że może zażądać uprawnień na poziomie dzierżawy.":::

1. W szczegółowym formularzu żądania wybierz żądaną rolę i uzasadnienie, dlaczego te uprawnienia są potrzebne.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Strona szczegółów do żądania uprawnień na poziomie dzierżawy od administratora globalnego platformy Azure":::

1. Wybierz pozycję **Żądaj dostępu**.

    Wiadomość e-mail jest wysyłana do administratora globalnego. Wiadomość e-mail zawiera link do Security Center, w którym można zatwierdzić lub odrzucić żądanie.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="Wyślij wiadomość e-mail do administratora globalnego, aby uzyskać nowe uprawnienia":::

    Po wybraniu przez administratora globalnego **zapoznania się z żądaniem** i zakończeniu procesu decyzja zostaje wysłana pocztą e-mail do żądającego użytkownika. 

## <a name="assign-azure-roles-to-other-users"></a>Przypisywanie ról platformy Azure do innych użytkowników

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Przypisywanie ról platformy Azure do użytkowników za pomocą Azure Portal: 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
1. Aby wyświetlić grupy zarządzania, wybierz pozycję **wszystkie usługi** w menu głównym platformy Azure, a następnie wybierz pozycję **grupy zarządzania**.
1.  Wybierz grupę zarządzania i wybierz pozycję **szczegóły**.

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="Zrzut ekranu szczegółów Grupy zarządzania":::

1. Wybierz pozycję **Kontrola dostępu (IAM)** , a następnie **przypisania ról**.
1. Wybierz pozycję **Dodaj przypisanie roli**.
1. Wybierz rolę do przypisania i użytkownika, a następnie wybierz pozycję **Zapisz**.  
   
   ![Dodaj zrzut ekranu roli czytnika zabezpieczeń](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>Przypisywanie ról platformy Azure użytkownikom przy użyciu programu PowerShell: 
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

## <a name="remove-elevated-access"></a>Usuń dostęp z podwyższonym poziomem uprawnień 

Po przypisaniu ról platformy Azure do użytkowników Administrator dzierżawy powinien usunąć siebie z roli administratora dostępu użytkownika.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) lub [Azure Active Directory centrum administracyjnego](https://aad.portal.azure.com).

2. Na liście Nawigacja wybierz pozycję **Azure Active Directory** a następnie wybierz pozycję **Właściwości**.

3. W obszarze **Zarządzanie dostępem do zasobów platformy Azure** Ustaw przełącznik na wartość **nie**.

4. Aby zapisać ustawienia, wybierz pozycję **Zapisz**.



## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono, jak uzyskać widoczność dla Azure Security Center w całej dzierżawie. Aby uzyskać powiązane informacje, zobacz:

- [Uprawnienia w usłudze Azure Security Center](security-center-permissions.md)