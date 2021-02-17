---
title: Wyświetlanie działań dostawcy usług
description: Klienci mogą wyświetlać zarejestrowane działanie, aby zobaczyć akcje wykonywane przez dostawców usług za pomocą funkcji zarządzania zasobami delegowanymi przez platformę Azure.
ms.date: 12/11/2020
ms.topic: how-to
ms.openlocfilehash: 40deca310eea2fc9618cfc83d34caadcf2b2b14d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571737"
---
# <a name="view-service-provider-activity"></a>Wyświetlanie działań dostawcy usług

Klienci, którzy mają delegowane subskrypcje [usługi Azure Lighthouse](../overview.md) , mogą [wyświetlać dane dziennika aktywności platformy Azure](../../azure-monitor/essentials/platform-logs-overview.md) , aby zobaczyć wszystkie wykonane akcje. Zapewnia to klientom pełny wgląd w operacje wykonywane przez dostawców usług za pomocą [delegowania zasobów platformy Azure](../concepts/azure-delegated-resource-management.md), a także operacji wykonywanych przez użytkowników w ramach dzierżawy Azure Active Directory (Azure AD) klienta.

> [!TIP]
> Udostępniamy również Azure Policy wbudowane definicje zasad w celu [ograniczenia delegowania do określonych dzierżawców zarządzania](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json) i [inspekcji przedelegowania zakresów do dzierżawy zarządzającej](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). Aby uzyskać więcej informacji, zobacz [Inspekcja delegowania w środowisku](view-manage-service-providers.md#audit-delegations-in-your-environment).

## <a name="view-activity-log-data"></a>Wyświetlanie danych dziennika aktywności

[Dziennik aktywności można wyświetlić](../../azure-monitor/essentials/activity-log.md#view-the-activity-log) z menu **Monitoruj** w Azure Portal. Aby ograniczyć wyniki do określonej subskrypcji, użyj filtrów, aby wybrać konkretną subskrypcję. Możesz również [wyświetlać i pobierać zdarzenia dziennika aktywności](../../azure-monitor/essentials/activity-log.md#view-the-activity-log) .

> [!NOTE]
> Użytkownicy w dzierżawie dostawcy usług mogą wyświetlać wyniki dziennika aktywności dla delegowanej subskrypcji w dzierżawie klienta, jeśli otrzymali rolę [czytelnika](../../role-based-access-control/built-in-roles.md#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika), gdy subskrypcja została dołączona do usługi Azure Lighthouse.

W dzienniku aktywności zobaczysz nazwę operacji i jej stan wraz z datą i godziną wykonania. **Zdarzenie zainicjowane przez** kolumnę pokazuje, który użytkownik wykonał operację, niezależnie od tego, czy był użytkownikiem w dzierżawie dostawcy usług działającym za pomocą usługi Azure Lighthouse, czy użytkownikiem w dzierżawie klienta. Należy zauważyć, że nazwa użytkownika jest wyświetlana zamiast dzierżawy lub roli, do której użytkownik został przypisany do tej subskrypcji.

Zarejestrowane działanie jest dostępne w Azure Portal przez ostatnie 90 dni. Aby dowiedzieć się, jak przechowywać te dane przez dłużej niż 90 dni, zobacz [zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym log Analytics](../../azure-monitor/essentials/activity-log.md).

> [!NOTE]
> Użytkownicy dostawcy usług są wyświetlani w dzienniku aktywności, ale nie są one wyświetlane w **Access Control (IAM)** ani podczas pobierania informacji o przypisywaniu ról za pośrednictwem interfejsów API.

## <a name="set-alerts-for-critical-operations"></a>Ustawianie alertów dla operacji krytycznych

Aby zachować świadomość krytycznych operacji wykonywanych przez dostawców usług (lub użytkowników w Twojej dzierżawie), zalecamy tworzenie [alertów dziennika aktywności](../../azure-monitor/alerts/activity-log-alerts.md). Na przykład możesz chcieć śledzić wszystkie akcje administracyjne dla subskrypcji lub otrzymywać powiadomienia o usunięciu dowolnej maszyny wirtualnej w danej grupie zasobów. Podczas tworzenia alertów będą one obejmować akcje wykonywane przez użytkowników w dzierżawie klienta, a także w innych dzierżawach.

Aby uzyskać więcej informacji, zobacz [tworzenie alertów dziennika aktywności i zarządzanie nimi](../../azure-monitor/alerts/alerts-activity-log.md).

## <a name="create-log-queries"></a>Tworzenie zapytań dziennika

Możesz tworzyć zapytania, aby analizować zarejestrowane działanie lub skupić się na określonych elementach. Na przykład, być może Inspekcja wymaga, aby zgłosić wszystkie akcje na poziomie administracyjnym wykonywane w ramach subskrypcji. Można utworzyć zapytanie, aby odfiltrować tylko te akcje i sortować wyniki według użytkownika, daty lub innej wartości.

Aby uzyskać więcej informacji, zobacz [Omówienie zapytań dzienników w Azure monitor](../../azure-monitor/logs/log-query-overview.md).

## <a name="view-user-activity-across-domains"></a>Wyświetl aktywność użytkowników w różnych domenach

Można wyświetlać działania poszczególnych użytkowników w wielu domenach przy użyciu [dzienników aktywności według](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) przykładowego skoroszytu.

Wyniki można filtrować według nazwy domeny. Można również zastosować dodatkowe filtry, takie jak kategoria, poziom lub Grupa zasobów.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Azure monitor](../../azure-monitor/index.yml).
- Dowiedz się, jak [wyświetlać oferty dostawców usług i zarządzać nimi](view-manage-service-providers.md) w Azure Portal.
