---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b7a29416b8a53a4a78dd915d4ba350a431ffdff0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870227"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja Azure Spring Cloud, w których śledzenie rozproszone nie jest włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Narzędzia do śledzenia rozproszonego w Azure Spring Cloud umożliwiają debugowanie i monitorowanie złożonych połączeń między mikrousługami w aplikacji. Narzędzia do śledzenia rozproszonego powinny być włączone i w dobrej kondycji. |Inspekcja, wyłączone |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure Spring Cloud należy używać wstrzykiwania sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure Spring Cloud powinny używać iniekcji sieci wirtualnej do następujących celów: 1. Odizoluj Azure Spring Cloud od Internetu. 2. Umożliwianie Azure Spring Cloud z systemami w lokalnych centrach danych lub usłudze platformy Azure w innych sieciach wirtualnych. 3. Umożliwianie klientom kontrolowania przychodzącej i wychodzącej komunikacji sieciowej na potrzeby Azure Spring Cloud. |Inspekcja, Wyłączone, Odmów |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
