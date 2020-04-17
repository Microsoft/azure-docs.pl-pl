---
title: Dodawanie istniejącej subskrypcji platformy Azure do dzierżawy — usługa Azure AD
description: Instrukcje dotyczące dodawania istniejącej subskrypcji platformy Azure do dzierżawy usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104bf51fb03d88ab0e5efd25ebebb0e3060bc264
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457930"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Kojarzenie subskrypcji platformy Azure z dzierżawą usługi Azure Active Directory lub dodawanie subskrypcji

Subskrypcja platformy Azure ma relację zaufania z usługą Azure Active Directory (Azure AD). Subskrypcja ufa usłudze Azure AD w celu uwierzytelnienia użytkowników, usług i urządzeń.

Wiele subskrypcji może ufać temu samejemu katalogowi usługi Azure AD. Każda subskrypcja może ufać tylko jednemu katalogowi.

Jeśli subskrypcja wygaśnie, utracisz dostęp do wszystkich innych zasobów skojarzonych z subskrypcją. Jednak katalog usługi Azure AD pozostaje na platformie Azure. Katalog można skojarzyć i zarządzać nim przy użyciu innej subskrypcji platformy Azure.

Wszyscy użytkownicy mają jeden katalog *macierzysty* do uwierzytelniania. Użytkownicy mogą być również gośćmi w innych katalogach. Możesz zobaczyć katalogi domowe i gościnne dla każdego użytkownika w usłudze Azure AD.

