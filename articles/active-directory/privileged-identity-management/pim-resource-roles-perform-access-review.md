---
title: Przeglądanie dostępu do ról zasobów platformy Azure w usłudze PIM — Azure AD | Microsoft Docs
description: Dowiedz się, jak przeglądać dostęp do ról zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8091890d174ae68f012a6ec24685a0e705100f8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743681"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Przejrzyj dostęp do ról zasobów platformy Azure w Privileged Identity Management

Przeglądy dostępu Privileged Identity Management (PIM) mogą pomóc w zabezpieczeniu dostępu do ról uprzywilejowanych w usłudze Azure Active Directory (Azure AD). W tym artykule opisano kroki, które należy wykonać, aby zapoznać się z przypisaniami ról uprzywilejowanych w przeglądzie dostępu do usługi Azure AD.

Jeśli masz przypisaną rolę administracyjną, może być konieczne ukończenie przeglądu dostępu przez administratora, aby potwierdzić potrzebę roli. Żądanie potwierdzenia może zawierać wiadomość e-mail zawierającą link lub można potwierdzić w [Azure Portal](https://portal.azure.com).

Jeśli jesteś administratorem roli uprzywilejowanej interesu w przeglądach dostępu, uzyskaj więcej szczegółowych informacji na [temat sposobu uruchamiania przeglądu dostępu](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Zatwierdź lub Odmów dostępu

Możesz zatwierdzić lub odmówić dostępu na podstawie tego, czy nadal używasz tej roli, czy nie. Wybierz pozycję **Zatwierdź** , jeśli chcesz pozostać w roli, lub **Odmów** , jeśli nie potrzebujesz już dostępu. Stan zmieni się tylko wtedy, gdy recenzent zastosuje wyniki.

Wykonaj następujące kroki, aby znaleźć i ukończyć przegląd dostępu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz **Azure Active Directory** i Otwórz **Privileged Identity Management**.
1. Wybierz pozycję **Przejrzyj dostęp**.

   ![Zrzut ekranu aplikacji Privileged Identity Management z wybranym blokiem przegląd dostępu](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Wybierz przegląd, który chcesz zakończyć.
1. Wybierz pozycję **Zatwierdź** lub **Odmów**. W **polu podaj przyczynę**wprowadź uzasadnienie biznesowe dla swojej decyzji w razie potrzeby.

   ![Zrzut ekranu przedstawiający stronę szczegółów przeglądu](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Następne kroki

- [Przeprowadź przegląd dostępu do ról usługi Azure AD w Privileged Identity Management](pim-how-to-perform-security-review.md)
