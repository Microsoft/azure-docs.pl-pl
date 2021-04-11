---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 85faccf99aa7f308e1d4084268e339c177bcd011
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105037612"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Klastry usługi Azure HDInsight należy wprowadzać do sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0ab5b05-1c98-40f7-bb9e-dc568e41b501) |Wstrzyknięcie klastrów usługi Azure HDInsight w sieci wirtualnej odblokowuje zaawansowaną funkcję sieciową HDInsight i funkcje zabezpieczeń oraz zapewnia kontrolę nad konfiguracją zabezpieczeń sieci. |Inspekcja, wyłączona, Odmów |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_VNETInjection_Audit.json) |
|[Klastry usługi Azure HDInsight powinny używać kluczy zarządzanych przez klienta do szyfrowania danych przechowywanych w stanie spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Klucze zarządzane przez klienta służą do zarządzania szyfrowaniem w pozostałej części klastrów usługi Azure HDInsight. Domyślnie dane klienta są szyfrowane przy użyciu kluczy zarządzanych przez usługę, ale klucze zarządzane przez klienta są często wymagane do spełnienia standardów zgodności z przepisami. Klucze zarządzane przez klienta umożliwiają szyfrowanie danych przy użyciu klucza Azure Key Vault utworzonego i należącego do użytkownika. Użytkownik ma pełną kontrolę i odpowiedzialność za kluczowy cykl życia, włącznie z rotacją i zarządzaniem. Dowiedz się więcej o [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk) . |Inspekcja, Odmów, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Klastry usługi Azure HDInsight powinny używać szyfrowania na hoście do szyfrowania danych przechowywanych w stanie spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |Włączenie szyfrowania na hoście pomaga chronić dane i zabezpieczać je w taki sposób, aby spełniały zobowiązania dotyczące zabezpieczeń i zgodności w organizacji. Po włączeniu szyfrowania na hoście dane przechowywane na hoście maszyny wirtualnej są szyfrowane w stanie spoczynku i są zaszyfrowane w usłudze Storage. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Klastry usługi Azure HDInsight powinny używać szyfrowania podczas przesyłania do szyfrowania komunikacji między węzłami klastra usługi Azure HDInsight](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Dane mogą zostać naruszone podczas przesyłania między węzłami klastra usługi Azure HDInsight. Włączenie szyfrowania podczas przesyłania rozwiązuje problemy związane z nieprawidłowym użyciem i manipulacją w trakcie tej transmisji. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
