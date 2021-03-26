---
title: Podstawa zabezpieczeń platformy Azure dla Azure Policy
description: Linia bazowa zabezpieczeń Azure Policy zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6e8bb4cf715c6cb8d0729399c1985376de18687b
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561291"
---
# <a name="azure-security-baseline-for-azure-policy"></a>Podstawa zabezpieczeń platformy Azure dla Azure Policy

Ta linia bazowa zabezpieczeń stosuje wskazówki z [testu porównawczego zabezpieczeń platformy Azure](../../../security/benchmarks/overview.md) do Azure Policy. Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure. Zawartość jest pogrupowana według **domen zgodności** i **kontroli zabezpieczeń** zdefiniowanych przez test wydajności Azure Security i powiązane wskazówki dotyczące Azure Policy. **Kontrolki** nie mają zastosowania do Azure Policy zostały wykluczone. Aby dowiedzieć się, jak Azure Policy całkowicie mapować do testu porównawczego zabezpieczeń platformy Azure, zobacz [pełny Azure Policy pliku mapowania linii bazowej zabezpieczeń](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Aby uzyskać Mapowanie formantów testów wydajnościowych platformy Azure do wbudowanych definicji zasad za pośrednictwem inicjatywy wbudowanej, zobacz Zgodność z [przepisami: test porównawczy zabezpieczeń platformy Azure](../samples/azure-security-benchmark.md).

Azure Policy używa warunku _własności_ zamiast _odpowiedzialności_. Aby uzyskać szczegółowe informacje na temat _własności_, zobacz [definicje zasad Azure Policy](./definition-structure.md#type) i [współdzielona odpowiedzialność w chmurze](../../../security/fundamentals/shared-responsibility.md).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: rejestrowanie i monitorowanie](../../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Włączanie rejestrowania inspekcji dla zasobów platformy Azure

**Wskazówki**: Azure Policy korzysta z dzienników aktywności, które są włączane automatycznie, aby uwzględnić Źródło zdarzenia, datę, użytkownika, sygnaturę czasową, adresy źródłowe, adresy docelowe i inne przydatne elementy.

- [Jak zbierać dzienniki platformy i metryki za pomocą Azure Monitor](../../../azure-monitor/essentials/diagnostic-settings.md)

- [Informacje o rejestrowaniu i różnych typach dzienników na platformie Azure](../../../azure-monitor/essentials/platform-logs-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Użyj dedykowanych kont administracyjnych

**Wskazówki**: Tworzenie standardowych procedur operacyjnych dotyczących korzystania z dedykowanych kont administracyjnych. Użyj Azure Security Center Zarządzanie tożsamościami i dostępem, aby monitorować liczbę kont administracyjnych. Możesz również włączyć rozwiązanie "just-in-Time" na poziomie dostępnym w [usłudze Azure Active Directory (Azure AD) Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) role uprzywilejowane lub [Azure Resource Manager](../../../azure-resource-manager/management/overview.md).

**Odpowiedzialność**: Klient

**Azure Security Center monitorowania**: [wzorzec zabezpieczeń platformy Azure](/azure/governance/policy/samples/azure-security-benchmark) jest domyślną inicjatywy zasad dla Security Center i jest podstawą dla [zaleceń Security Center](/azure/security-center/security-center-recommendations). Definicje Azure Policy powiązane z tym formantem są włączane automatycznie przez Security Center. Alerty związane z tym formantem mogą wymagać planu [usługi Azure Defender](/azure/security-center/azure-defender) dla powiązanych usług.

**Azure Policy wbudowane definicje — Microsoft. GuestConfiguration**:

[!INCLUDE [Resource Policy for Microsoft.GuestConfiguration 3.3](../../../../includes/policy/standards/asb/rp-controls/microsoft.guestconfiguration-3-3.md)]

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Korzystaj z bezpiecznych stacji roboczych zarządzanych przez platformę Azure na potrzeby zadań administracyjnych

**Wskazówki**: Użyj stacji roboczych dostępu uprzywilejowanego (dostępem uprzywilejowanym) z uwierzytelnianiem wieloskładnikowym skonfigurowanym do logowania się i konfigurowania zasobów platformy Azure.

- [Dowiedz się więcej o stacjach roboczych uprzywilejowanego dostępu](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak włączyć uwierzytelnianie wieloskładnikowe na platformie Azure](../../../active-directory/authentication/howto-mfa-getstarted.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów 

**Wskazówki**: Kontrola dostępu do Azure Policy przy użyciu kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure.

- [Uprawnienia usługi Azure RBAC w Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy)

- [Jak skonfigurować usługę Azure RBAC](../../../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

**Wskazówki**: Użyj Azure monitor z dziennikami aktywności, aby utworzyć alerty, kiedy zmiany są wykonywane w Azure Policy.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../../../azure-monitor/alerts/alerts-activity-log.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

**Wskazówki**: stosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię. Użyj efektu Azure Policy _Modyfikuj_ , aby raportować i wymuszać zgodność i spójne zarządzanie tagami.

- [Samouczek: Tworzenie zasad i zarządzanie nimi](../tutorials/create-and-manage.md)

- [Samouczek: zarządzanie tagami tagów](../tutorials/govern-tags.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Utwórz spis zatwierdzonych definicji zasad i przypisań zasad zgodnie z potrzebami organizacji.

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj Azure Policy, aby wprowadzić ograniczenia dotyczące typu zasobów, które mogą być tworzone w ramach subskrypcji.

- [Jak skonfigurować usługę Azure Policy i zarządzać nią](../tutorials/create-and-manage.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../../../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../../../security/benchmarks/security-baselines-overview.md)