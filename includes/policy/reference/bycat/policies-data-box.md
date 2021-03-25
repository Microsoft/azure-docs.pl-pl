---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a7832cd8f67420aeee64a57e59be8da7861ee977
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105033298"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zadania Azure Data Box powinny włączać podwójne szyfrowanie danych przechowywanych na urządzeniu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Włącz drugą warstwę szyfrowania opartego na oprogramowaniu dla danych przechowywanych na urządzeniu. Urządzenie jest już chronione za pośrednictwem Advanced Encryption Standard 256-bitowego szyfrowania danych przechowywanych w spoczynku. Ta opcja umożliwia dodanie drugiej warstwy szyfrowania danych. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Zadania Azure Data Box powinny używać klucza zarządzanego przez klienta do szyfrowania hasła odblokowywania urządzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Użyj klucza zarządzanego przez klienta, aby kontrolować szyfrowanie hasła odblokowywania urządzenia dla Azure Data Box. Klucze zarządzane przez klienta ułatwiają również zarządzanie dostępem do urządzenia odblokowania za pomocą usługi urządzenie Data Box w celu przygotowania urządzenia i kopiowania danych w zautomatyzowany sposób. Dane na urządzeniu są już szyfrowane przy użyciu szyfrowania Advanced Encryption Standard 256-bitowego, a hasło odblokowywania urządzenia jest domyślnie szyfrowane przy użyciu klucza zarządzanego przez firmę Microsoft. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
