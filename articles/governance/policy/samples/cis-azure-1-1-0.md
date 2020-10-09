---
title: Szczegóły zgodności z przepisami dla usług CIS Microsoft Azure testy porównawcze
description: Szczegółowe informacje na temat usług CIS Microsoft Azure stwierdzenia zgodności z przepisami testowymi. Każda kontrolka jest zamapowana na co najmniej jedną definicję Azure Policy, która pomaga w ocenie.
ms.date: 10/07/2020
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 93d875c55a671fe68cec720bc78507fcbec638b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91820019"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-regulatory-compliance-built-in-initiative"></a>Szczegółowe informacje na temat usług CIS Microsoft Azure stwierdzenia zgodności z przepisami testowymi

W poniższym artykule szczegółowo opisano sposób, w jaki Azure Policy wbudowana definicja zgodności z przepisami, mapuje się do **domen zgodności** i **kontrolek** w ramach testu porównawczego wykrytych Microsoft Azure.
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz artykuł dotyczący [testów usług CIS Microsoft Azure](https://www.cisecurity.org/benchmark/azure/). Aby zrozumieć _własność_, zobacz [Azure Policy definicję zasad](../concepts/definition-structure.md#type) i [współdzieloną odpowiedzialność w chmurze](../../../security/fundamentals/shared-responsibility.md).

Następujące mapowania znajdują się w formantach **testu porównawczego** w ramach usług CIS Microsoft Azure. Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do konkretnej **domeny zgodności**. Wiele kontrolek jest implementowanych z definicją inicjatywy [Azure Policy](../overview.md) . Aby zapoznać się z pełną definicją inicjatywy, Otwórz pozycję **zasady** w Azure Portal a następnie wybierz stronę **definicje** .
Następnie Znajdź i wybierz pozycję **CIS Microsoft Azure fundacje testów porównawczych 1.1.0** zgodności z przepisami.

Ta inicjatywa wbudowana jest wdrażana jako część [przykładowego planu porównawczego usługi CIS Microsoft Azure](../../blueprints/samples/cis-azure-1-1-0.md).

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../overview.md) .
> Te zasady mogą pomóc w [ocenie zgodności](../how-to/get-compliance-data.md) z kontrolką; Niemniej jednak często nie jest to 1:1 ani kompletna zgodność między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy dotyczy tylko definicji zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między domenami zgodności, kontrolkami i definicjami Azure Policy dla tego standardu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_1_0_audit.json).

## <a name="identity-and-access-management"></a>Zarządzanie tożsamością i dostępem

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Upewnij się, że uwierzytelnianie wieloskładnikowe jest włączone dla wszystkich uprzywilejowanych użytkowników

**Identyfikator**: microsoft Azure 1,1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami właściciela, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Upewnij się, że uwierzytelnianie wieloskładnikowe jest włączone dla wszystkich użytkowników bez uprawnień

**Identyfikator**: microsoft Azure 1,2 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do odczytu, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-that-there-are-no-guest-users"></a>Upewnij się, że nie ma żadnych użytkowników-Gości

**Identyfikator**: microsoft Azure 1,3 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Konta zewnętrzne z uprawnieniami właściciela należy usunąć z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Konta zewnętrzne z uprawnieniami do zapisu należy usunąć z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Upewnij się, że nie są tworzone role niestandardowego właściciela subskrypcji

