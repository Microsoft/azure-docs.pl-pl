---
title: Wyświetlanie działań dostawcy usług
description: Klienci mogą wyświetlać zarejestrowane działanie, aby zobaczyć akcje wykonywane przez dostawców usług za pomocą funkcji zarządzania zasobami delegowanymi przez platformę Azure.
ms.date: 01/15/2020
ms.topic: how-to
ms.openlocfilehash: 42af2aa7fca5031eb50bd421a5207320858b70cf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636448"
---
# <a name="view-service-provider-activity"></a>Wyświetlanie działań dostawcy usług

Klienci, którzy mają delegowane subskrypcje do zarządzania zasobami delegowanymi przez platformę Azure, mogą [wyświetlać dane dziennika aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md) , aby zobaczyć wszystkie wykonane akcje. Zapewnia to klientom pełny wgląd w operacje wykonywane przez dostawców usług za pomocą delegowania zasobów platformy Azure, a także operacji wykonywanych przez użytkowników w ramach dzierżawy Azure Active Directory (Azure AD) klienta.

> [!TIP]
> Firma Microsoft udostępnia również wbudowaną Azure Policy definicję zasad służącą do inspekcji delegowania zakresów do dzierżawy zarządzającej. Aby uzyskać więcej informacji, zobacz [Inspekcja delegowania w środowisku](view-manage-service-providers.md#audit-delegations-in-your-environment).

## <a name="view-activity-log-data"></a>Wyświetl dane dziennika aktywności

[Dziennik aktywności można wyświetlić](../../azure-monitor/platform/activity-log-view.md) z menu **Monitoruj** w Azure Portal. Aby ograniczyć wyniki do określonej subskrypcji, użyj filtrów, aby wybrać konkretną subskrypcję. Możesz również [wyświetlać i pobierać zdarzenia dziennika aktywności](../../azure-monitor/platform/activity-log-view.md) .

> [!NOTE]
> Użytkownicy w dzierżawie dostawcy usług mogą wyświetlać wyniki dziennika aktywności dla delegowanej subskrypcji w dzierżawie klienta, jeśli otrzymali rolę [czytelnika](../../role-based-access-control/built-in-roles.md#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika), gdy subskrypcja została dołączona do funkcji zarządzania zasobami delegowanymi przez platformę Azure.

W dzienniku aktywności zobaczysz nazwę operacji i jej stan wraz z datą i godziną wykonania. **Zdarzenie zainicjowane przez** kolumnę pokazuje, który użytkownik wykonał operację, niezależnie od tego, czy był on użytkownikiem w dzierżawie dostawcy usług działającym przez delegowane zarządzanie zasobami platformy Azure, czy użytkownikiem w dzierżawie klienta. Należy zauważyć, że nazwa użytkownika jest wyświetlana zamiast dzierżawy lub roli, do której użytkownik został przypisany do tej subskrypcji.

Zarejestrowane działanie jest dostępne w Azure Portal przez ostatnie 90 dni. Aby dowiedzieć się, jak przechowywać te dane przez dłużej niż 90 dni, zobacz [zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym log Analytics](../../azure-monitor/platform/activity-log-collect.md).

> [!NOTE]
> Użytkownicy dostawcy usług są wyświetlani w dzienniku aktywności, ale nie są one wyświetlane w **Access Control (IAM)** ani podczas pobierania informacji o przypisywaniu ról za pośrednictwem interfejsów API.

## <a name="set-alerts-for-critical-operations"></a>Ustawianie alertów dla operacji krytycznych

Aby zachować świadomość krytycznych operacji wykonywanych przez dostawców usług (lub użytkowników w Twojej dzierżawie), zalecamy tworzenie [alertów dziennika aktywności](../../azure-monitor/platform/activity-log-alerts.md). Na przykład możesz chcieć śledzić wszystkie akcje administracyjne dla subskrypcji lub otrzymywać powiadomienia o usunięciu dowolnej maszyny wirtualnej w danej grupie zasobów. Podczas tworzenia alertów będą one obejmować akcje wykonywane przez użytkowników w dzierżawie klienta, a także w innych dzierżawach.

Aby uzyskać więcej informacji, zobacz [tworzenie alertów dziennika aktywności i zarządzanie nimi](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Tworzenie zapytań dziennika

Możesz tworzyć zapytania, aby analizować zarejestrowane działanie lub skupić się na określonych elementach. Na przykład, być może Inspekcja wymaga, aby zgłosić wszystkie akcje na poziomie administracyjnym wykonywane w ramach subskrypcji. Można utworzyć zapytanie, aby odfiltrować tylko te akcje i sortować wyniki według użytkownika, daty lub innej wartości.

Aby uzyskać więcej informacji, zobacz [Omówienie zapytań dzienników w Azure monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Azure monitor](../../azure-monitor/index.yml).
- Dowiedz się, jak [wyświetlać oferty dostawców usług i zarządzać nimi](view-manage-service-providers.md) w Azure Portal.