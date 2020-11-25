---
title: Usługa Azure Lighthouse w scenariuszach dla przedsiębiorstw
description: Możliwości usługi Azure Lighthouse umożliwiają uproszczenie zarządzania różnymi dzierżawcami w przedsiębiorstwie, które korzystają z wielu dzierżawców usługi Azure AD.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 3f452e6810fa6809b5ba1b83b664f8b38d82a895
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004249"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Usługa Azure Lighthouse w scenariuszach dla przedsiębiorstw

Typowym scenariuszem dla [usługi Azure Lighthouse](../overview.md) jest dostawca usług zarządzający zasobami w dzierżawach Azure Active Directory (Azure AD) klientów. Jednak możliwości usługi Azure Lighthouse umożliwiają również uproszczenie zarządzania dzierżawcą w przedsiębiorstwie korzystającym z wielu dzierżawców usługi Azure AD.

## <a name="single-vs-multiple-tenants"></a>Pojedyncza a wiele dzierżawców

W przypadku większości organizacji zarządzanie jest łatwiejsze w przypadku pojedynczej dzierżawy usługi Azure AD. Posiadanie wszystkich zasobów w ramach jednej dzierżawy umożliwia scentralizowanie zadań zarządzania według wyznaczonych użytkowników, grup użytkowników lub jednostek usługi w ramach tej dzierżawy. Jeśli to możliwe, zalecamy używanie jednej dzierżawy dla organizacji. Jednak niektóre organizacje mogą mieć wiele dzierżawców usługi Azure AD. Czasami może to być sytuacja tymczasowa, tak jak w przypadku pozyskiwania i długoterminowa strategia konsolidacji dzierżawców nie została jeszcze zdefiniowana. Czasami organizacje mogą wymagać regularnego utrzymania wielu dzierżawców ze względu na całkowite niezależne jednostki zależne, wymagania geograficzne lub prawne lub inne zagadnienia.

W przypadkach, gdy wymagana jest architektura wielodostępna, usługa Azure Lighthouse może pomóc w scentralizowaniu i usprawnianiu operacji zarządzania. Korzystając z funkcji [zarządzania zasobami delegowanymi przez platformę Azure](azure-delegated-resource-management.md), użytkownicy w jednej dzierżawie zarządzającej mogą wykonywać [funkcje zarządzania między dzierżawcami](cross-tenant-management-experience.md) w scentralizowany i skalowalny sposób.

## <a name="tenant-management-architecture"></a>Architektura zarządzania dzierżawcą

Aby korzystać z usługi Azure Lighthouse w przedsiębiorstwie, należy określić, która dzierżawa będzie obejmować użytkowników, którzy wykonują operacje zarządzania w innych dzierżawach. Innymi słowy, należy określić, który dzierżawca będzie dzierżawą zarządzającą dla innych dzierżawców.

Załóżmy na przykład, że organizacja ma jedną dzierżawę, która wywoła *dzierżawę a*. Organizacja uzyskuje dostęp do *dzierżawy B* i *dzierżawy C* i ma powody biznesowe, które wymagają utrzymania ich jako osobnych dzierżawców.

Organizacja chce używać tych samych definicji zasad, metod tworzenia kopii zapasowych i procesów zabezpieczeń we wszystkich dzierżawach. Ponieważ dzierżawca zawiera już użytkowników odpowiedzialnych za te zadania, można dołączyć subskrypcje w ramach dzierżawy B i dzierżawy C, umożliwiając tym samym użytkownikom w dzierżawie A wykonywanie tych zadań.