**Identyfikator**: microsoft Azure 1,23 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Role niestandardowego właściciela subskrypcji nie powinny istnieć](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Te zasady zapewniają, że nie istnieją żadne role niestandardowego właściciela subskrypcji. |Inspekcja, wyłączona |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Upewnij się, że ustawienie "Automatyczna obsługa administracyjna agenta monitorowania" ma wartość "on"

**Identyfikator**: microsoft Azure 2,2 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Automatyczna obsługa administracyjna agenta monitorowania Log Analytics powinna być włączona w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Włącz automatyczną obsługę administracyjną agenta monitorowania Log Analytics w celu zbierania danych zabezpieczeń |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj aktualizacje systemu" nie jest wyłączone

**Identyfikator**: microsoft Azure 2,3 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy zainstalować aktualizacje systemu na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center jak zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>Upewnij się, że ustawienie zasad domyślnych "Monitoruj luki w zabezpieczeniach systemu operacyjnego" nie jest wyłączone

**Identyfikator**: microsoft Azure 2,4 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Serwery, które nie spełniają skonfigurowanej linii bazowej, będą monitorowane przez Azure Security Center jako zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>Upewnij się, że domyślne ustawienie zasad ASC "Monitor Endpoint Protection nie jest" wyłączone "

**Identyfikator**: microsoft Azure 2,5 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Monitoruj brakujące Endpoint Protection w Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Serwery bez zainstalowanego agenta Endpoint Protection będą monitorowane przez Azure Security Center jako zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj szyfrowanie dysków" nie jest wyłączone

**Identyfikator**: microsoft Azure 2,6 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Maszyny wirtualne bez włączonego szyfrowania dysków będą monitorowane przez Azure Security Center jako zalecenia. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>Upewnij się, że domyślne ustawienie zasad ASC "monitorowanie sieciowych grup zabezpieczeń" nie jest "wyłączone"

**Identyfikator**: microsoft Azure 2,7 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych mających dostęp do Internetu i zawiera zalecenia dotyczące reguły sieciowej grupy zabezpieczeń, które zmniejszają potencjalną podatność na ataki |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>Upewnij się, że domyślne ustawienie zasad ASC "Włącz monitorowanie zapory nowej generacji (zapory następnej generacji)" nie jest wyłączone

**Identyfikator**: microsoft Azure 2,9 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ochrona maszyn wirtualnych przed potencjalnymi zagrożeniami przez ograniczenie dostępu do nich za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). Dowiedz się więcej o kontrolowaniu ruchu z sieciowych grup zabezpieczeń na [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ochrona podsieci przed potencjalnymi zagrożeniami przez ograniczenie dostępu do niej przy użyciu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). Sieciowych grup zabezpieczeń zawierają listę reguł listy kontroli dostępu (ACL) Access Control, które zezwalają na ruch sieciowy lub odmawiają go w podsieci. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>Upewnij się, że domyślne ustawienie zasad ASC "Ocena luk w zabezpieczeniach monitora" nie jest wyłączone

**Identyfikator**: microsoft Azure 2,10 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Wykrywa luki w zabezpieczeniach wykryte przez rozwiązanie do oceny luk w zabezpieczeniach i maszyny wirtualne bez rozwiązania do oceny luk w zabezpieczeniach Azure Security Center jak zalecenia. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj dostęp do sieci JIT" nie jest wyłączone

**Identyfikator**: microsoft Azure 2,12 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp do sieci just in Time (JIT) będzie monitorowany przez Azure Security Center jak zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj adaptacyjne aplikacji listy dozwolonych" nie jest "wyłączone"

**Identyfikator**: microsoft Azure 2,13 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Na maszynach powinny być włączone adaptacyjne kontrole aplikacji umożliwiające Definiowanie bezpiecznych aplikacji.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Włącz kontrolki aplikacji, aby zdefiniować listę znanych bezpiecznych aplikacji uruchomionych na maszynach, i Zgłoś alert, gdy inne aplikacje zostaną uruchomione. Pozwala to na zabezpieczanie maszyn przed złośliwym oprogramowaniem. Aby uprościć proces konfigurowania i utrzymywania reguł, Security Center korzysta z uczenia maszynowego do analizowania aplikacji uruchomionych na poszczególnych maszynach i sugerują listę znanych bezpiecznych aplikacji. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj inspekcję SQL" nie jest "wyłączone"

**Identyfikator**: microsoft Azure 2,14 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja w programie SQL Server powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Inspekcja w SQL Server powinna być włączona, aby śledzić działania bazy danych we wszystkich bazach danych na serwerze, z wyjątkiem Synapse i zapisywać je w dzienniku inspekcji. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>Upewnij się, że domyślne ustawienie zasad ASC "Monitoruj szyfrowanie SQL" nie jest wyłączone

**Identyfikator**: microsoft Azure 2,15 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć Transparent Data Encryption baz danych SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Należy włączyć funkcję przezroczystego szyfrowania danych w celu ochrony danych w czasie spoczynku i spełnienia wymagań dotyczących zgodności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-security-contact-emails-is-set"></a>Upewnij się, że ustawiono wartość "adresy e-mail kontaktu z zabezpieczeniami"

