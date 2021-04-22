---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: daac33f853d0f482c5a259e8135ed905d3154d08
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867026"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Explorer danych w spoczynku powinno używać klucza zarządzanego przez klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Włączenie szyfrowania danych w spoczynku przy użyciu klucza zarządzanego przez klienta w klastrze Azure Data Explorer zapewnia dodatkową kontrolę nad kluczem używanym przez szyfrowanie danych w spoczynku. Ta funkcja jest często stosowana w przypadku klientów ze specjalnymi wymaganiami zgodności i wymaga Key Vault zarządzania kluczami. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Szyfrowanie dysków powinno być włączone na Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |Włączenie szyfrowania dysków pomaga chronić dane zgodnie z wymaganiami organizacji w zakresie zabezpieczeń i zgodności. |Inspekcja, Odmowa, Wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Na urządzeniach powinno być włączone podwójne Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |Włączenie podwójnego szyfrowania pomaga chronić dane zgodnie z wymaganiami organizacji w zakresie zabezpieczeń i zgodności. Po włączeniu podwójnego szyfrowania dane na koncie magazynu są szyfrowane dwukrotnie, raz na poziomie usługi i raz na poziomie infrastruktury, przy użyciu dwóch różnych algorytmów szyfrowania i dwóch różnych kluczy. |Inspekcja, Odmowa, Wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[Należy włączyć wstrzykiwanie sieci wirtualnej dla Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Zabezpiecz obwód sieci za pomocą iniekcji sieci wirtualnej, co pozwala wymuszać reguły sieciowej grupy zabezpieczeń, łączyć się ze środowiskiem lokalnym i zabezpieczać źródła połączeń danych przy użyciu punktów końcowych usługi. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |
