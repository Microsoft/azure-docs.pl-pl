---
title: Wbudowane definicje zasad dla Azure DDoS Protection Standard
description: Wyświetla listę wbudowanych definicji zasad Azure Policy dla Azure DDoS Protection Standard. Te wbudowane definicje zasad zapewniają typowe podejścia do zarządzania zasobami platformy Azure.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 6a0496740fbd82090ba7199ca3e064f5bd2bf7c5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108269"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Azure Policy wbudowane definicje Azure DDoS Protection Standard

Ta strona jest indeksem [Azure Policy](../governance/policy/overview.md) wbudowanych definicji zasad dla Azure DDoS Protection Standard. Aby uzyskać dodatkowe Azure Policy wbudowane dla innych usług, zobacz [Azure Policy wbudowane definicje](../governance/policy/samples/built-in-policies.md).

Nazwa każdej wbudowanej definicji zasad łączy się z definicją zasad w Azure Portal. Użyj linku w kolumnie **wersja** , aby wyświetlić źródło w [Azure Policy repozytorium GitHub](https://github.com/Azure/azure-policy).

## <a name="azure-ddos-protection-standard"></a>Usługa Azure DDoS Protection w warstwie Standardowa

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Sieci wirtualne powinny być chronione przez Azure DDoS Protection Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Ochrona sieci wirtualnych przed atakami typu "i" przy użyciu protokołu Azure DDoS Protection Standard. Aby uzyskać więcej informacji, odwiedź stronę [https://aka.ms/ddosprotectiondocs](https://aka.ms/ddosprotectiondocs) .|Modyfikowanie, inspekcja, wyłączone|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[Publiczne adresy IP powinny mieć włączone dzienniki zasobów dla Azure DDoS Protection Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Włącz dzienniki zasobów dla publicznych adresów IP w ustawieniach diagnostycznych, aby przesyłać strumieniowo do obszaru roboczego Log Analytics. Zapoznaj się z szczegółowym wglądem w ruch i działania podejmowane w celu ograniczenia ataków DDoS za pośrednictwem powiadomień, raportów i dzienników przepływów.|AuditIfNotExists, DeployIfNotExists, wyłączone|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>Następne kroki

- Zobacz wbudowane elementy w [repozytorium GitHub usługi Azure Policy](https://github.com/Azure/azure-policy).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../governance/policy/concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../governance/policy/concepts/effects.md).