**Identyfikator**: microsoft Azure 2,16 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy podać adres e-mail kontaktu z zabezpieczeniami dla Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Wprowadź adres e-mail, aby otrzymywać powiadomienia, gdy Azure Security Center wykryje naruszone zasoby |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-security-contact-phone-number-is-set"></a>Upewnij się, że ustawiono numer telefonu kontaktu z zabezpieczeniami

**Identyfikator**: microsoft Azure 2,17 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy podać numer telefonu kontaktu z zabezpieczeniami dla Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4d66858-c922-44e3-9566-5cdb7a7be744) |Wprowadź numer telefonu, aby otrzymywać powiadomienia, gdy Azure Security Center wykryje naruszone zasoby |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_phone_number.json) |

### <a name="ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>Upewnij się, że dla alertów o wysokiej ważności jest ustawiona wartość "on".

**Identyfikator**: microsoft Azure 2,18 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć powiadomienia e-mail o alertach o wysokiej ważności](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Włącz wysyłanie alertów zabezpieczeń pocztą e-mail do kontaktu zabezpieczeń, aby otrzymywać wiadomości e-mail z alertami zabezpieczeń od firmy Microsoft. Gwarantuje to, że odpowiednie osoby będą wiedzieć o potencjalnych problemach z bezpieczeństwem i mogą złagodzić ryzyko |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

### <a name="ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>Upewnij się, że ustawienie "Wyślij wiadomość e-mail również do właścicieli subskrypcji" ma wartość "on"

**Identyfikator**: microsoft Azure 2,19 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Powiadomienie e-mail dla właściciela subskrypcji w celu uzyskania alertów o wysokiej ważności powinno być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |Włącz wysyłanie alertów zabezpieczeń pocztą e-mail do właściciela subskrypcji, aby otrzymywać wiadomości e-mail z alertami zabezpieczeń od firmy Microsoft. Gwarantuje to, że są one świadome potencjalnych problemów z zabezpieczeniami i mogą w odpowiednim czasie ograniczyć ryzyko |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |

## <a name="storage-accounts"></a>Konta magazynu

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Upewnij się, że element "Secure transfer Required" ma ustawioną wartość "Enabled"

**Identyfikator**: microsoft Azure 3,1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Inspekcja wymagania bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Upewnij się, że domyślna reguła dostępu do sieci dla kont magazynu jest ustawiona na Odmów

**Identyfikator**: microsoft Azure 3,7 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konta magazynu powinny ograniczać dostęp do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Dostęp sieciowy do kont magazynu powinien być ograniczony. Skonfiguruj reguły sieciowe, tak aby tylko aplikacje z dozwolonych sieci mogły uzyskiwać dostęp do konta magazynu. Aby zezwolić na połączenia z określonych klientów internetowych lub lokalnych, można udzielić dostępu do ruchu z określonych sieci wirtualnych platformy Azure lub do publicznych zakresów adresów IP w Internecie |Inspekcja, Odmów, wyłączone |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Upewnij się, że w obszarze "zaufane usługi firmy Microsoft" włączono dostęp do konta magazynu

**Identyfikator**: microsoft Azure 3,8 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konta magazynu powinny zezwalać na dostęp z zaufanych usług firmy Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Niektóre usługi firmy Microsoft, które współdziałają z kontami magazynu, działają z sieci, do których nie można uzyskać dostępu za pośrednictwem reguł sieci. Aby zapewnić działanie tego typu usługi zgodnie z oczekiwaniami, zezwól zestawowi zaufanych usług firmy Microsoft na pomijanie reguł sieci. Te usługi będą następnie używały silnego uwierzytelniania w celu uzyskania dostępu do konta magazynu. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

## <a name="database-services"></a>Usługi bazy danych

### <a name="ensure-that-auditing-is-set-to-on"></a>Upewnij się, że wartość "inspekcja" jest ustawiona na wartość "on"

**Identyfikator**: microsoft Azure 4,1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja w programie SQL Server powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Inspekcja w SQL Server powinna być włączona, aby śledzić działania bazy danych we wszystkich bazach danych na serwerze, z wyjątkiem Synapse i zapisywać je w dzienniku inspekcji. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>Upewnij się, że element "AuditActionGroups" w zasadach "inspekcja" dla programu SQL Server jest skonfigurowany prawidłowo

**Identyfikator**: microsoft Azure 4,2 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ustawienia inspekcji SQL powinny mieć Action-Groups skonfigurowany do przechwytywania działań krytycznych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |Właściwość AuditActionsAndGroups powinna zawierać co najmniej SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP, FAILED_DATABASE_AUTHENTICATION_GROUP BATCH_COMPLETED_GROUP w celu zapewnienia dokładnego rejestrowania inspekcji |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Upewnij się, że przechowywanie "inspekcja" jest większe niż 90 dni

**Identyfikator**: microsoft Azure 4,3 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Serwery SQL powinny być skonfigurowane z okresem przechowywania inspekcji większym niż 90 dni.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Inspekcja serwerów SQL skonfigurowanych z okresem przechowywania inspekcji krótszym niż 90 dni. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>Upewnij się, że w programie SQL Server ustawiono wartość "on" (Zaawansowane zabezpieczenia danych).

**Identyfikator**: microsoft Azure 4,4 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zaawansowana ochrona danych powinna być włączona w wystąpieniu zarządzanym SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Inspekcja każdego wystąpienia zarządzanego SQL bez zaawansowanego zabezpieczenia danych. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Zaawansowana ochrona danych powinna być włączona na serwerach SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Inspekcja serwerów SQL bez zaawansowanej ochrony danych |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Upewnij się, że skonfigurowano Azure Active Directory administratora

**Identyfikator**: microsoft Azure 4,8 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Inspekcja aprowizacji Azure Active Directory administratora programu SQL Server w celu włączenia uwierzytelniania usługi Azure AD. Uwierzytelnianie usługi Azure AD umożliwia uproszczone zarządzanie uprawnieniami oraz scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Upewnij się, że "szyfrowanie danych" jest ustawione na wartość "on" na SQL Database

**Identyfikator**: microsoft Azure 4,9 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć Transparent Data Encryption baz danych SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Należy włączyć funkcję przezroczystego szyfrowania danych w celu ochrony danych w czasie spoczynku i spełnienia wymagań dotyczących zgodności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>Upewnij się, że funkcja ochrony TDE programu SQL Server jest zaszyfrowana z BYOK (Użyj własnego klucza)

**Identyfikator**: microsoft Azure 4,10 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Funkcja ochrony TDE wystąpienia zarządzanego SQL powinna być szyfrowana przy użyciu własnego klucza](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Transparent Data Encryption (TDE) z własną kluczową obsługą zapewnia zwiększoną przejrzystość i kontrolę nad ochroną TDE, zwiększoną ochronę za pomocą usługi zewnętrznej z obsługą modułu HSM i promocją rozdzielenia obowiązków. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Funkcja ochrony programu SQL Server TDE powinna być szyfrowana przy użyciu własnego klucza](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Transparent Data Encryption (TDE) z własną kluczową obsługą zapewnia zwiększoną przejrzystość i kontrolę nad ochroną TDE, zwiększoną ochronę za pomocą usługi zewnętrznej z obsługą modułu HSM i promocją rozdzielenia obowiązków. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Upewnij się, że ustawienie "Wymuszaj połączenie SSL" ma wartość "ENABLED" dla serwera bazy danych MySQL

**Identyfikator**: microsoft Azure 4,11 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Upewnij się, że parametr serwera "log_checkpoints" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

**Identyfikator**: microsoft Azure 4,12 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Punkty kontrolne dziennika powinny być włączone dla serwerów baz danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Te zasady ułatwiają inspekcję wszystkich baz danych PostgreSQL w środowisku bez włączonego ustawienia log_checkpoints. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Upewnij się, że ustawienie "Wymuszaj połączenie SSL" ma wartość "ENABLED" dla serwera bazy danych PostgreSQL

**Identyfikator**: microsoft Azure 4,13 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Upewnij się, że parametr serwera "log_connections" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

**Identyfikator**: microsoft Azure 4,14 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć połączenia dzienników dla serwerów baz danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Te zasady ułatwiają inspekcję wszystkich baz danych PostgreSQL w środowisku bez włączonego ustawienia log_connections. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Upewnij się, że parametr serwera "log_disconnections" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

**Identyfikator**: microsoft Azure 4,15 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Odłączenia powinny być rejestrowane dla serwerów baz danych PostgreSQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Te zasady ułatwiają inspekcję wszystkich baz danych PostgreSQL w środowisku bez włączonej log_disconnections. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Upewnij się, że parametr serwera "connection_throttling" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

**Identyfikator**: microsoft Azure 4,17 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla serwerów baz danych PostgreSQL powinna być włączona funkcja ograniczania połączeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Te zasady ułatwiają inspekcję wszystkich baz danych PostgreSQL w środowisku bez włączonej funkcji ograniczania połączeń. To ustawienie umożliwia tymczasowe ograniczenie połączeń na adres IP dla zbyt wielu nieprawidłowych błędów logowania do hasła. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

### <a name="ensure-that-a-log-profile-exists"></a>Upewnij się, że profil dziennika istnieje

**Identyfikator**: WNP Azure 5.1.1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Subskrypcje platformy Azure powinny mieć profil dziennika aktywności](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |Te zasady zapewniają, że profil dziennika jest włączony do eksportowania dzienników aktywności. Przeprowadza inspekcję, jeśli nie istnieje profil dziennika utworzony w celu wyeksportowania dzienników do konta magazynu lub do centrum zdarzeń. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>Upewnij się, że czas przechowywania dziennika aktywności jest ustawiony na 365 dni lub więcej

**Identyfikator**: **prawa własności**WNP platformy Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dziennik aktywności powinien być zachowywany przez co najmniej jeden rok.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |Ta zasada przeprowadza inspekcję dziennika aktywności, jeśli czas przechowywania nie jest ustawiony na 365 dni lub bez ograniczeń (dni przechowywania ustawione na 0). |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="ensure-audit-profile-captures-all-the-activities"></a>Upewnij się, że profil inspekcji przechwytuje wszystkie działania

**Identyfikator**: WNP Azure 5.1.3 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Profil dziennika Azure Monitor powinien zbierać dzienniki dla kategorii "Write", "Delete" i "Action"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Te zasady zapewniają, że profil dziennika zbiera dzienniki dla kategorii "zapis", "Usuń" i "Akcja" |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>Upewnij się, że profil dziennika przechwytuje dzienniki aktywności dla wszystkich regionów, w tym globalnego

**Identyfikator**: WNP Azure 5.1.4 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor powinna zbierać dzienniki aktywności ze wszystkich regionów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Ta zasada przeprowadza inspekcję profilu dziennika Azure Monitor, który nie eksportuje działań ze wszystkich regionów obsługiwanych przez platformę Azure, w tym globalnego. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Upewnij się, że konto magazynu zawierające kontener z dziennikami aktywności jest zaszyfrowane za pomocą BYOK (Użyj własnego klucza)

**Identyfikator**: 5.1.6 **własność**usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konto magazynu zawierające kontener z dziennikami aktywności musi być zaszyfrowane za pomocą BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Ta zasada przeprowadza inspekcję, jeśli konto magazynu zawierające kontener z dziennikami aktywności jest szyfrowane za pomocą BYOK. Zasady działają tylko wtedy, gdy konto magazynu należy do tej samej subskrypcji, co w przypadku dzienników aktywności. Więcej informacji na temat szyfrowania usługi Azure Storage w spoczynku można znaleźć tutaj [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) .  |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Upewnij się, że rejestrowanie dla magazynu kluczy platformy Azure jest włączone

**Identyfikator**: 5.1.7 **własność**usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dzienniki diagnostyczne w Key Vault powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie końcowych działań do użycia w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Upewnij się, że alert dziennika aktywności istnieje dla tworzenia przypisania zasad

**Identyfikator**: WNP Azure 5.2.1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla określonych operacji zasad powinien istnieć alert dziennika aktywności](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Ta zasada przeprowadza inspekcję określonych operacji zasad bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Upewnij się, że alert dziennika aktywności istnieje dla tworzenia lub aktualizowania sieciowej grupy zabezpieczeń

**Identyfikator**: usługa CIS systemu Azure — **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ta zasada przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Upewnij się, że alert dziennika aktywności istnieje dla operacji usuwania sieciowej grupy zabezpieczeń

**Identyfikator**: **własność**usługi CIS Azure 5.2.3: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ta zasada przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Upewnij się, że alert dziennika aktywności istnieje dla reguły tworzenia lub aktualizowania sieciowej grupy zabezpieczeń

**Identyfikator**: **własność**usługi CIS Azure 5.2.4: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ta zasada przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Upewnij się, że alert dziennika aktywności istnieje dla reguły usuwania sieciowej grupy zabezpieczeń

**Identyfikator**: 5.2.5 **własność**usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ta zasada przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Upewnij się, że alert dziennika aktywności istnieje dla rozwiązania do tworzenia lub aktualizacji zabezpieczeń

**Identyfikator**: 5.2.6 **własność**usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Ta zasada przeprowadza inspekcję określonych operacji zabezpieczeń bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Upewnij się, że dla rozwiązania do usuwania zabezpieczeń istnieje alert dziennika aktywności

**Identyfikator**: 5.2.7 **własność**usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Ta zasada przeprowadza inspekcję określonych operacji zabezpieczeń bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Upewnij się, że alert dziennika aktywności istnieje dla reguły zapory tworzenia lub aktualizowania lub usuwania SQL Server

**Identyfikator**: 5.2.8 **własność**usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ta zasada przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-update-security-policy"></a>Upewnij się, że alert dziennika aktywności istnieje dla zasad zabezpieczeń aktualizacji

**Identyfikator**: 5.2.9 **własność**usługi CIS Azure: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Ta zasada przeprowadza inspekcję określonych operacji zabezpieczeń bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

## <a name="networking"></a>Networking

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
|[Network Watcher powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci w, do i z platformy Azure. Monitorowanie poziomu scenariusza umożliwia diagnozowanie problemów w widoku na poziomie sieci. Narzędzia do diagnostyki i wizualizacji sieci dostępne w Network Watcher pomagają zrozumieć, zdiagnozować i uzyskiwać wgląd w sieć na platformie Azure. |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-that-os-disk-are-encrypted"></a>Upewnij się, że "dysk systemu operacyjnego" jest szyfrowany

**Identyfikator**: microsoft Azure 7,1 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Maszyny wirtualne bez włączonego szyfrowania dysków będą monitorowane przez Azure Security Center jako zalecenia. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-data-disks-are-encrypted"></a>Upewnij się, że "dyski danych" są zaszyfrowane

**Identyfikator**: microsoft Azure 7,2 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Maszyny wirtualne bez włączonego szyfrowania dysków będą monitorowane przez Azure Security Center jako zalecenia. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted"></a>Upewnij się, że "niedołączone dyski" są zaszyfrowane

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
|[Należy zainstalować aktualizacje systemu na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center jak zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Upewnij się, że program Endpoint Protection dla wszystkich Virtual Machines jest zainstalowany

**Identyfikator**: microsoft Azure 7,6 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Monitoruj brakujące Endpoint Protection w Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Serwery bez zainstalowanego agenta Endpoint Protection będą monitorowane przez Azure Security Center jako zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Inne uwagi dotyczące zabezpieczeń

### <a name="ensure-the-key-vault-is-recoverable"></a>Upewnij się, że magazyn kluczy jest możliwy do odzyskania

**Identyfikator**: microsoft Azure 8,4 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault obiektów powinno być możliwe do odzyskania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Ta zasada przeprowadza inspekcję w przypadku braku możliwości odzyskania obiektów magazynu kluczy. Funkcja usuwania nietrwałego pomaga efektywnie przechowywać zasoby dla danego okresu przechowywania (90 dni) nawet po operacji usuwania, jednocześnie zapewniając, że obiekt jest usuwany. Gdy jest włączona ochrona przed czyszczeniem, nie można wyczyścić magazynu lub obiektu w stanie usuniętym, dopóki nie zostanie przekroczony okres przechowywania 90 dni. Te magazyny i obiekty nadal mogą być odzyskiwane, dzięki czemu klienci będą przestrzegani zasad przechowywania. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Włączanie kontroli dostępu opartej na rolach (RBAC) w ramach usług Azure Kubernetes Services

**Identyfikator**: microsoft Azure 8,5 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Access Control oparte na rolach (RBAC) powinny być używane w usługach Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Aby zapewnić szczegółowe filtrowanie działań, które użytkownicy mogą wykonywać, użyj Role-Based Access Control (RBAC) do zarządzania uprawnieniami w klastrach usługi Kubernetes i konfigurowania odpowiednich zasad autoryzacji. |Inspekcja, wyłączona |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="appservice"></a>AppService

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
|[Upewnij się, że aplikacja funkcji ma wartość "certyfikaty klienta (przychodzące certyfikaty klienta") "on"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Certyfikaty klienta umożliwiają aplikacji żądanie certyfikatu dla żądań przychodzących. Tylko klienci z prawidłowym certyfikatem będą mogli uzyskać dostęp do aplikacji. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |
|[Upewnij się, że aplikacja sieci WEB ma "certyfikaty klienta (przychodzące certyfikaty klienta") "on"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Certyfikaty klienta umożliwiają aplikacji żądanie certyfikatu dla żądań przychodzących. Tylko klienci z prawidłowym certyfikatem będą mogli uzyskać dostęp do aplikacji. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Upewnij się, że Azure Active Directory jest włączona na App Service

**Identyfikator**: microsoft Azure 9,5 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tożsamość zarządzana powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Korzystanie z tożsamości zarządzanej na potrzeby ulepszonego zabezpieczeń uwierzytelniania |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Tożsamość zarządzana powinna być używana w aplikacja funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Korzystanie z tożsamości zarządzanej na potrzeby ulepszonego zabezpieczeń uwierzytelniania |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Tożsamość zarządzana powinna być używana w aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Korzystanie z tożsamości zarządzanej na potrzeby ulepszonego zabezpieczeń uwierzytelniania |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Upewnij się, że "wersja języka PHP" jest najnowsza, jeśli zostanie użyta do uruchomienia aplikacji sieci Web

**Identyfikator**: microsoft Azure 9,7 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Okresowo nowsze wersje oprogramowania PHP są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest używanie najnowszej wersji języka PHP dla aplikacji interfejsu API, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji sieci WEB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Okresowo nowsze wersje oprogramowania PHP są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest używanie najnowszej wersji języka PHP dla aplikacji sieci Web, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Upewnij się, że "wersja języka Python" jest najnowsza, jeśli zostanie użyta do uruchomienia aplikacji sieci Web

**Identyfikator**: microsoft Azure 9,8 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Okresowo nowsze wersje oprogramowania Python są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest używanie najnowszej wersji języka Python dla aplikacji interfejsu API, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Okresowo nowsze wersje oprogramowania Python są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Użycie najnowszej wersji języka Python dla aplikacji funkcji jest zalecane, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Okresowo nowsze wersje oprogramowania Python są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest używanie najnowszej wersji języka Python dla aplikacji sieci Web, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Upewnij się, że "wersja Java" jest najnowsza, jeśli zostanie użyta do uruchomienia aplikacji sieci Web

**Identyfikator**: microsoft Azure 9,9 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Okresowo nowsze wersje są udostępniane dla środowiska Java ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest używanie najnowszej wersji języka Python dla aplikacji interfejsu API, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Okresowo nowsze wersje oprogramowania Java są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest używanie najnowszej wersji języka Java dla aplikacji funkcji, aby można było korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Okresowo nowsze wersje oprogramowania Java są udostępniane ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Zalecane jest korzystanie z najnowszej wersji języka Java dla aplikacji sieci Web, aby korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje najnowszej wersji. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji sieci Web

**Identyfikator**: microsoft Azure 9,10 **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Okresowo nowsze wersje są wydawane dla protokołu HTTP ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Korzystanie z najnowszej wersji protokołu HTTP dla aplikacji sieci Web, aby korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje nowszej wersji. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Okresowo nowsze wersje są wydawane dla protokołu HTTP ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Korzystanie z najnowszej wersji protokołu HTTP dla aplikacji sieci Web, aby korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje nowszej wersji. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Okresowo nowsze wersje są wydawane dla protokołu HTTP ze względu na wady zabezpieczeń lub w celu uwzględnienia dodatkowych funkcji. Korzystanie z najnowszej wersji protokołu HTTP dla aplikacji sieci Web, aby korzystać z poprawek zabezpieczeń, jeśli istnieją i/lub nowe funkcje nowszej wersji. |AuditIfNotExists, wyłączone |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

> [!NOTE]
> Dostępność określonych definicji Azure Policy może się różnić w zależności od Azure Government i innych chmur krajowych.

## <a name="next-steps"></a>Następne kroki

Dodatkowe artykuły dotyczące Azure Policy:

- Przegląd [zgodności z przepisami](../concepts/regulatory-compliance.md) .
- Zapoznaj się ze [strukturą definicji inicjatywy](../concepts/initiative-definition-structure.md).
- Zapoznaj się z innymi przykładami w [Azure Policy Samples](./index.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
