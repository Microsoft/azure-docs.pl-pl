---
title: Usługa Azure Lighthouse w scenariuszach dla przedsiębiorstw
description: Możliwości usługi Azure Lighthouse umożliwiają uproszczenie zarządzania różnymi dzierżawcami w przedsiębiorstwie, które korzystają z wielu dzierżawców usługi Azure AD.
ms.date: 07/06/2020
ms.topic: conceptual
ms.openlocfilehash: 9f9a7aa81772a1edda5fd1915918b547a3066455
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114146"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Usługa Azure Lighthouse w scenariuszach dla przedsiębiorstw

Najbardziej typowym scenariuszem dla [usługi Azure Lighthouse](../overview.md) jest dostawca usług zarządzający zasobami w dzierżawach Azure Active Directory (Azure AD) klientów. Jednak możliwości usługi Azure Lighthouse umożliwiają również uproszczenie zarządzania dzierżawcą w przedsiębiorstwie korzystającym z wielu dzierżawców usługi Azure AD.

## <a name="single-vs-multiple-tenants"></a>Pojedyncza a wiele dzierżawców

W przypadku większości organizacji zarządzanie jest łatwiejsze w przypadku pojedynczej dzierżawy usługi Azure AD. Posiadanie wszystkich zasobów w ramach jednej dzierżawy umożliwia scentralizowanie zadań zarządzania według wyznaczonych użytkowników, grup użytkowników lub jednostek usługi w ramach tej dzierżawy. Jeśli to możliwe, zalecamy używanie jednej dzierżawy dla organizacji.

W tym samym czasie istnieją sytuacje, w których organizacja może wymagać utrzymania wielu dzierżawców usługi Azure AD. W niektórych przypadkach może to być sytuacja tymczasowa, tak jak w przypadku pozyskiwania i długotrwała strategia konsolidacji dzierżawców zajmie trochę czasu. Organizacja może również wymagać regularnego utrzymania wielu dzierżawców (ze względu na całkowite niezależne jednostki zależne, wymagania geograficzne lub prawne itd.). W przypadkach, gdy wymagana jest architektura z wieloma dzierżawcami, usługa Azure Lighthouse może służyć do scentralizowania i usprawniania operacji zarządzania. Subskrypcje z wielu dzierżawców można dołączać do [zarządzania zasobami delegowanymi przez platformę Azure](azure-delegated-resource-management.md), umożliwiając Wyznaczeni użytkownikom w dzierżawie zarządzającej wykonywanie [funkcji zarządzania między dzierżawcami](cross-tenant-management-experience.md) w scentralizowany i skalowalny sposób.

## <a name="tenant-management-architecture"></a>Architektura zarządzania dzierżawcą

Podczas scentralizowania operacji zarządzania dla wielu dzierżawców należy określić, która dzierżawa będzie obejmować użytkowników wykonujących operacje zarządzania dla innych dzierżawców. Innymi słowy, należy określić, który dzierżawca będzie dzierżawą zarządzającą dla innych dzierżawców.

Załóżmy na przykład, że organizacja ma jedną dzierżawę, która wywoła *dzierżawę a*. Organizacja uzyskuje dwie dodatkowe dzierżawy, *dzierżawę B* i *dzierżawcę C*, a także masz przyczyny biznesowe, które wymagają utrzymania ich jako osobnych dzierżawców.

Organizacja chce używać tych samych definicji zasad, metod tworzenia kopii zapasowych i procesów zabezpieczeń we wszystkich dzierżawach. Ponieważ masz już użytkowników (w tym grupy użytkowników i jednostek usługi) odpowiedzialnych za wykonywanie tych zadań w ramach dzierżawy A, możesz dołączyć wszystkie subskrypcje w ramach dzierżawy B i dzierżawy C, aby Ci użytkownicy w dzierżawie mogli wykonywać te zadania.

