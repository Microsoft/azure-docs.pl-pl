---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7839d6be9eab20253c1518a550bcb2b6ebd3dcc4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867061"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Box zadania powinny włączać podwójne szyfrowanie danych w spoczynku na urządzeniu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Włącz drugą warstwę programowego szyfrowania danych w spoczynku na urządzeniu. Urządzenie jest już chronione za pośrednictwem Advanced Encryption Standard 256-bitowego dla danych w spoczynku. Ta opcja dodaje drugą warstwę szyfrowania danych. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Data Box powinny używać klucza zarządzanego przez klienta do szyfrowania hasła odblokowania urządzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Użyj klucza zarządzanego przez klienta, aby kontrolować szyfrowanie hasła odblokowania urządzenia dla Azure Data Box. Klucze zarządzane przez klienta ułatwiają również zarządzanie dostępem do hasła odblokowania urządzenia przez usługę urządzenie Data Box, aby przygotować urządzenie i skopiować dane w zautomatyzowany sposób. Dane na samym urządzeniu są już szyfrowane w spoczynku przy użyciu szyfrowania Advanced Encryption Standard 256-bitowego, a hasło odblokowania urządzenia jest domyślnie szyfrowane przy użyciu klucza zarządzanego przez firmę Microsoft. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
