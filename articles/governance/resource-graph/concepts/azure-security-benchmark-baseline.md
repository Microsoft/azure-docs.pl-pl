---
title: Podstawowa baza zabezpieczeń Azure dla wykresu zasobów platformy Azure
description: Linia bazowa zabezpieczeń grafu zasobów platformy Azure zawiera wskazówki i zasoby dotyczące procedur związanych z wdrażaniem zaleceń dotyczących zabezpieczeń określonych w teście zabezpieczeń platformy Azure.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad8968fdb6548da29a031f0e44bd3671f67b5553
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557721"
---
# <a name="azure-security-baseline-for-azure-resource-graph"></a>Podstawowa baza zabezpieczeń Azure dla wykresu zasobów platformy Azure

Ta linia bazowa zabezpieczeń ma zastosowanie do grafu zasobów platformy Azure ze wskazówkami dotyczącymi [usługi Azure Security test w wersji 1,0](../../../security/benchmarks/overview-v1.md) . Test porównawczy zabezpieczeń platformy Azure zawiera zalecenia dotyczące sposobu zabezpieczania rozwiązań w chmurze na platformie Azure.
Zawartość jest pogrupowana według **kontroli zabezpieczeń** zdefiniowanych przez program Azure Security test i powiązane wskazówki dotyczące wykresu zasobów platformy Azure. Wykluczenia **nie mają** zastosowania do grafu zasobów platformy Azure.

 
Aby dowiedzieć się, jak usługa Azure Resource Graph jest całkowicie mapowana na test porównawczy zabezpieczeń Azure, zapoznaj się z [pełnym plikiem mapowania linii bazowej zabezpieczeń grafu zasobów platformy Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-and-access-control"></a>Tożsamość i kontrola dostępu

*Aby uzyskać więcej informacji, zobacz informacje o [teście zabezpieczeń Azure: Identity i Access Control](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regularnie Przeglądaj i Uzgodnij dostęp użytkowników

**Wskazówki**: usługa Azure Resource Graph zapewnia dostęp do typów zasobów i właściwości na podstawie kontroli dostępu opartej na ROLACH (RBAC) platformy Azure. Regularnie przeprowadzaj inspekcję i przeglądanie dostępu udzielanego do podmiotów zabezpieczeń (użytkowników, grup i kont usług), aby upewnić się, że zapytania zwracają wyniki dla odpowiednich zasobów.

- [Uprawnienia w usłudze Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

- [Jak korzystać z przeglądów dostępu do tożsamości platformy Azure](../../../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="data-protection"></a>Ochrona danych

*Aby uzyskać więcej informacji, zobacz [Test porównawczy platformy Azure: ochrona danych](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów 

**Wskazówki**: korzystanie z usługi Azure RBAC do kontrolowania dostępu do danych i zasobów. Aby korzystać z grafu zasobów platformy Azure, musisz mieć również odpowiedni dostęp do zasobów, które chcesz zbadać. Ten dostęp powinien być objęty zakresem tylko do odczytu i może być przyznany tylko do wymaganego personelu.

- [Uprawnienia w usłudze Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

- [Jak skonfigurować usługę Azure RBAC](../../../role-based-access-control/role-assignments-rest.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="inventory-and-asset-management"></a>Zarządzanie magazynem i zasobami

*Aby uzyskać więcej informacji, zobacz temat [Azure Security test: Inventory and Asset Management](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Użyj rozwiązania automatycznego odnajdywania zasobów

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania wszystkich obsługiwanych zasobów w ramach subskrypcji, grup zarządzania i dzierżawców. Upewnij się, że masz odpowiednie uprawnienia w dzierżawie i że można wyliczyć wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

- [Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure](../first-query-portal.md)

- [Opis kontroli RBAC platformy Azure](../../../role-based-access-control/overview.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: Definiowanie i konserwowanie spisu zatwierdzonych zasobów platformy Azure

**Wskazówki**: Tworzenie spisu zatwierdzonych zasobów platformy Azure oraz zatwierdzonego oprogramowania do zasobów obliczeniowych zgodnie z potrzebami organizacji. Użyj grafu zasobów platformy Azure, aby wykonać zapytanie o zatwierdzone zasoby platformy Azure i historię zmian (wersja zapoznawcza) w celu przejrzenia migawek i zobacz, co zmieniono.

- [Tworzenie zapytań dotyczących zasobów platformy Azure za pomocą usługi Azure Resource Graph](../first-query-portal.md)

- [Uzyskiwanie zmian zasobów](../how-to/get-resource-changes.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

**Wskazówki**: Użyj grafu zasobów platformy Azure do wykonywania zapytań i odnajdywania zasobów w ramach subskrypcji, grup zarządzania i dzierżawców. Upewnij się, że wszystkie zasoby platformy Azure w środowisku są zatwierdzone.

- [Tworzenie zapytań dotyczących zasobów platformy Azure za pomocą usługi Azure Resource Graph](../first-query-portal.md)

- [Przykłady: początkowe zapytania dla wykresu zasobów platformy Azure](../samples/starter.md)

**Odpowiedzialność**: Klient

**Monitorowanie Azure Security Center**: brak

## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie testu porównawczego zabezpieczeń platformy Azure w wersji 2](../../../security/benchmarks/overview.md)
- Dowiedz się więcej o [punktach odniesienia zabezpieczeń platformy Azure](../../../security/benchmarks/security-baselines-overview.md)