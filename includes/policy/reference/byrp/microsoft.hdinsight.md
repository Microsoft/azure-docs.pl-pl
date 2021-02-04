---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 578cd451b53634bc63876e8ca2d25e3251eb8cc2
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556449"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Klastry usługi Azure HDInsight powinny używać kluczy zarządzanych przez klienta do szyfrowania danych przechowywanych w stanie spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Klucze zarządzane przez klienta (CMK) służą do zarządzania szyfrowaniem w pozostałej części klastrów usługi Azure HDInsight. Domyślnie dane klienta są szyfrowane przy użyciu kluczy zarządzanych przez usługę, ale CMKs są często wymagane do spełnienia standardów zgodności z przepisami. CMKs umożliwiają szyfrowanie danych przy użyciu klucza Azure Key Vault utworzonego i należącego do użytkownika. Użytkownik ma pełną kontrolę i odpowiedzialność za kluczowy cykl życia, włącznie z rotacją i zarządzaniem. Dowiedz się więcej o szyfrowaniu CMK w [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk) .  |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Klastry usługi Azure HDInsight powinny używać szyfrowania na hoście do szyfrowania danych przechowywanych w stanie spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |Włączenie szyfrowania na hoście pomaga chronić dane i zabezpieczać je w taki sposób, aby spełniały zobowiązania dotyczące zabezpieczeń i zgodności w organizacji. Po włączeniu szyfrowania na hoście dane przechowywane na hoście maszyny wirtualnej są szyfrowane w stanie spoczynku i są zaszyfrowane w usłudze Storage. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Klastry usługi Azure HDInsight powinny używać szyfrowania podczas przesyłania do szyfrowania komunikacji między węzłami klastra usługi Azure HDInsight](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Dane mogą zostać naruszone podczas przesyłania między węzłami klastra usługi Azure HDInsight. Włączenie szyfrowania podczas przesyłania rozwiązuje problemy związane z nieprawidłowym użyciem i manipulacją w trakcie tej transmisji. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
