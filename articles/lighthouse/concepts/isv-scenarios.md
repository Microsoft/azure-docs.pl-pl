---
title: Azure Lighthouse w scenariuszach niezależnych dostawców oprogramowania
description: Możliwości platformy Azure Lighthouse mogą być używane przez niezależnych dostawców oprogramowania w celu uzyskania większej elastyczności z ofertami klientów.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696385"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>Azure Lighthouse w scenariuszach niezależnych dostawców oprogramowania

Typowym scenariuszem dla [usługi Azure Lighthouse](../overview.md) jest dostawca usług zarządzający zasobami w dzierżawach Azure Active Directory (Azure AD) klientów. Możliwości platformy Azure Lighthouse mogą być również używane przez niezależnych dostawców oprogramowania (ISV) korzystających z ofert opartych na systemie SaaS u klientów. Usługa Azure Lighthouse może być szczególnie przydatna dla niezależnych dostawców oprogramowania, którzy oferują zarządzane usługi lub pomoc techniczną, która wymaga dostępu do zakresu subskrypcji.

## <a name="managed-service-offers-in-azure-marketplace"></a>Oferty usługi zarządzanej w witrynie Azure Marketplace

Jako niezależny dostawca oprogramowania może już publikować opublikowane rozwiązania w portalu Azure Marketplace. Jeśli oferujesz klientom usługi zarządzane, możesz to zrobić przez opublikowanie oferty usługi zarządzanej. Oferty te usprawniają proces dołączania i zwiększają skalowalność usług do tylu klientów w razie potrzeby. Usługa Azure Lighthouse obsługuje szeroką gamę [zadań zarządzania i scenariuszy](cross-tenant-management-experience.md#enhanced-services-and-scenarios) , których można użyć w celu zapewnienia klientom wartości.

Aby uzyskać więcej informacji, zobacz temat [Publikowanie oferty usługi zarządzanej w witrynie Azure Marketplace](../how-to/publish-managed-services-offers.md).

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>Korzystanie z usługi Azure Lighthouse z aplikacjami zarządzanymi przez platformę Azure

[Aplikacje zarządzane przez platformę Azure](../../azure-resource-manager/managed-applications/overview.md) są inne, dzięki którym dostawcy oprogramowania mogą zapewnić klientom usługi. Możesz użyć usługi Azure Lighthouse wraz z aplikacjami zarządzanymi przez platformę Azure, aby włączyć ulepszone scenariusze.

Aby uzyskać więcej informacji, zobacz [Azure Lighthouse i aplikacje zarządzane przez platformę Azure](managed-applications.md).

## <a name="saas-based-multi-tenant-offerings"></a>Oferty wielodostępne oparte na SaaS

Dodatkowy scenariusz polega na tym, że dostawca ISV obsługuje zasoby w ramach subskrypcji w ramach własnej dzierżawy, a następnie korzysta z usługi Azure Lighthouse, aby umożliwić klientom dostęp do tych zasobów. Klient może następnie zalogować się do swojej dzierżawy i uzyskać dostęp do tych zasobów zgodnie z potrzebami. Dostawcy ISV utrzymują swój adres IP w swojej dzierżawie i mogą korzystać z własnych planów pomocy technicznej w celu zgłaszania biletów związanych z rozwiązaniem hostowanym w swojej dzierżawie, a nie z użyciem planu klienta. Ponieważ zasoby znajdują się w dzierżawie niezależnego dostawcy oprogramowania, wszystkie akcje mogą być wykonywane bezpośrednio przez niezależnego dostawcę oprogramowania, takich jak logowanie do maszyn wirtualnych, instalowanie aplikacji i wykonywanie zadań konserwacyjnych.

W tym scenariuszu użytkownicy w dzierżawie klienta otrzymują zasadniczo dostęp jako "Zarządzanie dzierżawcą", mimo że klient nie zarządza zasobami niezależnego dostawcy oprogramowania. Ponieważ uzyskują bezpośredni dostęp do dzierżawcy niezależnego dostawcy oprogramowania, należy udzielić tylko minimalnych uprawnień, aby klienci nie mogli przypadkowo wprowadzać zmian do rozwiązania lub innych zasobów niezależnych dostawców oprogramowania.

Aby włączyć tę architekturę, dostawca oprogramowania musi uzyskać identyfikator obiektu dla grupy użytkowników w dzierżawie usługi Azure AD klienta wraz z IDENTYFIKATORem dzierżawy. Niezależny dostawca oprogramowania kompiluje następnie szablon usługi ARM przyznającej tej grupie użytkowników odpowiednie uprawnienia i [wdraża ją w ramach subskrypcji niezależnego dostawcy oprogramowania](../how-to/onboard-customer.md) , która zawiera zasoby, do których klient będzie miał dostęp.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](cross-tenant-management-experience.md).
- Dowiedz się więcej o [zarządzaniu zasobami delegowanymi na platformie Azure](azure-delegated-resource-management.md).