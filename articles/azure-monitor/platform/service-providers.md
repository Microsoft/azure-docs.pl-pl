---
title: Dzienniki Azure Monitor dla dostawców usług | Microsoft Docs
description: Dzienniki Azure Monitor mogą pomóc zarządzanym dostawcom usług (msp), dużymi przedsiębiorstwami, niezależnymi dostawcami oprogramowania (ISV) i dostawcami usług hostingowych oraz zarządzać serwerami w infrastrukturze lokalnej lub w chmurze klienta.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: 0869de4ccfe89cc3919ec2d2d80aa3e18749039a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87921096"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Dzienniki Azure Monitor dla dostawców usług

Log Analytics obszary robocze w Azure Monitor mogą pomóc zarządzanym dostawcom usług (msp), dużymi przedsiębiorstwami, niezależnymi dostawcami oprogramowania (ISV) i dostawcami usług hostingowych oraz zarządzać serwerami w infrastrukturze lokalnej i w chmurze klienta.

Duże przedsiębiorstwa współużytkują wiele podobieństw z dostawcami usług, szczególnie w przypadku scentralizowanego zespołu IT, który jest odpowiedzialny za zarządzanie nim dla wielu różnych jednostek roboczych. Dla uproszczenia w tym dokumencie jest stosowany *dostawca usług* , ale te same funkcje są również dostępne dla przedsiębiorstw i innych klientów.

