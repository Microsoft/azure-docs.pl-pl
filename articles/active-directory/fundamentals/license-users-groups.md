---
title: Przypisywanie lub usuwanie licencji — Azure Active Directory | Microsoft Docs
description: Instrukcje dotyczące sposobu przypisywania lub usuwania licencji Azure Active Directory od użytkowników lub grup.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6900647acf7182529f34c8cc065dbb039de38be
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504410"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Przypisywanie lub usuwanie licencji w portalu Azure Active Directory

Wiele usług Azure Active Directory (Azure AD) wymaga podania licencji dla wszystkich użytkowników lub grup (i skojarzonych członków) dla tej usługi. Tylko użytkownicy z aktywnymi licencjami będą mogli uzyskiwać dostęp do licencjonowanych usług Azure AD i korzystać z nich, których to dotyczy. Licencje są stosowane na dzierżawcę i nie są przekazywane do innych dzierżawców. 

## <a name="available-license-plans"></a>Dostępne plany licencji

Istnieje kilka planów licencji dostępnych dla usługi Azure AD, w tym:

- Usługa Azure AD — warstwa Bezpłatna

- Usługa Azure AD — wersja Premium P1

- Usługa Azure AD — wersja Premium P2

Aby uzyskać szczegółowe informacje na temat każdego planu licencjonowania i skojarzonych ze sobą szczegółów licencji, zobacz [Jaka jest potrzebna licencja?](https://azure.microsoft.com/pricing/details/active-directory/). Aby zarejestrować się w planach licencji usługi Azure AD Premium, zobacz [tutaj](./active-directory-get-started-premium.md).

Nie wszystkie usługi firmy Microsoft są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do grupy, należy określić **lokalizację użycia** dla wszystkich elementów członkowskich. Tę wartość można ustawić w obszarze **&gt; &gt; &gt; ustawień profilu użytkownicy Azure Active Directory** w usłudze Azure AD. Każdy użytkownik, którego lokalizacja użycia nie została określona, dziedziczy lokalizację organizacji usługi Azure AD.

## <a name="view-license-plans-and-plan-details"></a>Wyświetl plany licencji i szczegóły planu

Możesz wyświetlić dostępne plany usług, w tym poszczególne licencje, sprawdzić oczekujące daty wygaśnięcia i wyświetlić liczbę dostępnych przypisań.

### <a name="to-find-your-service-plan-and-plan-details"></a>Aby znaleźć szczegóły planu usługi i planu

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora licencji w organizacji usługi Azure AD.

1. Wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **licencje**.

    :::image type="content" source="media/license-users-groups/license-details-blade.png" alt-text="Strona licencje z liczbą zakupionych usług i przypisanymi licencjami":::

1. Wybierz pozycję **wszystkie produkty** , aby wyświetlić stronę wszystkie produkty i wyświetlić **łączną**, **przypisaną**, **dostępną** i **wygaśnie liczbę wkrótce** dla planów licencji.

    :::image type="content" source="media/license-users-groups/license-products-blade-with-products.png" alt-text="Strona usług — z planami licencji usługi — informacje o licencji skojarzonej":::

    > [!NOTE]
    > Liczby są zdefiniowane jako: 
    > - Łącznie: całkowita liczba zakupionych licencji
    > - Przypisane: liczba licencji przypisanych do użytkowników
    > - Dostępne: liczba licencji dostępnych do przypisania, w tym wkrótce wygaśnie
    > - Wkrótce wygaśnie: liczba licencji wkrótce wygaśnie

1. Wybierz nazwę planu, aby wyświetlić jego licencjonowanych użytkowników i grupy.

## <a name="assign-licenses-to-users-or-groups"></a>Przypisywanie licencji do użytkowników lub grup

Upewnij się, że każda osoba, która chce korzystać z licencjonowanej usługi Azure AD, ma odpowiednią licencję. Możesz dodać prawa licencjonowania do użytkowników lub do całej grupy.

### <a name="to-assign-a-license-to-a-user"></a>Aby przypisać licencję do użytkownika

1. Na stronie **produkty** wybierz nazwę planu licencji, który ma zostać przypisany do użytkownika.

    ![Strona usług z wyróżnionym planem licencji usługi](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na stronie Przegląd planu licencji wybierz pozycję **Przypisz**.

    ![Strona usług z wyróżnioną opcją przypisania](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na stronie **przypisanie** wybierz pozycję **Użytkownicy i grupy**, a następnie wyszukaj i wybierz użytkownika, którego przypiszesz licencję.

    ![Strona Przypisywanie licencji z wyróżnionym wyszukiwaniem i wybieraniem opcji](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Wybierz **Opcje przypisania**, upewnij się, że masz włączone odpowiednie opcje licencji, a następnie wybierz przycisk **OK**.

    ![Strona opcji licencji z wszystkimi opcjami dostępnymi w planie licencji](media/license-users-groups/license-option-blade-assignments.png)

    Na stronie **Przypisz licencję** są aktualizowane aktualizacje pokazujące, że wybrano użytkownika i skonfigurowano przypisania.

    > [!NOTE]
    > Nie wszystkie usługi firmy Microsoft są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do użytkownika, należy określić **lokalizację użycia**. Tę wartość można ustawić w obszarze **&gt; &gt; &gt; ustawień profilu użytkownicy Azure Active Directory** w usłudze Azure AD. Każdy użytkownik, którego lokalizacja użycia nie została określona, dziedziczy lokalizację organizacji usługi Azure AD.

1. Wybierz opcję **Przypisz**.

    Użytkownik zostanie dodany do listy licencjonowanych użytkowników i ma dostęp do uwzględnionych usług Azure AD.
    > [!NOTE]
    > Licencje można także przypisać bezpośrednio do użytkownika ze strony **licencje** użytkownika. Jeśli użytkownik ma licencję przypisaną za pośrednictwem członkostwa w grupie i chcesz bezpośrednio przypisać tę samą licencję do użytkownika, można to zrobić tylko z poziomu strony **produkty** wymienione tylko w kroku 1.

### <a name="to-assign-a-license-to-a-group"></a>Aby przypisać licencję do grupy

1. Na stronie **produkty** wybierz nazwę planu licencji, który ma zostać przypisany do użytkownika.

    ![Blok produkty z wyróżnionym planem licencjonowania produktu](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na stronie **Azure Active Directory — wersja Premium plan 2** wybierz pozycję **Przypisz**.

    ![Strona produkty z wyróżnioną opcją przypisania](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na stronie **przypisywanie** wybierz pozycję **Użytkownicy i grupy**, a następnie wyszukaj i wybierz grupę, do której jest przypisana licencja.

    ![Strona Przypisywanie licencji z wyróżnionym wyszukiwaniem i wybieraniem opcji 2](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Wybierz **Opcje przypisania**, upewnij się, że masz włączone odpowiednie opcje licencji, a następnie wybierz przycisk **OK**.

    ![Strona opcji licencji z wszystkimi opcjami dostępnymi w planie licencji 2](media/license-users-groups/license-option-blade-group-assignments.png)

    Na stronie **Przypisz licencję** są aktualizowane aktualizacje pokazujące, że wybrano użytkownika i skonfigurowano przypisania.

1. Wybierz opcję **Przypisz**.

    Grupa zostanie dodana do listy licencjonowanych grup i wszyscy członkowie mają dostęp do uwzględnionych usług Azure AD.

## <a name="remove-a-license"></a>Usuwanie licencji

Licencję można usunąć ze strony użytkownika usługi Azure AD użytkownika, z poziomu strony Przegląd grupy dla przypisania grupy lub od strony **licencje** usługi Azure AD w celu wyświetlenia użytkowników i grup licencji.

### <a name="to-remove-a-license-from-a-user"></a>Aby usunąć licencję z użytkownika

1. Na stronie **Licencjonowani użytkownicy** dla planu usługi wybierz użytkownika, który nie powinien już korzystać z licencji. Na przykład _Alain Charon_.

1. Wybierz pozycję **Usuń licencję**.

    ![Strona Licencjonowani użytkownicy z wyróżnioną opcją Usuń licencję](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> Licencje, które użytkownik dziedziczy z grupy, nie mogą zostać usunięte bezpośrednio. Zamiast tego należy usunąć użytkownika z grupy, z której są dziedziczone licencje.

### <a name="to-remove-a-license-from-a-group"></a>Aby usunąć licencję z grupy

1. Na stronie **grupy licencjonowane** dla planu licencji wybierz grupę, która nie powinna już mieć licencji.

1. Wybierz pozycję **Usuń licencję**.

    ![Strona licencjonowane grupy z wyróżnioną opcją Usuń licencję 2](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Jeśli konto użytkownika lokalnego zsynchronizowane z usługą Azure AD wykracza poza zakres synchronizacji lub gdy synchronizacja zostanie usunięta, użytkownik zostanie usunięty w usłudze Azure AD. W takim przypadku licencje przypisane do użytkownika bezpośrednio lub za pośrednictwem licencjonowania opartego na grupach zostaną oznaczone jako **zawieszone** , a nie **usunięte**.

## <a name="next-steps"></a>Następne kroki

Po przypisaniu licencji możesz wykonać następujące procesy:

- [Identyfikowanie i rozwiązywanie problemów z przypisaniem licencji](../enterprise-users/licensing-groups-resolve-problems.md)

- [Dodawanie licencjonowanych użytkowników do grupy na potrzeby licencjonowania](../enterprise-users/licensing-groups-migrate-users.md)

- [Scenariusze, ograniczenia i znane problemy przy użyciu grup do zarządzania licencjonowaniem w programie Azure Active Directory](../enterprise-users/licensing-group-advanced.md)

- [Dodawanie lub zmiana informacji o profilu](active-directory-users-profile-azure-portal.md)
