---
title: Co to jest Azure Lighthouse?
description: Usługa Azure Lighthouse umożliwia dostawcom usług dostarczanie usług zarządzanych klientom o większej wydajności i skalowalności.
ms.date: 12/16/2020
ms.topic: overview
ms.openlocfilehash: b9215f42e3ad3b7517d14fdaee710a31680ce453
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97617157"
---
# <a name="what-is-azure-lighthouse"></a>Co to jest Azure Lighthouse?

Usługa Azure Lighthouse umożliwia zarządzanie wieloma dzierżawami, zapewniając wyższy poziom automatyzacji i skalowalności oraz ulepszony nadzór nad wieloma zasobami i dzierżawami.

Dzięki usłudze Azure Lighthouse dostawcy usług mogą dostarczać zarządzane usługi przy użyciu kompleksowych i niezawodnych narzędzi do zarządzania wbudowanych w platformę Azure. Klienci zachowują kontrolę nad tym, kto może uzyskać dostęp do swojej dzierżawy, do której zasobów mogą uzyskać dostęp oraz jakie akcje mogą być podejmowane. Ta oferta może również przynieść [organizacjom IT przedsiębiorstwa](concepts/enterprise.md) zarządzanie zasobami w wielu dzierżawach.

![Diagram omówienia usługi Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Korzyści

Usługa Azure Lighthouse pomaga dostawcom usług efektywnie kompilować i dostarczać zarządzane usługi. Korzyści to:

- **Zarządzanie na dużą skalę**: zaangażowanie klientów i operacje związane z cyklem życia w celu zarządzania zasobami klientów są łatwiejsze i bardziej skalowalne. Istniejące interfejsy API, narzędzia do zarządzania i przepływy pracy mogą być używane z delegowanymi zasobami, w tym maszynami hostowanymi poza platformą Azure, niezależnie od regionów, w których się znajdują.
- **Większa widoczność i kontrola dla klientów**: klienci mają precyzyjną kontrolę nad zakresami delegatów dla zarządzania i dozwolonymi uprawnieniami. Mogą oni przeprowadzać inspekcję akcji dostawcy usług i w razie potrzeby usunąć dostęp.
- **Kompleksowe i ujednolicone narzędzia platformy**: nasze środowisko ułatwiające korzystanie z kluczowych scenariuszy dostawców usług, w tym wielu modeli licencjonowania, takich jak EA, CSP i płatność zgodnie z rzeczywistym użyciem. Usługa Azure Lighthouse współpracuje z istniejącymi narzędziami i interfejsami API, modelami licencjonowania, [aplikacjami zarządzanymi platformy Azure](concepts/managed-applications.md)i programami partnerskimi, takimi jak [program Cloud Solution Provider (CSP)](/partner-center/csp-overview). Możesz zintegrować usługę Azure Lighthouse z istniejącymi przepływami pracy i aplikacjami oraz śledzić wpływ na zaangażowanie klientów, [ŁĄCZĄC identyfikator partnera](./how-to/partner-earned-credit.md).

Zarządzanie zasobami platformy Azure nie wiąże się z dodatkowymi kosztami związanymi z korzystaniem z usługi Azure Lighthouse. Wszyscy klienci lub partnerzy platformy Azure mogą korzystać z usługi Azure Lighthouse.

## <a name="capabilities"></a>Możliwości

Usługa Azure Lighthouse obejmuje wiele sposobów usprawniania zaangażowania i zarządzania:

- **Zarządzanie zasobami delegowanymi przez platformę Azure**: [bezpieczne zarządzanie zasobami platformy Azure klientów w ramach własnej dzierżawy](concepts/azure-delegated-resource-management.md)bez konieczności przełączania kontekstu i płaszczyzny kontroli. Subskrypcje i grupy zasobów klientów mogą być delegowane do określonych użytkowników i ról w dzierżawie zarządzającej, z możliwością usuwania dostępu zgodnie z wymaganiami.
- **Nowe Azure Portal środowiska**: Wyświetl informacje o różnych dzierżawach na [stronie **moi klienci**](how-to/view-manage-customers.md) w Azure Portal. Odpowiednia strona [ **dostawcy usług**](how-to/view-manage-service-providers.md) pozwala klientom wyświetlać i zarządzać dostępem dostawcy usług.
- **Szablony Azure Resource Manager**: Użyj szablonów ARM, aby [dołączyć delegowane zasoby klienta](how-to/onboard-customer.md) i [wykonać zadania zarządzania między dzierżawcami](samples/index.md).
- **Oferty usług zarządzanych w witrynie Azure Marketplace**: [oferowanie usług klientom](concepts/managed-services-offers.md) za pomocą ofert prywatnych lub publicznych oraz automatyczne dołączanie ich do usługi Azure Lighthouse.

> [!TIP]
> Podobna oferta [Microsoft 365 Lighthouse](https://techcommunity.microsoft.com/t5/small-and-medium-business-blog/announcing-microsoft-365-lighthouse-for-managed-service/ba-p/1698181)ułatwia partnerom IT dołączenie, monitorowanie i zarządzanie Microsoft 365 klientami w odpowiedniej skali. Microsoft 365 Lighthouse jest obecnie w prywatnej wersji zapoznawczej.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarządzaniu zasobami delegowanymi na platformie Azure](concepts/azure-delegated-resource-management.md).
- Poznaj [środowiska zarządzania między dzierżawcami](concepts/cross-tenant-management-experience.md).
- Zobacz, jak [korzystać z usługi Azure Lighthouse w przedsiębiorstwie](concepts/enterprise.md).
- Wyświetl szczegóły zakresu inspekcji [dostępności](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all) i [FEDRAMP i DoD CC SRG](../azure-government/compliance/azure-services-in-fedramp-auditscope.md) dla usługi Azure Lighthouse.