![Diagram przedstawiający użytkowników w dzierżawie, którzy zarządzają zasobami w dzierżawie B i w dzierżawie C.](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Zagadnienia dotyczące zabezpieczeń i dostępu

W większości scenariuszy przedsiębiorstwa należy delegować pełną subskrypcję usługi Azure Lighthouse. Można również delegować tylko określone grupy zasobów w ramach subskrypcji.

W obu przypadkach należy [przestrzegać zasad najniższych uprawnień podczas definiowania użytkowników, którzy będą mieli dostęp do zasobów delegowanych](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Dzięki temu użytkownicy mają uprawnienia wymagane do wykonywania wymaganych zadań i zmniejszają ryzyko przypadkowych błędów.

Usługa Azure Lighthouse udostępnia jedynie logiczne linki między zarządzaną dzierżawą i zarządzanymi dzierżawcami, a nie fizycznym przeniesieniem danych ani zasobów. Ponadto dostęp do usługi zawsze odbywa się tylko w jednym kierunku — od dzierżawy zarządzającej do zarządzanych dzierżawców.  Użytkownicy i grupy w dzierżawie zarządzającej powinny nadal korzystać z uwierzytelniania wieloskładnikowego podczas wykonywania operacji zarządzania na zarządzanych zasobach dzierżawy.

Przedsiębiorstwa z wewnętrznym lub zewnętrznym zarządzaniem i zgodnością guardrails mogą korzystać z [dzienników aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md) w celu spełnienia wymagań dotyczących przejrzystości. Gdy dzierżawy przedsiębiorstwa ustanowiły zarządzanie relacjami i zarządzasz nimi, użytkownicy w każdej dzierżawie mogą wyświetlić zarejestrowane działanie, aby zobaczyć akcje podejmowane przez użytkowników w dzierżawie zarządzającej.

## <a name="onboarding-considerations"></a>Zagadnienia dotyczące dołączania

Subskrypcje (lub grupy zasobów w ramach subskrypcji) można dołączać do usługi Azure Lighthouse, wdrażając szablony Azure Resource Manager lub korzystając z usług zarządzanych, ofert opublikowanych w portalu Azure Marketplace.

Ze względu na to, że użytkownicy korporacyjni zwykle mają bezpośredni dostęp do dzierżawców przedsiębiorstwa i nie ma potrzeby wprowadzenia na rynek ani promowania oferty zarządzania, jest ona zwykle szybsza i bardziej prosta do wdrażania Azure Resource Manager szablonów. Ze względu na to, że [wskazówki dotyczące](../how-to/onboard-customer.md) dołączania odnoszą się do dostawców usług i klientów, przedsiębiorstwa mogą używać tych samych procesów do dołączania swoich dzierżawców.

Jeśli wolisz, dzierżawcy w przedsiębiorstwie można dołączyć, [publikując ofertę usług zarządzanych w witrynie Azure Marketplace](../how-to/publish-managed-services-offers.md). Aby upewnić się, że oferta jest dostępna tylko dla odpowiednich dzierżawców, upewnij się, że plany są oznaczone jako prywatne. Korzystając z planu prywatnego, podaj identyfikatory subskrypcji dla każdej dzierżawy, która ma zostać dołączona, a nikt inny nie będzie mógł uzyskać swojej oferty.

## <a name="terminology-notes"></a>Uwagi dotyczące terminologii

W przypadku zarządzania przez wiele dzierżawców w ramach przedsiębiorstwa odwołania do dostawców usług w dokumentacji usługi Azure Lighthouse można zrozumieć, aby zastosować je do dzierżawy zarządzającej w przedsiębiorstwie — czyli dzierżawcy obejmującej użytkowników, którzy będą zarządzać zasobami w innych dzierżawcach za pomocą usługi Azure Lighthouse. Podobnie wszystkie odwołania do klientów można zrozumieć, aby zastosować je do dzierżawców, którzy delegowania zasobów do zarządzania przez użytkowników w dzierżawie zarządzającej.

Na przykład w powyższym przykładzie dzierżawca A może być uważana za dzierżawę dostawcy usług (dzierżawa zarządzająca) oraz dzierżawę B i dzierżawca C mogą być uważane za dzierżawców klientów.

W tym przykładzie Dzierżawca użytkownicy z odpowiednimi uprawnieniami mogą [wyświetlać delegowane zasoby i zarządzać nimi](../how-to/view-manage-customers.md) na stronie **moi klienci** w Azure Portal. Podobnie użytkownicy dzierżawy B i dzierżawy C z odpowiednimi uprawnieniami mogą [wyświetlać zasoby delegowane do dzierżawy i zarządzać nimi](../how-to/view-manage-service-providers.md) na stronie **dostawcy usług** w Azure Portal.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](cross-tenant-management-experience.md).
- Dowiedz się więcej o [zarządzaniu zasobami delegowanymi na platformie Azure](azure-delegated-resource-management.md).