![Użytkownicy w dzierżawie to zarządzanie zasobami w dzierżawie B i w ramach dzierżawy C](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Zagadnienia dotyczące zabezpieczeń i dostępu

W większości scenariuszy przedsiębiorstwa należy delegować pełną subskrypcję usługi Azure Lighthouse, chociaż można także delegować tylko określone grupy zasobów w ramach subskrypcji.

W obu przypadkach należy [przestrzegać zasad najniższych uprawnień podczas definiowania użytkowników, którzy będą mieli dostęp do zasobów](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Dzięki temu użytkownicy mają uprawnienia wymagane do wykonywania wymaganych zadań i zmniejszają ryzyko przypadkowych błędów.

Usługa Azure Lighthouse udostępnia jedynie logiczne linki między zarządzaną dzierżawą i zarządzanymi dzierżawcami, a nie fizycznym przeniesieniem danych ani zasobów. Ponadto dostęp do usługi zawsze odbywa się tylko w jednym kierunku — od dzierżawy zarządzającej do zarządzanych dzierżawców.  Użytkownicy i grupy w dzierżawie zarządzającej powinny nadal korzystać z uwierzytelniania wieloskładnikowego podczas wykonywania operacji zarządzania na zarządzanych zasobach dzierżawy.

Przedsiębiorstwa z wewnętrznym lub zewnętrznym zarządzaniem i zgodnością guardrails mogą korzystać z [dzienników aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md) w celu spełnienia wymagań dotyczących przejrzystości. Gdy dzierżawy przedsiębiorstwa ustanowiły zarządzanie relacjami i zarządzasz nimi, użytkownicy w poszczególnych dzierżawców mogą monitorować i uzyskiwać wgląd w akcje podejmowane przez użytkowników w innej dzierżawie, wyświetlając zarejestrowane działanie.

## <a name="onboarding-process-considerations"></a>Zagadnienia dotyczące procesu dołączania

Subskrypcje (lub grupy zasobów w ramach subskrypcji) można dołączać do usługi Azure Lighthouse, wdrażając szablony Azure Resource Manager lub korzystając z usług zarządzanych, ofert opublikowanych w portalu Azure Marketplace — prywatnie lub publicznie.

Ze względu na to, że użytkownicy korporacyjni zwykle będą mogli uzyskać bezpośredni dostęp do dzierżawców przedsiębiorstwa i nie ma potrzeby wprowadzenia na rynek ani promowania oferty zarządzania, jest to zwykle szybsze i bardziej bezpośrednie do wdrożenia przy użyciu szablonów Azure Resource Manager. W odniesieniu do dostawców usług i klientów w [wytycznych dotyczących](../how-to/onboard-customer.md)dołączania przedsiębiorstwa mogą korzystać z tych samych procesów.

Jeśli wolisz, dzierżawcy w przedsiębiorstwie można dołączyć, [publikując ofertę usług zarządzanych w witrynie Azure Marketplace](../how-to/publish-managed-services-offers.md). Aby upewnić się, że oferta jest dostępna tylko dla odpowiednich dzierżawców, upewnij się, że plany są oznaczone jako prywatne. Korzystając z planu prywatnego, możesz podać identyfikatory subskrypcji dla każdej dzierżawy, która ma zostać dołączona, a nikt inny nie będzie mógł uzyskać swojej oferty.

## <a name="terminology-notes"></a>Uwagi dotyczące terminologii

W przypadku zarządzania przez wiele dzierżawców w ramach przedsiębiorstwa odwołania do dostawców usług w dokumentacji usługi Azure Lighthouse można zrozumieć, aby zastosować je do dzierżawy zarządzającej w przedsiębiorstwie — czyli dzierżawcy obejmującej użytkowników, którzy będą zarządzać zasobami w innych dzierżawcach za pomocą delegowanego zarządzania zasobami platformy Azure. Podobnie odwołania do klientów mogą być zrozumiałe dla dzierżawców, którzy delegowania zasobów do zarządzania przez użytkowników w dzierżawie zarządzającej.

Na przykład w powyższym przykładzie dzierżawca A może być uważana za dzierżawę dostawcy usług (dzierżawa zarządzająca) oraz dzierżawę B i dzierżawca C mogą być uważane za dzierżawców klientów.

W tym przykładzie Dzierżawca użytkownicy z odpowiednimi uprawnieniami mogą [wyświetlać delegowane zasoby i zarządzać nimi](../how-to/view-manage-customers.md) na stronie **moi klienci** w Azure Portal. Podobnie użytkownicy dzierżawy B i dzierżawy C z odpowiednimi uprawnieniami mogą [wyświetlać zasoby delegowane do dzierżawy i zarządzać nimi](../how-to/view-manage-service-providers.md) na stronie **dostawcy usług** w Azure Portal.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](cross-tenant-management-experience.md).
- Dowiedz się więcej o [zarządzaniu zasobami delegowanymi na platformie Azure](azure-delegated-resource-management.md).