---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 175db3e437e55ab176a9eaa056b501c7742a515c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98046921"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja wystąpień chmury wiosennej platformy Azure, w których nie włączono śledzenia rozproszonego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Rozproszone narzędzia śledzenia w chmurze Azure wiosennej umożliwiają debugowanie i monitorowanie złożonych połączeń między mikrousługami w aplikacji. Narzędzia śledzenia rozproszonego powinny być włączone i w dobrej kondycji. |Inspekcja, wyłączona |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Chmura ze sprężyną na platformie Azure powinna używać iniekcji sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Wystąpienia chmur wirtualnych platformy Azure powinny używać iniekcji sieci wirtualnej w następujących celach: 1. Izoluj chmurę wiosenną platformy Azure z Internetu. 2. Włącz chmurę ze sprężyną Azure, aby korzystać z systemów w lokalnych centrach danych lub usłudze platformy Azure w innych sieciach wirtualnych. 3. Umożliwianie klientom kontrolowania komunikacji przychodzącej i wychodzącej w chmurze platformy Azure. |Inspekcja, wyłączona, Odmów |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
