---
title: Wbudowane definicje zasad dla Azure Security Center
description: Wyświetla Azure Policy wbudowane definicje zasad dla Azure Security Center. Te wbudowane definicje zasad zapewniają typowe podejścia do zarządzania zasobami platformy Azure.
ms.date: 04/14/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 34a839eb6996f2b37c07bb2e54f81a1d21044fa9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502686"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Wbudowane definicje usługi Azure Policy dla usługi Azure Security Center

Ta strona jest indeksem [Azure Policy](../governance/policy/overview.md) wbudowanych definicji zasad związanych z Azure Security Center. Dostępne są następujące grupy definicji zasad:

- Grupa [inicjatyw](#azure-security-center-initiatives) wyświetla listę Azure Policy inicjatywy w kategorii "Security Center".
- Domyślna [grupa inicjatyw](#azure-security-center-initiatives) zawiera listę wszystkich Azure Policy, które są częścią domyślnej inicjatywy usługi Security Center, Azure [Security Benchmark.](../security/benchmarks/introduction.md) Ten autorstwo firmy Microsoft, szeroko ceniony test porównawczy, opiera się na mechanizmach kontroli nist [(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) z naciskiem na zabezpieczenia skoncentrowane na chmurze.
- Grupa [kategorii](#azure-security-center-category) zawiera listę wszystkich Azure Policy kategorii "Security Center".

Aby uzyskać więcej informacji na temat zasad zabezpieczeń, zobacz [Praca z zasadami zabezpieczeń](./tutorial-security-policy.md). Aby uzyskać Azure Policy wbudowanych funkcji dla innych usług, [Azure Policy wbudowane definicje](../governance/policy/samples/built-in-policies.md).

Nazwa każdej wbudowanej definicji zasad łączy się z definicją zasad w Azure Portal. Użyj linku w kolumnie **Wersja,** aby wyświetlić źródło w [repozytorium Azure Policy GitHub.](https://github.com/Azure/azure-policy)

## <a name="azure-security-center-initiatives"></a>Azure Security Center inicjatyw

Aby dowiedzieć się więcej o wbudowanych inicjatywach monitorowanych przez Security Center, zobacz następującą tabelę:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Security Center domyślna inicjatywa firmy Security Center (Test porównawczy zabezpieczeń platformy Azure)

Aby dowiedzieć się więcej o wbudowanych zasadach monitorowanych przez Security Center, zobacz następującą tabelę:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure Security Center kategorii

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Następne kroki

W tym artykule zadano informacje o definicjach Azure Policy zabezpieczeń w Security Center. Aby dowiedzieć się więcej na temat inicjatyw, zasad i ich związku z zaleceniami firmy Security Center, zobacz Co to są zasady zabezpieczeń, inicjatywy [i zalecenia?](security-policy-concept.md).
