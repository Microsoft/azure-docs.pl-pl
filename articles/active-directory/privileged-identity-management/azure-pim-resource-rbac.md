---
title: Wyświetlanie raportu inspekcji dla ról zasobów platformy Azure w Privileged Identity Management (PIM) — Azure AD | Microsoft Docs
description: Wyświetl historię działań i inspekcji dla ról zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45144f64789a19390984c3f9f6a660e3c3300215
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002497"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Wyświetlanie historii działań i inspekcji dla ról zasobów platformy Azure w Privileged Identity Management

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można wyświetlać działania, aktywacje i historię inspekcji dla ról zasobów platformy Azure w organizacji. Obejmuje to subskrypcje, grupy zasobów, a nawet maszyny wirtualne. Każdy zasób w Azure Portal, który korzysta z funkcji kontroli dostępu opartej na rolach platformy Azure, może korzystać z możliwości zarządzania zabezpieczeniami i cyklem życia w Privileged Identity Management.

> [!NOTE]
> Jeśli organizacja ma funkcje zarządzania, które są używane przez usługę zarządzania [zasobami delegowanymi przez platformę Azure](../../lighthouse/concepts/azure-delegated-resource-management.md), w tym miejscu nie będą wyświetlane przypisania ról autoryzowane przez tego dostawcę usług.

## <a name="view-activity-and-activations"></a>Wyświetl aktywność i aktywacje

Aby zobaczyć akcje wykonywane przez określonego użytkownika w różnych zasobach, można wyświetlić aktywność zasobów platformy Azure skojarzoną z danym okresem aktywacji.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **zasoby platformy Azure**.

1. Wybierz zasób, dla którego chcesz wyświetlić aktywność i aktywacje.

1. Wybierz **role** lub **członków**.

