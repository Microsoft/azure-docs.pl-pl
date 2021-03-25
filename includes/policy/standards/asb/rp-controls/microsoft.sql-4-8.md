---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0eb83417350d5dca85802269b698aee30a310f6f
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105861"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Wystąpienia zarządzane SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych magazynowanych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Wdrożenie Transparent Data Encryption (TDE) z własnym kluczem zapewnia zwiększoną przejrzystość i kontrolę nad ochroną TDE, zwiększoną ochronę za pomocą usługi zewnętrznej z obsługą mechanizmu HSM i promocją rozdzielenia obowiązków. To zalecenie ma zastosowanie do organizacji z powiązanym wymaganiem zgodności. |AuditIfNotExists, wyłączone |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Serwery SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych magazynowanych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Wdrożenie Transparent Data Encryption (TDE) z własnym kluczem zapewnia zwiększoną przejrzystość i kontrolę nad ochroną TDE, zwiększoną ochronę za pomocą usług zewnętrznych opartych na module HSM i promocją rozdzielenia obowiązków. To zalecenie ma zastosowanie do organizacji z powiązanym wymaganiem zgodności. |AuditIfNotExists, wyłączone |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Należy włączyć Transparent Data Encryption baz danych SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Należy włączyć funkcję przezroczystego szyfrowania danych w celu ochrony danych w czasie spoczynku i spełnienia wymagań dotyczących zgodności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
