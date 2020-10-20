---
author: baanders
description: plik dołączany dla wymagania wstępnego dotyczącego uprawnień w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205498"
---
## <a name="prerequisites-permission-requirements"></a>Warunki wstępne: wymagania dotyczące uprawnień

Aby można było wykonać wszystkie kroki opisane w tym artykule, musisz mieć [rolę w subskrypcji](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) , która ma następujące uprawnienia:
* Tworzenie zasobów platformy Azure i zarządzanie nimi
* Zarządzanie dostępem użytkowników do zasobów platformy Azure (w tym przyznawanie i delegowanie uprawnień)

Typowe role spełniające to wymaganie są *właścicielami*, *administratorami kont*lub kombinacją administratorów i *współautorów* *dostępu użytkowników* . Aby zapoznać się ze szczegółowymi informacjami o rolach i uprawnieniach, w tym o uprawnieniach dołączanych do innych ról, zobacz [*klasyczne role administratora subskrypcji, role platformy Azure i role usługi Azure AD*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) w dokumentacji usługi Azure RBAC.

Aby wyświetlić swoją rolę w subskrypcji, odwiedź [stronę subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w Azure Portal (możesz użyć tego linku lub wyszukać *subskrypcje* na pasku wyszukiwania portalu). Poszukaj nazwy używanej subskrypcji i Wyświetl jej rolę w kolumnie *My role* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Widok strony subskrypcji w Azure Portal, który pokazuje użytkownika jako właściciela" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Jeśli okaże się, że wartość jest *współautorem*lub inną rolą, która nie ma wymaganych uprawnień, można skontaktować się z użytkownikiem w ramach subskrypcji, *która ma* te uprawnienia (na przykład właściciela subskrypcji lub administratora konta) i wykonać jedną z następujących czynności:
* Zażądaj, aby wykonali kroki opisane w tym artykule w Twoim imieniu
* Poproś o podwyższenie poziomu roli w ramach subskrypcji, aby mieć uprawnienia do samodzielnego wykonywania tych działań. Niezależnie od tego, czy jest to odpowiednie, zależy od organizacji i Twojej roli.