1. Wybierz użytkownika.

    Zobaczysz podsumowanie akcji użytkownika w zasobach platformy Azure według daty. Pokazuje także ostatnie aktywacje ról w tym samym okresie czasu.

    ![Szczegóły użytkownika z podsumowaniem aktywności zasobów i aktywacjami ról](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Wybierz aktywację określonej roli, aby zobaczyć szczegóły i odpowiednie działanie zasobów platformy Azure, które wystąpiło, gdy ten użytkownik był aktywny.

    [![Wybrana aktywacja roli i szczegóły działania](media/azure-pim-resource-rbac/export-membership.png "Wybrana aktywacja roli i szczegóły działania")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Eksportowanie przypisań ról z elementami podrzędnymi

Może istnieć wymóg zgodności, w którym należy podać pełną listę przypisań ról do audytorów. Privileged Identity Management umożliwia wykonywanie zapytań dotyczących przypisań ról w określonym zasobie, które obejmują przypisania ról dla wszystkich zasobów podrzędnych. Wcześniej było trudne, aby administratorzy mogli uzyskać pełną listę przypisań ról dla subskrypcji i musiały wyeksportować przypisania ról dla każdego określonego zasobu. Za pomocą Privileged Identity Management można wysyłać zapytania dotyczące wszystkich aktywnych i kwalifikujących się przypisań ról w ramach subskrypcji, łącznie z przypisaniami ról dla wszystkich grup zasobów i zasobów.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **zasoby platformy Azure**.

1. Wybierz zasób, dla którego chcesz wyeksportować przypisania ról, takie jak subskrypcja.

1. Wybierz **członków**.

1. Wybierz pozycję **Eksportuj** , aby otworzyć okienko Eksportuj członkostwo.

    [![Eksportuj okienko członkostwa, aby wyeksportować wszystkie elementy członkowskie](media/azure-pim-resource-rbac/export-membership.png "Eksportuj stronę członkostwa, aby wyeksportować wszystkie elementy członkowskie")](media/azure-pim-resource-rbac/export-membership.png)

1. Wybierz opcję **Eksportuj wszystkich członków** , aby wyeksportować wszystkie przypisania ról w pliku CSV.

    ![Wyeksportowane przypisania ról w pliku CSV jako wyświetlane w programie Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Wyświetl historię inspekcji zasobów

Inspekcja zasobów umożliwia wyświetlenie wszystkich działań roli dla zasobu.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **zasoby platformy Azure**.

1. Wybierz zasób, dla którego ma zostać wyświetlona Historia inspekcji.

1. Wybierz pozycję **Inspekcja zasobów**.

1. Przefiltruj historię przy użyciu wstępnie zdefiniowanej daty lub niestandardowego zakresu.

    [![Lista inspekcji zasobów z filtrami](media/azure-pim-resource-rbac/rbac-resource-audit.png "Lista inspekcji zasobów z filtrami")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. W obszarze **Typ inspekcji** wybierz pozycję **Aktywuj (przypisane + aktywowane)**.

    [![Lista inspekcji zasobów filtrowana według typu](media/azure-pim-resource-rbac/rbac-audit-activity.png "Lista inspekcji zasobów filtrowana według aktywacji")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![ przeprowadzenia inspekcji Lista inspekcji zasobów filtrowana według typu przeprowadzenia inspekcji](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. W obszarze **Akcja** kliknij pozycję **(działanie)** dla użytkownika, aby zobaczyć szczegóły aktywności tego użytkownika w zasobach platformy Azure.

    ![Szczegóły aktywności użytkownika dla określonej akcji](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Wyświetl moją inspekcję

Moja Inspekcja umożliwia wyświetlenie własnej aktywności roli użytkownika.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **zasoby platformy Azure**.

1. Wybierz zasób, dla którego ma zostać wyświetlona Historia inspekcji.

1. Wybierz pozycję **Moje inspekcje**.

1. Przefiltruj historię przy użyciu wstępnie zdefiniowanej daty lub niestandardowego zakresu.

    [![Lista inspekcji dla bieżącego użytkownika](media/azure-pim-resource-rbac/my-audit-time.png "Lista inspekcji dla bieżącego użytkownika")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> Dostęp do historii inspekcji wymaga roli Administrator globalny lub administrator ról uprzywilejowanych.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Pobierz przyczynę, osobę zatwierdzającą i numer biletu dla zdarzeń zatwierdzenia

1. Zaloguj się do [Azure Portal](https://aad.portal.azure.com) z uprawnieniami roli administrator ról uprzywilejowanych i Otwórz usługę Azure AD.
1. Wybierz pozycję **Dzienniki inspekcji**.
1. Użyj filtru **usługi** , aby wyświetlić tylko zdarzenia inspekcji dla usługi Privileged Identity Management. Na stronie **dzienniki inspekcji** można wykonać następujące instrukcje:

    - Zobacz przyczynę zdarzenia inspekcji w kolumnie **Przyczyna stanu** .
    - Zobacz osobę zatwierdzającą w kolumnie **zainicjowane przez (aktor)** dla zdarzenia "Dodaj członka do żądania roli zatwierdzonego".

    [![Filtrowanie dziennika inspekcji usługi PIM](media/azure-pim-resource-rbac/filter-audit-logs.png "Filtrowanie dziennika inspekcji usługi PIM")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Wybierz zdarzenie dziennika inspekcji, aby wyświetlić numer biletu na karcie **działanie** w okienku **szczegółów** .
  
    [![Sprawdź numer biletu dla zdarzenia inspekcji](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Sprawdź numer biletu dla zdarzenia inspekcji")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. Obiekt żądający (osoba, który uaktywnia rolę) można wyświetlić na karcie **targets** okienka **szczegółów** zdarzenia inspekcji. Istnieją trzy typy docelowe dla ról zasobów platformy Azure:

    - Rola (**Typ** = rola)
    - Obiekt żądający (**Typ** = inny)
    - Osoba zatwierdzająca (**Typ** = użytkownik)

    [![Sprawdź typ docelowy](media/azure-pim-resource-rbac/audit-event-target-type.png "Sprawdź typ docelowy")](media/azure-pim-resource-rbac/audit-event-target-type.png)

Zazwyczaj zdarzenie dziennika bezpośrednio powyżej zdarzenia zatwierdzenia jest zdarzeniem dla "Dodaj członka do roli ukończone", gdzie **zainicjowany przez (aktor)** jest obiektem żądającym. W większości przypadków nie trzeba znaleźć żądającego w żądaniu zatwierdzenia z perspektywy inspekcji.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Zatwierdzanie lub odrzucanie żądań dotyczących ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Wyświetlanie historii inspekcji dla ról usługi Azure AD w Privileged Identity Management](pim-how-to-use-audit-log.md)
