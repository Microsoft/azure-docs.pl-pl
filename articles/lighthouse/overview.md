---
title: Co to jest Azure Lighthouse?
description: Usługa Azure Lighthouse umożliwia dostawcom usług dostarczanie usług zarządzanych klientom o większej wydajności i skalowalności.
ms.date: 08/19/2020
ms.topic: overview
ms.openlocfilehash: 41fa836321da7cdb42b9a3419bc0bb32963a5849
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017867"
---
# <a name="what-is-azure-lighthouse"></a>Co to jest Azure Lighthouse?

Usługa Azure Lighthouse umożliwia zarządzanie wieloma dzierżawami, zapewniając wyższy poziom automatyzacji i skalowalności oraz ulepszony nadzór nad wieloma zasobami i dzierżawami. Dzięki usłudze Azure Lighthouse dostawcy usług mogą dostarczać zarządzane usługi przy użyciu kompleksowych i niezawodnych narzędzi do zarządzania wbudowanych w platformę Azure. Ta oferta może również przynieść organizacjom IT przedsiębiorstwa zarządzanie zasobami w wielu dzierżawach.

![Diagram omówienia usługi Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Korzyści

Usługa Azure Lighthouse pozwala na opłacalną i wydajną kompilację i dostarczanie usług zarządzanych. Korzyści:

- **Zarządzanie na dużą skalę**: zaangażowanie klientów i operacje związane z cyklem życia w celu zarządzania zasobami klientów są łatwiejsze i bardziej skalowalne. Istniejące interfejsy API, narzędzia do zarządzania i przepływy pracy mogą być używane z delegowanymi zasobami, niezależnie od regionów, w których się znajdują.
- **Większa widoczność i precyzja dla klientów**: klienci będą mieli lepszy wgląd w swoje działania i precyzyjną kontrolę nad zakresem delegowanym do zarządzania, w tym możliwość całkowitego usuwania dostępu, gdy adres IP jest zachowywany.
- **Kompleksowe i ujednolicone narzędzia platformy**: nasze środowisko ułatwiające korzystanie z kluczowych scenariuszy dostawców usług, w tym wielu modeli licencjonowania, takich jak EA, CSP i płatność zgodnie z rzeczywistym użyciem. Nowe możliwości działają z istniejącymi narzędziami i interfejsami API, modelami licencjonowania i programami partnerskimi, takimi jak [program Cloud Solution Provider (CSP)](/partner-center/csp-overview). Usługę Azure Lighthouse można zintegrować z istniejącymi przepływami pracy i aplikacjami. możesz śledzić wpływ na zaangażowanie klientów, [ŁĄCZĄC identyfikator partnera](./how-to/partner-earned-credit.md).

Zarządzanie zasobami platformy Azure nie wiąże się z dodatkowymi kosztami związanymi z korzystaniem z usługi Azure Lighthouse. Wszyscy klienci lub partnerzy platformy Azure mogą korzystać z usługi Azure Lighthouse.

## <a name="capabilities"></a>Możliwości

Usługa Azure Lighthouse obejmuje wiele sposobów usprawniania zaangażowania i zarządzania:

- **Zarządzanie zasobami delegowanymi przez platformę Azure**: bezpieczne zarządzanie zasobami platformy Azure klientów w ramach własnej dzierżawy bez konieczności przełączania kontekstu i płaszczyzny kontroli. Subskrypcje i grupy zasobów można delegować do określonych użytkowników i ról w dzierżawie zarządzającej, z możliwością usuwania dostępu zgodnie z wymaganiami. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami delegowanymi przez platformę Azure](concepts/azure-delegated-resource-management.md).
- **Nowe Azure Portal środowiska**: Wyświetl informacje o różnych dzierżawach na [stronie **moi klienci** ](./how-to/view-manage-customers.md) w Azure Portal. Odpowiednia strona [ **dostawcy usług** ](how-to/view-manage-service-providers.md) pozwala klientom wyświetlać i zarządzać dostępem dostawcy usług.
- **Szablony Azure Resource Manager**: nasze Szablony ułatwiają wykonywanie zadań związanych z zarządzaniem między dzierżawcami i Dodawanie zasobów klientów do delegowania. Aby uzyskać więcej informacji, zobacz nasze [przykładowe repozytorium](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) i Dołącz [klienta do usługi Azure Lighthouse](how-to/onboard-customer.md).
- **Oferty usług zarządzanych w witrynie Azure Marketplace**: umożliwiają klientom świadczenie usług za pomocą ofert prywatnych i publicznych oraz automatyczne dołączanie do usługi Azure Lighthouse. Aby uzyskać więcej informacji, zobacz [oferty usług zarządzanych w witrynie Azure Marketplace](concepts/managed-services-offers.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarządzaniu zasobami delegowanymi na platformie Azure](concepts/azure-delegated-resource-management.md).
- Poznaj [środowiska zarządzania między dzierżawcami](concepts/cross-tenant-management-experience.md).
- Zobacz, jak [korzystać z usługi Azure Lighthouse w przedsiębiorstwie](concepts/enterprise.md).
