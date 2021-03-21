---
title: Dodawanie istniejącej subskrypcji platformy Azure do dzierżawy — Azure AD
description: Instrukcje dotyczące dodawania istniejącej subskrypcji platformy Azure do dzierżawy usługi Azure Active Directory (Azure AD).
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ac9553660aace8242c81b41fa2cc9171d28219
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594635"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Kojarzenie subskrypcji platformy Azure z dzierżawą usługi Azure Active Directory lub dodawanie subskrypcji

Subskrypcja platformy Azure ma relację zaufania z usługą Azure Active Directory (Azure AD). Subskrypcja ufa usłudze Azure AD w celu uwierzytelniania użytkowników, usług i urządzeń.

Wiele subskrypcji może ufać temu samemu katalogowi usługi Azure AD. Każda subskrypcja może ufać tylko jednemu katalogowi.

Co najmniej jedna subskrypcja platformy Azure może ustanowić relację zaufania z wystąpieniem Azure Active Directory (Azure AD) w celu uwierzytelniania i autoryzowania podmiotów zabezpieczeń oraz urządzeń w odniesieniu do usług platformy Azure.  Po wygaśnięciu subskrypcji zaufane wystąpienie usługi Azure AD pozostanie, ale podmioty zabezpieczeń utracą dostęp do zasobów platformy Azure.

Gdy użytkownik loguje się do usługi w chmurze firmy Microsoft, zostanie utworzona nowa dzierżawa usługi Azure AD, a użytkownik jest członkiem roli administratora globalnego. Jeśli jednak właściciel subskrypcji przyłączy swoją subskrypcję do istniejącej dzierżawy, właściciel nie zostanie przypisany do roli administratora globalnego.

Wszyscy użytkownicy mają jeden katalog *macierzysty* na potrzeby uwierzytelniania. Użytkownicy mogą również być Gośćmi w innych katalogach. Dla każdego użytkownika w usłudze Azure AD można zobaczyć zarówno katalog macierzysty, jak i gość.

> [!Important]
> Po skojarzeniu subskrypcji z innym katalogiem użytkownicy, którzy mają role przypisane przy użyciu [kontroli dostępu opartej na rolach platformy Azure](../../role-based-access-control/role-assignments-portal.md) , tracą dostęp. Klasyczni administratorzy subskrypcji, w tym administrator usługi i współadministratorzy, również utracą dostęp.
>
> Przeniesienie klastra usługi Azure Kubernetes Service (AKS) do innej subskrypcji lub przeniesienie subskrypcji będącej właścicielem klastra do nowej dzierżawy powoduje utratę funkcjonalności klastra z powodu utraty przypisań ról i uprawnień jednostki usługi. Aby uzyskać więcej informacji na temat AKS, zobacz [Azure Kubernetes Service (AKS)](../../aks/index.yml).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby można było skojarzyć lub dodać subskrypcję, wykonaj następujące zadania:

- Przejrzyj następującą listę zmian, które nastąpią po skojarzeniu lub dodaniu subskrypcji oraz o tym, jak może to mieć wpływ:

  - Użytkownicy, którym przypisano role przy użyciu usługi Azure RBAC, utracą dostęp.
  - Administrator usługi i Co-Administrators utraci dostęp.
  - Jeśli masz jakieś magazyny kluczy, staną się niedostępne i będziesz musiał rozwiązać te problemy po skojarzeniu.
  - Jeśli masz jakiekolwiek zarządzane tożsamości dla zasobów, takich jak Virtual Machines lub Logic Apps, musisz je ponownie włączyć lub utworzyć po skojarzeniu.
  - Jeśli masz zarejestrowaną Azure Stack, musisz ponownie zarejestrować ją po skojarzeniu.
  - Aby uzyskać więcej informacji, zobacz [Przenoszenie subskrypcji platformy Azure do innego katalogu usługi Azure AD](../../role-based-access-control/transfer-subscription.md).

