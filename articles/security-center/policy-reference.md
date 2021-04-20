---
title: Wbudowane definicje zasad dla Azure Security Center
description: Wyświetla Azure Policy wbudowane definicje zasad dla Azure Security Center. Te wbudowane definicje zasad zapewniają typowe podejścia do zarządzania zasobami platformy Azure.
ms.date: 04/14/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 8023dcc29fe8ea3af853237e911524549e4d2bcb
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739642"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Wbudowane definicje usługi Azure Policy dla usługi Azure Security Center

Ta strona jest indeksem [Azure Policy](../governance/policy/overview.md) wbudowanych definicji zasad powiązanych z Azure Security Center. Dostępne są następujące grupy definicji zasad:

- Grupa [inicjatyw](#azure-security-center-initiatives) wyświetla listę Azure Policy inicjatywy w kategorii "Security Center".
- Domyślna [grupa inicjatyw](#azure-security-center-initiatives) zawiera listę wszystkich Azure Policy, które są częścią domyślnej inicjatywy Security Center, [Test porównawczy zabezpieczeń platformy Azure.](https://docs.microsoft.com/security/benchmark/azure/introduction) Ten autorstwa firmy Microsoft, szeroko ceniony test porównawczy opiera się na mechanizmach kontroli nist [(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) i National [Institute of Standards and Technology (NIST)](https://www.nist.gov/) z naciskiem na zabezpieczenia skoncentrowane na chmurze.
- Grupa [kategorii](#azure-security-center-category) wyświetla listę wszystkich Azure Policy w kategorii "Security Center".

Aby uzyskać więcej informacji na temat zasad zabezpieczeń, zobacz [Praca z zasadami zabezpieczeń](./tutorial-security-policy.md). Aby uzyskać Azure Policy wbudowanych dla innych usług, [zobacz Azure Policy wbudowane definicje](../governance/policy/samples/built-in-policies.md).

Nazwa każdej wbudowanej definicji zasad jest linkiem do definicji zasad w Azure Portal. Użyj linku w kolumnie **Wersja,** aby wyświetlić źródło w [repozytorium Azure Policy GitHub.](https://github.com/Azure/azure-policy)

## <a name="azure-security-center-initiatives"></a>Azure Security Center inicjatyw

Aby dowiedzieć się więcej o wbudowanych inicjatywach monitorowanych przez Security Center, zobacz następującą tabelę:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Security Center domyślna inicjatywa firmy (Test porównawczy zabezpieczeń platformy Azure)

Aby dowiedzieć się więcej o wbudowanych zasadach monitorowanych przez Security Center, zobacz następującą tabelę:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure Security Center kategorii

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Następne kroki

W tym artykule zadano informacje na temat Azure Policy zasad zabezpieczeń w Security Center. Aby dowiedzieć się więcej o inicjatywach, zasadach i związek z zaleceniami firmy Security Center, zobacz Co to są zasady zabezpieczeń, inicjatywy [i zalecenia?](security-policy-concept.md).
