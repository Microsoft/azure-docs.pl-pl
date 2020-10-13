---
title: Wyświetlanie raportu dziennika inspekcji dla ról usługi Azure AD w usłudze Azure AD PIM | Microsoft Docs
description: Dowiedz się, jak wyświetlić historię dziennika inspekcji dla ról usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe2f464791995b9168868f1661a3c303d46f7987
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530123"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Wyświetlanie historii inspekcji dla ról usługi Azure AD w Privileged Identity Management

Możesz użyć historii inspekcji Privileged Identity Management (PIM), aby zobaczyć wszystkie przypisania ról i aktywacje w ciągu ostatnich 30 dni dla wszystkich ról uprzywilejowanych. Jeśli chcesz zobaczyć pełną historię inspekcji działania w organizacji usługi Azure Active Directory (Azure AD), w tym administratora, użytkownika końcowego i działania synchronizacji, możesz użyć [raportów zabezpieczeń i działań Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Określanie używanej wersji programu PIM

Od listopada 2019 część Privileged Identity Management ról usługi Azure AD jest aktualizowana do nowej wersji, która jest zgodna z środowiskami dla ról zasobów platformy Azure. Spowoduje to utworzenie dodatkowych funkcji, a także [zmian w istniejącym interfejsie API](azure-ad-roles-features.md#api-changes). Gdy nowa wersja jest wdrażana, procedury, które należy wykonać w tym artykule, zależą od aktualnie posiadanej wersji Privileged Identity Management. Wykonaj kroki opisane w tej sekcji, aby określić, która wersja Privileged Identity Management masz. Po uzyskaniu informacji o wersji Privileged Identity Management możesz wybrać procedury w tym artykule, które pasują do tej wersji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który znajduje się w roli [administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Otwórz **Azure AD Privileged Identity Management**. Jeśli masz baner w górnej części strony przegląd, postępuj zgodnie z instrukcjami wyświetlanymi na karcie **Nowa wersja** tego artykułu. W przeciwnym razie postępuj zgodnie z instrukcjami podanymi w **poprzedniej wersji** karty.

    [![Zrzut ekranu przedstawiający stronę "role usługi Azure AD — Historia inspekcji ról katalogu".](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Wybierz kartę dla swojej wersji")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Nowa wersja](#tab/new)

Wykonaj następujące kroki, aby wyświetlić historię inspekcji dla ról usługi Azure AD.

## <a name="view-resource-audit-history"></a>Wyświetl historię inspekcji zasobów

Inspekcja zasobów umożliwia wyświetlenie wszystkich działań skojarzonych z rolami usługi Azure AD.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz pozycję **Inspekcja zasobów**.

1. Przefiltruj historię przy użyciu wstępnie zdefiniowanej daty lub niestandardowego zakresu.

    ![Lista inspekcji zasobów z filtrami](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Wyświetl moją inspekcję

Moja Inspekcja umożliwia wyświetlenie własnej aktywności roli użytkownika.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz zasób, dla którego ma zostać wyświetlona Historia inspekcji.

1. Wybierz pozycję **Moje inspekcje**.

1. Przefiltruj historię przy użyciu wstępnie zdefiniowanej daty lub niestandardowego zakresu.

    ![Lista inspekcji dla bieżącego użytkownika](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

## <a name="view-audit-history"></a>Wyświetlanie historii inspekcji

Wykonaj następujące kroki, aby wyświetlić historię inspekcji dla ról usługi Azure AD.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który jest członkiem roli [administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz opcję **Historia inspekcji ról katalogu**.

    W zależności od historii inspekcji zostanie wyświetlony wykres kolumnowy wraz z łączną liczbą aktywacji, maksymalną liczbą aktywacji dziennie i średnimi aktywacje dziennie.

    [![Nowa wersja ról usługi Azure AD](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Wyświetl historię inspekcji ról katalogu")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    W dolnej części strony zostanie wyświetlona tabela z informacjami o każdej akcji w dostępnej historii inspekcji. Kolumny mają następujące znaczenie:

    | Kolumna | Opis |
    | --- | --- |
    | Godzina | Gdy wystąpiła akcja. |
    | Requestor | Użytkownik, który zażądał aktywacji lub zmiany roli. Jeśli wartość to **Azure system**, sprawdź historię inspekcji platformy Azure, aby uzyskać więcej informacji. |
    | Akcja | Akcje podejmowane przez zleceniodawcę. Akcje mogą obejmować Przypisywanie, cofanie przypisania, aktywowanie, dezaktywowanie lub AddedOutsidePIM. |
    | Członek | Użytkownik, który jest uaktywniany lub przypisany do roli. |
    | Rola | Rola przypisana lub aktywowana przez użytkownika. |
    | Rozsądkiem | Tekst wprowadzony w polu przyczyna podczas aktywacji. |
    | Wygaśnięcie | Po wygaśnięciu aktywowanej roli. Dotyczy tylko kwalifikujących się przypisań ról. |

1. Aby posortować historię inspekcji, kliknij przycisk **godziny**, **akcję**i **rola** .

## <a name="filter-audit-history"></a>Filtruj historię inspekcji

1. W górnej części strony historia inspekcji kliknij przycisk **Filtruj** .

    Zostanie wyświetlone okienko **Aktualizuj parametry wykresu** .

1. W obszarze **zakres czasu**wybierz zakres czasu.

1. W obszarze **role**zaznacz pola wyboru, aby wskazać role, które chcesz wyświetlić.

    ![Okienko aktualizowanie parametrów wykresu](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Wybierz pozycję **gotowe** , aby wyświetlić przefiltrowaną historię inspekcji.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Pobierz przyczynę, osobę zatwierdzającą i numer biletu dla zdarzeń zatwierdzenia

1. Zaloguj się do [Azure Portal](https://aad.portal.azure.com) z uprawnieniami roli administrator ról uprzywilejowanych i Otwórz usługę Azure AD.
1. Wybierz pozycję **Dzienniki inspekcji**.
1. Użyj filtru **usługi** , aby wyświetlić tylko zdarzenia inspekcji dla usługi Privileged Identity Management. Na stronie **dzienniki inspekcji** można wykonać następujące instrukcje:

    - Zobacz przyczynę zdarzenia inspekcji w kolumnie **Przyczyna stanu** .
    - Zobacz osobę zatwierdzającą w kolumnie **zainicjowane przez (aktor)** dla zdarzenia "Dodaj członka do żądania roli zatwierdzonego".

    [![Zrzut ekranu przedstawiający stronę "dzienniki inspekcji" z wybranym menu "zainicjowane przez (aktor)" otwartego i "PIM".](media/pim-how-to-use-audit-log/filter-audit-logs.png "Filtrowanie dziennika inspekcji usługi PIM")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Wybierz zdarzenie dziennika inspekcji, aby wyświetlić numer biletu na karcie **działanie** w okienku **szczegółów** .
  
    [![Zrzut ekranu przedstawiający stronę "dzienniki inspekcji" z numerem biletu wyróżnionym w okienku "Szczegóły".](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Sprawdź numer biletu dla zdarzenia inspekcji")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. Obiekt żądający (osoba, który uaktywnia rolę) można wyświetlić na karcie **targets** okienka **szczegółów** zdarzenia inspekcji. Istnieją dwa typy docelowe dla ról usługi Azure AD:

    - Rola (**Typ** = rola)
    - Obiekt żądający (**Typ** = użytkownik)

Zazwyczaj zdarzenie dziennika inspekcji bezpośrednio powyżej zdarzenia zatwierdzenia jest zdarzeniem dla "Dodaj członka do roli ukończone", gdzie **zainicjowany przez (aktor)** jest zleceniodawcą. W większości przypadków nie trzeba znaleźć żądającego w żądaniu zatwierdzenia z perspektywy inspekcji.

---

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie historii działań i inspekcji dla ról zasobów platformy Azure w Privileged Identity Management](azure-pim-resource-rbac.md)
