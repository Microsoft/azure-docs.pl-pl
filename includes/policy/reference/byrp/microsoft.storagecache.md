---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f14136e77bb0018ea5e15f6e07fa7a633addf49f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876036"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[HPC Cache powinny używać klucza zarządzanego przez klienta do szyfrowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Zarządzanie szyfrowaniem w pozostałych Azure HPC Cache kluczami zarządzanymi przez klienta. Domyślnie dane klientów są szyfrowane za pomocą kluczy zarządzanych przez usługę, ale klucze zarządzane przez klienta są często wymagane do spełnienia standardów zgodności z przepisami. Klucze zarządzane przez klienta umożliwiają szyfrowanie danych przy użyciu klucza Azure Key Vault utworzonego i należącego do Ciebie. Masz pełną kontrolę i odpowiedzialność za cykl życia klucza, w tym rotację i zarządzanie. |Inspekcja, Wyłączone, Odmów |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
