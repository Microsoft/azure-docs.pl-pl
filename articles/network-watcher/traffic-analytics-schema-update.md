---
title: Aktualizacja schematu Analiza ruchu platformy Azure — marzec 2020 | Microsoft Docs
description: Przykładowe zapytania z nowymi polami w schemacie Analiza ruchu.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: d7c4f1853ff8dcb9249ab6ec4f536e1f8cfa10e5
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018228"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>Przykładowe zapytania z nowymi polami w schemacie Analiza ruchu (aktualizacja schematu z sierpnia 2019)

[Schemat dziennika Analiza ruchu](./traffic-analytics-schema.md) zawiera następujące nowe pola: **SrcPublicIPs_s**, **DestPublicIPs_s** **NSGRule_s**. Nowe pola zawierają informacje o źródłowym i docelowym adresie IP oraz upraszczają zapytania.

W ciągu następnych kilku miesięcy następujące starsze pola będą przestarzałe: **VMIP_s**, **Subscription_g**, **Region_s**, **NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s**, **FlowCount_d**.

Poniższe trzy przykłady pokazują, jak zastąpić Stare pola nowymi.

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>Przykład 1: VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s i PublicIPs_s pól

Nie ma potrzeby wnioskowania przypadków źródłowych i docelowych z pola **FlowDirection_s** dla przepływów AzurePublic i ExternalPublic. Może być również nieodpowiedni do użycia pola **FlowDirection_s** dla sieciowego urządzenia wirtualnego.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```

## <a name="example-2-nsgrules_s-field"></a>Przykład 2: pole NSGRules_s

Stare pole używało formatu:

`<Index value 0)>|<NSG_ RuleName>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>`

Dane w sieciowej grupie zabezpieczeń (sieciowej grupy zabezpieczeń) nie są już agregowane. W zaktualizowanym schemacie **NSGList_s** zawiera tylko jeden sieciowej grupy zabezpieczeń. **NSGRules** również zawiera tylko jedną regułę. Firma Microsoft usunęła skomplikowane formatowanie tutaj i w innych polach, jak pokazano w przykładzie.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3-flowcount_d-field"></a>Przykład 3: pole FlowCount_d

Ponieważ nie zbieramy danych w sieciowej grupy zabezpieczeń, **FlowCount_d** jest po prostu:

**AllowedInFlows_d**  +  **DeniedInFlows_d**  +  **AllowedOutFlows_d**  +  **DeniedOutFlows_d**

Tylko jeden z czterech pól będzie różny od zera. Pozostałe trzy pola będą mieć wartość zero. Pola wypełniają, aby wskazać stan i liczbę na karcie sieciowej, w której został przechwycony przepływ.

Aby zilustrować te warunki:

- Jeśli przepływ był dozwolony, jedno z pól z prefiksem "dozwolone" zostanie wypełnione.
- Jeśli przepływ został odrzucony, jedno z pól z prefiksem "odmowa" zostanie wypełnione.
- Jeśli przepływ został przychodzący, zostanie wypełnione jedno z pól z sufiksem "InFlows_d".
- Jeśli przepływ został wychodzący, zostanie wypełnione jedno z pól z sufiksem "OutFlows_d".

W zależności od warunków wiemy, że jedno z czterech pól zostanie wypełnione.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać odpowiedzi na często zadawane pytania, zapoznaj się z tematem [Analiza ruchu często](traffic-analytics-faq.md)zadawanych pytań.
- Aby wyświetlić szczegółowe informacje na temat funkcji, zobacz [dokumentację Analiza ruchu](traffic-analytics.md).