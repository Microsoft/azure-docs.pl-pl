---
title: Publikowanie oferty usługi zarządzanej w witrynie Azure Marketplace
description: Dowiedz się, jak opublikować ofertę usługi zarządzanej, która dołączy klientów do zarządzania zasobami delegowanymi przez platformę Azure.
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: 04da33d0232371f4175a935bf1e35925376babbc
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788743"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publikowanie oferty usługi zarządzanej w witrynie Azure Marketplace

W tym artykule dowiesz się, jak opublikować publiczną lub prywatną ofertę usługi zarządzanej w [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com) , korzystając z [komercyjnego](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) programu w centrum partnerskim. Klienci, którzy kupili ofertę, będą mogli dodać subskrypcje i grupy zasobów na potrzeby [zarządzania zasobami delegowanymi przez platformę Azure](../concepts/azure-delegated-resource-management.md).

## <a name="publishing-requirements"></a>Wymagania dotyczące publikowania

Musisz mieć prawidłowe [konto w centrum partnerskim](../../marketplace/partner-center-portal/create-account.md) , aby tworzyć i publikować oferty. Jeśli nie masz jeszcze konta, [proces rejestracji](https://aka.ms/joinmarketplace) przeprowadzi Cię przez kroki tworzenia konta w centrum partnerskim i rejestrowania się w komercyjnym programie Marketplace.

Zgodnie z [wymaganiami dotyczącymi certyfikacji oferty usługi zarządzanej](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)musisz mieć [poziom kompetencji w chmurze Silver lub Gold](https://docs.microsoft.com/partner-center/learn-about-competencies) , aby opublikować ofertę [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) usługi zarządzanej.

Identyfikator Microsoft Partner Network (MPN) zostanie [automatycznie skojarzony](../../billing/billing-partner-admin-link-started.md) z ofertami, które publikujesz, aby śledzić swój wpływ na zaangażowanie klientów.

> [!NOTE]
> Jeśli nie chcesz publikować oferty w witrynie Azure Marketplace, możesz dołączyć klientów ręcznie przy użyciu szablonów Azure Resource Manager. Aby uzyskać więcej informacji, zobacz Dołączanie [klienta do zarządzania zasobami delegowanymi przez platformę Azure](onboard-customer.md).

## <a name="create-your-offer"></a>Tworzenie oferty

Aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia oferty, w tym wszystkich informacji i zasobów, które należy podać, zobacz [Tworzenie oferty usługi zarządzanej](../../marketplace/partner-center-portal/create-new-managed-service-offer.md).

Aby dowiedzieć się więcej o ogólnym procesie publikowania, zobacz artykuł [Azure Marketplace i Podręcznik publikowania AppSource](../../marketplace/marketplace-publishers-guide.md). Należy również zapoznać się z [komercyjnymi zasadami certyfikacji portalu Marketplace](https://docs.microsoft.com/legal/marketplace/certification-policies), szczególnie w sekcji [usługi zarządzane](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) .

Po dodaniu oferty przez klienta będzie można delegować jedną lub więcej subskrypcji lub grup zasobów, które zostaną następnie dołączone [do zarządzania zasobami delegowanymi przez platformę Azure](#the-customer-onboarding-process).

> [!IMPORTANT]
> Każdy plan w ofercie usługi zarządzanej zawiera sekcję **szczegóły manifestu** , w której definiujesz jednostki Azure Active Directory (Azure AD) w dzierżawie, które będą miały dostęp do delegowanych grup zasobów i/lub subskrypcji dla klientów, którzy kupili ten plan. Należy pamiętać, że każda dołączona grupa (lub użytkownik lub usługa) będzie mieć takie same uprawnienia dla każdego klienta, który kupuje plan. Aby przypisać różne grupy do pracy z każdym klientem, należy opublikować oddzielny [Plan prywatny](../../marketplace/private-offers.md) , który jest wyłączny dla każdego klienta.

## <a name="publish-your-offer"></a>Publikowanie oferty

Po zakończeniu wszystkich sekcji następnym krokiem jest opublikowanie oferty w portalu Azure Marketplace. Wybierz przycisk **Publikuj** , aby zainicjować proces tworzenia oferty na żywo. Więcej informacji o tym procesie można znaleźć [tutaj](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish). 

[Zaktualizowaną wersję oferty można opublikować](../..//marketplace/partner-center-portal/update-existing-offer.md) w dowolnym momencie. Na przykład możesz chcieć dodać nową definicję roli do wcześniej opublikowanej oferty. Po wykonaniu tej czynności klienci, którzy już dodaliśmy do oferty, będą widzieć ikonę na stronie [**dostawcy usług**](view-manage-service-providers.md) w Azure Portal, która pozwala im znać aktualizację. Każdy klient będzie mógł [przejrzeć zmiany](view-manage-service-providers.md#update-service-provider-offers) i zdecydować, czy chcą one zaktualizować do nowej wersji. 

## <a name="the-customer-onboarding-process"></a>Proces dołączania klienta

Po dodaniu oferty przez klienta będzie można [delegować co najmniej jedną określoną subskrypcję lub grupę zasobów](view-manage-service-providers.md#delegate-resources), która następnie zostanie dołączona do zarządzania zasobami delegowanymi przez platformę Azure. Jeśli klient zaakceptuje ofertę, ale nie oddelegowano jeszcze żadnych zasobów, w górnej części **strony dostawcy** [**usług**](view-manage-service-providers.md) w Azure Portal zostanie wyświetlona Uwaga.

> [!IMPORTANT]
> Delegowanie musi odbywać się przez konto niebędące Gośćmi w dzierżawie klienta, które ma [wbudowaną rolę właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) dla dołączanej subskrypcji (lub która zawiera grupy zasobów, które są dołączane). Aby wyświetlić wszystkich użytkowników, którzy mogą delegować subskrypcję, użytkownik w dzierżawie może wybrać subskrypcję w Azure Portal, otworzyć funkcję **Kontrola dostępu (IAM)** i [wyświetlić wszystkich użytkowników z rolą właściciela](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Gdy klient deleguje subskrypcję (lub co najmniej jedną grupę zasobów w ramach subskrypcji), dostawca zasobów **Microsoft. ManagedServices** zostanie zarejestrowany dla tej subskrypcji, a użytkownicy w Twojej dzierżawie będą mogli uzyskiwać dostęp do delegowanych zasobów zgodnie z autoryzacjami w ofercie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [komercyjnym rynku](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
- [Wyświetlaj klientów i zarządzaj nimi](view-manage-customers.md) , przechodząc do **moich klientów** w Azure Portal.
