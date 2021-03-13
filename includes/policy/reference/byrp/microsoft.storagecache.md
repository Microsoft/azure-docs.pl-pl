---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2471564ead93f8b7aa678eb7eece07abea30b96
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "103419911"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konta pamięci podręcznej HPC powinny używać klucza zarządzanego przez klienta do szyfrowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Zarządzaj szyfrowaniem w dalszej części pamięci podręcznej platformy Azure HPC przy użyciu kluczy zarządzanych przez klienta. Domyślnie dane klienta są szyfrowane przy użyciu kluczy zarządzanych przez usługę, ale klucze zarządzane przez klienta są często wymagane do spełnienia standardów zgodności z przepisami. Klucze zarządzane przez klienta umożliwiają szyfrowanie danych przy użyciu klucza Azure Key Vault utworzonego i należącego do użytkownika. Użytkownik ma pełną kontrolę i odpowiedzialność za kluczowy cykl życia, włącznie z rotacją i zarządzaniem. |Inspekcja, wyłączona, Odmów |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
