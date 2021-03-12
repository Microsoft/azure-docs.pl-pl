---
title: Wbudowane definicje zasad dla Azure Security Center
description: Wyświetla listę wbudowanych definicji zasad Azure Policy dla Azure Security Center. Te wbudowane definicje zasad zapewniają typowe podejścia do zarządzania zasobami platformy Azure.
ms.date: 03/10/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 4509ea09dc75a366e1360d0eeef481552f500f2f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613405"
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

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Domyślna inicjatywa Security Center (test zabezpieczeń platformy Azure)

Aby dowiedzieć się więcej na temat wbudowanych zasad monitorowanych przez Security Center, zobacz następującą tabelę:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Kategoria Azure Security Center

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje dotyczące Azure Policy definicji zasad zabezpieczeń w programie Security Center. Aby dowiedzieć się więcej o inicjatywach, zasadach i sposobach ich odnoszących się do Security Center zaleceń, zobacz [co to są zasady zabezpieczeń, inicjatywy i zalecenia?](security-policy-concept.md).
