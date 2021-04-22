---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c0a77896099bac5d4b3d819cfbd17ed339cb1b72
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869561"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure HDInsight klastrach należy wstrzyknąć do sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0ab5b05-1c98-40f7-bb9e-dc568e41b501) |Wstrzykiwanie Azure HDInsight sieci wirtualnej odblokowuje zaawansowane funkcje sieci i zabezpieczeń usługi HDInsight oraz zapewnia kontrolę nad konfiguracją zabezpieczeń sieci. |Inspekcja, Wyłączone, Odmów |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_VNETInjection_Audit.json) |
|[Azure HDInsight powinny używać kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Użyj kluczy zarządzanych przez klienta, aby zarządzać szyfrowaniem w pozostałych klastrach Azure HDInsight klastrach. Domyślnie dane klientów są szyfrowane za pomocą kluczy zarządzanych przez usługę, ale klucze zarządzane przez klienta są często wymagane do spełnienia standardów zgodności z przepisami. Klucze zarządzane przez klienta umożliwiają szyfrowanie danych przy użyciu klucza Azure Key Vault utworzonego i należącego do Ciebie. Masz pełną kontrolę i odpowiedzialność za cykl życia klucza, w tym rotację i zarządzanie. Dowiedz się więcej na stronie [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk) . |Inspekcja, Odmowa, Wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Azure HDInsight klastry powinny używać szyfrowania na hoście do szyfrowania danych w spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |Włączenie szyfrowania na hoście pomaga chronić i chronić dane zgodnie z wymaganiami organizacji w zakresie zabezpieczeń i zgodności. Po włączeniu szyfrowania na hoście dane przechowywane na hoście maszyny wirtualnej są szyfrowane podczas magazynowania i przepływy szyfrowane w usłudze Storage. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Azure HDInsight powinny używać szyfrowania podczas przesyłania do szyfrowania komunikacji między Azure HDInsight węzłami klastra](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Dane mogą zostać naruszone podczas transmisji między Azure HDInsight klastra. Włączenie szyfrowania podczas przesyłania rozwiązuje problemy z nieprawidłowym użyciem i manipulowaniem podczas tej transmisji. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
