---
title: Azure Policy podstawy zabezpieczeń dla usługi Azure Security Baseline
description: Linia bazowa zabezpieczeń Azure Policy zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e8915e1c15972341befd176b412925f4e87c94f6
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201454"
---
# <a name="azure-policy-security-baseline-for-azure-security-benchmark"></a>Azure Policy podstawy zabezpieczeń dla usługi Azure Security Baseline

Ta linia bazowa zabezpieczeń stosuje wskazówki z [testu porównawczego zabezpieczeń platformy Azure](../../../security/benchmarks/overview.md) do Azure Policy. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **domen zgodności** i **kontroli zabezpieczeń** zdefiniowanych przez test wydajności Azure Security i powiązane wskazówki dotyczące Azure Policy. **Kontrolki** nie mają zastosowania do Azure Policy zostały wykluczone. Aby dowiedzieć się, jak Azure Policy całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Azure Policy pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Aby uzyskać Mapowanie formantów testów wydajnościowych platformy Azure do wbudowanych definicji zasad za pośrednictwem inicjatywy wbudowanej, zobacz Zgodność z [przepisami: test porównawczy zabezpieczeń platformy Azure](../samples/azure-security-benchmark.md).

Azure Policy używa warunku _własności_ zamiast _odpowiedzialności_. Aby uzyskać szczegółowe informacje na temat _własności_, zobacz [definicje zasad Azure Policy](./definition-structure.md#type) i [współdzielona odpowiedzialność w chmurze](../../../security/fundamentals/shared-responsibility.md).


## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: rejestrowanie i monitorowanie](../../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Azure Policy korzysta z dzienników aktywności, które są włączane automatycznie, aby uwzględnić Źródło zdarzenia, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy.

* [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../../../azure-monitor/platform/diagnostic-settings.md)

* [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../../../azure-monitor/platform/platform-logs-overview.md)


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: tożsamość i kontrola dostępu](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych. 

Możesz również włączyć rozwiązanie "just-in-Time/" tylko do odczytu, używając [Azure AD Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) ról uprzywilejowanych lub [Azure Resource Manager](../../../azure-resource-manager/management/overview.md).


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Używaj dedykowanych maszyn (uprzywilejowany dostęp do stacji roboczych) dla wszystkich zadań administracyjnych

**Wskazówki**: Użyj dostępem uprzywilejowanym (uprzywilejowanych stacji roboczych dostępu) za pomocą usługi MFA skonfigurowanej do logowania się i konfigurowania zasobów platformy Azure.

* [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Jak włączyć usługę MFA na platformie Azure](../../../active-directory/authentication/howto-mfa-getstarted.md)


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Ochrona danych](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

**Wskazówki**: Kontrola dostępu do Azure Policy przy użyciu kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure.

* [Uprawnienia usługi Azure RBAC w Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy)

* [Jak skonfigurować usługę Azure RBAC](../../../role-based-access-control/role-assignments-portal.md)


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikami aktywności, aby utworzyć alerty, kiedy zmiany są wykonywane w Azure Policy.

* [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../../../azure-monitor/platform/alerts-activity-log.md)


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz [Kontrola zabezpieczeń: Spis i zarządzanie zasobami](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię. Użyj efektu Azure Policy _Modyfikuj_ , aby raportować i wymuszać zgodność i spójne zarządzanie tagami.

* [Samouczek: Tworzenie zasad i zarządzanie nimi](../tutorials/create-and-manage.md)

* [Samouczek: zarządzanie tagami tagów](../tutorials/govern-tags.md)


**Monitorowanie usługi Azure Security Center**: Obecnie niedostępne

**Odpowiedzialność**: Klient

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Utwórz spis zatwierdzonych definicji zasad i przypisań zasad zgodnie z potrzebami organizacji.

**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji.

* [Jak skonfigurować usługę Azure Policy i zarządzać nią](../tutorials/create-and-manage.md)


**Monitorowanie usługi Azure Security Center**: Nie dotyczy

**Odpowiedzialność**: Klient

## <a name="next-steps"></a>Następne kroki

- Zobacz [test porównawczy zabezpieczeń platformy Azure](../../../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../../../security/benchmarks/security-baselines-overview.md)