W przypadku partnerów i dostawców usług, którzy są częścią programu [Cloud Solution Provider (CSP)](https://partner.microsoft.com/membership/cloud-solution-provider) , Log Analytics w Azure monitor to jedna z usług platformy Azure dostępnych w ramach subskrypcji CSP platformy Azure.

Log Analytics w Azure Monitor może być również używany przez dostawcę usług do zarządzania zasobami klienta za pomocą funkcji zarządzania zasobami delegowanymi platformy Azure w [usłudze Azure Lighthouse](../../lighthouse/overview.md).

## <a name="architectures-for-service-providers"></a>Architektury dla dostawców usług

Log Analytics obszary robocze umożliwiają administratorowi sterowanie przepływem i izolacją danych [dziennika](data-platform-logs.md) oraz tworzenie architektury, która odpowiada konkretnym potrzebom biznesowym. W [tym artykule](design-logs-deployment.md) wyjaśniono zagadnienia dotyczące projektowania, wdrażania i migracji dla obszaru roboczego, a w artykule [Zarządzanie dostępem](manage-access.md) omówiono sposób stosowania uprawnień i zarządzania nimi w celu uzyskania danych dziennika. Dostawcy usług mają dodatkowe uwagi.

Istnieją trzy możliwe architektury dla dostawców usług, które dotyczą Log Analytics obszarów roboczych:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. dzienniki rozproszone są przechowywane w obszarach roboczych znajdujących się w dzierżawie klienta

W tej architekturze obszar roboczy jest wdrażany w dzierżawie klienta, który jest używany dla wszystkich dzienników tego klienta.

Istnieją dwa sposoby, które Administratorzy dostawcy usług mogą uzyskać dostęp do obszaru roboczego Log Analytics w dzierżawie klienta:

- Klient może dodawać poszczególnych użytkowników od usługodawcy jako [Azure Active Directory użytkowników-Gości (B2B)](../../active-directory/b2b/what-is-b2b.md). Administratorzy dostawcy usług będą musieli zalogować się do katalogu każdego klienta w Azure Portal, aby mogli uzyskać dostęp do tych obszarów roboczych. Wymaga to również od klientów zarządzania indywidualnym dostępem każdego administratora dostawcy usług.
- Aby zapewnić lepszą skalowalność i elastyczność, dostawcy usług mogą korzystać z funkcji [zarządzania zasobami delegowanymi przez platformę Azure](../../lighthouse/concepts/azure-delegated-resource-management.md) [Lighthouse na platformie Azure](../../lighthouse/overview.md) w celu uzyskania dostępu do dzierżawy klienta. Ta metoda polega na tym, że Administratorzy dostawcy usług są włączeni do grupy użytkowników usługi Azure AD w dzierżawie dostawcy usług, a podczas procesu dołączania dla każdego klienta zostaje udzielony dostęp do tej grupy. Ci Administratorzy mogą następnie uzyskiwać dostęp do obszarów roboczych każdego klienta z własnej dzierżawy usługodawcy, zamiast logować się do każdej dzierżawy każdego klienta osobno. W ten sposób uzyskuje się dostęp do zasobów obszarów roboczych Log Analytics w ten sposób, co zmniejsza pracę wymaganą przez klienta i może ułatwić zbieranie i analizowanie danych przez wielu klientów zarządzanych przez tego samego dostawcę usług za pośrednictwem takich narzędzi, jak [Azure monitor skoroszytów](./workbooks-overview.md). Aby uzyskać więcej informacji, zobacz [monitorowanie zasobów klienta w odpowiedniej skali](../../lighthouse/how-to/monitor-at-scale.md).

Zalety architektury rozproszonej są następujące:

* Klient może potwierdzić określone poziomy uprawnień za pośrednictwem funkcji [zarządzania zasobami delegowanymi przez platformę Azure](../../lighthouse/concepts/azure-delegated-resource-management.md)lub zarządzać dostępem do dzienników przy użyciu własnej [kontroli dostępu opartej na ROLACH (Azure RBAC)](../../role-based-access-control/overview.md).
* Dzienniki mogą być zbierane z wszystkich typów zasobów, a nie tylko z danych maszyn wirtualnych opartych na agentach. Na przykład dzienniki inspekcji platformy Azure.
* Każdy klient może mieć różne ustawienia dla swojego obszaru roboczego, takie jak przechowywanie danych i ich limit.
* Izolacja klientów na potrzeby regulacji i zgodności.
* Opłata za poszczególne obszary robocze zostanie przeniesiona na subskrypcję klienta.

Wady architektury rozproszonej są następujące:

* Centralne wizualizacje i analizowanie danych w dzierżawach klientów dzięki narzędziom, takim jak skoroszyty Azure Monitor mogą powodować wolniejsze środowiska, szczególnie podczas analizowania danych w ponad 50 obszarach roboczych.
* Jeśli klienci nie zostaną dołączeni do zarządzania zasobami delegowanymi przez platformę Azure, Administratorzy dostawcy usług muszą być obsługiwani w katalogu klienta i trudniejsze jest użycie dostawcy usług w celu jednoczesnego zarządzania dużą liczbą dzierżawców klientów.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. dzienniki centralne są przechowywane w obszarze roboczym znajdującym się w dzierżawie dostawcy usług

W tej architekturze dzienniki nie są przechowywane w dzierżawach klienta, ale tylko w centralnej lokalizacji w ramach subskrypcji dostawcy usług. Agenci zainstalowani na maszynach wirtualnych klienta są skonfigurowani do wysyłania dzienników do tego obszaru roboczego przy użyciu identyfikatora obszaru roboczego i klucza tajnego.

Zalety scentralizowanej architektury są następujące:

* Zarządzanie dużą liczbą klientów i integrowanie ich z różnymi systemami zaplecza jest proste.
* Dostawca usług ma pełną własność w odniesieniu do dzienników i różnych artefaktów, takich jak funkcje i zapisane zapytania.
* Dostawca usług może przeprowadzić analizę we wszystkich swoich klientach.

Wady centralnej architektury są następujące:

* Ta architektura ma zastosowanie tylko do danych maszyn wirtualnych opartych na agencie, ale nie obejmuje źródeł danych PaaS, SaaS i Azure Fabric.
* Po scaleniu z jednym obszarem roboczym może być trudno rozdzielić dane między klientami. Jedyną dobrą metodą jest użycie w pełni kwalifikowanej nazwy domeny (FQDN) komputera lub identyfikatora subskrypcji platformy Azure.
* Wszystkie dane ze wszystkich klientów będą przechowywane w tym samym regionie z pojedynczym rachunkiem i tymi samymi ustawieniami przechowywania i konfiguracji.
* Usługi Azure Fabric i PaaS Services, takie jak Diagnostyka Azure i dzienniki inspekcji platformy Azure, wymagają, aby obszar roboczy znajdował się w tej samej dzierżawie co zasób, dlatego nie może wysyłać dzienników do centralnego obszaru roboczego.
* Wszyscy agenci maszyn wirtualnych wszyscy klienci będą uwierzytelniani w centralnym obszarze roboczym przy użyciu tego samego identyfikatora i klucza obszaru roboczego. Nie ma metody blokowania dzienników od określonego klienta bez zakłócania pracy przez innych klientów.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. dzienniki hybrydowe są przechowywane w obszarze roboczym znajdującym się w dzierżawie klienta, a niektóre z nich są ściągane do lokalizacji centralnej.

Trzecia architektura między tymi dwiema opcjami. Jest on oparty na pierwszej architekturze rozproszonej, w której dzienniki są lokalne dla każdego klienta, ale przy użyciu pewnego mechanizmu można utworzyć centralne repozytorium dzienników. Część dzienników jest pobierana do centralnej lokalizacji na potrzeby raportowania i analizy. Ta część może być małą liczbą typów danych lub podsumowaniem działania, na przykład Statystyka dzienna.

Istnieją dwie opcje implementowania dzienników w centralnej lokalizacji:

1. Centralny obszar roboczy: dostawca usług może utworzyć obszar roboczy w swojej dzierżawie i użyć skryptu, który korzysta z [interfejsu API zapytania](https://dev.loganalytics.io/) z [interfejsem API zbierania danych](./data-collector-api.md) , aby przenieść dane z różnych obszarów roboczych do tej centralnej lokalizacji. Inną opcją, inną niż skrypt, jest użycie [Azure Logic Apps](../../logic-apps/logic-apps-overview.md).

2. Power BI jako centralnej lokalizacji: Power BI może pełnić rolę centralnej lokalizacji, gdy różne obszary robocze eksportują do niego dane przy użyciu integracji między obszarem roboczym Log Analytics a [Power BI](./powerbi.md).

## <a name="next-steps"></a>Następne kroki

* Automatyzowanie tworzenia i konfigurowania obszarów roboczych przy użyciu [szablonów Menedżer zasobów](template-workspace-configuration.md)

* Automatyzowanie tworzenia obszarów roboczych przy użyciu [programu PowerShell](./powershell-workspace-configuration.md)

* Korzystanie z [alertów](./alerts-overview.md) w celu integracji z istniejącymi systemami

* Generowanie raportów podsumowujących przy użyciu [Power BI](./powerbi.md)

* Dołączanie klientów do [zarządzania zasobami delegowanymi przez platformę Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).
