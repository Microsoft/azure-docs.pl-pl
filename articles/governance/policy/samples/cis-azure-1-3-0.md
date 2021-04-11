---
title: Szczegóły zgodności z przepisami dla usług CIS Microsoft Azure testy porównawcze 1.3.0
description: Szczegółowe informacje na temat usług CIS Microsoft Azure testów porównawczych 1.3.0 zgodności z przepisami. Każda kontrolka jest zamapowana na co najmniej jedną definicję Azure Policy, która pomaga w ocenie.
ms.date: 03/31/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 03e8d5ae24e379928910783f9638327f0b7a42dd
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091654"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-130-regulatory-compliance-built-in-initiative"></a>Szczegółowe informacje na temat usługi CIS Microsoft Azure testy porównawcze 1.3.0 zgodności z przepisami

W poniższym artykule szczegółowo opisano sposób, w jaki Azure Policy wbudowana definicja zgodności z przepisami, mapuje się do **domen zgodności** i **kontrolek** w usłudze CIS Microsoft Azure testów porównawczych 1.3.0.
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz artykuł dotyczący usługi [CIS Microsoft Azure 1.3.0](https://www.cisecurity.org/benchmark/azure/). Aby zrozumieć _własność_, zobacz [Azure Policy definicję zasad](../concepts/definition-structure.md#type) i [współdzieloną odpowiedzialność w chmurze](../../../security/fundamentals/shared-responsibility.md).

Poniższe mapowania są do **1.3.0Ch testów porównawczych w programie Microsoft Azure CIS** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do konkretnej **domeny zgodności**. Wiele kontrolek jest implementowanych z definicją inicjatywy [Azure Policy](../overview.md) . Aby zapoznać się z pełną definicją inicjatywy, Otwórz pozycję **zasady** w Azure Portal a następnie wybierz stronę **definicje** .
Następnie Znajdź i wybierz pozycję **CIS Microsoft Azure fundacje testy porównawcze 1.3.0** zgodności z przepisami.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../overview.md) .
> Te zasady mogą pomóc w [ocenie zgodności](../how-to/get-compliance-data.md) z kontrolką; jednak często nie jest to jeden lub kompletny odpowiednik między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy dotyczy tylko definicji zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między domenami zgodności, kontrolkami i definicjami Azure Policy dla tego standardu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_3_0.json).

## <a name="identity-and-access-management"></a>Zarządzanie tożsamością i dostępem

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Upewnij się, że uwierzytelnianie wieloskładnikowe jest włączone dla wszystkich uprzywilejowanych użytkowników

**Identyfikator**: microsoft Azure 1,1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami właściciela, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Upewnij się, że uwierzytelnianie wieloskładnikowe jest włączone dla wszystkich użytkowników bez uprawnień

**Identyfikator**: microsoft Azure 1,2 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do odczytu, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-guest-users-are-reviewed-on-a-monthly-basis"></a>Upewnij się, że użytkownicy-Goście są przeglądani miesięcznie

**Identyfikator**: microsoft Azure 1,3 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Konta zewnętrzne z uprawnieniami właściciela należy usunąć z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Konta zewnętrzne z uprawnieniami do zapisu należy usunąć z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Upewnij się, że nie są tworzone role niestandardowego właściciela subskrypcji

**Identyfikator**: microsoft Azure 1,21 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Role niestandardowego właściciela subskrypcji nie powinny istnieć](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Te zasady zapewniają, że nie istnieją żadne role niestandardowego właściciela subskrypcji. |Inspekcja, wyłączona |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-azure-defender-is-set-to-on-for-servers"></a>Upewnij się, że usługa Azure Defender jest włączona dla serwerów

**Identyfikator**: microsoft Azure 2,1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Usługa Azure Defender dla serwerów powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |Usługa Azure Defender dla serwerów zapewnia ochronę przed zagrożeniami w czasie rzeczywistym dla obciążeń serwera i generuje zalecenia dotyczące ograniczania funkcjonalności, a także alerty dotyczące podejrzanych działań. |AuditIfNotExists, wyłączone |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-app-service"></a>Upewnij się, że usługa Azure Defender jest ustawiona na wartość włączone dla App Service

**Identyfikator**: microsoft Azure 2,2 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Usługa Azure Defender dla App Service powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Usługa Azure Defender dla App Service wykorzystuje skalę chmury oraz widoczność, którą platforma Azure ma jako dostawcę chmury, aby monitorować typowe ataki aplikacji sieci Web. |AuditIfNotExists, wyłączone |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-azure-sql-database-servers"></a>Upewnij się, że usługa Azure Defender jest włączona dla serwerów usługi Azure SQL Database

**Identyfikator**: microsoft Azure 2,3 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Usługa Azure Defender dla serwerów Azure SQL Database powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Usługa Azure Defender dla programu SQL udostępnia funkcje umożliwiające poddanie i łagodzenie potencjalnych luk w zabezpieczeniach bazy danych, wykrywanie nietypowych działań, które mogą wskazywać na zagrożenia baz danych SQL oraz odnajdywanie i klasyfikowanie poufnych danych. |AuditIfNotExists, wyłączone |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-sql-servers-on-machines"></a>Upewnij się, że usługa Azure Defender jest włączona dla serwerów SQL na maszynach

**Identyfikator**: microsoft Azure 2,4 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Usługa Azure Defender dla serwerów SQL na maszynach powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Usługa Azure Defender dla programu SQL udostępnia funkcje umożliwiające poddanie i łagodzenie potencjalnych luk w zabezpieczeniach bazy danych, wykrywanie nietypowych działań, które mogą wskazywać na zagrożenia baz danych SQL oraz odnajdywanie i klasyfikowanie poufnych danych. |AuditIfNotExists, wyłączone |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-storage"></a>Upewnij się, że usługa Azure Defender jest ustawiona na wartość włączone dla magazynu

**Identyfikator**: microsoft Azure 2,5 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Usługa Azure Defender dla magazynu powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Usługa Azure Defender dla magazynu zapewnia wykrywanie nietypowych i potencjalnie szkodliwych prób dostępu do kont magazynu lub korzystania z nich. |AuditIfNotExists, wyłączone |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-kubernetes"></a>Upewnij się, że usługa Azure Defender jest włączona dla Kubernetes

**Identyfikator**: microsoft Azure 2,6 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Usługa Azure Defender for Kubernetes powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Usługa Azure Defender for Kubernetes zapewnia ochronę przed zagrożeniami w czasie rzeczywistym dla środowisk kontenerów i generuje alerty dla podejrzanych działań. |AuditIfNotExists, wyłączone |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-container-registries"></a>Upewnij się, że usługa Azure Defender jest włączona dla rejestrów kontenerów

**Identyfikator**: microsoft Azure 2,7 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Usługa Azure Defender dla rejestrów kontenerów powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |Usługa Azure Defender dla rejestrów kontenerów zapewnia luki w zabezpieczeniach, które zostały pobrane w ciągu ostatnich 30 dni, wypchnięte do rejestru lub zaimportowane i uwidacznia szczegółowe wyniki na obrazie. |AuditIfNotExists, wyłączone |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-key-vault"></a>Upewnij się, że usługa Azure Defender jest ustawiona na wartość włączone dla Key Vault

**Identyfikator**: microsoft Azure 2,8 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Usługa Azure Defender dla Key Vault powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Usługa Azure Defender dla Key Vault oferuje dodatkową warstwę ochrony i analizy zabezpieczeń dzięki wykrywaniu nietypowych i potencjalnie szkodliwych prób dostępu lub korzystania z kont magazynu kluczy. |AuditIfNotExists, wyłączone |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Upewnij się, że ustawienie "Automatyczna obsługa administracyjna agenta monitorowania" ma wartość "on"

**Identyfikator**: microsoft Azure 2,11 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Funkcja autoaprowizacji agenta Log Analytics powinna być włączona w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |W celu monitorowania luk w zabezpieczeniach i zagrożeń Azure Security Center zbiera dane z maszyn wirtualnych platformy Azure. Dane są zbierane przez agenta Log Analytics, dawniej znany jako Microsoft Monitoring Agent (MMA), który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z komputera i kopiuje dane do obszaru roboczego Log Analytics na potrzeby analizy. Zalecamy włączenie automatycznej aprowizacji, aby automatycznie wdrażać agenta na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i utworzonych nowych. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-additional-email-addresses-is-configured-with-a-security-contact-email"></a>Upewnij się, że w obszarze "dodatkowe adresy e-mail" skonfigurowano adres e-mail kontaktu z zabezpieczeniami

**Identyfikator**: microsoft Azure 2,13 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Subskrypcje powinny mieć adres e-mail kontaktu pod kątem problemów z zabezpieczeniami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Aby upewnić się, że odpowiednie osoby w organizacji zostaną powiadomione w przypadku wystąpienia potencjalnego naruszenia zabezpieczeń w ramach jednej z subskrypcji, należy ustawić kontakt z zabezpieczeniami w celu otrzymywania powiadomień e-mail z Security Center. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-notify-about-alerts-with-the-following-severity-is-set-to-high"></a>Upewnij się, że "powiadamianie o alertach o następującej ważności" ma wartość "High"

**Identyfikator**: microsoft Azure 2,14 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć powiadomienia e-mail o alertach o wysokiej ważności](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Aby upewnić się, że odpowiednie osoby w organizacji są powiadamiane w przypadku wystąpienia potencjalnego naruszenia zabezpieczeń w ramach jednej z subskrypcji, należy włączyć powiadomienia e-mail dotyczące alertów o wysokiej ważności w Security Center. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

## <a name="storage-accounts"></a>Konta magazynu

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Upewnij się, że element "Secure transfer Required" ma ustawioną wartość "Enabled"

**Identyfikator**: microsoft Azure 3,1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Inspekcja wymagania bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Upewnij się, że element "publiczny poziom dostępu" jest ustawiony jako prywatny dla kontenerów obiektów BLOB

**Identyfikator**: microsoft Azure 3,5 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Publiczny dostęp do konta magazynu powinien być niedozwolony](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Anonimowy publiczny dostęp do odczytu do kontenerów i obiektów BLOB w usłudze Azure Storage jest wygodnym sposobem udostępniania danych, ale może stwarzać zagrożenie bezpieczeństwa. Aby zapobiec naruszeniu danych spowodowanym przez niepożądany dostęp anonimowy, firma Microsoft zaleca uniemożliwienie publicznego dostępu do konta magazynu, chyba że wymaga tego scenariusz. |Inspekcja, Odmów, wyłączone |[2.0.1 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Upewnij się, że domyślna reguła dostępu do sieci dla kont magazynu jest ustawiona na Odmów

**Identyfikator**: microsoft Azure 3,6 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konta magazynu powinny ograniczać dostęp do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Dostęp sieciowy do kont magazynu powinien być ograniczony. Skonfiguruj reguły sieciowe, tak aby tylko aplikacje z dozwolonych sieci mogły uzyskiwać dostęp do konta magazynu. Aby zezwolić na połączenia z określonych klientów internetowych lub lokalnych, można udzielić dostępu do ruchu z określonych sieci wirtualnych platformy Azure lub do publicznych zakresów adresów IP w Internecie |Inspekcja, Odmów, wyłączone |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[Konta magazynu powinny ograniczać dostęp do sieci przy użyciu reguł sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |Należy chronić konta magazynu przed potencjalnymi zagrożeniami przy użyciu reguł sieci wirtualnej jako preferowanej metody zamiast filtrowania opartego na protokole IP. Wyłączenie filtrowania opartego na protokole IP uniemożliwia dostęp do kont magazynu przez publiczne adresy IP. |Inspekcja, Odmów, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Upewnij się, że w obszarze "zaufane usługi firmy Microsoft" włączono dostęp do konta magazynu

**Identyfikator**: microsoft Azure 3,7 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konta magazynu powinny zezwalać na dostęp z zaufanych usług firmy Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Niektóre usługi firmy Microsoft, które współdziałają z kontami magazynu, działają z sieci, do których nie można uzyskać dostępu za pośrednictwem reguł sieci. Aby zapewnić działanie tego typu usługi zgodnie z oczekiwaniami, zezwól zestawowi zaufanych usług firmy Microsoft na pomijanie reguł sieci. Te usługi będą następnie używały silnego uwierzytelniania w celu uzyskania dostępu do konta magazynu. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

### <a name="ensure-storage-for-critical-data-are-encrypted-with-customer-managed-key"></a>Upewnij się, że magazyn dla danych o kluczowym znaczeniu jest szyfrowany przy użyciu klucza zarządzanego przez klienta

**Identyfikator**: microsoft Azure 3,9 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konta magazynu powinny używać klucza zarządzanego przez klienta do szyfrowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |Zabezpiecz swoje konto magazynu, zapewniając większą elastyczność przy użyciu kluczy zarządzanych przez klienta. Gdy określisz klucz zarządzany przez klienta, ten klucz będzie używany w celu ochrony i kontroli dostępu do klucza szyfrującego dane. Użycie kluczy zarządzanych przez klienta zapewnia dodatkowe możliwości sterowania obrotem klucza szyfrowania klucza lub kryptograficznego usuwania danych. |Inspekcja, wyłączona |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

## <a name="database-services"></a>Usługi bazy danych

### <a name="ensure-that-auditing-is-set-to-on"></a>Upewnij się, że wartość "inspekcja" jest ustawiona na wartość "on"

**Identyfikator**: **własność** WNP platformy Azure 4.1.1: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja w programie SQL Server powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Inspekcja w SQL Server powinna być włączona w celu śledzenia działań bazy danych we wszystkich bazach danych na serwerze i zapisywania ich w dzienniku inspekcji. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Upewnij się, że "szyfrowanie danych" jest ustawione na wartość "on" na SQL Database

**Identyfikator**: Microsoft Azure 4.1.2 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć Transparent Data Encryption baz danych SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Należy włączyć funkcję przezroczystego szyfrowania danych w celu ochrony danych w czasie spoczynku i spełnienia wymagań dotyczących zgodności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Upewnij się, że przechowywanie "inspekcja" jest większe niż 90 dni

**Identyfikator**: Microsoft 4.1.3 ( **własność**): klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Serwery SQL z inspekcją w miejscu docelowym konta magazynu powinny być skonfigurowane z zachowaniem 90 dni lub nowszym](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |W celu zbadania zdarzenia zalecamy ustawienie przechowywania danych dla inspekcji SQL Server na konto magazynu na co najmniej 90 dni. Upewnij się, że spełniasz niezbędne reguły przechowywania dla regionów, w których pracujesz. Jest to czasami wymagane do zgodności ze standardami prawnymi. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-threat-protection-atp-on-a-sql-server-is-set-to-enabled"></a>Upewnij się, że Zaawansowana ochrona przed zagrożeniami (ATP) w programie SQL Server ma ustawioną wartość "Enabled"

**Identyfikator**: WNP Azure 4.2.1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zaawansowana ochrona danych powinna być włączona w wystąpieniu zarządzanym SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Inspekcja każdego wystąpienia zarządzanego SQL bez zaawansowanego zabezpieczenia danych. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Zaawansowana ochrona danych powinna być włączona na serwerach SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Inspekcja serwerów SQL bez zaawansowanej ochrony danych |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-vulnerability-assessment-va-is-enabled-on-a-sql-server-by-setting-a-storage-account"></a>Upewnij się, że Ocena luk w zabezpieczeniach (VA) jest włączona na serwerze SQL przez ustawienie konta magazynu

**Identyfikator**: Microsoft Azure 4.2.2 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniu zarządzanym SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Przeprowadź inspekcję każdego wystąpienia zarządzanego SQL, które nie ma włączonych skanów oceny luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może wykrywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Inspekcja serwerów SQL platformy Azure, które nie mają włączonej cyklicznej oceny ocen luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może wykrywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

### <a name="ensure-that-va-setting-send-scan-reports-to-is-configured-for-a-sql-server"></a>Upewnij się, że ustawienie VA Wyślij raporty skanowania do programu SQL Server

**Identyfikator**: **własność** usługi CIS Azure 4.2.4: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ustawienia oceny luk w zabezpieczeniach dla programu SQL Server powinny zawierać adres e-mail do otrzymywania raportów skanowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057d6cfe-9c4f-4a6d-bc60-14420ea1f1a9) |Upewnij się, że adres e-mail jest podany dla pola "Wyślij raporty skanowania do" w ustawieniach oceny luk w zabezpieczeniach. Ten adres e-mail odbiera podsumowanie wyników skanowania po okresowym skanowaniu na serwerach SQL. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Upewnij się, że ustawienie "Wymuszaj połączenie SSL" ma wartość "ENABLED" dla serwera bazy danych PostgreSQL

**Identyfikator**: **prawa własności** do usługi CIS platformy Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Upewnij się, że ustawienie "Wymuszaj połączenie SSL" ma wartość "ENABLED" dla serwera bazy danych MySQL

**Identyfikator**: WNP Azure 4.3.2 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Upewnij się, że parametr serwera "log_checkpoints" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

**Identyfikator**: **własność** usługi CIS platformy Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Punkty kontrolne dziennika powinny być włączone dla serwerów baz danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Te zasady ułatwiają inspekcję wszystkich baz danych PostgreSQL w środowisku bez włączonego ustawienia log_checkpoints. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Upewnij się, że parametr serwera "log_connections" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

**Identyfikator**: 4.3.4 **własność** usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć połączenia dzienników dla serwerów baz danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Te zasady ułatwiają inspekcję wszystkich baz danych PostgreSQL w środowisku bez włączonego ustawienia log_connections. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Upewnij się, że parametr serwera "log_disconnections" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

**Identyfikator**: 4.3.5 **własność** usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Odłączenia powinny być rejestrowane dla serwerów baz danych PostgreSQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Te zasady ułatwiają inspekcję wszystkich baz danych PostgreSQL w środowisku bez włączonej log_disconnections. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Upewnij się, że parametr serwera "connection_throttling" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

**Identyfikator**: WNP Azure 4.3.6 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla serwerów baz danych PostgreSQL powinna być włączona funkcja ograniczania połączeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Te zasady ułatwiają inspekcję wszystkich baz danych PostgreSQL w środowisku bez włączonej funkcji ograniczania połączeń. To ustawienie umożliwia tymczasowe ograniczenie połączeń na adres IP dla zbyt wielu nieprawidłowych błędów logowania do hasła. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Upewnij się, że skonfigurowano Azure Active Directory administratora

**Identyfikator**: microsoft Azure 4,4 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Inspekcja aprowizacji Azure Active Directory administratora programu SQL Server w celu włączenia uwierzytelniania usługi Azure AD. Uwierzytelnianie usługi Azure AD umożliwia uproszczone zarządzanie uprawnieniami oraz scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-customer-managed-key"></a>Upewnij się, że funkcja ochrony TDE programu SQL Server jest zaszyfrowana przy użyciu klucza zarządzanego przez klienta

**Identyfikator**: microsoft Azure 4,5 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Wystąpienia zarządzane SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych magazynowanych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Wdrożenie Transparent Data Encryption (TDE) z własnym kluczem zapewnia zwiększoną przejrzystość i kontrolę nad ochroną TDE, zwiększoną ochronę za pomocą usługi zewnętrznej z obsługą mechanizmu HSM i promocją rozdzielenia obowiązków. To zalecenie ma zastosowanie do organizacji z powiązanym wymaganiem zgodności. |AuditIfNotExists, wyłączone |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Serwery SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych magazynowanych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Wdrożenie Transparent Data Encryption (TDE) z własnym kluczem zapewnia zwiększoną przejrzystość i kontrolę nad ochroną TDE, zwiększoną ochronę za pomocą usług zewnętrznych opartych na module HSM i promocją rozdzielenia obowiązków. To zalecenie ma zastosowanie do organizacji z powiązanym wymaganiem zgodności. |AuditIfNotExists, wyłączone |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Upewnij się, że kontener magazynu przechowujący dzienniki aktywności nie jest publicznie dostępny

**Identyfikator**: WNP Azure 5.1.3 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Publiczny dostęp do konta magazynu powinien być niedozwolony](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Anonimowy publiczny dostęp do odczytu do kontenerów i obiektów BLOB w usłudze Azure Storage jest wygodnym sposobem udostępniania danych, ale może stwarzać zagrożenie bezpieczeństwa. Aby zapobiec naruszeniu danych spowodowanym przez niepożądany dostęp anonimowy, firma Microsoft zaleca uniemożliwienie publicznego dostępu do konta magazynu, chyba że wymaga tego scenariusz. |Inspekcja, Odmów, wyłączone |[2.0.1 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Upewnij się, że konto magazynu zawierające kontener z dziennikami aktywności jest zaszyfrowane za pomocą BYOK (Użyj własnego klucza)

**Identyfikator**: WNP Azure 5.1.4 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konto magazynu zawierające kontener z dziennikami aktywności musi być zaszyfrowane za pomocą BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Ta zasada przeprowadza inspekcję, jeśli konto magazynu zawierające kontener z dziennikami aktywności jest szyfrowane za pomocą BYOK. Zasady działają tylko wtedy, gdy konto magazynu należy do tej samej subskrypcji, co w przypadku dzienników aktywności. Więcej informacji na temat szyfrowania usługi Azure Storage w spoczynku można znaleźć tutaj [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) .  |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Upewnij się, że rejestrowanie dla magazynu kluczy platformy Azure jest włączone

**Identyfikator**: usługa CIS Azure — **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć dzienniki zasobów w Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Inspekcja włączenia dzienników zasobów. Pozwala to na ponowne utworzenie końcowych działań do użycia w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Upewnij się, że alert dziennika aktywności istnieje dla tworzenia przypisania zasad

**Identyfikator**: WNP Azure 5.2.1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla określonych operacji zasad powinien istnieć alert dziennika aktywności](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Ta zasada przeprowadza inspekcję określonych operacji zasad bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-policy-assignment"></a>Upewnij się, że alert dotyczący usuwania zasad istnieje w dzienniku aktywności

**Identyfikator**: usługa CIS systemu Azure — **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla określonych operacji zasad powinien istnieć alert dziennika aktywności](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Ta zasada przeprowadza inspekcję określonych operacji zasad bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Upewnij się, że alert dziennika aktywności istnieje dla tworzenia lub aktualizowania sieciowej grupy zabezpieczeń

**Identyfikator**: **własność** usługi CIS Azure 5.2.3: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ta zasada przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Upewnij się, że alert dziennika aktywności istnieje dla operacji usuwania sieciowej grupy zabezpieczeń

**Identyfikator**: **własność** usługi CIS Azure 5.2.4: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ta zasada przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Upewnij się, że alert dziennika aktywności istnieje dla reguły tworzenia lub aktualizowania sieciowej grupy zabezpieczeń

**Identyfikator**: 5.2.5 **własność** usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ta zasada przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Upewnij się, że alert dziennika aktywności istnieje dla reguły usuwania sieciowej grupy zabezpieczeń

**Identyfikator**: 5.2.6 **własność** usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ta zasada przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Upewnij się, że alert dziennika aktywności istnieje dla rozwiązania do tworzenia lub aktualizacji zabezpieczeń

**Identyfikator**: 5.2.7 **własność** usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Ta zasada przeprowadza inspekcję określonych operacji zabezpieczeń bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Upewnij się, że dla rozwiązania do usuwania zabezpieczeń istnieje alert dziennika aktywności

**Identyfikator**: 5.2.8 **własność** usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Ta zasada przeprowadza inspekcję określonych operacji zabezpieczeń bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Upewnij się, że alert dziennika aktywności istnieje dla reguły zapory tworzenia lub aktualizowania lub usuwania SQL Server

**Identyfikator**: 5.2.9 **własność** usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ta zasada przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-diagnostic-logs-are-enabled-for-all-services-which-support-it"></a>Upewnij się, że dzienniki diagnostyczne są włączone dla wszystkich usług, które je obsługują.

**Identyfikator**: microsoft Azure 5,3 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dzienniki diagnostyczne w App Services powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Inspekcja włączenia dzienników diagnostycznych w aplikacji. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[Należy włączyć dzienniki zasobów w Azure Data Lake Store](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Inspekcja włączenia dzienników zasobów. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Należy włączyć dzienniki zasobów w Azure Stream Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Inspekcja włączenia dzienników zasobów. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Należy włączyć dzienniki zasobów w kontach wsadowych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Inspekcja włączenia dzienników zasobów. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Należy włączyć dzienniki zasobów w Data Lake Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Inspekcja włączenia dzienników zasobów. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[Należy włączyć dzienniki zasobów w centrum zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Inspekcja włączenia dzienników zasobów. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[Należy włączyć dzienniki zasobów w IoT Hub](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Inspekcja włączenia dzienników zasobów. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Należy włączyć dzienniki zasobów w Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Inspekcja włączenia dzienników zasobów. Pozwala to na ponowne utworzenie końcowych działań do użycia w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Należy włączyć dzienniki zasobów w Logic Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Inspekcja włączenia dzienników zasobów. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Należy włączyć dzienniki zasobów w usługach wyszukiwania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Inspekcja włączenia dzienników zasobów. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Należy włączyć dzienniki zasobów w Service Bus](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Inspekcja włączenia dzienników zasobów. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Należy włączyć dzienniki zasobów w Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Zaleca się włączenie dzienników, aby można było odtworzyć dziennik aktywności w przypadku, gdy w przypadku incydentu jest wymagane badanie lub naruszenie zabezpieczeń. |AuditIfNotExists, wyłączone |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

## <a name="networking"></a>Sieć

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Upewnij się, że dostęp do protokołu RDP jest ograniczony z Internetu

**Identyfikator**: microsoft Azure 6,1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dostęp RDP z Internetu powinien być zablokowany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Ta zasada przeprowadza inspekcję wszystkich reguł zabezpieczeń sieci, które zezwalają na dostęp za pośrednictwem protokołu RDP z Internetu |Inspekcja, wyłączona |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Upewnij się, że dostęp SSH jest ograniczony z Internetu

**Identyfikator**: microsoft Azure 6,2 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dostęp SSH z Internetu powinien być zablokowany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Ta zasada przeprowadza inspekcję wszystkich reguł zabezpieczeń sieci, które zezwalają na dostęp SSH z Internetu |Inspekcja, wyłączona |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Upewnij się, że Network Watcher jest "Enabled"

**Identyfikator**: microsoft Azure 6,5 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Network Watcher powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci w, do i z platformy Azure. Monitorowanie poziomu scenariusza umożliwia diagnozowanie problemów w widoku na poziomie sieci. Narzędzia do diagnostyki i wizualizacji sieci dostępne w Network Watcher pomagają zrozumieć, zdiagnozować i uzyskiwać wgląd w sieć na platformie Azure. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-virtual-machines-are-utilizing-managed-disks"></a>Upewnij się, że Virtual Machines używają Managed Disks

**Identyfikator**: microsoft Azure 7,1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Ta zasada przeprowadza inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych |wizyjn |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |

### <a name="ensure-that-os-and-data-disks-are-encrypted-with-cmk"></a>Upewnij się, że dyski systemu operacyjnego i danych są szyfrowane za pomocą CMK

**Identyfikator**: microsoft Azure 7,2 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Maszyny wirtualne bez włączonego szyfrowania dysków będą monitorowane przez Azure Security Center jako zalecenia. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted-with-cmk"></a>Upewnij się, że "niedołączone dyski" są zaszyfrowane za pomocą CMK

**Identyfikator**: microsoft Azure 7,3 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Niedołączone dyski powinny być szyfrowane](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Ta zasada przeprowadza inspekcję wszelkich niedołączonych dysków bez włączonego szyfrowania. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Upewnij się, że zainstalowano tylko zatwierdzone rozszerzenia

**Identyfikator**: microsoft Azure 7,4 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy zainstalować tylko zatwierdzone rozszerzenia maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Te zasady regulują rozszerzenia maszyny wirtualnej, które nie są zatwierdzone. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Upewnij się, że są stosowane najnowsze poprawki systemu operacyjnego dla wszystkich Virtual Machines

**Identyfikator**: microsoft Azure 7,5 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy zainstalować aktualizacje systemu na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center jak zalecenia |AuditIfNotExists, wyłączone |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Upewnij się, że program Endpoint Protection dla wszystkich Virtual Machines jest zainstalowany

**Identyfikator**: microsoft Azure 7,6 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Monitoruj brakujące Endpoint Protection w Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Serwery bez zainstalowanego agenta Endpoint Protection będą monitorowane przez Azure Security Center jako zalecenia |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Inne uwagi dotyczące zabezpieczeń

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Upewnij się, że data wygaśnięcia została ustawiona na wszystkie klucze

**Identyfikator**: microsoft Azure 8,1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Klucze Key Vault powinny mieć datę wygaśnięcia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |Klucze kryptograficzne powinny mieć zdefiniowaną datę wygaśnięcia i nie powinny być trwałe. Klucze, które są prawidłowe w nieskończoność, zapewniają potencjalną osobę atakującą z większą ilością czasu na złamanie klucza. Zalecane jest, aby ustawić daty wygaśnięcia kluczy kryptograficznych. |Inspekcja, Odmów, wyłączone |[1.0.1 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Upewnij się, że data wygaśnięcia jest ustawiona dla wszystkich wpisów tajnych

**Identyfikator**: microsoft Azure 8,2 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Wpisy tajne Key Vault powinny mieć datę wygaśnięcia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |Wpisy tajne powinny mieć zdefiniowaną datę wygaśnięcia i nie powinny być trwałe. Wpisy tajne, które są prawidłowe w nieskończoność, zapewniają potencjalną atakującemu więcej czasu na ich złamanie. Zalecane jest, aby ustawić daty wygaśnięcia dla wpisów tajnych. |Inspekcja, Odmów, wyłączone |[1.0.1 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Upewnij się, że magazyn kluczy jest możliwy do odzyskania

**Identyfikator**: microsoft Azure 8,4 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[W magazynach kluczy powinna być włączona ochrona przed czyszczeniem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Złośliwe usunięcie magazynu kluczy może prowadzić do utraty danych trwałych. Złośliwy Tester w organizacji może potencjalnie usuwać i czyścić magazyny kluczy. Ochrona przed przeczyszczeniem chroni przed atakami z niejawnym testerem, wymuszając obowiązkowy okres przechowywania dla nieusuniętych magazynów kluczy. Nikt nie jest w Twojej organizacji, a firma Microsoft będzie mogła przeczyścić Twoje magazyny kluczy w okresie przechowywania nietrwałego. |Inspekcja, Odmów, wyłączone |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Włączanie kontroli dostępu opartej na rolach (RBAC) w ramach usług Azure Kubernetes Services

**Identyfikator**: microsoft Azure 8,5 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Access Control oparte na rolach (RBAC) powinny być używane w usługach Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Aby zapewnić szczegółowe filtrowanie działań, które użytkownicy mogą wykonywać, użyj Role-Based Access Control (RBAC) do zarządzania uprawnieniami w klastrach usługi Kubernetes i konfigurowania odpowiednich zasad autoryzacji. |Inspekcja, wyłączona |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="app-service"></a>App Service

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Upewnij się, że App Service jest ustawiona na Azure App Service

**Identyfikator**: microsoft Azure 9,1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć uwierzytelnianie w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Uwierzytelnianie Azure App Service to funkcja, która może uniemożliwić anonimowe żądania HTTP przed osiągnięciem aplikacji interfejsu API lub uwierzytelnić te, które mają tokeny przed osiągnięciem aplikacji interfejsu API |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[Należy włączyć uwierzytelnianie w aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Uwierzytelnianie Azure App Service to funkcja, która może uniemożliwić anonimowe żądania HTTP przed osiągnięciem aplikacji funkcji lub uwierzytelnić te, które mają tokeny przed osiągnięciem aplikacji funkcji |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[Należy włączyć uwierzytelnianie w aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Uwierzytelnianie Azure App Service to funkcja, która może uniemożliwić anonimowe żądania HTTP z docierania do aplikacji sieci Web lub uwierzytelnić te, które mają tokeny przed uzyskaniem dostępu do aplikacji sieci Web. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Upewnij się, że aplikacja internetowa przekierowuje cały ruch HTTP do protokołu HTTPS w Azure App Service

**Identyfikator**: microsoft Azure 9,2 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Upewnij się, że aplikacja sieci Web używa najnowszej wersji szyfrowania TLS

**Identyfikator**: microsoft Azure 9,3 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Upewnij się, że aplikacja sieci Web ma "certyfikaty klienta (przychodzące certyfikaty klienta") "on"

**Identyfikator**: microsoft Azure 9,4 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Upewnij się, że aplikacja interfejsu API ma wartość "certyfikaty klienta (przychodzące certyfikaty klienta") "on"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Certyfikaty klienta umożliwiają aplikacji żądanie certyfikatu dla żądań przychodzących. Tylko klienci z prawidłowym certyfikatem będą mogli uzyskać dostęp do aplikacji. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Upewnij się, że aplikacja sieci WEB ma "certyfikaty klienta (przychodzące certyfikaty klienta") "on"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Certyfikaty klienta umożliwiają aplikacji żądanie certyfikatu dla żądań przychodzących. Tylko klienci z prawidłowym certyfikatem będą mogli uzyskać dostęp do aplikacji. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[Aplikacje funkcji powinny mieć włączone "certyfikaty klienta (przychodzące certyfikaty klienta)"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Certyfikaty klienta umożliwiają aplikacji żądanie certyfikatu dla żądań przychodzących. Tylko klienci z prawidłowymi certyfikatami będą mogli uzyskać dostęp do aplikacji. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Upewnij się, że Azure Active Directory jest włączona na App Service

**Identyfikator**: microsoft Azure 9,5 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tożsamość zarządzana powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Korzystanie z tożsamości zarządzanej na potrzeby ulepszonego zabezpieczeń uwierzytelniania |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Tożsamość zarządzana powinna być używana w aplikacja funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Korzystanie z tożsamości zarządzanej na potrzeby ulepszonego zabezpieczeń uwierzytelniania |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Tożsamość zarządzana powinna być używana w aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Korzystanie z tożsamości zarządzanej na potrzeby ulepszonego zabezpieczeń uwierzytelniania |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Upewnij się, że "wersja języka PHP" jest najnowsza, jeśli zostanie użyta do uruchomienia aplikacji sieci Web

**Identyfikator**: microsoft Azure 9,6 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Okresowo nowsze wersje oprogramowania PHP są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest używanie najnowszej wersji języka PHP dla aplikacji interfejsu API, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. Obecnie te zasady mają zastosowanie tylko do aplikacji sieci Web systemu Linux. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji sieci WEB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Okresowo nowsze wersje oprogramowania PHP są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest używanie najnowszej wersji języka PHP dla aplikacji sieci Web, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. Obecnie te zasady mają zastosowanie tylko do aplikacji sieci Web systemu Linux. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Upewnij się, że "wersja języka Python" jest najnowsza, jeśli zostanie użyta do uruchomienia aplikacji sieci Web

**Identyfikator**: microsoft Azure 9,7 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Okresowo nowsze wersje oprogramowania Python są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest używanie najnowszej wersji języka Python dla aplikacji interfejsu API, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. Obecnie te zasady mają zastosowanie tylko do aplikacji sieci Web systemu Linux. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Okresowo nowsze wersje oprogramowania Python są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Użycie najnowszej wersji języka Python dla aplikacji funkcji jest zalecane, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. Obecnie te zasady mają zastosowanie tylko do aplikacji sieci Web systemu Linux. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Okresowo nowsze wersje oprogramowania Python są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest używanie najnowszej wersji języka Python dla aplikacji sieci Web, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. Obecnie te zasady mają zastosowanie tylko do aplikacji sieci Web systemu Linux. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Upewnij się, że "wersja Java" jest najnowsza, jeśli zostanie użyta do uruchomienia aplikacji sieci Web

**Identyfikator**: microsoft Azure 9,8 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Okresowo nowsze wersje są udostępniane dla środowiska Java ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest używanie najnowszej wersji języka Python dla aplikacji interfejsu API, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. Obecnie te zasady mają zastosowanie tylko do aplikacji sieci Web systemu Linux. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Okresowo nowsze wersje oprogramowania Java są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest używanie najnowszej wersji języka Java dla aplikacji funkcji, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. Obecnie te zasady mają zastosowanie tylko do aplikacji sieci Web systemu Linux. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Okresowo nowsze wersje oprogramowania Java są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest korzystanie z najnowszej wersji języka Java dla aplikacji sieci Web, aby korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. Obecnie te zasady mają zastosowanie tylko do aplikacji sieci Web systemu Linux. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji sieci Web

**Identyfikator**: microsoft Azure 9,9 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Okresowo nowsze wersje są wydawane dla protokołu HTTP ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Korzystanie z najnowszej wersji protokołu HTTP dla aplikacji sieci Web, aby korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje nowszej wersji. Obecnie te zasady mają zastosowanie tylko do aplikacji sieci Web systemu Linux. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Okresowo nowsze wersje są wydawane dla protokołu HTTP ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Korzystanie z najnowszej wersji protokołu HTTP dla aplikacji sieci Web, aby korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje nowszej wersji. Obecnie te zasady mają zastosowanie tylko do aplikacji sieci Web systemu Linux. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Okresowo nowsze wersje są wydawane dla protokołu HTTP ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Korzystanie z najnowszej wersji protokołu HTTP dla aplikacji sieci Web, aby korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje nowszej wersji. Obecnie te zasady mają zastosowanie tylko do aplikacji sieci Web systemu Linux. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

### <a name="ensure-ftp-deployments-are-disabled"></a>Upewnij się, że wdrożenia FTP są wyłączone

**Identyfikator**: microsoft Azure 9,10 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tylko FTPS powinna być wymagana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |Włącz wymuszanie FTPS na potrzeby zwiększonych zabezpieczeń |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[FTPS powinny być wymagane tylko w aplikacja funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |Włącz wymuszanie FTPS na potrzeby zwiększonych zabezpieczeń |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[FTPS powinna być wymagana w aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |Włącz wymuszanie FTPS na potrzeby zwiększonych zabezpieczeń |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |

> [!NOTE]
> Dostępność określonych definicji Azure Policy może się różnić w zależności od Azure Government i innych chmur krajowych.

## <a name="next-steps"></a>Następne kroki

Dodatkowe artykuły dotyczące Azure Policy:

- Przegląd [zgodności z przepisami](../concepts/regulatory-compliance.md) .
- Zapoznaj się ze [strukturą definicji inicjatywy](../concepts/initiative-definition-structure.md).
- Zapoznaj się z innymi przykładami w [Azure Policy Samples](./index.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
