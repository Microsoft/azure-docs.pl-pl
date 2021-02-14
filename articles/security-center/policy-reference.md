---
title: Wbudowane definicje zasad dla Azure Security Center
description: Wyświetla listę wbudowanych definicji zasad Azure Policy dla Azure Security Center. Te wbudowane definicje zasad zapewniają typowe podejścia do zarządzania zasobami platformy Azure.
ms.date: 02/09/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: ab6556bc391eb3136fa8c4ba40f5a5d71da956e7
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093062"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Wbudowane definicje usługi Azure Policy dla usługi Azure Security Center

Ta strona jest indeksem [Azure Policy](../governance/policy/overview.md) wbudowanych definicji zasad związanych z Azure Security Center. Dostępne są następujące grupy definicji zasad:

- Grupa [inicjatywy](#azure-security-center-initiatives) zawiera listę definicji Azure Policy inicjatyw w kategorii "Security Center".
- Grupa [inicjatywa domyślna](#azure-security-center-initiatives) zawiera wszystkie definicje Azure Policy, które są częścią inicjatywy Security Center, z których korzysta [usługa Azure Security](../security/benchmarks/introduction.md). Ten utworzony przez firmę Microsoft, powszechnie przestrzegany test porównawczy, jest oparty na kontrolkach z [centrum dla zabezpieczeń internetowych (CIS)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) , które koncentrują się na zabezpieczeniach skoncentrowanych na chmurze.
- Grupa [kategorii](#azure-security-center-category) zawiera wszystkie definicje Azure Policy w kategorii "Security Center".

Aby uzyskać więcej informacji na temat zasad zabezpieczeń, zobacz [Praca z zasadami zabezpieczeń](./tutorial-security-policy.md). Aby uzyskać dodatkowe Azure Policy wbudowane dla innych usług, zobacz [Azure Policy wbudowane definicje](../governance/policy/samples/built-in-policies.md).

Nazwa każdej wbudowanej definicji zasad łączy się z definicją zasad w Azure Portal. Użyj linku w kolumnie **wersja** , aby wyświetlić źródło w [Azure Policy repozytorium GitHub](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Inicjatywy Azure Security Center

Aby dowiedzieć się więcej na temat wbudowanych inicjatyw, które są monitorowane przez Security Center, zobacz następującą tabelę:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="azure-security-center-default-initiative"></a>Azure Security Center inicjatywy domyślnej

Aby dowiedzieć się więcej na temat wbudowanych zasad monitorowanych przez Security Center, zobacz następującą tabelę:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Kategoria Azure Security Center

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje dotyczące Azure Policy definicji zasad zabezpieczeń w programie Security Center. Aby dowiedzieć się więcej, zobacz następujące artykuły.

- Zobacz wbudowane elementy w [repozytorium GitHub usługi Azure Policy](https://github.com/Azure/azure-policy).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../governance/policy/concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../governance/policy/concepts/effects.md).
- [Azure Security Center Przewodnik planowania i](./security-center-planning-and-operations-guide.md)obsługi: informacje na temat planowania i zrozumienia zagadnień projektowych w programie Azure Security Center.
- [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](./security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
- [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](./security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
- [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](./security-center-partner-integration.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
- [Azure Policy](../governance/policy/overview.md): informacje na temat inspekcji i zarządzania zasobami platformy Azure.
