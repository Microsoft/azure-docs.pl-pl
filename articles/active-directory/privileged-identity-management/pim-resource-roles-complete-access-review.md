---
title: Kończenie przeglądu dostępu do ról zasobów platformy Azure w usłudze PIM — Azure AD | Microsoft Docs
description: Dowiedz się, jak wykonać przegląd dostępu do ról zasobów platformy Azure Privileged Identity Management w Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1601c9fc750df1f8e18bdaa072ede1b5ee164329
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84743817"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Dokończ przegląd dostępu ról zasobów platformy Azure w Privileged Identity Management

Administratorzy ról uprzywilejowanych mogą przeglądać dostęp uprzywilejowany po [rozpoczęciu przeglądu dostępu](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) w usłudze Azure Active Directory (Azure AD) automatycznie wysyła wiadomość e-mail z prośbą o przejrzenie dostępu przez użytkowników. Jeśli użytkownik nie otrzyma wiadomości e-mail, można wysłać im instrukcje dotyczące [sposobu przeprowadzania przeglądu dostępu](pim-resource-roles-perform-access-review.md).

Po przekroczeniu okresu przeglądu dostępu lub po zakończeniu samooceny przez wszystkich użytkowników wykonaj kroki opisane w tym artykule, aby zarządzać przeglądem i zobaczyć wyniki.

## <a name="manage-access-reviews"></a>Zarządzanie recenzjami dostępu

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). Na pulpicie nawigacyjnym wybierz usługę **Azure Resources** .

2. Wybierz zasób.

3. Wybierz sekcję **przeglądy dostępu** na pulpicie nawigacyjnym.

    ![Zasoby platformy Azure — lista przeglądów dostępu pokazująca rolę, właściciela, datę rozpoczęcia, datę zakończenia i stan](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Wybierz przegląd dostępu, który chcesz zarządzać.

Na stronie szczegółów przeglądu dostępu dostępne są różne opcje zarządzania tym przeglądem. Dostępne są następujące opcje:

![Opcje zarządzania przeglądem — zatrzymywanie, resetowanie, stosowanie, usuwanie](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stop

Wszystkie przeglądy dostępu mają datę zakończenia. Wybierz pozycję **Zatrzymaj** , aby zakończyć ją wczesnie. Wszyscy użytkownicy, którzy nie ukończyli recenzji przez ten czas, nie będą mogli jej zakończyć po zatrzymaniu przeglądu. Po zatrzymaniu nie można ponownie uruchomić recenzji.

### <a name="reset"></a>Reset

Możesz zresetować przegląd dostępu, aby usunąć wszystkie podejmowane decyzje. Po zresetowaniu przeglądu dostępu wszyscy użytkownicy są oznaczeni jako Nieprzejrzane ponownie.

### <a name="apply"></a>Zastosuj

Po zakończeniu przeglądu dostępu wybierz pozycję **Zastosuj** , aby zaimplementować wynik przeglądu. W przypadku odmowy dostępu użytkownika do przeglądu ten krok spowoduje usunięcie przypisania roli.  

### <a name="delete"></a>Usuń

Jeśli nie chcesz dowiedzieć się więcej, usuń je. Wybierz pozycję **Usuń** yo Usuń przegląd z usługi Privileged Identity Management.

## <a name="results"></a>Wyniki

Na stronie **wyniki** Wyświetl i Pobierz listę wyników przeglądu.

![Strona wyników z listą użytkowników, wyników, przyczyn, zrecenzowanych przez, stosowanych przez i Zastosuj wynik](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Weryfikatorzy

Wyświetlanie i Dodawanie recenzentów do istniejących przeglądów dostępu. Przypomnij recenzentom, aby dokończyli swoje przeglądy.

![Strona recenzentów nazwa i nazwa główna użytkownika](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Następne kroki

- [Rozpocznij przegląd dostępu dla ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-start-access-review.md)
- [Przeprowadź przegląd dostępu ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-perform-access-review.md)
