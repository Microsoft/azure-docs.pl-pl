---
title: Przenoszenie subskrypcji ujętych w planie platformy Azure między partnerami (wersja zapoznawcza)
description: W tym artykule zawarto informacje dotyczące przenoszenia własności rozliczeń subskrypcji platformy Azure.
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.openlocfilehash: d234227de98beeed6898697fb6ed8e0865546e16
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555844"
---
# <a name="transfer-subscriptions-under-an-azure-plan-from-one-partner-to-another-preview"></a>Przenoszenie subskrypcji ujętych w planie platformy Azure między partnerami (wersja zapoznawcza)

W tym artykule zawarto informacje dotyczące przenoszenia własności rozliczeń subskrypcji platformy Azure. Aby rozpocząć przenoszenie subskrypcji platformy Azure ujętej w planie platformy Azure pomiędzy partnerami firmy Microsoft, musisz skontaktować się z partnerem. Od partnera dostaniesz instrukcje dotyczące rozpoczęcia tego procesu. Po zakończeniu procesu przenoszenia własność rozliczeń subskrypcji zostanie zmieniona.

## <a name="user-access"></a>Dostęp użytkowników

Przejście nie wpływa na dostęp do istniejących użytkowników, grup lub jednostek usługi, które zostały przypisane przy użyciu kontroli dostępu na podstawie ról (RBAC) platformy Azure. [Kontrola dostępu na podstawie ról na platformie Azure](../../role-based-access-control/overview.md) ułatwia zarządzanie osobami mającymi dostęp do zasobów platformy Azure, czynnościami, jakie mogą wykonywać, oraz obszarami, do których mają dostęp. Przeniesienie subskrypcji nie oznacza, że nowy partner otrzymuje dostęp RBAC platformy Azure do Twoich zasobów. Poprzedni partner zatrzymuje dostęp RBAC platformy Azure.

W związku z tym musisz usunąć dostęp RBAC starego partnera do platformy Azure i nadać dostęp nowemu partnerowi. Aby uzyskać więcej informacji na temat nadawania dostępu nowemu partnerowi, zobacz [Co to jest kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)?](../../role-based-access-control/overview.md) Aby uzyskać więcej informacji na temat usuwania dostępu z usługi Azure RBAC przez poprzedni partner, zobacz [usuwanie przypisań ról platformy Azure](../../role-based-access-control/role-assignments-remove.md).

Ponadto nowy partner nie otrzymuje automatycznie dostępu do subskrypcji w ramach funkcji [Administruj w imieniu (Admin on Behalf Of, AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO). Funkcja AOBO jest niezbędna, aby nowy partner mógł zarządzać subskrypcjami platformy Azure w Twoim imieniu. Aby uzyskać więcej informacji na temat uprawnień platformy Azure, zobacz [Uzyskiwanie uprawnień do zarządzania usługą lub subskrypcją klienta](/partner-center/customers-revoke-admin-privileges).

## <a name="stop-a-transfer"></a>Zatrzymywanie przenoszenia

Po otrzymaniu potwierdzenia, że żądanie przeniesienia zostało złożone, możesz użyć jednej z następujących opcji, **jeśli nie chcesz, aby przenoszenie było kontynuowane**.

- Jeśli żądanie nie zostało jeszcze zaakceptowane przez Twojego bieżącego partnera, możesz poprosić nowego partnera o anulowanie rozpoczętego żądania przeniesienia (gdy ma stan „oczekujące”).
- Poproś bieżącego partnera, aby nie reagował po otrzymaniu żądania przeniesienia. Przeniesienie nie może być kontynuowane bez zgody bieżącego partnera. Żądanie wygaśnie.
- Możesz _usunąć relację odsprzedawcy_ ze swoim nowym partnerem. To działanie usuwa możliwość przenoszenia subskrypcji. W rezultacie powoduje anulowania żądania.

Możesz również poszukać pomocy, a także zgłosić nieprawidłowe lub podejrzane działanie za pomocą jednej z opcji opisanych w witrynie [Microsoft Legal](https://www.microsoft.com/legal/). Opcję zgłoszenia problemu można znaleźć w sekcji Compliance & ethics (Zgodność i etyka).

## <a name="next-steps"></a>Następne kroki

- Aby nadać dostęp RBAC platformy Azure nowemu partnerowi, zobacz [Co to jest kontrola dostępu na podstawie ról (RBAC) platformy Azure?](../../role-based-access-control/overview.md)
- [Uzyskiwanie uprawnień do zarządzania usługą lub subskrypcją klientów](/partner-center/customers-revoke-admin-privileges).