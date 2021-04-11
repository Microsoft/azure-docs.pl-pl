---
title: Publikowanie oferty usługi zarządzanej w witrynie Azure Marketplace
description: Dowiedz się, jak opublikować ofertę usługi zarządzanej, która dołączy klientów do usługi Azure Lighthouse.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: f59d9708925b52bcf9432663fe6d3137a54b37ad
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107452"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publikowanie oferty usługi zarządzanej w witrynie Azure Marketplace

W tym artykule dowiesz się, jak opublikować publiczną lub prywatną ofertę usługi zarządzanej w [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com) , korzystając z [komercyjnego](../../marketplace/overview.md) programu w centrum partnerskim. Klienci, którzy kupili ofertę, będą delegować subskrypcje lub grupy zasobów, co umożliwi zarządzanie nimi za pomocą [usługi Azure Lighthouse](../overview.md).

## <a name="publishing-requirements"></a>Wymagania dotyczące publikowania

Musisz mieć prawidłowe [konto w centrum partnerskim](../../marketplace/create-account.md) , aby tworzyć i publikować oferty. Jeśli nie masz jeszcze konta, [proces rejestracji](https://aka.ms/joinmarketplace) przeprowadzi Cię przez kroki tworzenia konta w centrum partnerskim i rejestrowania się w komercyjnym programie Marketplace.

Zgodnie z [wymaganiami dotyczącymi certyfikacji oferty usługi zarządzanej](/legal/marketplace/certification-policies#700-managed-services)musisz mieć [poziom kompetencji w chmurze Silver lub Gold](/partner-center/learn-about-competencies) , aby opublikować ofertę [](https://partner.microsoft.com/membership/azure-expert-msp) usługi zarządzanej. Należy również [wprowadzić miejsce docelowe potencjalnego klienta, które utworzy rekord w systemie CRM przy](../../marketplace/plan-managed-service-offer.md#customer-leads) każdym wdrożeniu oferty przez klienta.

Jeśli nie chcesz publikować oferty w witrynie Azure Marketplace lub nie spełniasz wszystkich wymagań, możesz dołączyć klientów ręcznie przy użyciu szablonów Azure Resource Manager. Aby uzyskać więcej informacji, zobacz Dołączanie [klienta do usługi Azure Lighthouse](onboard-customer.md).

Poniższa tabela może pomóc w ustaleniu, czy klienci mają zostać dołączeni przez opublikowanie oferty usługi zarządzanej, czy za pomocą szablonów Azure Resource Manager.

|**Kwestie do rozważenia**  |**Oferta usługi zarządzanej**  |**Szablony usługi ARM**  |
|---------|---------|---------|
|Wymaga [konta Centrum partnerskiego](../../marketplace/create-account.md)   |Tak         |Nie        |
|Wymaga [poziomu kompetencji dla platformy Cloud Platform Silver lub Gold](/partner-center/learn-about-competencies) lub [msp dla systemu Azure](https://partner.microsoft.com/membership/azure-expert-msp)      |Tak         |Nie         |
|Dostępne dla nowych klientów w portalu Azure Marketplace     |Tak     |Nie       |
|Może ograniczać ofertę do określonych klientów     |Tak (tylko z ofertami prywatnymi, których nie można używać w przypadku subskrypcji ustanowionych przez odsprzedawcę programu dostawcy rozwiązań w chmurze (CSP))         |Tak         |
|Wymaga akceptacji klienta w Azure Portal     |Tak     |Nie   |
|Może używać automatyzacji do dołączania wielu subskrypcji, grup zasobów lub klientów |Nie     |Tak    |
|Natychmiastowy dostęp do nowych ról wbudowanych i funkcji usługi Azure Lighthouse     |Nie zawsze (zazwyczaj dostępne po pewnym opóźnieniu)         |Tak         |
|Klienci mogą przeglądać i akceptować zaktualizowane oferty w Azure Portal | Tak | Nie |

> [!NOTE]
> Oferty usług zarządzanych mogą nie być dostępne w Azure Government i innych chmurach narodowych.

## <a name="create-your-offer"></a>Tworzenie oferty

Aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia oferty, w tym wszystkich informacji i zasobów, które należy podać, zobacz [Tworzenie oferty usługi zarządzanej](../../marketplace/create-managed-service-offer.md).

Aby dowiedzieć się więcej o ogólnym procesie publikowania, zapoznaj się z [dokumentacją komercyjnej witryny Marketplace](../../marketplace/overview.md). Należy również zapoznać się z [komercyjnymi zasadami certyfikacji portalu Marketplace](/legal/marketplace/certification-policies), szczególnie w sekcji [usługi zarządzane](/legal/marketplace/certification-policies#700-managed-services) .

Po dodaniu oferty przez klienta będzie można delegować jedną lub więcej subskrypcji lub grup zasobów, które zostaną następnie dołączone [do usługi Azure Lighthouse](#the-customer-onboarding-process).

> [!IMPORTANT]
> Każdy plan w ofercie usługi zarządzanej zawiera sekcję **szczegóły manifestu** , w której definiujesz jednostki Azure Active Directory (Azure AD) w dzierżawie, które będą miały dostęp do delegowanych grup zasobów i/lub subskrypcji dla klientów, którzy kupili ten plan. Należy pamiętać, że każda dołączona grupa (lub użytkownik lub usługa) będzie mieć takie same uprawnienia dla każdego klienta, który kupuje plan. Aby przypisać różne grupy do pracy z każdym klientem, można opublikować oddzielny [Plan prywatny](../../marketplace/private-offers.md) , który jest wyłączny dla każdego klienta. Należy pamiętać, że plany prywatne nie są obsługiwane w przypadku subskrypcji ustanowionych przez odsprzedawcę programu w programie Cloud Solution Provider (CSP).

## <a name="publish-your-offer"></a>Publikowanie oferty

Po zakończeniu wszystkich sekcji następnym krokiem jest opublikowanie oferty w portalu Azure Marketplace. Wybierz przycisk **Publikuj** , aby zainicjować proces tworzenia oferty na żywo. Więcej informacji o tym procesie można znaleźć [tutaj](../../marketplace/review-publish-offer.md).

[Zaktualizowaną wersję oferty można opublikować](../..//marketplace/partner-center-portal/update-existing-offer.md) w dowolnym momencie. Na przykład możesz chcieć dodać nową definicję roli do wcześniej opublikowanej oferty. Po wykonaniu tej czynności klienci, którzy już dodaliśmy do oferty, będą widzieć ikonę na stronie [**dostawcy usług**](view-manage-service-providers.md) w Azure Portal, która pozwala im znać aktualizację. Każdy klient będzie mógł [przejrzeć zmiany](view-manage-service-providers.md#update-service-provider-offers) i zdecydować, czy chcą one zaktualizować do nowej wersji. 

## <a name="the-customer-onboarding-process"></a>Proces dołączania klienta

Po dodaniu oferty przez klienta będzie można [delegować co najmniej jedną określoną subskrypcję lub grupę zasobów](view-manage-service-providers.md#delegate-resources), która następnie zostanie dołączona do usługi Azure Lighthouse. Jeśli klient zaakceptuje ofertę, ale nie oddelegowano jeszcze żadnych zasobów, w górnej części **strony dostawcy** [**usług**](view-manage-service-providers.md) w Azure Portal zostanie wyświetlona Uwaga.

> [!IMPORTANT]
> Delegowanie musi odbywać się na koncie niegościa w dzierżawie klienta mającym rolę z `Microsoft.Authorization/roleAssignments/write` uprawnieniem, takim jak [właściciel](../../role-based-access-control/built-in-roles.md#owner), dla dołączanej subskrypcji (lub zawierającej grupy zasobów, które są dołączane). Aby znaleźć użytkowników, którzy mogą delegować subskrypcję, użytkownik w dzierżawie klienta może wybrać subskrypcję w Azure Portal, otworzyć funkcję **Kontrola dostępu (IAM)** i [wyświetlić wszystkich użytkowników z rolą właściciela](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Gdy klient deleguje subskrypcję (lub co najmniej jedną grupę zasobów w ramach subskrypcji), dostawca zasobów **Microsoft. ManagedServices** zostanie zarejestrowany dla tej subskrypcji, a użytkownicy w Twojej dzierżawie będą mogli uzyskiwać dostęp do delegowanych zasobów zgodnie z autoryzacjami w ofercie.

W przypadku opublikowania zaktualizowanej wersji oferty klient może [przejrzeć zmiany w Azure Portal i zaakceptować nową wersję](view-manage-service-providers.md#update-service-provider-offers).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [komercyjnym rynku](../../marketplace/overview.md).
- [Połącz swój identyfikator partnera](partner-earned-credit.md) , aby śledzić swój wpływ na zaangażowanie klientów.
- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
- [Wyświetlaj klientów i zarządzaj nimi](view-manage-customers.md) , przechodząc do **moich klientów** w Azure Portal.
