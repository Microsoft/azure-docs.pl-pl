---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1d5006cf1afcf3e98b568a86e5a361bda954b0fe
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100411"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Usługa Azure Eksplorator danych Encryption w spoczynku powinna używać klucza zarządzanego przez klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Włączenie szyfrowania w spoczynku przy użyciu klucza zarządzanego przez klienta w klastrze usługi Azure Eksplorator danych zapewnia dodatkową kontrolę nad kluczem używanym przez szyfrowanie w spoczynku. Ta funkcja jest często przeznaczona dla klientów mających specjalne wymagania dotyczące zgodności i wymaga Key Vault do zarządzania kluczami. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Szyfrowanie dysków powinno być włączone na platformie Azure Eksplorator danych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |Włączenie szyfrowania dysków pomaga chronić dane i zabezpieczać je w celu spełnienia wymagań dotyczących zabezpieczeń i zgodności organizacji. |Inspekcja, Odmów, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Na platformie Eksplorator danych Azure należy włączyć podwójne szyfrowanie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |Włączenie podwójnego szyfrowania pomaga chronić dane i zabezpieczać je przed zobowiązaniami dotyczącymi zabezpieczeń i zgodności w organizacji. Po włączeniu podwójnego szyfrowania dane na koncie magazynu są szyfrowane dwa razy, raz na poziomie usługi i raz na poziomie infrastruktury, przy użyciu dwóch różnych algorytmów szyfrowania i dwóch różnych kluczy. |Inspekcja, Odmów, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[Dla usługi Azure Eksplorator danych należy włączyć iniekcję sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Zabezpiecz sieć obwodową przy użyciu iniekcji sieci wirtualnej, która umożliwia Wymuszanie reguł sieciowych grup zabezpieczeń, łączenie lokalnych i zabezpieczanie źródeł połączeń danych za pomocą punktów końcowych usługi. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |
