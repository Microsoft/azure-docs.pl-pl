---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fb5f6dbb410a15bf88fccc1ed3f9c3ca0f830c70
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "96007978"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Obszary robocze usługi Azure Synapse powinny używać kluczy zarządzanych przez klienta do szyfrowania danych magazynowanych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |Klucze zarządzane przez klienta umożliwiają sterowanie szyfrowaniem w pozostałej części danych przechowywanych w obszarze roboczym usługi Azure Synapse. Klucze zarządzane przez klienta oferują podwójne szyfrowanie, dodając drugą warstwę szyfrowania od domyślnego szyfrowania przy użyciu kluczy zarządzanych przez usługę. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[Reguły zapory IP w obszarach roboczych usługi Azure Synapse należy usunąć](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56fd377d-098c-4f02-8406-81eb055902b8) |Usuwanie wszystkich reguł zapory adresów IP usprawnia zabezpieczenia, zapewniając dostęp do obszaru roboczego usługi Azure Synapse tylko z prywatnego punktu końcowego. Ta konfiguracja przeprowadza inspekcję tworzenia reguł zapory zezwalających na dostęp do sieci publicznej w obszarze roboczym. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceFirewallRules_Audit.json) |
|[Sieć wirtualna zarządzanego obszaru roboczego w obszarze roboczym usługi Azure Synapse powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d9dbfa3-927b-4cf0-9d0f-08747f971650) |Włączenie zarządzanej sieci wirtualnej obszaru roboczego gwarantuje, że obszar roboczy jest odizolowany od innych obszarów roboczych. Integracja danych i zasoby platformy Spark wdrożone w tej sieci wirtualnej również zapewniają izolację poziomu użytkownika dla działań Spark. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceManagedVnet_Audit.json) |
|[Połączenia prywatnych punktów końcowych w obszarach roboczych usługi Azure Synapse powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Prywatne punkty końcowe można skonfigurować tak, aby łączyły się prywatnie z obszarem roboczym usługi Azure Synapse. Służy do wymuszania bezpiecznego kanału komunikacyjnego z obszarem roboczym usługi Azure Synapse. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Zarządzane prywatne punkty końcowe Synapse powinny łączyć się tylko z zasobami w zatwierdzonych dzierżawach Azure Active Directory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a003702-13d2-4679-941b-937e58c443f0) |Ochrona obszaru roboczego usługi Synapse przez umożliwienie połączeń z zasobami w zatwierdzonych dzierżawach Azure Active Directory (Azure AD). Zatwierdzone dzierżawy usługi Azure AD można zdefiniować podczas przypisywania zasad. |Inspekcja, wyłączona, Odmów |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/Workspace_DataExfiltrationPrevention_Deny.json) |
