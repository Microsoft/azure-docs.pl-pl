---
title: Szczegóły zgodności z przepisami cis Microsoft Azure Foundations Benchmark 1.3.0
description: Szczegóły wbudowanej inicjatywy CIS Microsoft Azure Foundations Benchmark 1.3.0 Regulatory Compliance. Każda kontrolka jest mapowana na co najmniej jedną Azure Policy, które pomagają w ocenie.
ms.date: 04/14/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 3dc7578af93f36ba8f0400099bb21cdabdcffb70
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497731"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-130-regulatory-compliance-built-in-initiative"></a>Szczegóły wbudowanej inicjatywy CIS Microsoft Azure Foundations Benchmark 1.3.0 Regulatory Compliance

W poniższym artykule szczegółowo opisano sposób mapowania wbudowanej definicji  inicjatywy  Azure Policy Regulatory Compliance na domeny zgodności i mechanizmy kontroli w programie CIS Microsoft Azure Foundations Benchmark 1.3.0.
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [CIS Microsoft Azure Foundations Benchmark 1.3.0](https://www.cisecurity.org/benchmark/azure/). Aby zrozumieć _własność_, [zobacz Azure Policy definicji zasad i](../concepts/definition-structure.md#type) [Wspólna odpowiedzialność w chmurze.](../../../security/fundamentals/shared-responsibility.md)

Poniższe mapowania są mapami na mechanizmy kontroli **CIS Microsoft Azure Foundations Benchmark 1.3.0.** Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonej **domeny zgodności.** Wiele kontrolek jest implementowych z Azure Policy [definicji](../overview.md) inicjatywy. Aby przejrzeć pełną definicję inicjatywy, **otwórz** pozycję Zasady w Azure Portal i wybierz **stronę Definicje.**
Następnie znajdź i wybierz wbudowaną definicję inicjatywy **CIS Microsoft Azure Foundations Benchmark w wersji 1.3.0** Zgodność z przepisami.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co [najmniej jedną Azure Policy](../overview.md) definicją.
> Te zasady mogą ułatwić [ocenę zgodności](../how-to/get-compliance-data.md) z kontrolą; Jednak często nie występuje dopasowanie "jeden do jednego" lub pełne dopasowanie między kontrolą a co najmniej jedną zasadą. W związku z **tym zgodność** w Azure Policy odnosi się tylko do samych definicji zasad; Nie gwarantuje to pełnej zgodności ze wszystkimi wymaganiami kontrolki. Ponadto standard zgodności obejmuje mechanizmy kontroli, które nie są obecnie Azure Policy przez żadne definicje. Dlatego zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między domenami zgodności, kontrolkami i Azure Policy dla tego standardu zgodności mogą zmieniać się z czasem. Aby wyświetlić historię zmian, zobacz [GitHub Commit History (Historia zatwierdzń w usłudze GitHub).](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_3_0.json)

## <a name="identity-and-access-management"></a>Zarządzanie tożsamością i dostępem

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Upewnij się, że uwierzytelnianie wieloskładnikowe jest włączone dla wszystkich uprzywilejowanych użytkowników

**Identyfikator:** CIS Azure 1.1 **Ownership**: Customer (Własność platformy Azure CIS 1.1): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami właściciela w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami właściciela, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Upewnij się, że uwierzytelnianie wieloskładnikowe jest włączone dla wszystkich użytkowników bez uprawnień

**Identyfikator:** CIS Azure 1.2 **Ownership**: Customer (Własność platformy Azure CIS 1.2): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami do odczytu w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do odczytu, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-guest-users-are-reviewed-on-a-monthly-basis"></a>Upewnij się, że użytkownicy-goście są przeglądani co miesiąc

**Identyfikator:** CIS Azure 1.3 **Ownership**: Customer (Własność platformy Azure CIS 1.3): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Konta zewnętrzne z uprawnieniami właściciela należy usunąć z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Upewnij się, że nie utworzono żadnych niestandardowych ról właściciela subskrypcji

**Identyfikator:** CIS Azure 1.21 **Ownership**: Customer (Własność platformy Azure CIS 1.21): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Niestandardowe role właściciela subskrypcji nie powinny istnieć](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Te zasady zapewniają, że role właściciela subskrypcji niestandardowej nie istnieją. |Inspekcja, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-azure-defender-is-set-to-on-for-servers"></a>Upewnij się, Azure Defender jest wł. dla serwerów

**Identyfikator:** CIS Azure 2.1 **Ownership**: Customer (Własność platformy Azure CIS 2.1): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender dla serwerów powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |Azure Defender dla serwerów zapewnia ochronę obciążeń serwera przed zagrożeniami w czasie rzeczywistym i generuje zalecenia dotyczące wzmacniania zabezpieczeń, a także alerty dotyczące podejrzanych działań. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-app-service"></a>Upewnij się, Azure Defender jest wł. dla App Service

**Identyfikator:** CIS Azure 2.2 **Ownership**: Customer (Własność platformy Azure CIS 2.2): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender dla App Service należy włączyć](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender dla App Service korzysta ze skali chmury i widoczności platformy Azure jako dostawcy usług w chmurze do monitorowania typowych ataków na aplikacje internetowe. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-azure-sql-database-servers"></a>Upewnij się, Azure Defender jest wł. dla serwerów Azure SQL bazy danych

**Identyfikator:** CIS Azure 2.3 **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender dla Azure SQL Database powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |usługa Azure Defender dla języka SQL udostępnia funkcje wykrywania i ograniczania potencjalnych luk w zabezpieczeniach bazy danych, wykrywania anomalii działań, które mogą wskazywać zagrożenia dla baz danych SQL, oraz odnajdywania i klasyfikowania poufnych danych. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-sql-servers-on-machines"></a>Upewnij się, Azure Defender jest wł. dla serwerów SQL na maszynach

**Identyfikator:** CIS Azure 2.4 **Ownership**: Customer (Własność platformy Azure CIS 2.4): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender dla serwerów SQL na maszynach należy włączyć](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |usługa Azure Defender dla języka SQL udostępnia funkcje wykrywania i ograniczania potencjalnych luk w zabezpieczeniach bazy danych, wykrywania anomalii działań, które mogą wskazywać zagrożenia dla baz danych SQL, oraz odnajdywania i klasyfikowania poufnych danych. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-storage"></a>Upewnij się, Azure Defender jest wł. dla magazynu

**Identyfikator:** CIS Azure 2.5 **Ownership**: Customer (Własność platformy Azure CIS 2.5): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender dla usługi Storage powinno być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender dla usługi Storage umożliwia wykrywanie nietypowych i potencjalnie szkodliwych prób uzyskania dostępu do kont magazynu lub wykorzystania ich. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-kubernetes"></a>Upewnij się, Azure Defender jest wł. dla kubernetes

**Identyfikator:** CIS Azure 2.6 **Ownership**: Customer (Własność platformy Azure CIS 2.6): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender dla Kubernetes powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender dla Kubernetes zapewnia ochronę przed zagrożeniami w czasie rzeczywistym dla środowisk konteneryzowanych i generuje alerty dotyczące podejrzanych działań. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-container-registries"></a>Upewnij się, Azure Defender jest wł. dla rejestrów kontenerów

**Identyfikator:** CIS Azure 2.7 **Ownership**: Customer (Własność platformy Azure CIS 2.7): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender dla rejestrów kontenerów powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |Azure Defender rejestrów kontenerów umożliwia skanowanie pod lukami w zabezpieczeniach wszystkich obrazów ściągnięłych w ciągu ostatnich 30 dni, wypchnięć do rejestru lub zaimportowanych oraz uwidoczniło szczegółowe wyniki dla każdego obrazu. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-key-vault"></a>Upewnij się, Azure Defender jest wł. dla Key Vault

**Identyfikator:** CIS Azure 2.8 **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender dla Key Vault należy włączyć](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault zapewnia dodatkową warstwę ochrony i analizy zabezpieczeń dzięki wykrywaniu nietypowych i potencjalnie szkodliwych prób uzyskania dostępu do kont magazynu kluczy lub wykorzystania ich. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Upewnij się, że dla ustawienia "Automatyczna aprownia agenta monitorowania" jest ustawiona wartość "Wł."

**Identyfikator:** CIS Azure 2.11 **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Automatyczne aprowizowanie agenta usługi Log Analytics powinno być włączone w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Aby monitorować luki w zabezpieczeniach i zagrożenia, Azure Security Center zbiera dane z maszyn wirtualnych platformy Azure. Dane są zbierane przez agenta usługi Log Analytics, wcześniej znanego jako Microsoft Monitoring Agent (MMA), który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyny i kopiuje dane do obszaru roboczego usługi Log Analytics w celu analizy. Zalecamy włączenie automatycznego aprowizowania, aby automatycznie wdrożyć agenta na wszystkich obsługiwanych maszyn wirtualnych platformy Azure i wszystkich nowo utworzonych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-additional-email-addresses-is-configured-with-a-security-contact-email"></a>Upewnij się, że skonfigurowano "Dodatkowe adresy e-mail" przy użyciu adresu e-mail kontaktu zabezpieczeń

**Identyfikator:** CIS Azure 2.13 **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Subskrypcje powinny mieć kontaktowy adres e-mail w przypadku problemów z zabezpieczeniami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Aby upewnić się, że odpowiednie osoby w organizacji są powiadamiane w przypadku potencjalnego naruszenia zabezpieczeń w jednej z Twoich subskrypcji, ustaw kontakt ds. zabezpieczeń, aby otrzymywać powiadomienia e-mail od Security Center. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-notify-about-alerts-with-the-following-severity-is-set-to-high"></a>Upewnij się, że dla ustawienia "Powiadom o alertach o następującej ważności" ustawiono wartość "Wysoka"

**Identyfikator:** CIS Azure 2.14 **Ownership**: Customer (Własność platformy Azure CIS 2.14): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Powiadomienia e-mail dotyczące alertów o wysokiej ważności powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Aby upewnić się, że odpowiednie osoby w organizacji są powiadamiane w przypadku potencjalnego naruszenia zabezpieczeń w jednej z Twoich subskrypcji, włącz powiadomienia e-mail dla alertów o wysokiej ważności w Security Center. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

## <a name="storage-accounts"></a>Konta magazynu

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Upewnij się, że opcja "Wymagany bezpieczny transfer" jest ustawiona na wartość "Włączone"

**IDENTYFIKATOR:** CIS Azure 3.1 **Ownership**: Customer (Własność cis Azure 3.1: klient)

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Wymaganie inspekcji bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie żądań tylko z bezpiecznych połączeń (HTTPS) konta magazynu. Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Upewnij się, że ustawienie "Poziom dostępu publicznego" ma wartość Prywatny dla kontenerów obiektów blob

**Identyfikator:** CIS Azure 3.5 **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dostęp publiczny do konta magazynu powinien być niedozwolone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Anonimowy publiczny dostęp do odczytu do kontenerów i obiektów blob w usłudze Azure Storage to wygodny sposób udostępniania danych, ale może stanowić zagrożenie bezpieczeństwa. Aby zapobiec naruszeniom bezpieczeństwa danych spowodowanym przez niepożądany dostęp anonimowy, firma Microsoft zaleca uniemożliwienie publicznego dostępu do konta magazynu, chyba że jest to wymagane w scenariuszu. |inspekcja, odmowa, wyłączone |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Upewnij się, że domyślna reguła dostępu do sieci dla kont magazynu jest ustawiona na odmów

**Identyfikator:** CIS Azure 3.6 **Ownership**: Customer (Własność cis Azure 3.6: klient)

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Konta magazynu powinny ograniczać dostęp sieciowy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Dostęp sieciowy do kont magazynu powinien być ograniczony. Skonfiguruj reguły sieci, aby tylko aplikacje z dozwolonych sieci mogły uzyskać dostęp do konta magazynu. Aby zezwolić na połączenia z określonego Internetu lub klientów lokalnych, można przyznać dostęp do ruchu z określonych sieci wirtualnych platformy Azure lub do zakresów publicznych internetowych adresów IP |Inspekcja, Odmowa, Wyłączone |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[Konta magazynu powinny ograniczać dostęp sieciowy przy użyciu reguł sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |Chroń konta magazynu przed potencjalnymi zagrożeniami, używając reguł sieci wirtualnej jako preferowanej metody zamiast filtrowania opartego na adresach IP. Wyłączenie filtrowania opartego na adresach IP uniemożliwia dostęp publicznych adresów IP do kont magazynu. |Inspekcja, Odmowa, Wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Upewnij się, że opcja "Zaufane usługi firmy Microsoft" jest włączona dla dostępu do konta magazynu

**Identyfikator:** CIS Azure 3.7 **Ownership**: Customer (Własność platformy Azure CIS 3.7): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Konta magazynu powinny zezwalać na dostęp z zaufanych usługi firmy Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Niektóre usługi firmy Microsoft, które współdziałają z kontami magazynu, działają z sieci, którym nie można przyznać dostępu za pośrednictwem reguł sieci. Aby ułatwić pracę tego typu usługi zgodnie z zamierzonymi zasadami, należy zezwolić zestawowi zaufanych usługi firmy Microsoft ominąć reguły sieci. Te usługi będą następnie używać silnego uwierzytelniania w celu uzyskania dostępu do konta magazynu. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

### <a name="ensure-storage-for-critical-data-are-encrypted-with-customer-managed-key"></a>Upewnij się, że magazyn danych o krytycznym znaczeniu jest szyfrowany za pomocą klucza zarządzanego przez klienta

**Identyfikator:** CIS Azure 3.9 **Ownership**: Customer (Własność cis Azure 3.9: klient)

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Konta magazynu powinny używać klucza zarządzanego przez klienta do szyfrowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |Zapewnij większą elastyczność konta magazynu przy użyciu kluczy zarządzanych przez klienta. Gdy określisz klucz zarządzany przez klienta, ten klucz będzie używany w celu ochrony i kontroli dostępu do klucza szyfrującego dane. Korzystanie z kluczy zarządzanych przez klienta zapewnia dodatkowe możliwości kontrolowania rotacji klucza szyfrowania klucza lub kryptograficznie wymazywania danych. |Inspekcja, wyłączone |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

## <a name="database-services"></a>Usługi bazy danych

### <a name="ensure-that-auditing-is-set-to-on"></a>Upewnij się, że dla ustawienia "Inspekcja" ustawiono wartość "Wł."

**Identyfikator:** CIS Azure 4.1.1 **Ownership**: Customer (Własność platformy Azure CIS 4.1.1): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja na serwerze SQL powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Należy włączyć inspekcję SQL Server, aby śledzić działania bazy danych we wszystkich bazach danych na serwerze i zapisywać je w dzienniku inspekcji. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Upewnij się, że ustawienie "Szyfrowanie danych" ma wartość "Wł." na SQL Database

**Identyfikator:** CIS Azure 4.1.2 **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Transparent Data Encryption w bazach danych SQL powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Należy włączyć funkcję Transparent Data Encryption, aby chronić dane w spoczynku i spełniać wymagania dotyczące zgodności |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Upewnij się, że okres przechowywania "Inspekcja" jest "większy niż 90 dni"

**Identyfikator:** CIS Azure 4.1.3 **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Serwery SQL z inspekcją w miejscu docelowym konta magazynu powinny mieć co najmniej 90 dni przechowywania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Na potrzeby badania zdarzeń zalecamy ustawienie przechowywania danych dla inspekcji SQL Server na miejsce docelowe konta magazynu na co najmniej 90 dni. Upewnij się, że są spełniane niezbędne reguły przechowywania dla regionów, w których działasz. Jest to czasami wymagane w celu zapewnienia zgodności ze standardami regulacyjną. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-threat-protection-atp-on-a-sql-server-is-set-to-enabled"></a>Upewnij się, że zaawansowana ochrona przed zagrożeniami (ATP) na serwerze SQL jest ustawiona na wartość "Włączono"

**IDENTYFIKATOR:** CIS Azure 4.2.1 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zaawansowane zabezpieczenia danych powinny być włączone na SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Przejmij każdą SQL Managed Instance bez zaawansowanego zabezpieczeń danych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na serwerach SQL należy włączyć zaawansowane zabezpieczenia danych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Inspekcja serwerów SQL bez Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-vulnerability-assessment-va-is-enabled-on-a-sql-server-by-setting-a-storage-account"></a>Upewnij się, że ocena luk w zabezpieczeniach jest włączona na serwerze SQL, ustawiając konto magazynu

**Identyfikator:** CIS Azure 4.2.2 **Ownership**: Customer (Własność platformy Azure CIS 4.2.2): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Ocena luk w zabezpieczeniach powinna być włączona na SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Przejmij SQL Managed Instance, które nie mają włączonych cyklicznych skanowań oceny luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może odnajdywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Inspekcja Azure SQL, które nie mają włączonych cyklicznych skanowań oceny luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może odnajdywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

### <a name="ensure-that-va-setting-send-scan-reports-to-is-configured-for-a-sql-server"></a>Upewnij się, że dla serwera SQL skonfigurowano ustawienie Va Va (Wyślij raporty skanowania do)

**Identyfikator:** CIS Azure 4.2.4 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Ustawienia oceny luk w zabezpieczeniach dla programu SQL Server powinny zawierać adres e-mail do odbierania raportów skanowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057d6cfe-9c4f-4a6d-bc60-14420ea1f1a9) |Upewnij się, że dla pola "Wyślij raporty skanowania" w ustawieniach oceny luk w zabezpieczeniach podano adres e-mail. Ten adres e-mail otrzymuje podsumowanie wyników skanowania po okresowym skanowaniu uruchomionym na serwerach SQL. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Upewnij się, że opcja "Wymuś połączenie SSL" jest ustawiona na wartość "WŁĄCZONE" dla serwera bazy danych PostgreSQL

**Identyfikator:** CIS Azure 4.3.1 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy włączyć wymuszanie połączenia SSL dla serwerów baz danych MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle", szyfrując strumień danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Upewnij się, że opcja "Wymuszaj połączenie SSL" jest ustawiona na wartość "WŁĄCZONE" dla serwera bazy danych MySQL

**Identyfikator:** CIS Azure 4.3.2 **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[W przypadku serwerów baz danych PostgreSQL należy włączyć wymuszanie połączenia SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle" przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Upewnij się, że parametr serwera "log_checkpoints" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

**Identyfikator:** CIS Azure 4.3.3 **Ownership**: Customer (Własność platformy Azure CIS 4.3.3): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Punkty kontrolne dzienników powinny być włączone dla serwerów baz danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Te zasady pomagają w inspekcji baz danych PostgreSQL w środowisku bez log_checkpoints włączone. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Upewnij się, że parametr serwera "log_connections" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

**Identyfikator:** CIS Azure 4.3.4 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Połączenia dzienników powinny być włączone dla serwerów baz danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Te zasady pomagają w inspekcji baz danych PostgreSQL w środowisku bez log_connections włączone. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Upewnij się, że parametr serwera "log_disconnections" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

**Identyfikator:** CIS Azure 4.3.5 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Odłączenia powinny być rejestrowane dla serwerów baz danych PostgreSQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Te zasady pomagają w inspekcji baz danych PostgreSQL w środowisku bez log_disconnections włączone. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Upewnij się, że parametr serwera "connection_throttling" jest ustawiony na wartość "ON" dla serwera bazy danych PostgreSQL

**Identyfikator:** CIS Azure 4.3.6 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Ograniczanie połączeń powinno być włączone dla serwerów baz danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Te zasady pomagają w inspekcji baz danych PostgreSQL w środowisku bez włączonego ograniczania połączenia. To ustawienie umożliwia tymczasowe ograniczanie przepustowości połączeń na adres IP w przypadku zbyt wielu niepowodzeń logowania nieprawidłowym hasłem. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Upewnij się, Azure Active Directory administrator jest skonfigurowany

**Identyfikator:** CIS Azure 4.4 **Ownership**: Customer (Własność platformy Azure CIS 4.4): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Administrator Azure Active Directory powinien być aprowstrowany dla serwerów SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Przejmij aprowizowanie konta Azure Active Directory dla serwera SQL, aby włączyć uwierzytelnianie usługi Azure AD. Uwierzytelnianie usługi Azure AD umożliwia uproszczone zarządzanie uprawnieniami i scentralizowane zarządzanie tożsamościami użytkowników bazy danych i innych usługi firmy Microsoft |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-customer-managed-key"></a>Upewnij się, że funkcji ochrony TDE programu SQL Server jest szyfrowana za pomocą klucza zarządzanego przez klienta

**Identyfikator:** CIS Azure 4.5 **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Wystąpienia zarządzane SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Zaimplementowanie funkcji Transparent Data Encryption (TDE) przy użyciu własnego klucza zapewnia większą przejrzystość i kontrolę nad funkcji ochrony TDE, zwiększone zabezpieczenia dzięki usłudze zewnętrznej, która jest sterowana przez moduł HSM, oraz podwyższy poziom rozdzielenia obowiązków. To zalecenie dotyczy organizacji, które mają powiązane wymagania dotyczące zgodności. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Serwery SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Zaimplementowanie funkcji Transparent Data Encryption (TDE) przy użyciu własnego klucza zapewnia większą przejrzystość i kontrolę nad funkcji ochrony TDE, zwiększone zabezpieczenia dzięki usłudze zewnętrznej, która jest sterowana przez moduł HSM, oraz podwyższy poziom rozdzielenia obowiązków. To zalecenie dotyczy organizacji, które mają powiązane wymagania dotyczące zgodności. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Upewnij się, że kontener magazynu przechowujący dzienniki aktywności nie jest publicznie dostępny

**Identyfikator:** CIS Azure 5.1.3 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dostęp publiczny do konta magazynu powinien być niedozwolone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Anonimowy publiczny dostęp do odczytu do kontenerów i obiektów blob w usłudze Azure Storage to wygodny sposób udostępniania danych, ale może stanowić zagrożenie bezpieczeństwa. Aby zapobiec naruszeniom bezpieczeństwa danych spowodowanym przez niepożądany dostęp anonimowy, firma Microsoft zaleca uniemożliwienie publicznego dostępu do konta magazynu, chyba że jest to wymagane w scenariuszu. |inspekcja, odmowa, wyłączone |[2.0.1 —wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Upewnij się, że konto magazynu zawierające kontener z dziennikami aktywności jest szyfrowane za pomocą usługi BYOK (Użyj własnego klucza)

**Identyfikator:** CIS Azure 5.1.4 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Konto magazynu zawierające kontener z dziennikami aktywności musi być zaszyfrowane za pomocą usługi BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Te zasady przeprowadza inspekcję, czy konto magazynu zawierające kontener z dziennikami aktywności jest szyfrowane za pomocą usługi BYOK. Zasady te są dostępne tylko wtedy, gdy konto magazynu znajduje się w tej samej subskrypcji co dzienniki aktywności. Więcej informacji na temat szyfrowania magazynów w usłudze Azure Storage można znaleźć [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) tutaj.  |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Upewnij się, że rejestrowanie dla usługi Azure KeyVault jest włączone

**Identyfikator:** CIS Azure 5.1.5 **Ownership**: Customer (Własność platformy Azure CIS 5.1.5): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dzienniki zasobów w Key Vault powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć ślady aktywności do użycia na potrzeby badania w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia bezpieczeństwa sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Upewnij się, że istnieje alert dziennika aktywności dla tworzenia przypisania zasad

**IDENTYFIKATOR:** CIS Azure 5.2.1 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji zasad](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Te zasady przeprowadza inspekcję określonych operacji zasad bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-policy-assignment"></a>Upewnij się, że istnieje alert dziennika aktywności dla przypisania zasad usuwania

**Identyfikator:** CIS Azure 5.2.2 **Ownership**( Własność platformy Azure CIS 5.2.2): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji zasad](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Te zasady przeprowadza inspekcję określonych operacji zasad bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Upewnij się, że istnieje alert dziennika aktywności dla tworzenia lub aktualizowania sieciowej grupy zabezpieczeń

**Identyfikator:** CIS Azure 5.2.3 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Te zasady przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Upewnij się, że istnieje alert dziennika aktywności dla usuwania sieciowej grupy zabezpieczeń

**Identyfikator:** CIS Azure 5.2.4 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Te zasady przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Upewnij się, że istnieje alert dziennika aktywności dla reguły tworzenia lub aktualizowania sieciowej grupy zabezpieczeń

**Identyfikator:** CIS Azure 5.2.5 **Ownership**: Customer (Własność platformy Azure CIS 5.2.5): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Te zasady przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Upewnij się, że istnieje alert dziennika aktywności dla reguły Usuwania sieciowej grupy zabezpieczeń

**Identyfikator:** CIS Azure 5.2.6 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Te zasady przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Upewnij się, że istnieje alert dziennika aktywności dla tworzenia lub aktualizowania rozwiązania zabezpieczeń

**Identyfikator:** CIS Azure 5.2.7 **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Te zasady przeprowadza inspekcję określonych operacji zabezpieczeń bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Upewnij się, że istnieje alert dziennika aktywności dla rozwiązania do usuwania zabezpieczeń

**Identyfikator:** CIS Azure 5.2.8 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Te zasady przeprowadza inspekcję określonych operacji zabezpieczeń bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Upewnij się, że istnieje alert dziennika aktywności dla tworzenia, aktualizowania lub SQL Server reguły zapory

**Identyfikator:** CIS Azure 5.2.9 **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Te zasady przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-diagnostic-logs-are-enabled-for-all-services-which-support-it"></a>Upewnij się, że dzienniki diagnostyczne są włączone dla wszystkich usług, które je obsługują.

**Identyfikator:** CIS Azure 5.3 **Ownership**: Customer (Własność platformy Azure CIS 5.3): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dzienniki diagnostyczne w App Services powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Inspekcja włączania dzienników diagnostycznych w aplikacji. Umożliwia to odtworzenie śladów aktywności na potrzeby badania w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia bezpieczeństwa sieci |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[Dzienniki zasobów w usłudze Azure Data Lake Store powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Inspekcja włączania dzienników zasobów. Dzięki temu można ponownie tworzyć trasy działań do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Dzienniki zasobów w Azure Stream Analytics powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć trasy działań do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Dzienniki zasobów na kontach usługi Batch powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć trasy działań do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Dzienniki zasobów w Data Lake Analytics powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Inspekcja włączania dzienników zasobów. Dzięki temu można ponownie tworzyć trasy działań do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[Dzienniki zasobów w centrum zdarzeń powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć ślady aktywności do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[Dzienniki zasobów w IoT Hub powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć ślady aktywności do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Dzienniki zasobów w Key Vault powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć ślady aktywności do użycia na potrzeby badania w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia bezpieczeństwa sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Dzienniki zasobów w Logic Apps powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć ślady aktywności do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Dzienniki zasobów w usługach wyszukiwania powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć trasy działań do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Dzienniki zasobów w Service Bus powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć trasy działań do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Dzienniki zasobów w Virtual Machine Scale Sets powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Zaleca się włączenie dzienników, aby można było odtworzyć dziennik aktywności, gdy badania są wymagane w przypadku zdarzenia lub naruszenia bezpieczeństwa. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

## <a name="networking"></a>Sieć

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Upewnij się, że dostęp RDP jest ograniczony z Internetu

**Identyfikator:** CIS Azure 6.1 **Ownership**: Customer (Własność platformy Azure CIS 6.1): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dostęp RDP z Internetu powinien być zablokowany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Te zasady umożliwiają inspekcję wszelkich reguł zabezpieczeń sieci, które zezwalają na dostęp RDP z Internetu |Inspekcja, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Upewnij się, że dostęp do SSH jest ograniczony z Internetu

**Identyfikator:** CIS Azure 6.2 **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dostęp SSH z Internetu powinien być zablokowany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Te zasady umożliwiają inspekcję wszelkich reguł zabezpieczeń sieci, które zezwalają na dostęp SSH z Internetu |Inspekcja, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Upewnij się, Network Watcher jest włączona

**Identyfikator:** CIS Azure 6.5 **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie sieci na poziomie sieci na platformie Azure, do i z niej. Monitorowanie na poziomie scenariusza umożliwia diagnozowanie problemów w widoku na poziomie sieci. Narzędzia do diagnostyki i wizualizacji sieci Network Watcher pomagają zrozumieć, zdiagnozować i uzyskać wgląd w sieć na platformie Azure. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-virtual-machines-are-utilizing-managed-disks"></a>Upewnij Virtual Machines, że Dyski zarządzane

**Identyfikator:** CIS Azure 7.1 **Ownership**: Customer (Własność platformy Azure CIS 7.1): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Te zasady przeprowadza inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |

### <a name="ensure-that-os-and-data-disks-are-encrypted-with-cmk"></a>Upewnij się, że dyski systemu operacyjnego i danych są szyfrowane za pomocą szyfrowania cmk

**Identyfikator:** CIS Azure 7.2 **Ownership**: Customer (Własność platformy Azure CIS 7.2): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Maszyny wirtualne bez włączonego szyfrowania dysków będą monitorowane przez Azure Security Center zgodnie z zaleceniami. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted-with-cmk"></a>Upewnij się, że "Niedołączene dyski" są szyfrowane przy użyciu szyfrowania cmk

**Identyfikator:** CIS Azure 7.3 **Ownership**: Customer (Własność platformy Azure CIS 7.3): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Niedołączane dyski powinny być szyfrowane](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Te zasady przeprowadza inspekcję niedołączanych dysków bez włączonego szyfrowania. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Upewnij się, że są zainstalowane tylko zatwierdzone rozszerzenia

**Identyfikator:** CIS Azure 7.4 **Ownership**: Customer (Własność platformy Azure CIS 7.4): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy zainstalować tylko zatwierdzone rozszerzenia maszyny wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Te zasady określają rozszerzenia maszyny wirtualnej, które nie zostały zatwierdzone. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Upewnij się, że są stosowane najnowsze poprawki systemu operacyjnego dla Virtual Machines systemu operacyjnego

**Identyfikator:** CIS Azure 7.5 **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy zainstalować aktualizacje systemu na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Upewnij się, że program Endpoint Protection Virtual Machines zainstalowany

**Identyfikator:** CIS Azure 7.6 **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Monitorowanie braku programu Endpoint Protection w programie Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Serwery bez zainstalowanego agenta programu Endpoint Protection będą monitorowane przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Inne uwagi dotyczące zabezpieczeń

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Upewnij się, że data wygaśnięcia jest ustawiona na wszystkich kluczach

**Identyfikator:** CIS Azure 8.1 **Ownership**: Customer (Własność platformy Azure CIS 8.1): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault kluczy powinny mieć datę wygaśnięcia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |Klucze kryptograficzne powinny mieć zdefiniowaną datę wygaśnięcia i nie powinny być trwałe. Klucze, które są prawidłowe, na zawsze zapewniają potencjalnej osobie atakującej więcej czasu na złamanie klucza. Zaleca się ustawienie dat wygaśnięcia kluczy kryptograficznych. |Inspekcja, Odmowa, Wyłączone |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Upewnij się, że data wygaśnięcia jest ustawiona dla wszystkich wpisów tajnych

**Identyfikator:** CIS Azure 8.2 **Ownership**: Customer (Własność platformy Azure CIS 8.2): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault tajne powinny mieć datę wygaśnięcia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |Wpisy tajne powinny mieć zdefiniowaną datę wygaśnięcia i nie powinny być trwałe. Wpisy tajne, które są prawidłowe, na zawsze zapewniają potencjalnej osobie atakującej więcej czasu na jego złamanie. Zalecanym rozwiązaniem w zakresie zabezpieczeń jest ustawienie dat wygaśnięcia wpisów tajnych. |Inspekcja, Odmowa, Wyłączone |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Upewnij się, że magazyn kluczy można odzyskać

**Identyfikator:** CIS Azure 8.4 **Ownership**: Customer (Własność platformy Azure CIS 8.4): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Magazyny kluczy powinny mieć włączoną ochronę przed przeczyszczaniem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Złośliwe usunięcie magazynu kluczy może prowadzić do trwałej utraty danych. Złośliwy niejawny tester w organizacji może potencjalnie usuwać i przeczyścić magazyny kluczy. Ochrona przed przeczyszczaniem chroni przed atakami niejawnych testerów, wymuszając obowiązkowy okres przechowywania nieukończonym magazynów kluczy. Nikt wewnątrz organizacji ani firma Microsoft nie będzie mógł przeczyścić twoich magazynów kluczy w okresie przechowywania usuwania nie soft delete. |Inspekcja, Odmowa, Wyłączone |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Włączanie kontroli dostępu opartej na rolach (RBAC) w usługach Azure Kubernetes Services

**Identyfikator:** CIS Azure 8.5 **Ownership**: Customer (Własność platformy Azure CIS 8.5): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[W usługach Kubernetes Access Control na podstawie ról (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Aby zapewnić szczegółowe filtrowanie akcji, które użytkownicy mogą wykonywać, użyj funkcji Role-Based Access Control (RBAC) do zarządzania uprawnieniami w klastrach usługi Kubernetes Service i skonfigurowania odpowiednich zasad autoryzacji. |Inspekcja, wyłączone |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="app-service"></a>App Service

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Upewnij App Service, że uwierzytelnianie jest ustawione na Azure App Service

**Identyfikator:** CIS Azure 9.1 **Ownership**: Customer (Własność platformy Azure CIS 9.1): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[W aplikacji interfejsu API należy włączyć uwierzytelnianie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Azure App Service Authentication to funkcja, która może uniemożliwić anonimowym żądaniom HTTP dotarcie do aplikacji interfejsu API lub uwierzytelnianie tych, które mają tokeny, zanim dotrą do aplikacji interfejsu API |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[Uwierzytelnianie powinno być włączone w aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Azure App Service Authentication to funkcja, która może uniemożliwić anonimowym żądaniom HTTP dotarcie do aplikacji funkcji lub uwierzytelnianie tych, które mają tokeny, zanim dotrą do aplikacji funkcji |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[W aplikacji internetowej powinno być włączone uwierzytelnianie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Azure App Service Authentication to funkcja, która może uniemożliwić anonimowym żądaniom HTTP dotarcie do aplikacji internetowej lub uwierzytelnienie tych, które mają tokeny, zanim dotrą do aplikacji internetowej |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Upewnij się, że aplikacja internetowa przekierowuje cały ruch HTTP do protokołu HTTPS w Azure App Service

**Identyfikator:** CIS Azure 9.2 **Ownership**: Customer (Własność platformy Azure CIS 9.2): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Upewnij się, że aplikacja internetowa korzysta z najnowszej wersji szyfrowania TLS

**Identyfikator:** CIS Azure 9.3 **Ownership**: Customer (Własność platformy Azure CIS 9.3): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Najnowsza wersja TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji internetowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Upewnij się, że aplikacja internetowa ma ustawioną wartość "Certyfikaty klienta (przychodzące certyfikaty klienta)" na wartość "Wł."

**Identyfikator:** CIS Azure 9.4 **Ownership**: Customer (Własność platformy Azure CIS 9.4): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Upewnij się, że aplikacja interfejsu API ma ustawioną wartość "Certyfikaty klienta (przychodzące certyfikaty klienta)" na wartość "Wł."](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Certyfikaty klienta umożliwiają aplikacji żądanie certyfikatu dla żądań przychodzących. Tylko klienci z prawidłowym certyfikatem będą mogli uzyskać dostęp do aplikacji. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Upewnij się, że dla aplikacji internetowej ustawiono wartość "Certyfikaty klienta (przychodzące certyfikaty klienta)" na wartość "Wł."](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Certyfikaty klienta umożliwiają aplikacji żądanie certyfikatu dla żądań przychodzących. Tylko klienci z prawidłowym certyfikatem będą mogli uzyskać dostęp do aplikacji. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[Aplikacje funkcji powinny mieć włączoną funkcję "Certyfikaty klienta (przychodzące certyfikaty klienta)"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Certyfikaty klienta umożliwiają aplikacji żądanie certyfikatu dla żądań przychodzących. Tylko klienci z prawidłowymi certyfikatami będą mogli uzyskać dostęp do aplikacji. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Upewnij się, że Azure Active Directory jest włączona na App Service

**Identyfikator:** CIS Azure 9.5 **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Tożsamość zarządzana powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Używanie tożsamości zarządzanej w celu zwiększenia bezpieczeństwa uwierzytelniania |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Tożsamość zarządzana powinna być używana w aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Używanie tożsamości zarządzanej w celu zwiększenia bezpieczeństwa uwierzytelniania |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Tożsamość zarządzana powinna być używana w aplikacji internetowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Używanie tożsamości zarządzanej w celu zwiększenia bezpieczeństwa uwierzytelniania |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Upewnij się, że "wersja PHP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji internetowej

**Identyfikator:** CIS Azure 9.6 **Ownership**: Customer (Własność platformy Azure CIS 9.6): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Upewnij się, że "wersja PHP" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Okresowo nowsze wersje są wydane dla oprogramowania PHP ze względu na wady zabezpieczeń lub w celu dołączyć dodatkowe funkcje. Używanie najnowszej wersji języka PHP dla aplikacji interfejsu API jest zalecane w celu skorzystania z poprawek zabezpieczeń, jeśli są dostępne, i/lub nowych funkcji najnowszej wersji. Obecnie te zasady dotyczą tylko aplikacji internetowych systemu Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Upewnij się, że "wersja PHP" jest najnowsza, jeśli jest używana jako część aplikacji internetowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Okresowo są wydane nowsze wersje oprogramowania PHP ze względu na luki w zabezpieczeniach lub w celu dołączyć dodatkowe funkcje. Używanie najnowszej wersji języka PHP dla aplikacji internetowych jest zalecane w celu skorzystania z poprawek zabezpieczeń, jeśli są dostępne, i/lub nowych funkcji najnowszej wersji. Obecnie te zasady dotyczą tylko aplikacji internetowych systemu Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana do uruchamiania aplikacji internetowej

**Identyfikator:** CIS Azure 9.7 **Ownership**: Customer (Własność platformy Azure CIS 9.7): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Okresowo są wydane nowsze wersje oprogramowania w języku Python ze względu na wady zabezpieczeń lub w celu dołączyć dodatkowe funkcje. Używanie najnowszej wersji języka Python dla aplikacji interfejsu API jest zalecane w celu skorzystania z poprawek zabezpieczeń, jeśli są dostępne, i/lub nowych funkcji najnowszej wersji. Obecnie te zasady dotyczą tylko aplikacji internetowych systemu Linux. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Okresowo nowsze wersje są zwalniane dla oprogramowania w języku Python ze względu na luki w zabezpieczeniach lub w celu dołączyć dodatkowe funkcje. Używanie najnowszej wersji języka Python dla aplikacji funkcji jest zalecane w celu skorzystania z poprawek zabezpieczeń, jeśli są dostępne, i/lub nowych funkcji najnowszej wersji. Obecnie te zasady dotyczą tylko aplikacji internetowych systemu Linux. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji internetowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Okresowo nowsze wersje są zwalniane dla oprogramowania w języku Python ze względu na luki w zabezpieczeniach lub w celu dołączyć dodatkowe funkcje. Używanie najnowszej wersji języka Python dla aplikacji internetowych jest zalecane w celu skorzystania z poprawek zabezpieczeń, jeśli są dostępne, i/lub nowych funkcji najnowszej wersji. Obecnie te zasady dotyczą tylko aplikacji internetowych systemu Linux. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Upewnij się, że "wersja języka Java" jest najnowsza, jeśli jest używana do uruchamiania aplikacji internetowej

**Identyfikator:** CIS Azure 9.8 **Ownership**: Customer (Własność platformy Azure CIS 9.8): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Upewnij się, że "wersja języka Java" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Okresowo dla języka Java są zwalniane nowsze wersje ze względu na luki w zabezpieczeniach lub w celu dołączyć dodatkowe funkcje. Używanie najnowszej wersji języka Python dla aplikacji interfejsu API jest zalecane w celu skorzystania z poprawek zabezpieczeń, jeśli są dostępne, i/lub nowych funkcji najnowszej wersji. Obecnie te zasady dotyczą tylko aplikacji internetowych systemu Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Upewnij się, że "wersja języka Java" jest najnowsza, jeśli jest używana jako część aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Okresowo są wydane nowsze wersje oprogramowania Java ze względu na luki w zabezpieczeniach lub w celu dołączyć dodatkowe funkcje. Używanie najnowszej wersji języka Java dla aplikacji funkcji jest zalecane w celu korzystania z poprawek zabezpieczeń, jeśli są dostępne, i/lub nowych funkcji najnowszej wersji. Obecnie te zasady dotyczą tylko aplikacji internetowych systemu Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Upewnij się, że "wersja języka Java" jest najnowsza, jeśli jest używana jako część aplikacji internetowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Okresowo są dostępne nowsze wersje oprogramowania Java ze względu na wady zabezpieczeń lub dodatkowe funkcje. Używanie najnowszej wersji języka Java dla aplikacji internetowych jest zalecane w celu skorzystania z poprawek zabezpieczeń, jeśli są dostępne, i/lub nowych funkcji najnowszej wersji. Obecnie te zasady dotyczą tylko aplikacji internetowych systemu Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Upewnij się, że "Wersja PROTOKOŁU HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji internetowej

**Identyfikator:** CIS Azure 9.9 **Ownership**: Customer (Własność platformy Azure CIS 9.9): Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Upewnij się, że "Wersja HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Okresowo nowsze wersje są zwalniane dla protokołu HTTP ze względu na luki w zabezpieczeniach lub w celu dołączyć dodatkowe funkcje. Korzystanie z najnowszej wersji protokołu HTTP dla aplikacji internetowych w celu korzystania z poprawek zabezpieczeń, jeśli są dostępne, i/lub nowych funkcji nowszej wersji. Obecnie te zasady dotyczą tylko aplikacji internetowych systemu Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Upewnij się, że "Wersja HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Okresowo nowsze wersje są zwalniane dla protokołu HTTP ze względu na luki w zabezpieczeniach lub w celu dołączyć dodatkowe funkcje. Korzystanie z najnowszej wersji protokołu HTTP dla aplikacji internetowych w celu korzystania z poprawek zabezpieczeń, jeśli są dostępne, i/lub nowych funkcji nowszej wersji. Obecnie te zasady dotyczą tylko aplikacji internetowych systemu Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Upewnij się, że "Wersja HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji internetowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Okresowo nowsze wersje są zwalniane dla protokołu HTTP ze względu na luki w zabezpieczeniach lub w celu dołączyć dodatkowe funkcje. Korzystanie z najnowszej wersji protokołu HTTP dla aplikacji internetowych w celu korzystania z poprawek zabezpieczeń, jeśli są dostępne, i/lub nowych funkcji nowszej wersji. Obecnie te zasady dotyczą tylko aplikacji internetowych systemu Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

### <a name="ensure-ftp-deployments-are-disabled"></a>Upewnij się, że wdrożenia FTP są wyłączone

**Identyfikator:** CIS Azure 9.10 **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Protokół FTPS powinien być wymagany tylko w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |Włączanie wymuszania protokołu FTPS w celu zwiększenia bezpieczeństwa |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[Protokół FTPS powinien być wymagany tylko w aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |Włączanie wymuszania protokołu FTPS w celu zwiększenia bezpieczeństwa |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[Usługa FTPS powinna być wymagana w aplikacji internetowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |Włączanie wymuszania protokołu FTPS w celu zwiększenia bezpieczeństwa |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |

> [!NOTE]
> Dostępność określonych definicji Azure Policy może się różnić w Azure Government i innych chmurach krajowych.

## <a name="next-steps"></a>Następne kroki

Dodatkowe artykuły dotyczące Azure Policy:

- [Omówienie zgodności z przepisami.](../concepts/regulatory-compliance.md)
- Zobacz strukturę [definicji inicjatywy](../concepts/initiative-definition-structure.md).
- Przejrzyj inne przykłady pod [Azure Policy przykładami.](./index.md)
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [korygować niezgodne zasoby.](../how-to/remediate-resources.md)
