---
title: Zarządzanie zasobami delegowanymi na platformie Azure
description: Zarządzanie delegowanymi zasobami platformy Azure jest kluczową częścią usługi Azure Lighthouse, która umożliwia dostawcom usług zarządzanie delegowanymi zasobami na dużą skalę z elastycznością i precyzją.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 93a3de257fe88de4915eff3582ff38fc03ef380e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767767"
---
# <a name="azure-delegated-resource-management"></a>Zarządzanie zasobami delegowanymi na platformie Azure

Zarządzanie delegowanymi zasobami platformy Azure jest jednym z kluczowych składników usługi [Azure Lighthouse](../overview.md). Dzięki zarządzaniu zasobami delegowanymi na platformie Azure dostawcy usług mogą uprościć środowisko angażowania klientów i dołączania, jednocześnie zarządzając delegowanymi zasobami na dużą skalę z elastycznością i precyzją. Klienci utrzymują kontrolę nad tym, kto może uzyskać dostęp do swojej dzierżawy, do jakich zasobów mogą uzyskać dostęp i jakie akcje można podjąć.

## <a name="what-is-azure-delegated-resource-management"></a>Co to jest zarządzanie delegowanymi zasobami platformy Azure?

Zarządzanie delegowanymi zasobami platformy Azure umożliwia logiczne projekcje zasobów z jednej dzierżawy do innej dzierżawy. Umożliwia to autoryzowanym użytkownikom w jednej dzierżawie Azure Active Directory (Azure AD) wykonywanie operacji zarządzania w różnych dzierżawach usługi Azure AD należących do ich klientów. Dostawcy usług mogą logować się do własnej dzierżawy usługi Azure AD i mieć autoryzację do pracy w delegowanych subskrypcjach klientów i grupach zasobów. Dzięki temu mogą wykonywać operacje zarządzania w imieniu swoich klientów bez konieczności logowania się do poszczególnych dzierżaw klientów.

> [!TIP]
> Delegowane zarządzanie zasobami platformy Azure może być również używane w przedsiębiorstwie, które ma wiele własnych dzierżaw usługi [Azure AD,](enterprise.md) aby uprościć zarządzanie wieloma dzierżawami.

Dzięki zarządzaniu zasobami delegowanymi na platformie Azure autoryzowani użytkownicy mogą pracować bezpośrednio w kontekście subskrypcji klienta bez konieczności posiadania konta w dzierżawie tego klienta lub współwłaścicieli dzierżawy klienta.

Środowisko [zarządzania między dzierżawami](cross-tenant-management-experience.md) umożliwia wydajniejszą pracę z usługami zarządzania platformy Azure, Azure Policy, Azure Security Center i nie tylko. Wszystkie działania dostawcy usług są śledzone w dzienniku aktywności, który jest przechowywany w dzierżawie klienta (i może być przeglądany przez użytkowników w dzierżawie zarządzającej). Oznacza to, że użytkownicy w dzierżawie zarządzającej i zarządzanej mogą łatwo zidentyfikować użytkownika skojarzonego ze zmianami.

Możesz [opublikować nowy typ oferty Usługi zarządzanej w usłudze Azure Marketplace,](../how-to/publish-managed-services-offers.md) aby łatwo dołączać klientów do Azure Lighthouse. Możesz też ukończyć [proces dołączania, wdrażając Azure Resource Manager szablonów.](../how-to/onboard-customer.md)

## <a name="how-azure-delegated-resource-management-works"></a>Jak działa zarządzanie delegowanymi zasobami platformy Azure

Na wysokim poziomie w ten sposób działa zarządzanie delegowanymi zasobami platformy Azure:

1. Najpierw należy zidentyfikować dostęp (role), który będzie potrzebny grupom, jednostkom usługi lub użytkownikom do zarządzania zasobami platformy Azure klienta. Definicja dostępu zawiera identyfikator dzierżawy zarządzania oraz tożsamości **principalId** z dzierżawy mapowane na wbudowane wartości [ **roleDefinition**](../../role-based-access-control/built-in-roles.md) (Współautor, Współautor maszyny wirtualnej, Czytelnik itp.).
2. Ten dostęp należy określić i doćć klienta do Azure Lighthouse na jeden z dwóch sposobów:
   - [Publikowanie Azure Marketplace usługi zarządzanej](../how-to/publish-managed-services-offers.md) (prywatnej lub publicznej), która zostanie zaakceptowana przez klienta
   - [Wdrażanie szablonu Azure Resource Manager dzierżawie](../how-to/onboard-customer.md) klienta dla co najmniej jednej konkretnej subskrypcji lub grupy zasobów

3. Po dojech z klientem autoryzowani użytkownicy mogą zalogować się do dzierżawy zarządzającej i wykonywać zadania w danym zakresie klienta na podstawie zdefiniowanego dostępu. Klienci mogą przeglądać akcje dostawcy usług i w razie potrzeby mogą usunąć dostęp.

> [!NOTE]
> Możesz zarządzać delegowanymi zasobami, które znajdują się w różnych [regionach.](../../availability-zones/az-overview.md#regions) Delegowanie subskrypcji w chmurze krajowej i chmurze publicznej platformy Azure lub w dwóch oddzielnych chmurach krajowych nie jest jednak obsługiwane. [](../../active-directory/develop/authentication-national-cloud.md)

## <a name="support-for-azure-delegated-resource-management"></a>Obsługa zarządzania zasobami delegowanymi na platformie Azure

Jeśli potrzebujesz pomocy dotyczącej zarządzania delegowanymi zasobami platformy Azure, możesz otworzyć żądanie pomocy technicznej w Azure Portal. W **przypadku typu problemu** wybierz pozycję **Techniczny.** Wybierz subskrypcję, a następnie wybierz pozycję **Lighthouse** (w **obszarze Monitorowanie & Management).**

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat obsługi zarządzania między dzierżawami.](cross-tenant-management-experience.md)
- Dowiedz się więcej [o ofertach usług zarządzanych w Azure Marketplace](managed-services-offers.md).
