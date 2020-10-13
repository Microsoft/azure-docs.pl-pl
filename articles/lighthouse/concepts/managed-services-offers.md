---
title: Oferty usługi zarządzanej w witrynie Azure Marketplace
description: Oferty usługi zarządzanej umożliwiają sprzedawanie ofert zarządzania zasobami klientom w portalu Azure Marketplace.
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 6c3047cd95128f689e75d9c1f5fba5a39f86291c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163326"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Oferty usługi zarządzanej w witrynie Azure Marketplace

W tym artykule opisano typ oferty **usługi zarządzanej** w [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com). Oferty usługi zarządzanej umożliwiają oferowanie klientom usług zarządzania zasobami za pomocą [usługi Azure Lighthouse](../overview.md). Oferty te można udostępnić wszystkim potencjalnym klientom lub tylko jednemu lub wielu konkretnym klientom. Ze względu na to, że opłaty są naliczane bezpośrednio za koszty związane z tymi usługami zarządzanymi, firma Microsoft nie pobiera żadnych opłat.

## <a name="understand-managed-service-offers"></a>Opis ofert usług zarządzanych

Oferty usługi zarządzanej usprawniają proces dołączania klientów do usługi Azure Lighthouse. Gdy klient kupuje ofertę w portalu Azure Marketplace, będzie mógł określić, które subskrypcje i/lub grupy zasobów powinny zostać dołączone.

Następnie użytkownicy w organizacji będą mogli korzystać z tych zasobów z dzierżawy zarządzającej za pośrednictwem [zarządzania zasobami delegowanymi przez platformę Azure](azure-delegated-resource-management.md), zgodnie z dostępem zdefiniowanym podczas tworzenia oferty. Jest to realizowane za pomocą manifestu, który określa użytkowników, grupy i jednostki usługi Azure Active Directory (Azure AD), które będą miały dostęp do zasobów klienta, wraz z rolami, które definiują poziom dostępu. Przypisując uprawnienia do grupy usługi Azure AD, a nie serii poszczególnych kont użytkowników lub aplikacji, można dodawać lub usuwać poszczególnych użytkowników, gdy zmienią się wymagania dotyczące dostępu.

## <a name="public-and-private-offers"></a>Oferty publiczne i prywatne

Każda oferta usług zarządzanych obejmuje jeden lub więcej planów. Plany mogą być prywatne lub publiczne.

Jeśli chcesz ograniczyć ofertę do określonych klientów, możesz opublikować plan prywatny. Po wykonaniu tej czynności plan można kupić tylko dla określonych identyfikatorów subskrypcji, które podano. Aby uzyskać więcej informacji, zobacz [prywatne oferty](../../marketplace/private-offers.md).

> [!NOTE]
> Oferty prywatne nie są obsługiwane w przypadku subskrypcji ustanowionych przez odsprzedawcę programu w programie Cloud Solution Provider (CSP).

Plany publiczne umożliwiają podwyższenie poziomu usług do nowych klientów. Są one zazwyczaj bardziej odpowiednie, gdy wymagany jest tylko ograniczony dostęp do dzierżawy klienta. Po ustanowieniu relacji z klientem, jeśli zdecydują się udzielić dodatkowej dostępu do organizacji, możesz to zrobić przez opublikowanie nowego planu prywatnego tylko dla tego klienta lub przez dołączenie [ich do dodatkowego dostępu przy użyciu szablonów Azure Resource Manager](../how-to/onboard-customer.md).

W razie potrzeby można uwzględnić zarówno plany publiczne, jak i prywatne w tej samej ofercie.

> [!IMPORTANT]
> Po opublikowaniu planu jako publicznego nie można go zmienić na prywatny. Aby kontrolować, którzy klienci mogą akceptować ofertę i delegować zasoby, należy użyć planu prywatnego. W przypadku planu publicznego nie można ograniczyć dostępności do określonych klientów, a nawet do określonej liczby klientów (mimo że można już zrezygnować z sprzedaży planu w przypadku wybrania tej opcji). [Dostęp do delegowania można usunąć](../how-to/remove-delegation.md) , gdy klient zaakceptuje ofertę tylko wtedy, gdy została uwzględniona **autoryzacja** z **definicją roli** ustawioną na [przypisanie rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) , po opublikowaniu oferty. Możesz również skontaktować się z klientem i poproś o [usunięcie dostępu](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publikuj oferty usługi zarządzanej

Aby dowiedzieć się, jak opublikować ofertę usług zarządzanych, zobacz temat [Publikowanie oferty usług zarządzanych w witrynie Azure Marketplace](../how-to/publish-managed-services-offers.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat funkcji [zarządzania zasobami delegowanymi przez platformę Azure](azure-delegated-resource-management.md) i [zarządzania wieloma dzierżawcami](cross-tenant-management-experience.md).
- [Publikowanie usług zarządzanych jest dostępne](../how-to/publish-managed-services-offers.md) w witrynie Azure Marketplace.
