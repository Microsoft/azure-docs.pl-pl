---
title: Usługa Azure Lighthouse i aplikacje zarządzane na platformie Azure
description: Dowiedz się, w jaki sposób usługa Azure Lighthouse i aplikacje zarządzane przez platformę Azure mogą pomóc w różnych scenariuszach i sposobach ich użycia.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: b3d6c83a50f1da006f2618f7f4ff59759dac2555
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436525"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Usługa Azure Lighthouse i aplikacje zarządzane na platformie Azure

Zarówno aplikacje zarządzane przez platformę Azure, jak i Azure Lighthouse działają przez umożliwienie dostawcy usług dostępu do zasobów znajdujących się w dzierżawie klienta. Pomocne może być zrozumienie różnic w sposobie ich działania oraz scenariusze, które ułatwiają włączenie, a także ich użycie razem.

> [!TIP]
> Chociaż odwołujemy się do dostawców usług i klientów w tym temacie, [przedsiębiorstwa zarządzające wieloma dzierżawcami](enterprise.md) mogą korzystać z tych samych procesów i narzędzi.

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Porównywanie usługi Azure Lighthouse i aplikacji zarządzanych przez platformę Azure

### <a name="azure-lighthouse"></a>Azure Lighthouse

Za pomocą [usługi Azure Lighthouse](../overview.md)dostawca usług może wykonywać szeroką gamę zadań zarządzania bezpośrednio w ramach subskrypcji klienta (lub grupy zasobów). Ten dostęp uzyskuje się za pomocą logicznej projekcji, co umożliwia dostawcom usług logowanie się do własnych dzierżawców i dostęp do zasobów należących do dzierżawy klienta. Klient może określić, które subskrypcje lub grupy zasobów mają być delegowane do dostawcy usług, a klient utrzymuje pełny dostęp do tych zasobów. W każdej chwili można także usunąć dostęp dostawcy usług.

Aby korzystać z usługi Azure Lighthouse, klienci są dołączeni do [zarządzania zasobami delegowanymi przez platformę Azure](azure-delegated-resource-management.md) przez [wdrażanie szablonów usługi ARM](../how-to/onboard-customer.md) lub za pomocą [oferty usług zarządzanych w witrynie Azure Marketplace](managed-services-offers.md). Możesz śledzić wpływ na zaangażowanie klientów, [ŁĄCZĄC identyfikator partnera](../how-to/partner-earned-credit.md).

Usługa Azure Lighthouse jest zwykle używana, gdy dostawca usług będzie regularnie wykonywał zadania zarządzania dla klienta.

### <a name="azure-managed-applications"></a>Aplikacje zarządzane na platformie Azure

[Aplikacje zarządzane przez platformę Azure](../../azure-resource-manager/managed-applications/overview.md) umożliwiają dostawcy usług lub niezależnego dostawcy oprogramowania oferowanie rozwiązań w chmurze, które są łatwe do wdrożenia i użycia w swoich własnych subskrypcjach.

W aplikacji zarządzanej zasoby używane przez aplikację są powiązane ze sobą i wdrażane w grupie zasobów, która jest zarządzana przez wydawcę. Ta grupa zasobów jest obecna w subskrypcji klienta, ale tożsamość w dzierżawie wydawcy ma do niej dostęp. Niezależny dostawca oprogramowania nadal zarządza zarządzaną aplikacją i utrzymuje ją, natomiast klient nie ma bezpośredniego dostępu do pracy w swojej grupie zasobów ani dostępu do zasobów.

Aplikacje zarządzane obsługują [niestandardowe środowiska Azure Portal](../../azure-resource-manager/managed-applications/concepts-view-definition.md) i [integrację z dostawcami niestandardowymi](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md). Te opcje mogą służyć do zapewnienia bardziej dostosowanego i zintegrowanego środowiska, ułatwiając klientom wykonywanie pewnych zadań związanych z zarządzaniem.

Aplikacje zarządzane można [publikować w witrynie Azure Marketplace](../../marketplace/create-new-azure-apps-offer.md)jako prywatna oferta do użycia przez określonego klienta lub jako publiczna oferta, którą wielu klientów może zakupić. Mogą być również dostarczane do użytkowników w organizacji, [publikując aplikacje zarządzane w katalogu usług](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md). Można wdrożyć zarówno katalog usług, jak i wystąpienia witryny Marketplace przy użyciu szablonów ARM, które mogą obejmować unikatowy identyfikator partnera portalu Marketplace w celu śledzenia [przypisywania użycia klientów](../../marketplace/azure-partner-customer-usage-attribution.md).

Aplikacje zarządzane platformy Azure są zwykle używane w przypadku konkretnego klienta, który można uzyskać za pomocą rozwiązania gotowe, które jest w pełni zarządzane przez dostawcę usług.

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Korzystanie z usługi Azure Lighthouse i aplikacji zarządzanych przez platformę Azure

Chociaż usługa Azure Lighthouse i aplikacje zarządzane przez platformę Azure korzystają z różnych mechanizmów dostępu w celu osiągnięcia różnych celów, mogą istnieć scenariusze, dla których dostawca usług powinien korzystać z obu tych klientów.

Klient może na przykład potrzebować usług zarządzanych dostarczonych przez dostawcę usług za pomocą usługi Azure Lighthouse, dzięki czemu mają wgląd w działania partnerów oraz ciągłą kontrolę nad delegowaną subskrypcją. Jednak dostawca usług może nie chcieć, aby klient mógł uzyskać dostęp do określonych zasobów, które będą przechowywane w dzierżawie klienta, lub zezwolić na jakiekolwiek niestandardowe działania dotyczące tych zasobów. Aby spełnić te cele, dostawca usług może opublikować ofertę prywatną jako zarządzaną aplikację. Aplikacja zarządzana może obejmować grupę zasobów wdrożoną w dzierżawie klienta, ale dostęp do niej nie jest możliwy bezpośrednio przez klienta.

Klienci mogą również być zainteresowani zarządzanymi aplikacjami od wielu dostawców usług, niezależnie od tego, czy korzystają z usług zarządzanych za pośrednictwem usługi Azure Lighthouse, od dowolnego dostawcy usług. Dodatkowo partnerzy w programie Cloud Solution Provider (CSP) mogą odsprzedawać niektóre zarządzane aplikacje opublikowane przez innych niezależnych dostawców oprogramowania do klientów obsługiwanych przez usługę Azure Lighthouse. W przypadku szerokiego zakresu opcji dostawcy usług mogą wybrać odpowiednie saldo do zaspokajania potrzeb klientów, ograniczając dostęp do zasobów w razie potrzeby.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [aplikacjach zarządzanych przez platformę Azure](../../azure-resource-manager/managed-applications/overview.md).
- Dowiedz się [, jak dołączyć subskrypcję do usługi Azure Lighthouse](../how-to/onboard-customer.md).