> [!Important]
> Po skojarzeniu subskrypcji z innym katalogiem użytkownicy, którym przypisano role przy użyciu [kontroli dostępu opartej na rolach (RBAC),](../../role-based-access-control/role-assignments-portal.md) tracą dostęp. Klasyczni administratorzy subskrypcji, w tym administrator usługi i współadministratorzy, również utracą dostęp.
>
> Przypisania zasad są również usuwane z subskrypcji, gdy subskrypcja jest kojarzona z innym katalogiem.
>
> Przenoszenie klastra usługi Azure Kubernetes Service (AKS) do innej subskrypcji lub przenoszenie subskrypcji posiadającej klastra do nowej dzierżawy powoduje utratę funkcji klastra z powodu utraty przypisania ról i praw jednostki usługi. Aby uzyskać więcej informacji na temat usługi AKS, zobacz [Usługa Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed skojarzeniem lub dodaniem subskrypcji wykonaj następujące czynności:

- Przejrzyj następującą listę zmian i sposób, w jaki może cię to dotyczyć:

  - Użytkownicy, którzy otrzymali role przy użyciu funkcji RBAC, stracą dostęp
  - Administrator usług i współadministratorzy stracą dostęp
  - Jeśli masz jakieś magazyny kluczy, będą niedostępne i będziesz musiał je naprawić po skojarzeniu
  - Jeśli masz żadnych zarządzanych tożsamości dla zasobów, takich jak maszyny wirtualne lub aplikacje logiki, należy ponownie włączyć lub ponownie utworzyć je po skojarzeniu
  - Jeśli masz zarejestrowany stos azure, musisz ponownie zarejestrować go po skojarzeniu

- Zaloguj się przy użyciu konta, które:

  - Ma przypisanie roli [właściciela](../../role-based-access-control/built-in-roles.md#owner) dla subskrypcji. Aby uzyskać informacje dotyczące przypisywania roli Właściciela, zobacz [Zarządzanie dostępem do zasobów platformy Azure przy użyciu funkcji RBAC i witryny Azure.](../../role-based-access-control/role-assignments-portal.md)
  - Istnieje zarówno w bieżącym katalogu, jak i w nowym katalogu. Bieżący katalog jest skojarzony z subskrypcją. Nowy katalog zostanie skojarzony z subskrypcją. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do innego katalogu, zobacz [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure portal](../b2b/add-users-administrator.md).

- Upewnij się, że nie używasz subskrypcji dostawców usług w chmurze platformy Azure (CSP) (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), subskrypcji wewnętrznej firmy Microsoft (MS-AZR-0015P) lub subskrypcji Microsoft Imagine (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Kojarzenie subskrypcji z katalogiem<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Aby skojarzyć istniejącą subskrypcję z katalogiem usługi Azure AD, wykonaj następujące kroki:

1. Zaloguj się i wybierz subskrypcję, której chcesz użyć, ze [strony Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Wybierz **pozycję Zmień katalog**.

    ![Strona Subskrypcje z wyróżnioną opcją Zmień katalog](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Przejrzyj wyświetlane ostrzeżenia, a następnie wybierz pozycję **Zmień**.

    ![Zmienianie strony katalogu z katalogiem, który ma się zmienić na](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Katalog zostanie zmieniony dla subskrypcji i pojawi się komunikat o powoda.

    ![Komunikat o sukcesie dotyczący zmiany katalogu](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Użyj **katalogu Przełącznik,** aby przejść do nowego katalogu. Może upłynąć kilka godzin, aby wszystko pojawiło się prawidłowo. Jeśli wydaje się, że trwa zbyt długo, sprawdź **filtr globalnej subskrypcji**. Upewnij się, że przeniesiona subskrypcja nie jest ukryta. Może być konieczne wylogowanie się z witryny Azure portal i zalogowanie się z powrotem, aby wyświetlić nowy katalog.

![Strona przełącznika katalogu z przykładowymi informacjami](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Zmiana katalogu subskrypcji jest operacją na poziomie usługi, więc nie ma wpływu na własność rozliczeń subskrypcji. Administrator konta nadal może zmieniać administratora usługi w [Centrum kont](https://account.azure.com/subscriptions). Aby usunąć oryginalny katalog, musisz przenieść własność rozliczeń subskrypcji na nowego administratora konta. Aby dowiedzieć się więcej o przenoszeniu własności rozliczeń, zobacz [Przenoszenie własności subskrypcji platformy Azure na inne konto.](../../cost-management-billing/manage/billing-subscription-transfer.md)

## <a name="post-association-steps"></a>Kroki po skojarzeniu

Po skojarzeniu subskrypcji z innym katalogiem może być konieczne wykonywanie następujących zadań w celu wznowienia operacji:

- Jeśli masz jakieś magazyny kluczy, należy zmienić identyfikator dzierżawy magazynu kluczy. Aby uzyskać więcej informacji, zobacz [Zmienianie identyfikatora dzierżawy magazynu kluczy po przejściu subskrypcji](../../key-vault/general/subscription-move-fix.md).

- Jeśli użyto tożsamości zarządzanych przypisanych do systemu dla zasobów, należy ponownie włączyć te tożsamości. Jeśli użyto tożsamości zarządzanych przypisanych przez użytkownika, należy ponownie utworzyć te tożsamości. Po ponownym włączeniu lub ponownym utworzeniu tożsamości zarządzanych należy ponownie ustanowić uprawnienia przypisane do tych tożsamości. Aby uzyskać więcej informacji, zobacz [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).

- Jeśli zarejestrowałeś usługę Azure Stack przy użyciu tej subskrypcji, musisz ponownie się zarejestrować. Aby uzyskać więcej informacji, zobacz [Rejestrowanie usługi Azure Stack na platformie Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Następne kroki

- Aby utworzyć nową dzierżawę usługi Azure AD, zobacz [Szybki start: Tworzenie nowej dzierżawy w usłudze Azure Active Directory](active-directory-access-create-new-tenant.md).

- Aby dowiedzieć się więcej o tym, jak platforma Microsoft Azure kontroluje dostęp do zasobów, zobacz [Klasyczne role administratora subskrypcji, role usługi Azure RBAC i role administratora usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Aby dowiedzieć się więcej o przypisywaniu ról w usłudze Azure AD, zobacz [Przypisywanie ról administratora i osób niebędących administratorami do użytkowników korzystających z usługi Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
