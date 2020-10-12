---
title: Zarządzanie zasobami delegowanymi na platformie Azure
description: Zarządzanie zasobami delegowanymi przez platformę Azure to kluczowa część usługi Azure Lighthouse, która umożliwia dostawcom usług Zarządzanie delegowanymi zasobami na dużą skalę z elastycznością i dokładnością.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 9a499ceda546b7ea5c71cd8c770f1a4b99001b08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163530"
---
# <a name="azure-delegated-resource-management"></a>Zarządzanie zasobami delegowanymi na platformie Azure

Zarządzanie zasobami delegowanymi przez platformę Azure to jeden z najważniejszych składników [usługi Azure Lighthouse](../overview.md). Dzięki funkcji zarządzania zasobami delegowanymi na platformie Azure dostawcy usług mogą uprościć zaangażowanie klientów i dołączanie, a jednocześnie zarządzać zasobami delegowanymi na dużą skalę z elastycznością i dokładnością.

## <a name="what-is-azure-delegated-resource-management"></a>Co to jest zarządzanie zasobami delegowanymi przez platformę Azure?

Zarządzanie zasobami delegowanymi przez platformę Azure umożliwia logiczne rzutowanie zasobów z jednej dzierżawy na inną dzierżawcę. Umożliwia to autoryzowanym użytkownikom w jednej Azure Active Directory (Azure AD) wykonywanie operacji zarządzania w różnych dzierżawach usługi Azure AD należących do klientów. Dostawcy usług mogą zalogować się do swojej dzierżawy usługi Azure AD i mieć autoryzację do pracy w delegowanych subskrypcjach klientów i grupach zasobów. Pozwala to na wykonywanie operacji zarządzania w imieniu swoich klientów, bez konieczności logowania się do poszczególnych dzierżaw klientów.

> [!TIP]
> Zarządzanie zasobami delegowanymi przez platformę Azure może być również używane [w przedsiębiorstwie, które ma wiele dzierżawców usługi Azure AD](enterprise.md) , aby uprościć zarządzanie między dzierżawcami.

Dzięki funkcji zarządzania zasobami delegowanymi na platformie Azure autoryzowani użytkownicy mogą współpracować bezpośrednio w kontekście subskrypcji klienta bez konieczności używania konta w dzierżawie klienta lub współwłaścicielem dzierżawy klienta.

[Środowisko zarządzania przez wiele dzierżawców](cross-tenant-management-experience.md) pozwala wydajniej współpracować z usługami zarządzania platformy Azure, takimi jak Azure Policy, Azure Security Center i wiele innych. Wszystkie działania dostawcy usług są śledzone w dzienniku aktywności, który jest przechowywany w dzierżawie klienta (i może być przeglądany przez użytkowników w dzierżawie zarządzającej). Oznacza to, że użytkownicy zarówno w ramach zarządzania, jak i zarządzanej dzierżawy mogą łatwo identyfikować użytkownika skojarzonego ze wszystkimi zmianami.

[Nowy typ oferty usługi zarządzanej można opublikować w witrynie Azure Marketplace](../how-to/publish-managed-services-offers.md) , aby łatwo dołączać klientów do usługi Azure Lighthouse. Możesz również [ukończyć proces dołączania, wdrażając szablony Azure Resource Manager](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Jak działa zarządzanie zasobami delegowanymi przez platformę Azure

Na wysokim poziomie poniżej przedstawiono sposób działania zarządzania zasobami delegowanymi przez platformę Azure:

1. Najpierw należy zidentyfikować dostęp (role), których grupy, nazwy główne usług lub użytkownicy będą musieli zarządzać zasobami platformy Azure klienta. Definicja dostępu zawiera identyfikator dzierżawy zarządzania oraz tożsamości **principalId** z dzierżawy zamapowane do [wbudowanych wartości **definicji** ](../../role-based-access-control/built-in-roles.md) (współautor, współautor maszyny wirtualnej, czytelnik itp.).
2. Należy określić ten dostęp i dołączyć klienta do usługi Azure Lighthouse na jeden z dwóch sposobów:
   - [Publikowanie oferty usługi zarządzanej w portalu Azure Marketplace](../how-to/publish-managed-services-offers.md) (prywatnej lub publicznej), która zostanie zaakceptowana przez klienta
   - [Wdrażanie szablonu Azure Resource Manager w dzierżawie klienta](../how-to/onboard-customer.md) dla jednej lub wielu określonych subskrypcji lub grup zasobów
3. Po dołączeniu klienta autoryzowani użytkownicy mogą zalogować się do dzierżawy zarządzającej i wykonywać zadania w danym zakresie klienta na podstawie zdefiniowanego dostępu.

> [!NOTE]
> Można zarządzać delegowanymi zasobami, które znajdują się w różnych [regionach](../../availability-zones/az-overview.md#regions). Jednak delegowanie subskrypcji w [chmurze krajowej](../../active-directory/develop/authentication-national-cloud.md) oraz w chmurze publicznej platformy Azure lub w dwóch oddzielnych chmurach narodowych nie jest obsługiwane.

## <a name="support-for-azure-delegated-resource-management"></a>Obsługa zarządzania zasobami delegowanymi przez platformę Azure

Jeśli potrzebujesz pomocy związanej z zarządzaniem zasobami delegowanymi przez platformę Azure, możesz otworzyć żądanie pomocy technicznej w Azure Portal. W obszarze **typ problemu**wybierz pozycję **techniczne**. Wybierz subskrypcję, a następnie wybierz pozycję **Lighthouse** (w obszarze **monitorowanie & zarządzanie**).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](cross-tenant-management-experience.md).
- Dowiedz się więcej o [ofertach usług zarządzanych w witrynie Azure Marketplace](managed-services-offers.md).