- Zaloguj się przy użyciu konta, które:

  - Ma przypisanie roli [właściciela](../../role-based-access-control/built-in-roles.md#owner) do subskrypcji. Aby uzyskać informacje na temat sposobu przypisywania roli właściciela, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md).
  - Istnieje zarówno w bieżącym katalogu, jak i w nowym katalogu. Bieżący katalog jest skojarzony z subskrypcją. Nowy katalog zostanie skojarzony z subskrypcją. Aby uzyskać więcej informacji o uzyskiwaniu dostępu do innego katalogu, zobacz [dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal](../external-identities/add-users-administrator.md).

- Upewnij się, że nie używasz subskrypcji dostawcy usług w chmurze (CSP) platformy Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), wewnętrznej subskrypcji firmy Microsoft (MS-AZR-0015P) ani subskrypcji Microsoft Azure for Students Starter (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Kojarzenie subskrypcji z katalogiem<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Aby skojarzyć istniejącą subskrypcję z katalogiem usługi Azure AD, wykonaj następujące kroki:

1. Zaloguj się i wybierz subskrypcję, której chcesz użyć na [stronie Subskrypcje w Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Wybierz pozycję **Zmień katalog**.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png" alt-text="Zrzut ekranu przedstawiający stronę subskrypcje z wyróżnioną opcją zmiany katalogu.":::

1. Przejrzyj wszystkie wyświetlone ostrzeżenia, a następnie wybierz pozycję **Zmień**.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png" alt-text="Zrzut ekranu pokazujący zmianę strony katalogu z przykładowym katalogiem i wyróżniony przycisk Zmień.":::

   Po zmianie katalogu dla subskrypcji zostanie wyświetlony komunikat o powodzeniu.

1. Wybierz pozycję **Przełącz katalogi** na stronie subskrypcja, aby przejść do nowego katalogu.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/directory-switcher.png" alt-text="Zrzut ekranu przedstawiający stronę przełącznik katalogów z przykładowymi informacjami.":::

   Poprawne wyświetlanie wszystkiego może potrwać kilka godzin. Jeśli wydaje się, że trwa zbyt długo, sprawdź **globalny filtr subskrypcji**. Upewnij się, że przeniesiona subskrypcja nie jest ukryta. Może być konieczne wylogowanie się z Azure Portal i ponowne zalogowanie się w celu wyświetlenia nowego katalogu.

Zmiana katalogu subskrypcji jest operacją na poziomie usługi, więc nie ma ona wpływu na własność rozliczeń subskrypcji. Aby usunąć oryginalny katalog, należy przenieść własność rozliczeń subskrypcji do nowego administratora konta. Aby dowiedzieć się więcej na temat przenoszenia własności rozliczeń, zobacz [przenoszenie własności subskrypcji platformy Azure na inne konto](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Kroki po skojarzeniu

Po skojarzeniu subskrypcji z innym katalogiem, może być konieczne wykonanie następujących zadań w celu wznowienia operacji:

- Jeśli masz jakieś magazyny kluczy, musisz zmienić identyfikator dzierżawy magazynu kluczy. Aby uzyskać więcej informacji, zobacz [zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji](../../key-vault/general/move-subscription.md).

- W przypadku używania tożsamości zarządzanych przypisanych przez system do zasobów należy ponownie włączyć te tożsamości. W przypadku używania tożsamości zarządzanych przypisanych przez użytkownika należy ponownie utworzyć te tożsamości. Po ponownym włączeniu lub odtworzeniu tożsamości zarządzanych należy ponownie ustanowić uprawnienia przypisane do tych tożsamości. Aby uzyskać więcej informacji, zobacz [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).

- Jeśli zarejestrowano Azure Stack przy użyciu tej subskrypcji, należy ponownie zarejestrować. Aby uzyskać więcej informacji, zobacz [Rejestrowanie centrum Azure Stack przy użyciu platformy Azure](/azure-stack/operator/azure-stack-registration).

- Aby uzyskać więcej informacji, zobacz [Przenoszenie subskrypcji platformy Azure do innego katalogu usługi Azure AD](../../role-based-access-control/transfer-subscription.md).

## <a name="next-steps"></a>Następne kroki

- Aby utworzyć nową dzierżawę usługi Azure AD, zobacz [Szybki Start: Tworzenie nowej dzierżawy w Azure Active Directory](active-directory-access-create-new-tenant.md).

- Aby dowiedzieć się więcej o tym, jak Microsoft Azure kontroluje dostęp do zasobów, zobacz Role [administratora subskrypcji klasycznej, role platformy Azure i role administratorów usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Aby dowiedzieć się więcej o sposobach przypisywania ról w usłudze Azure AD, zobacz [Przypisywanie ról administratora i niebędących administratorami do użytkowników z Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
