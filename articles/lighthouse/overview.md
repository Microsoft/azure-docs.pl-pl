---
title: Co to jest Azure Lighthouse?
description: Usługa Azure Lighthouse umożliwia dostawcom usług dostarczanie usług zarządzanych klientom o większej wydajności i skalowalności.
ms.date: 04/23/2020
ms.topic: overview
ms.openlocfilehash: aea1451d995d135506fb93e3fc9e86f67227d5b8
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144260"
---
# <a name="what-is-azure-lighthouse"></a>Co to jest Azure Lighthouse?

Usługa Azure Lighthouse oferuje dostawcom usług jedną płaszczyznę kontroli do wyświetlania i zarządzania platformą Azure wśród wszystkich klientów, którzy mają wyższą automatyzację, skalę i ulepszone zarządzanie. Dzięki usłudze Azure Lighthouse dostawcy usług mogą dostarczać zarządzane usługi przy użyciu kompleksowych i niezawodnych narzędzi do zarządzania wbudowanych w platformę Azure. Ta oferta może również przynieść organizacjom IT przedsiębiorstwa zarządzanie zasobami w wielu dzierżawach.

![Diagram omówienia usługi Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Korzyści

Usługa Azure Lighthouse pozwala na opłacalną i wydajną kompilację i dostarczanie usług zarządzanych klientom. Korzyści:

- **Zarządzanie na dużą skalę**: zaangażowanie klientów i operacje związane z cyklem życia w celu zarządzania zasobami klientów są łatwiejsze i bardziej skalowalne.
- **Większa widoczność i precyzja dla klientów**: klienci będą mieli lepszy wgląd w swoje działania i precyzyjną kontrolę nad zakresem delegowanym do zarządzania, w tym możliwość całkowitego usuwania dostępu, gdy adres IP jest zachowywany.
- **Kompleksowe i ujednolicone narzędzia platformy**: nasze środowisko ułatwiające korzystanie z kluczowych scenariuszy dostawców usług, w tym wielu modeli licencjonowania, takich jak EA, CSP i płatność zgodnie z rzeczywistym użyciem. Nowe możliwości działają z istniejącymi narzędziami i interfejsami API, modelami licencjonowania i programami partnerskimi, takimi jak [program Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/csp-overview). Wybrane opcje usługi Azure Lighthouse można zintegrować z istniejącymi przepływami pracy i aplikacjami. możesz śledzić wpływ na zaangażowanie klientów, [ŁĄCZĄC identyfikator partnera](../billing/billing-partner-admin-link-started.md).

Nie ma dodatkowych kosztów związanych z usługą Azure Lighthouse do zarządzania zasobami platformy Azure dla klientów.

## <a name="capabilities"></a>Możliwości

Usługa Azure Lighthouse obejmuje wiele sposobów usprawniania zaangażowania klientów i zarządzania nimi:

- **Zarządzanie zasobami delegowanymi przez platformę Azure**: bezpieczne zarządzanie zasobami platformy Azure klientów w ramach własnej dzierżawy bez konieczności przełączania kontekstu i płaszczyzny kontroli. Subskrypcje i grupy zasobów można delegować do określonych użytkowników i ról w dzierżawie zarządzającej, z możliwością usuwania dostępu zgodnie z wymaganiami. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami delegowanymi przez platformę Azure](concepts/azure-delegated-resource-management.md).
- **Nowe Azure Portal środowiska**: Wyświetl informacje między dzierżawcami na stronie nowe **moi klienci** w [Azure Portal](https://portal.azure.com). Odpowiedni blok **dostawców usług** umożliwia klientom wyświetlanie dostępu dostawcy usług i zarządzanie nim. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i zarządzanie klientami](./how-to/view-manage-customers.md) oraz [Wyświetlanie i zarządzanie dostawcami usług](how-to/view-manage-service-providers.md).
- **Szablony Azure Resource Manager**: łatwiejsze wykonywanie zadań zarządzania, w tym klientów dołączania do zarządzania zasobami delegowanymi przez platformę Azure. Aby uzyskać więcej informacji, zobacz nasze [przykładowe repozytorium](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) i Dołącz [klienta do zarządzania zasobami delegowanymi przez platformę Azure](how-to/onboard-customer.md).
- **Oferty usług zarządzanych w witrynie Azure Marketplace**: umożliwiają oferowanie usług klientom za pośrednictwem ofert prywatnych i publicznych oraz automatyczne dołączanie do funkcji zarządzania zasobami delegowanymi przez platformę Azure jako alternatywę do dołączania przy użyciu szablonów Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [oferty usług zarządzanych w witrynie Azure Marketplace](concepts/managed-services-offers.md).
- **Aplikacje zarządzane przez platformę Azure**: aplikacje pakietu i dostarczania dla klientów, które są łatwe do wdrożenia i użycia w swoich własnych subskrypcjach. Aplikacja jest wdrażana w grupie zasobów, do której masz dostęp z dzierżawy, co pozwala na zarządzanie usługą w ramach ogólnego środowiska Azure Lighthouse. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Managed Applications](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarządzaniu zasobami delegowanymi na platformie Azure](concepts/azure-delegated-resource-management.md).
- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](concepts/cross-tenant-management-experience.md).
