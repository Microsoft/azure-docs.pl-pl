---
title: Wbudowane definicje zasad dla Azure DDoS Protection Standardowa
description: Wyświetla Azure Policy wbudowanych definicji zasad dla Azure DDoS Protection Standardowa. Te wbudowane definicje zasad zapewniają typowe podejścia do zarządzania zasobami platformy Azure.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: b58dddef0aa63aec525e039f333a7a785c32d23e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875651"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Azure Policy wbudowane definicje dla Azure DDoS Protection Standardowa

Ta strona jest indeksem [Azure Policy](../governance/policy/overview.md) wbudowanych definicji zasad dla Azure DDoS Protection Standardowa. Aby uzyskać Azure Policy wbudowanych dla innych usług, [zobacz Azure Policy wbudowane definicje](../governance/policy/samples/built-in-policies.md).

Nazwa każdej wbudowanej definicji zasad jest linkiem do definicji zasad w Azure Portal. Użyj linku w kolumnie **Wersja,** aby wyświetlić źródło w [repozytorium Azure Policy GitHub.](https://github.com/Azure/azure-policy)

## <a name="azure-ddos-protection-standard"></a>Usługa Azure DDoS Protection w warstwie Standardowa

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Sieci wirtualne powinny być chronione przez Azure DDoS Protection Standardowa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Chroń sieci wirtualne przed atakami wolumetryzowymi i protokółowymi za pomocą Azure DDoS Protection Standardowa. Aby uzyskać więcej informacji, odwiedź stronę [https://aka.ms/ddosprotectiondocs](https://aka.ms/ddosprotectiondocs) .|Modyfikowanie, inspekcja, wyłączone|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[Publiczne adresy IP powinny mieć włączone dzienniki zasobów dla Azure DDoS Protection Standardowa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Włącz dzienniki zasobów dla publicznych adresów IP w ustawieniach diagnostycznych, aby przesyłać strumieniowo do obszaru roboczego usługi Log Analytics. Uzyskaj szczegółowy wgląd w ruch ataku i akcje podejmowane w celu ograniczenia ataków DDoS za pośrednictwem powiadomień, raportów i dzienników przepływu.|AuditIfNotExists, DeployIfNotExists, Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>Następne kroki

- Zobacz wbudowane elementy w [repozytorium GitHub usługi Azure Policy](https://github.com/Azure/azure-policy).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../governance/policy/concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../governance/policy/concepts/effects.md).
