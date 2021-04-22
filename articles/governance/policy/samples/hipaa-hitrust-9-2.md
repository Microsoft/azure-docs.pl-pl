---
title: Szczegóły zgodności z przepisami HIPAA HITRUST 9.2
description: Szczegóły wbudowanej inicjatywy HIPAA HITRUST 9.2 Dotyczącej zgodności z przepisami. Każda kontrolka jest mapowana na co najmniej jedną Azure Policy, które pomagają w ocenie.
ms.date: 04/21/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: b2843e569854947a83b93c0dde5529cc865721e2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864383"
---
# <a name="details-of-the-hipaa-hitrust-92-regulatory-compliance-built-in-initiative"></a>Szczegóły wbudowanej inicjatywy HIPAA HITRUST 9.2 Dotyczącej zgodności z przepisami

W poniższym artykule szczegółowo opisano sposób mapowania Azure Policy zgodności  z  przepisami na domeny zgodności i mechanizmy kontroli w ustawie HIPAA HITRUST 9.2.
Aby uzyskać więcej informacji na temat tego standardu zgodności, [zobacz HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html). Aby zrozumieć _własność_, zobacz [Azure Policy definicji zasad i](../concepts/definition-structure.md#type) [Wspólna odpowiedzialność w chmurze.](../../../security/fundamentals/shared-responsibility.md)

Poniższe mapowania są mapami na **kontrolki HIPAA HITRUST 9.2.** Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonej **domeny zgodności.** Wiele kontrolek jest implementowane za pomocą [Azure Policy](../overview.md) definicji inicjatywy. Aby przejrzeć pełną definicję inicjatywy, otwórz pozycję **Zasady** w Azure Portal i wybierz **stronę Definicje.**
Następnie znajdź i wybierz wbudowaną definicję inicjatywy **HITRUST/HIPAA** Regulatory Compliance.

Ta wbudowana inicjatywa jest wdrażana w ramach przykładu strategii [HIPAA HITRUST 9.2](../../blueprints/samples/hipaa-hitrust-9-2.md).

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co [najmniej jedną Azure Policy](../overview.md) definicją.
> Te zasady mogą ułatwić [ocenę zgodności](../how-to/get-compliance-data.md) z kontrolą; Jednak często nie występuje dopasowanie jeden do jednego lub pełne dopasowanie między kontrolą a co najmniej jedną zasadą. W związku z **tym zgodność** w Azure Policy odnosi się tylko do samych definicji zasad; Nie gwarantuje to pełnej zgodności ze wszystkimi wymaganiami kontrolki. Ponadto standard zgodności obejmuje mechanizmy kontroli, które nie są obecnie Azure Policy przez żadne definicje. Dlatego zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między domenami zgodności, kontrolkami i Azure Policy dla tego standardu zgodności mogą zmieniać się z czasem. Aby wyświetlić historię zmian, zobacz [GitHub Commit History (Historia zatwierdzń w usłudze GitHub).](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/HIPAA_HITRUST_audit.json)

## <a name="privilege-management"></a>Zarządzanie uprawnieniami

### <a name="access-to-management-functions-or-administrative-consoles-for-systems-hosting-virtualized-systems-are-restricted-to-personnel-based-upon-the-principle-of-least-privilege-and-supported-through-technical-controls"></a>Dostęp do funkcji zarządzania lub konsol administracyjnych dla systemów hostowanych zwirtualizowanych systemów jest ograniczony do personelu zgodnie z zasadą najmniejszych uprawnień i jest obsługiwany za pośrednictwem kontroli technicznej.

**Identyfikator:** 11180.01c3System.6 — 01.c **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu do sieci just in time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp do sieci just in time (JIT) będzie monitorowany przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="privileges-are-formally-authorized-and-controlled-allocated-to-users-on-a-need-to-use-and-event-by-event-basis-for-their-functional-role-eg-user-or-administrator-and-documented-for-each-system-productelement"></a>Uprawnienia są formalnie autoryzowane i kontrolowane, przydzielane użytkownikom na podstawie potrzeb i zdarzeń dla ich roli funkcjonalnej (np. użytkownika lub administratora) i udokumentowane dla każdego produktu/elementu systemu.

**Identyfikator:** 1143.01c1System.123 - 01.c **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Porty zarządzania powinny być zamknięte na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Otwarte porty zarządzania zdalnego narażają maszynę wirtualną na wysoki poziom ryzyka związanego z atakami internetowymi. Te ataki usiłują atak siłowy na poświadczenia w celu uzyskania dostępu administratora do maszyny. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-explicitly-authorizes-access-to-specific-security-relevant-functions-deployed-in-hardware-software-and-firmware-and-security-relevant-information"></a>Organizacja jawnie autoryzuje dostęp do określonych funkcji istotnych dla bezpieczeństwa (wdrożonych w sprzęcie, oprogramowaniu i oprogramowaniu układowym) oraz informacji istotnych dla bezpieczeństwa.

**Identyfikator:** 1144.01c1System.4 — 01.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[W ramach subskrypcji należy wyznaczyć maksymalnie 3 właścicieli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Zaleca się wyznaczenie maksymalnie 3 właścicieli subskrypcji w celu ograniczenia ryzyka naruszenia zabezpieczeń przez naruszonego właściciela. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="role-based-access-control-is-implemented-and-capable-of-mapping-each-user-to-one-or-more-roles-and-each-role-to-one-or-more-system-functions"></a>Zaimplementowano kontrolę dostępu opartą na rolach, która umożliwia mapowanie poszczególnych użytkowników na co najmniej jedną rolę, a każda rola na co najmniej jedną funkcję systemową.

**Identyfikator:** 1145.01c2System.1 - 01.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Do subskrypcji powinien być przypisany więcej niż jeden właściciel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Zaleca się wyznaczenie więcej niż jednego właściciela subskrypcji w celu zapewnienia nadmiarowości dostępu administratora. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="the-organization-promotes-the-development-and-use-of-programs-that-avoid-the-need-to-run-with-elevated-privileges-and-system-routines-to-avoid-the-need-to-grant-privileges-to-users"></a>Organizacja wspiera opracowywanie i używanie programów, które unikają konieczności uruchamiania z podwyższonym poziomem uprawnień i procedur systemowych, aby uniknąć konieczności udzielania uprawnień użytkownikom.

**Identyfikator:** 1146.01c2System.23 — własność 01.c: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Konta zewnętrzne z uprawnieniami właściciela należy usunąć z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |

### <a name="elevated-privileges-are-assigned-to-a-different-user-id-from-those-used-for-normal-business-use-all-users-access-privileged-services-in-a-single-role-and-such-privileged-access-is-minimized"></a>Podniesione uprawnienia są przypisywane do innego identyfikatora użytkownika niż używany do normalnego użytku biznesowego, wszyscy użytkownicy mają dostęp do usług uprzywilejowanych w ramach jednej roli, a taki dostęp uprzywilejowany jest zminimalizowany.

**Identyfikator:** 1147.01c2System.456 - 01.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji.  Przestarzałe konta to konta, których logowanie zostało zablokowane. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="the-organization-restricts-access-to-privileged-functions-and-all-security-relevant-information"></a>Organizacja ogranicza dostęp do funkcji uprzywilejowanych i wszystkich informacji istotnych dla bezpieczeństwa.

**Identyfikator:** 1148.01c2System.78 - 01.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja użycia niestandardowych reguł RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Inspekcja ról wbudowanych, takich jak "Właściciel, Współtator, Czytelnik" zamiast niestandardowych ról RBAC, które są podatne na błędy. Używanie ról niestandardowych jest traktowane jako wyjątek i wymaga rygorystycznego przeglądu i modelowania zagrożeń |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń — konta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee984370-154a-4ee8-9726-19d900e56fc0) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Opcje zabezpieczeń — konta", aby ograniczyć użycie pustych haseł i stanu konta gościa na koncie lokalnym. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAccounts_AINE.json) |

### <a name="the-organization-facilitates-information-sharing-by-enabling-authorized-users-to-determine-a-business-partners-access-when-discretion-is-allowed-as-defined-by-the-organization-and-by-employing-manual-processes-or-automated-mechanisms-to-assist-users-in-making-information-sharingcollaboration-decisions"></a>Organizacja ułatwia udostępnianie informacji, umożliwiając autoryzowanym użytkownikom określenie dostępu partnera biznesowego, gdy jest to dozwolone zgodnie z definicją organizacji, oraz przez zastosowanie ręcznych procesów lub zautomatyzowanych mechanizmów, aby pomóc użytkownikom w podejmowaniu decyzji dotyczących udostępniania/współpracy informacji.

**Identyfikator:** 1149.01c2System.9 — 01.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[W usługach Kubernetes Access Control na podstawie ról (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Aby zapewnić szczegółowe filtrowanie akcji, które użytkownicy mogą wykonywać, użyj funkcji Role-Based Access Control (RBAC) do zarządzania uprawnieniami w klastrach usługi Kubernetes Service i skonfigurowania odpowiednich zasad autoryzacji. |Inspekcja, wyłączone |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="the-access-control-system-for-the-system-components-storing-processing-or-transmitting-covered-information-is-set-with-a-default-quotdeny-allquot-setting"></a>System kontroli dostępu dla składników systemu przechowujący, przetwarzających lub przesyłających objęte informacje jest ustawiany z domyślnym ustawieniem &quot; "odmów &quot; wszystkim".

**Identyfikator:** 1150.01c2System.10 - 01.c **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Porty zarządzania powinny być zamknięte na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Otwarte porty zarządzania zdalnego narażają maszynę wirtualną na wysoki poziom ryzyka związanego z atakami internetowymi. Te ataki usiłują atak siłowy na poświadczenia w celu uzyskania dostępu administratora do maszyny. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-limits-authorization-to-privileged-accounts-on-information-systems-to-a-pre-defined-subset-of-users"></a>Organizacja ogranicza autoryzację do uprzywilejowanych kont w systemach informacyjnych do wstępnie zdefiniowanego podzestawu użytkowników.

**Identyfikator:** 1151.01c3System.1 - 01.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[W ramach subskrypcji należy wyznaczyć maksymalnie 3 właścicieli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Zaleca się wyznaczenie maksymalnie 3 właścicieli subskrypcji w celu zmniejszenia ryzyka naruszenia zabezpieczeń przez naruszonego właściciela. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-audits-the-execution-of-privileged-functions-on-information-systems-and-ensures-information-systems-prevent-non-privileged-users-from-executing-privileged-functions"></a>Organizacja przeprowadza inspekcję wykonywania funkcji uprzywilejowanych w systemach informacyjnych i zapewnia, że systemy informacyjne uniemożliwiają użytkownikom bez uprawnień wykonywanie funkcji uprzywilejowanych.

**Identyfikator:** 1152.01c3System.2 — własność 01.c: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Do subskrypcji powinien być przypisany więcej niż jeden właściciel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Zaleca się wyznaczenie więcej niż jednego właściciela subskrypcji w celu zapewnienia nadmiarowości dostępu administratora. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="all-file-system-access-not-explicitly-required-is-disabled-and-only-authorized-users-are-permitted-access-to-only-that-which-is-expressly-required-for-the-performance-of-the-users-job-duties"></a>Cały dostęp do systemu plików, który nie jest jawnie wymagany, jest wyłączony, a tylko autoryzowani użytkownicy mają dostęp tylko do tych, które są wyraźnie wymagane do wykonywania zadań użytkowników.

**Identyfikator:** 1153.01c3System.35 - 01.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[W usługach Kubernetes Access Control na podstawie ról (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Aby zapewnić szczegółowe filtrowanie akcji, które użytkownicy mogą wykonywać, użyj funkcji Role-Based Access Control (RBAC) do zarządzania uprawnieniami w klastrach usługi Kubernetes Service i skonfigurowania odpowiednich zasad autoryzacji. |Inspekcja, wyłączone |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="contractors-are-provided-with-minimal-system-and-physical-access-only-after-the-organization-assesses-the-contractors-ability-to-comply-with-its-security-requirements-and-the-contractor-agrees-to-comply"></a>Wykonawcy mają minimalny dostęp systemowy i fizyczny dopiero wtedy, gdy organizacja oceni zdolność wykonawcy do spełnienia wymagań dotyczących zabezpieczeń i zgodzi się na ich przestrzeganie.

**Identyfikator:** 1154.01c3System.4 — 01.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Zaleca się wyznaczenie maksymalnie 3 właścicieli subskrypcji w celu zmniejszenia ryzyka naruszenia zabezpieczeń przez naruszonego właściciela. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

## <a name="user-authentication-for-external-connections"></a>Uwierzytelnianie użytkowników dla połączeń zewnętrznych

### <a name="strong-authentication-methods-such-as-multi-factor-radius-or-kerberos-for-privileged-access-and-chap-for-encryption-of-credentials-for-dialup-methods-are-implemented-for-all-external-connections-to-the-organizations-network"></a>Silne metody uwierzytelniania, takie jak wieloskładnikowe, Radius lub Kerberos (dla dostępu uprzywilejowanego) i CHAP (do szyfrowania poświadczeń dla metod wybierania numerów) są implementowane dla wszystkich połączeń zewnętrznych z siecią organizacji.

**Identyfikator:** 1116.01j1Organizational.145 - 01.j **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami właściciela w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Aby zapobiec naruszeniu zabezpieczeń kont lub zasobów, należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami właściciela. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="remote-access-by-vendors-and-business-partners-eg-for-remote-maintenance-is-disableddeactivated-when-not-in-use"></a>Dostęp zdalny dostawców i partnerów biznesowych (np. w celu konserwacji zdalnej) jest wyłączany/dezaktywowany, gdy nie jest w użyciu.

**Identyfikator:** 1117.01j1Organizational.23 - 01.j **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="organizations-implement-encryption-eg-nbspvpn-solutions-or-private-lines-and-logs-remote-access-to-the-organizations-network-by-employees-contractors-or-third-party-eg-vendors"></a>Organizacje implementują szyfrowanie (np. rozwiązania sieci VPN lub linie prywatne) i rejestrują zdalny dostęp do sieci organizacji przez pracowników, wykonawców lub inne firmy &nbsp; (np. dostawców).

**Identyfikator:** 1118.01j2Organizational.124 - 01.j **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami do odczytu w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do odczytu, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="network-equipment-is-checked-for-unanticipated-dial-up-capabilities"></a>Sprzęt sieciowy jest sprawdzany pod kątem nieprzewidzianych możliwości wybierania numerów.

**Identyfikator:** 1119.01j2Organizational.3 — 01.j **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu do sieci just in time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp just in time (JIT) do sieci będzie monitorowany przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="remote-administration-sessions-are-authorized-encrypted-and-employ-increased-security-measures"></a>Sesje administracji zdalnej są autoryzowane, zaszyfrowane i wykorzystują zwiększone środki bezpieczeństwa.

**Identyfikator:** 1121.01j3Organizational.2 - 01.j **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami właściciela w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Aby zapobiec naruszeniu zabezpieczeń kont lub zasobów, należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami właściciela. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="if-encryption-is-not-used-for-dial-up-connections-the-cio-or-hisher-designated-representative-provides-specific-written-authorization"></a>Jeśli szyfrowanie nie jest używane w przypadku połączeń telefonicznych, CIO lub jego wyznaczony przedstawiciel zapewnia określoną pisemnej autoryzacji.

**Identyfikator:** 1173.01j1Organizational.6 — 01.j **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="the-organization-protects-wireless-access-to-systems-containing-sensitive-information-by-authenticating-both-users-and-devices"></a>Organizacja chroni dostęp bezprzewodowy do systemów zawierających poufne informacje przez uwierzytelnianie użytkowników i urządzeń.

**Identyfikator:** 1174.01j1Organizational.7 - 01.j **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami do odczytu w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do odczytu, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="remote-access-to-business-information-across-public-networks-only-takes-place-after-successful-identification-and-authentication"></a>Zdalny dostęp do informacji biznesowych w sieciach publicznych odbywa się tylko po pomyślnej identyfikacji i uwierzytelnieniu.

**Identyfikator:** 1175.01j1Organizational.8 — 01.j **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu do sieci just in time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp just in time (JIT) do sieci będzie monitorowany przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="the-organization-requires-a-callback-capability-with-re-authentication-to-verify-dial-up-connections-from-authorized-locations"></a>Organizacja wymaga możliwości wywołania zwrotnego z ponownym uwierzytelnieniem w celu zweryfikowania połączeń telefonicznych z autoryzowanych lokalizacji.

**Identyfikator:** 1176.01j2Organizational.5 — 01.j **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami właściciela w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami właściciela, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="user-ids-assigned-to-vendors-are-reviewed-in-accordance-with-the-organizations-access-review-policy-at-a-minimum-annually"></a>Identyfikatory użytkowników przypisane do dostawców są przeglądane zgodnie z zasadami przeglądu dostępu organizacji co najmniej raz do roku.

**Identyfikator:** 1177.01j2Organizational.6 — 01.j **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="node-authentication-including-cryptographic-techniques-eg-machine-certificates-serves-as-an-alternative-means-of-authenticating-groups-of-remote-users-where-they-are-connected-to-a-secure-shared-computer-facility"></a>Uwierzytelnianie węzłów, w tym techniki kryptograficzne (np. certyfikaty komputera), służy jako alternatywny sposób uwierzytelniania grup użytkowników zdalnych, gdy są połączeni z bezpieczną, udostępnioną komputerem.

**Identyfikator:** 1178.01j2Organizational.7 — 01.j **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami do odczytu w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do odczytu, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-monitors-and-controls-remote-access-methods"></a>System informacji monitoruje i kontroluje metody dostępu zdalnego.

**Identyfikator:** 1179.01j3Organizational.1 - 01.j **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu do sieci just in time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp just in time (JIT) do sieci będzie monitorowany przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Zdalna diagnostyka i ochrona portów konfiguracji

### <a name="access-to-network-equipment-is-physically-protected"></a>Dostęp do sprzętu sieciowego jest fizycznie chroniony.

**Identyfikator:** 1192.01l1Organizational.1 — 01.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu do sieci just in time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp just in time (JIT) do sieci będzie monitorowany przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="controls-for-the-access-to-diagnostic-and-configuration-ports-include-the-use-of-a-key-lock-and-the-implementation-of-supporting-procedures-to-control-physical-access-to-the-port"></a>Mechanizmy kontroli dostępu do portów diagnostycznych i konfiguracyjnych obejmują użycie blokady klucza i implementację procedur obsługi w celu kontrolowania fizycznego dostępu do portu.

**Identyfikator:** 1193.01l2Organizational.13 - 01.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Porty zarządzania powinny być zamknięte na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Otwarte porty zarządzania zdalnego narażają maszynę wirtualną na wysoki poziom ryzyka związanego z atakami internetowymi. Te ataki usiłują atak siłowy na poświadczenia w celu uzyskania dostępu administratora do maszyny. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="ports-services-and-similar-applications-installed-on-a-computer-or-network-systems-which-are-not-specifically-required-for-business-functionality-are-disabled-or-removed"></a>Porty, usługi i podobne aplikacje zainstalowane na komputerze lub w systemach sieciowych, które nie są specjalnie wymagane dla funkcjonalności biznesowej, są wyłączone lub usunięte.

**Identyfikator:** 1194.01l2Organizational.2 - 01.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zdalne debugowanie powinno być wyłączone dla aplikacji internetowych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Debugowanie zdalne wymaga otwarcia portów przychodzących w aplikacji internetowej. Zdalne debugowanie powinno być wyłączone. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="the-organization-reviews-the-information-system-within-every-three-hundred-and-sixty--five-365-days-to-identify-and-disables-unnecessary-and-non-secure-functions-ports-protocols-andor-services"></a>Organizacja przegląda system informacji co trzysta i sześćdziesiąt pięć (365) dni, aby zidentyfikować i wyłączyć niepotrzebne i niezabezpieczne funkcje, porty, protokoły i/lub usługi.

**Identyfikator:** 1195.01l3Organizational.1 — 01.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zdalne debugowanie powinno być wyłączone dla aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Debugowanie zdalne wymaga otwarcia portów przychodzących w aplikacjach funkcji. Debugowanie zdalne powinno być wyłączone. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-identifies-unauthorized-blacklisted-software-on-the-information-system-prevents-program-execution-in-accordance-with-a-list-of-unauthorized-blacklisted-software-programs-employs-an-allow-all-deny-by-exception-policy-to-prohibit-execution-of-known-unauthorized-blacklisted-software-and-reviews-and-updates-the-list-of-unauthorized-blacklisted-software-programs-annually"></a>Organizacja identyfikuje nieautoryzowane (na czarnej liście) oprogramowanie w systemie informacyjnym, uniemożliwia wykonywanie programów zgodnie z listą nieautoryzowanych programów (na czarnej liście), stosuje zasady zezwalania na wszystkie, odmawiania przez wyjątek w celu uniemożliwinia wykonywania znanego nieautoryzowanego (na czarnej liście) oprogramowania oraz przegląda i aktualizuje listę nieautoryzowanych (zabronionych) programów rocznie.

**Identyfikator:** 1196.01l3Organizational.24 — 01.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zdalne debugowanie powinno być wyłączone dla API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Debugowanie zdalne wymaga otwarcia portów przychodzących w aplikacjach interfejsu API. Debugowanie zdalne powinno być wyłączone. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-disables-bluetooth-and-peer-to-peer-networking-protocols-within-the-information-system-determined-to-be-unnecessary-or-non-secure"></a>Organizacja wyłącza protokoły sieci Bluetooth i peer-to-peer w systemie informacyjnym, które są niepotrzebne lub niezabezpieczne.

**Identyfikator:** 1197.01l3Organizational.3 - 01.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Na maszynach należy włączyć funkcje adaptacyjnego sterowania aplikacjami do definiowania bezpiecznych aplikacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Włącz kontrolki aplikacji, aby zdefiniować listę znanych bezpiecznych aplikacji uruchomionych na maszynach i powiadamiać o uruchomieniu innych aplikacji. Ułatwia to ochronę maszyn przed złośliwym oprogramowaniem. Aby uprościć proces konfigurowania i obsługi reguł, program Security Center uczenia maszynowego do analizowania aplikacji uruchomionych na poszczególnych maszynach i sugerowania listy znanych bezpiecznych aplikacji. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="segregation-in-networks"></a>Podział w sieciach

### <a name="the-organizations-security-gateways-eg-firewalls-enforce-security-policies-and-are-configured-to-filter-traffic-between-domains-block-unauthorized-access-and-are-used-to-maintain-segregation-between-internal-wired-internal-wireless-and-external-network-segments-eg-the-internet-including-dmzs-and-enforce-access-control-policies-for-each-of-the-domains"></a>Bramy zabezpieczeń organizacji (np. zapory) wymuszają zasady zabezpieczeń i są skonfigurowane do filtrowania ruchu między domenami, blokowania nieautoryzowanego dostępu i są używane do zachowania podziału między wewnętrznymi segmentami sieci przewodowej, wewnętrznej bezprzewodowej i zewnętrznej (np. Internetem), w tym sieciami DMZ, oraz wymuszania zasad kontroli dostępu dla każdej domeny.

**Identyfikator:** 0805.01m1Organizational.12 - 01.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Te zasady przeprowadzają inspekcję App Service nieskonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Te zasady przeprowadza inspekcję Container Registry, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Te zasady przeprowadza inspekcję Cosmos DB, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Centrum zdarzeń powinno używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Te zasady przeprowadza inspekcję wszelkich centrów zdarzeń, które nie zostały skonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Podsieci bramy nie powinny być skonfigurowane przy użyciu sieciowej grupy zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Te zasady nie zezwalają, jeśli podsieć bramy jest skonfigurowana przy użyciu sieciowej grupy zabezpieczeń. Przypisanie sieciowej grupy zabezpieczeń do podsieci bramy spowoduje, że brama przestanie działać. |Odmów |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Maszyny wirtualne z Dostępem do Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chroń maszyny wirtualne przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowych grup zabezpieczeń. Dowiedz się więcej na temat kontrolowania ruchu za pomocą sieciowych sieciowych sieciowych sieci pod [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Te zasady przeprowadza inspekcję Key Vault, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Te zasady przeprowadzają inspekcję Service Bus nieskonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Te zasady przeprowadzają inspekcję SQL Server nieskonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Konta magazynu powinny używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Te zasady przeprowadza inspekcję wszystkich kont magazynu, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chroń podsieć przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowej grupy zabezpieczeń. Sieciowegrupy zasad sieci zawierają listę reguł listy Access Control (ACL), które zezwalają na ruch sieciowy do podsieci lub go odrzucają. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Te zasady przeprowadza inspekcję każdej maszyny wirtualnej podłączonej do sieci wirtualnej, która nie została zatwierdzona. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="the-organizations-network-is-logically-and-physically-segmented-with-a-defined-security-perimeter-and-a-graduated-set-of-controls-including-subnetworks-for-publicly-accessible-system-components-that-are-logically-separated-from-the-internal-network-based-on-organizational-requirements-and-traffic-is-controlled-based-on-functionality-required-and-classification-of-the-datasystems-based-on-a-risk-assessment-and-their-respective-security-requirements"></a>Sieć organizacji jest logicznie i fizycznie segmentowana przy użyciu zdefiniowanego obwodu zabezpieczeń i stopniowanego zestawu kontrolek, w tym podsieci dla publicznie dostępnych składników systemowych, które są logicznie oddzielone od sieci wewnętrznej na podstawie wymagań organizacyjnych; Ruch jest kontrolowany na podstawie wymaganych funkcji i klasyfikacji danych/systemów na podstawie oceny ryzyka i odpowiednich wymagań dotyczących zabezpieczeń.

**Identyfikator:** 0806.01m2Organizational.12356 - 01.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Te zasady przeprowadza inspekcję App Service, które nie zostały skonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Te zasady przeprowadza inspekcję Container Registry, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Te zasady przeprowadza inspekcję Cosmos DB, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Centrum zdarzeń powinno używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Te zasady przeprowadza inspekcję wszelkich centrów zdarzeń, które nie zostały skonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Podsieci bramy nie powinny być skonfigurowane przy użyciu sieciowej grupy zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Te zasady nie zezwalają, jeśli podsieć bramy jest skonfigurowana przy użyciu sieciowej grupy zabezpieczeń. Przypisanie sieciowej grupy zabezpieczeń do podsieci bramy spowoduje, że brama przestanie działać. |Odmów |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Maszyny wirtualne z Internetem powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chroń maszyny wirtualne przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowych grup zabezpieczeń. Dowiedz się więcej na temat kontrolowania ruchu za pomocą sieciowych sieciowych sieciowych [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Te zasady przeprowadza inspekcję Key Vault nieskonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Te zasady przeprowadzają inspekcję Service Bus nieskonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Te zasady przeprowadzają inspekcję SQL Server nieskonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Konta magazynu powinny używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Te zasady przeprowadza inspekcję wszystkich kont magazynu, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chroń podsieć przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowej grupy zabezpieczeń. Sieciowegrupy zasad sieciowych zawierają listę reguł Access Control (ACL), które zezwalają na ruch sieciowy do podsieci lub go odrzucają. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Te zasady przeprowadza inspekcję każdej maszyny wirtualnej podłączonej do sieci wirtualnej, która nie została zatwierdzona. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="networks-are-segregated-from-production-level-networks-when-migrating-physical-servers-applications-or-data-to-virtualized-servers"></a>Sieci są oddzielone od sieci na poziomie produkcyjnym podczas migrowania serwerów fizycznych, aplikacji lub danych do serwerów zwirtualizowanych.

**Identyfikator:** 0894.01m2Organizational.7 — 01.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Te zasady przeprowadza inspekcję App Service, które nie zostały skonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Te zasady przeprowadza inspekcję Container Registry, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Te zasady przeprowadza inspekcję Cosmos DB, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Wdrażanie usługi Network Watcher podczas tworzenia sieci wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9b99dd8-06c5-4317-8629-9d86a3c6e7d9) |Te zasady tworzą zasób usługi Network Watcher w regionach z sieciami wirtualnymi. Należy zapewnić istnienie grupy zasobów o nazwie networkWatcherRG, która będzie używana do wdrażania wystąpień usługi Network Watcher. |DeployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |
|[Centrum zdarzeń powinno używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Te zasady przeprowadza inspekcję wszystkich centrów zdarzeń, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Podsieci bramy nie powinny być skonfigurowane przy użyciu sieciowej grupy zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Te zasady nie zezwalają na skonfigurowanie podsieci bramy przy użyciu sieciowej grupy zabezpieczeń. Przypisanie sieciowej grupy zabezpieczeń do podsieci bramy spowoduje, że brama przestanie działać. |Odmów |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Maszyny wirtualne z Internetem powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chroń maszyny wirtualne przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowych grup zabezpieczeń. Dowiedz się więcej na temat kontrolowania ruchu za pomocą sieciowych sieciowych sieciowych [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Te zasady przeprowadza inspekcję Key Vault nieskonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Te zasady przeprowadzają inspekcję Service Bus nieskonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Te zasady przeprowadza inspekcję SQL Server, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Konta magazynu powinny używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Te zasady przeprowadza inspekcję konta magazynu, które nie jest skonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chroń podsieć przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowej grupy zabezpieczeń. Sieciowegrupy zasad sieciowych zawierają listę reguł Access Control (ACL), które zezwalają na ruch sieciowy do podsieci lub go odrzucają. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Te zasady przeprowadza inspekcję każdej maszyny wirtualnej podłączonej do sieci wirtualnej, która nie została zatwierdzona. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

## <a name="network-connection-control"></a>Kontrola połączenia sieciowego

### <a name="network-traffic-is-controlled-in-accordance-with-the-organizations-access-control-policy-through-firewall-and-other-network-related-restrictions-for-each-network-access-point-or-external-telecommunication-services-managed-interface"></a>Ruch sieciowy jest kontrolowany zgodnie z zasadami kontroli dostępu organizacji za pośrednictwem zapory i innych ograniczeń związanych z siecią dla każdego punktu dostępu do sieci lub interfejsu zarządzanego zewnętrznej usługi telekomunikacyjnej.

**Identyfikator:** 0809.01n2Organizational.1234 - 01.n **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zalecenia dotyczące adaptacyjnego wzmacniania zabezpieczeń sieci powinny być stosowane na maszynach wirtualnych skierowanych do Internetu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych skierowanych do Internetu i udostępnia zalecenia dotyczące reguł sieciowej grupy zabezpieczeń, które zmniejszają ryzyko potencjalnych ataków |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Należy włączyć wymuszanie połączenia SSL dla serwerów baz danych MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle", szyfrując strumień danych między serwerem a aplikacją. Ta konfiguracja wymusza, aby protokół SSL był zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[W przypadku serwerów baz danych PostgreSQL należy włączyć wymuszanie połączenia SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle", szyfrując strumień danych między serwerem a aplikacją. Ta konfiguracja wymusza, aby protokół SSL był zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Maszyny wirtualne z Dostępem do Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chroń maszyny wirtualne przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowych grup zabezpieczeń. Dowiedz się więcej na temat kontrolowania ruchu za pomocą sieciowych sieciowych sieciowych sieci pod [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[W aplikacji funkcji powinna być używana najnowsza wersja TLS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji internetowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączania tylko połączeń za pośrednictwem protokołu SSL do Azure Cache for Redis. Korzystanie z bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Przejmij wymaganie dotyczące bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza na koncie magazynu akceptowanie żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chroń podsieć przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowej grupy zabezpieczeń. Sieciowegrupy zasad sieci zawierają listę reguł listy Access Control (ACL), które zezwalają na ruch sieciowy do podsieci lub go odrzucają. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Te zasady przeprowadza inspekcję każdej maszyny wirtualnej podłączonej do sieci wirtualnej, która nie została zatwierdzona. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="transmitted-information-is-secured-and-at-a-minimum-encrypted-over-open-public-networks"></a>Przesyłane informacje są zabezpieczane i szyfrowane co najmniej za pośrednictwem otwartych sieci publicznych.

**Identyfikator:** 0810.01n2Organizational.5 - 01.n **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zalecenia dotyczące adaptacyjnego wzmacniania zabezpieczeń sieci powinny być stosowane na maszynach wirtualnych skierowanych do Internetu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych skierowanych do Internetu i udostępnia zalecenia dotyczące reguł sieciowej grupy zabezpieczeń, które zmniejszają ryzyko ataku |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Wymuszanie połączenia SSL powinno być włączone dla serwerów baz danych MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle" przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Wymuszanie połączenia SSL powinno być włączone dla serwerów baz danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle" przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Maszyny wirtualne z Dostępem do Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chroń maszyny wirtualne przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowych grup zabezpieczeń. Dowiedz się więcej na temat kontrolowania ruchu za pomocą sieciowych sieciowych sieciowych [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji internetowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Należy włączyć tylko bezpieczne Azure Cache for Redis z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączania tylko połączeń za pośrednictwem protokołu SSL do Azure Cache for Redis. Korzystanie z bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Wymaganie inspekcji bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza na koncie magazynu akceptowanie żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chroń podsieć przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowej grupy zabezpieczeń. Sieciowegrupy zasad sieciowych zawierają listę reguł Access Control (ACL), które zezwalają na ruch sieciowy do podsieci lub go odrzucają. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Te zasady przeprowadza inspekcję każdej maszyny wirtualnej podłączonej do sieci wirtualnej, która nie została zatwierdzona. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="exceptions-to-the-traffic-flow-policy-are-documented-with-a-supporting-missionbusiness-need-duration-of-the-exception-and-reviewed-at-least-annually-traffic-flow-policy-exceptions-are-removed-when-no-longer-supported-by-an-explicit-missionbusiness-need"></a>Wyjątki od zasad przepływu ruchu są udokumentowane z potrzebą obsługi misji/działalności biznesowej, czasem trwania wyjątku i przeglądane co najmniej raz w roku; Wyjątki zasad przepływu ruchu są usuwane, gdy nie są już obsługiwane przez jawną misję/potrzeby biznesowe.

**Identyfikator:** 0811.01n2Organizational.6 — 01.n **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Na maszynach wirtualnych z Dostępem do Internetu należy stosować zalecenia dotyczące adaptacyjnego wzmacniania zabezpieczeń sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych skierowanych do Internetu i udostępnia zalecenia dotyczące reguł sieciowej grupy zabezpieczeń, które zmniejszają ryzyko ataku |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Należy włączyć wymuszanie połączenia SSL dla serwerów baz danych MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle", szyfrując strumień danych między serwerem a aplikacją. Ta konfiguracja wymusza, aby protokół SSL był zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[W przypadku serwerów baz danych PostgreSQL należy włączyć wymuszanie połączenia SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle", szyfrując strumień danych między serwerem a aplikacją. Ta konfiguracja wymusza, aby protokół SSL był zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Maszyny wirtualne z Internetem powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chroń maszyny wirtualne przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowych grup zabezpieczeń. Dowiedz się więcej na temat kontrolowania ruchu za pomocą sieciowych sieciowych sieciowych [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[W aplikacji funkcji powinna być używana najnowsza wersja TLS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji internetowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączania tylko połączeń za pośrednictwem protokołu SSL do Azure Cache for Redis. Korzystanie z bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Przejmij wymaganie dotyczące bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie żądań tylko z bezpiecznych połączeń (HTTPS) konta magazynu. Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chroń podsieć przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowej grupy zabezpieczeń. Sieciowegrupy zasad sieciowych zawierają listę reguł Access Control (ACL), które zezwalają na ruch sieciowy do podsieci lub go odrzucają. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Te zasady przeprowadza inspekcję każdej maszyny wirtualnej podłączonej do sieci wirtualnej, która nie została zatwierdzona. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwały w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="remote-devices-establishing-a-non-remote-connection-are-not-allowed-to-communicate-with-external-remote-resources"></a>Urządzenia zdalne nawiązywanie połączenia nie zdalnego nie mogą komunikować się z zasobami zewnętrznymi (zdalnymi).

**Identyfikator:** 0812.01n2Organizational.8 - 01.n **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zalecenia dotyczące adaptacyjnego wzmacniania zabezpieczeń sieci powinny być stosowane na maszynach wirtualnych skierowanych do Internetu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych skierowanych do Internetu i udostępnia zalecenia dotyczące reguł sieciowej grupy zabezpieczeń, które zmniejszają ryzyko ataku |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Wymuszanie połączenia SSL powinno być włączone dla serwerów baz danych MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle" przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Wymuszanie połączenia SSL powinno być włączone dla serwerów baz danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle" przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Maszyny wirtualne z Dostępem do Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chroń maszyny wirtualne przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowych grup zabezpieczeń. Dowiedz się więcej na temat kontrolowania ruchu za pomocą sieciowych sieciowych sieciowych sieci pod [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji internetowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Należy włączyć tylko bezpieczne Azure Cache for Redis z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączania tylko połączeń za pośrednictwem protokołu SSL do Azure Cache for Redis. Korzystanie z bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Wymaganie inspekcji bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza na koncie magazynu akceptowanie żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chroń podsieć przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowej grupy zabezpieczeń. Sieciowegrupy zasad sieci zawierają listę reguł listy Access Control (ACL), które zezwalają na ruch sieciowy do podsieci lub go odrzucają. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Te zasady przeprowadza inspekcję każdej maszyny wirtualnej podłączonej do sieci wirtualnej, która nie została zatwierdzona. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="the-ability-of-users-to-connect-to-the-internal-network-is-restricted-using-a-deny-by-default-and-allow-by-exception-policy-at-managed-interfaces-according-to-the-access-control-policy-and-the-requirements-of-clinical-and-business-applications"></a>Możliwość łączenia się użytkowników z siecią wewnętrzną jest ograniczona przy użyciu domyślnych zasad odmowy i zezwalania na wyjątek w interfejsach zarządzanych zgodnie z zasadami kontroli dostępu i wymaganiami aplikacji klinicznych i biznesowych.

**Identyfikator:** 0814.01n1Organizational.12 — 01.n **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Na maszynach wirtualnych z Dostępem do Internetu należy stosować zalecenia dotyczące adaptacyjnego wzmacniania zabezpieczeń sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych skierowanych do Internetu i udostępnia zalecenia dotyczące reguł sieciowej grupy zabezpieczeń, które zmniejszają ryzyko ataku |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Należy włączyć wymuszanie połączenia SSL dla serwerów baz danych MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle", szyfrując strumień danych między serwerem a aplikacją. Ta konfiguracja wymusza, aby protokół SSL był zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[W przypadku serwerów baz danych PostgreSQL należy włączyć wymuszanie połączenia SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle", szyfrując strumień danych między serwerem a aplikacją. Ta konfiguracja wymusza, aby protokół SSL był zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Maszyny wirtualne z Internetem powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Chroń maszyny wirtualne przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowych grup zabezpieczeń. Dowiedz się więcej na temat kontrolowania ruchu za pomocą sieciowych sieciowych sieciowych [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[W aplikacji funkcji powinna być używana najnowsza wersja TLS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji internetowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączania tylko połączeń za pośrednictwem protokołu SSL do Azure Cache for Redis. Korzystanie z bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Przejmij wymaganie dotyczące bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie żądań tylko z bezpiecznych połączeń (HTTPS) konta magazynu. Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Chroń podsieć przed potencjalnymi zagrożeniami, ograniczając dostęp do nich za pomocą sieciowej grupy zabezpieczeń. Sieciowegrupy zasad sieciowych zawierają listę reguł Access Control (ACL), które zezwalają na ruch sieciowy do podsieci lub go odrzucają. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Te zasady przeprowadza inspekcję każdej maszyny wirtualnej podłączonej do sieci wirtualnej, która nie została zatwierdzona. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwały w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="user-identification-and-authentication"></a>Identyfikacja i uwierzytelnianie użytkowników

### <a name="the-organization-ensures-that-redundant-user-ids-are-not-issued-to-other-users-and-that-all-users-are-uniquely-identified-and-authenticated-for-both-local-and-remote-access-to-information-systems"></a>Organizacja zapewnia, że nadmiarowe identyfikatory użytkowników nie są wydawane innym użytkownikom oraz że wszyscy użytkownicy są jednoznacznie identyfikowani i uwierzytelniani na potrzeby lokalnego i zdalnego dostępu do systemów informacyjnych.

**Identyfikator:** 11109.01q1Organizational.57 - 01.q **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami właściciela w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Aby zapobiec naruszeniu zabezpieczeń kont lub zasobów, należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami właściciela. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="non-organizational-users-all-information-system-users-other-than-organizational-users-such-as-patients-customers-contractors-or-foreign-nationals-or-processes-acting-on-behalf-of-non-organizational-users-determined-to-need-access-to-information-residing-on-the-organizations-information-systems-are-uniquely-identified-and-authenticated"></a>Użytkownicy spoza organizacji (wszyscy użytkownicy systemu informacyjnego inni niż użytkownicy organizacji, tacy jak pacjenci, klienci, wykonawcy lub obce osoby) lub procesy działające w imieniu użytkowników spoza organizacji, określone jako potrzebujące dostępu do informacji znajdujących się w systemach informacyjnych organizacji, są jednoznacznie identyfikowane i uwierzytelniane.

**Identyfikator:** 11110.01q1Organizational.6 — 01.q **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="when-pki-based-authentication-is-used-the-information-system-validates-certificates-by-constructing-and-verifying-a-certification-path-to-an-accepted-trust-anchor-including-checking-certificate-status-information-enforces-access-to-the-corresponding-private-key-maps-the-identity-to-the-corresponding-account-of-the-individual-or-group-and-implements-a-local-cache-of-revocation-data-to-support-path-discovery-and-validation-in-case-of-an-inability-to-access-revocation-information-via-the-network"></a>W przypadku korzystania z uwierzytelniania opartego na PKI system informacyjny weryfikuje certyfikaty, konstruując i weryfikując ścieżkę certyfikacji do zaakceptowanej kotwicy zaufania, w tym sprawdzając informacje o stanie certyfikatu; wymusza dostęp do odpowiedniego klucza prywatnego; mapuje tożsamość na odpowiednie konto osoby lub grupy; System i implementuje lokalną pamięć podręczną danych odwołania w celu obsługi odnajdywania i weryfikacji ścieżek w przypadku, gdy nie można uzyskać dostępu do informacji o odwołaniu za pośrednictwem sieci.

**Identyfikator:** 11111.01q2System.4 — własność 01.q: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami do odczytu w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do odczytu, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-employs-replay-resistant-authentication-mechanisms-such-as-nonce-one-time-passwords-or-time-stamps-to-secure-network-access-for-privileged-accounts-and-for-hardware-token-based-authentication-employs-mechanisms-that-satisfy-minimum-token-requirements-discussed-in-nist-sp-800-63-2-electronic-authentication-guideline"></a>System informacji wykorzystuje mechanizmy uwierzytelniania odpornego na powtarzanie, takie jak nonce, hasła czasowe lub sygnatury czasowe, aby zabezpieczyć dostęp do sieci dla kont uprzywilejowanych; W przypadku uwierzytelniania opartego na tokenach sprzętowych program wykorzystuje mechanizmy spełniające minimalne wymagania dotyczące tokenów omówione w wytycznych dotyczących uwierzytelniania elektronicznego w NIST SP 800-63-2.

**Identyfikator:** 11112.01q2Organizational.67 — 01.q **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[W ramach subskrypcji należy wyznaczyć maksymalnie 3 właścicieli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Zaleca się wyznaczenie maksymalnie 3 właścicieli subskrypcji w celu ograniczenia ryzyka naruszenia zabezpieczeń przez naruszonego właściciela. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-requires-that-electronic-signatures-unique-to-one-individual-cannot-be-reused-by-or-reassigned-to-anyone-else"></a>Organizacja wymaga, aby podpisy elektroniczne, unikatowe dla jednej osoby, nie mogą być ponownie używane ani ponownie nadpisywano innym osobom.

**Identyfikator:** 11208.01q1Organizational.8 — 01.q **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Do Subskrypcji powinien być przypisany więcej niż jeden właściciel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Zaleca się wyznaczenie więcej niż jednego właściciela subskrypcji w celu zapewnienia nadmiarowości dostępu administratora. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="electronic-signatures-and-handwritten-signatures-executed-to-electronic-records-shall-be-linked-to-their-respective-electronic-records"></a>Podpisy elektroniczne i podpisy odręczne wykonywane w rekordach elektronicznych muszą być połączone z odpowiednimi rekordami elektronicznymi.

**Identyfikator:** 11210.01q2Organizational.10 - 01.q **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, które mają określonych członków w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli lokalna grupa Administratorzy zawiera co najmniej jeden z elementów członkowskich wymienionych w parametrze zasad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="signed-electronic-records-shall-contain-information-associated-with-the-signing-in-human-readable-format"></a>Podpisane rekordy elektroniczne muszą zawierać informacje skojarzone z podpisem w formacie czytelnym dla człowieka.

**Identyfikator:** 11211.01q2Organizational.11 — 01.q **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, na których brakuje dowolnego z określonych członków w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli lokalna grupa Administratorów nie zawiera co najmniej jednego członka wymienionego w parametrze zasad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

### <a name="users-who-performed-privileged-functions-eg-system-administration-use-separate-accounts-when-performing-those-privileged-functions"></a>Użytkownicy, którzy wykonywali funkcje uprzywilejowane (np. administrowanie systemem), używają oddzielnych kont podczas wykonywania tych funkcji uprzywilejowanych.

**Identyfikator:** 1123.01q1System.2 — własność 01.q: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, które mają dodatkowe konta w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli lokalna grupa Administratorzy zawiera elementy członkowskie, które nie są wymienione w parametrze zasad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |

### <a name="multi-factor-authentication-methods-are-used-in-accordance-with-organizational-policy-eg-for-remote-network-access"></a>Metody uwierzytelniania wieloskładnikowego są używane zgodnie z zasadami organizacji (np. w przypadku dostępu do sieci zdalnej).

**Identyfikator:** 1125.01q2System.1 — własność 01.q: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, które mają określonych członków w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli lokalna grupa Administratorzy zawiera co najmniej jeden z elementów członkowskich wymienionych w parametrze zasad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="where-tokens-are-provided-for-multi-factor-authentication-in-person-verification-is-required-prior-to-granting-access"></a>W przypadku gdy tokeny są udostępniane na potrzeby uwierzytelniania wieloskładnikowego, przed udzieleniem dostępu jest wymagana weryfikacja osobiście.

**Identyfikator:** 1127.01q2System.3 — własność 01.q: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, na których brakuje dowolnego z określonych członków w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli lokalna grupa Administratorów nie zawiera co najmniej jednego członka wymienionego w parametrze zasad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

## <a name="identification-of-risks-related-to-external-parties"></a>Identyfikacja czynników ryzyka związanych ze stronami zewnętrznymi

### <a name="access-to-the-organizations-information-and-systems-by-external-parties-is-not-permitted-until-due-diligence-has-been-conducted-the-appropriate-controls-have-been-implemented-and-a-contractagreement-reflecting-the-security-requirements-is-signed-acknowledging-they-understand-and-accept-their-obligations"></a>Dostęp do informacji i systemów organizacji przez podmioty zewnętrzne nie jest dozwolony, dopóki nie zostanie przeprowadzona należyta staranność, zostaną wdrożone odpowiednie mechanizmy kontroli, a umowa/umowa odzwierciedlająca wymagania dotyczące zabezpieczeń zostanie podpisana z potwierdzeniem, że rozumieją i akceptują swoje zobowiązania.

**Identyfikator:** 1401.05i1Organizational.1239 - 05.i **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Przejmij wymaganie dotyczące bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie żądań tylko z bezpiecznych połączeń (HTTPS) konta magazynu. Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="remote-access-connections-between-the-organization-and-external-parties-are-encrypted"></a>Połączenia dostępu zdalnego między organizacją i podmiotami zewnętrznymi są szyfrowane.

**Identyfikator:** 1402.05i1Organizational.45 - 05.i **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |

### <a name="access-granted-to-external-parties-is-limited-to-the-minimum-necessary-and-granted-only-for-the-duration-required"></a>Dostęp udzielany stronom zewnętrznym jest ograniczony do niezbędnego minimum i udzielany tylko przez wymagany czas.

**Identyfikator:** 1403.05i1Organizational.67 - 05.i **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="due-diligence-of-the-external-party-includes-interviews-document-review-checklists-certification-reviews-eg-hitrustnbspor-other-remote-means"></a>Należyta staranność strony zewnętrznej obejmuje rozmowy, przegląd dokumentów, listy kontrolne, przeglądy certyfikacji (np. HITRUST) lub inne &nbsp; zdalne sposoby.

**Identyfikator:** 1404.05i2Organizational.1 - 05.i **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |

### <a name="the-identification-of-risks-related-to-external-party-access-takes-into-account-a-minimal-set-of-specifically-defined-issues"></a>Identyfikacja ryzyka związanego z dostępem osób zewnętrznych uwzględnia minimalny zestaw ściśle zdefiniowanych problemów.

**Identyfikator:** 1418.05i1Organizational.8 - 05.i **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Wymuszanie połączenia SSL powinno być włączone dla serwerów baz danych MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle" przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-organization-obtains-satisfactory-assurances-that-reasonable-information-security-exists-across-their-information-supply-chain-by-performing-an-annual-review-which-includes-all-partnersthird-party-providers-upon-which-their-information-supply-chain-depends"></a>Organizacja uzyskuje zadowalający poziom gwarancji, że w jej łańcuchu dostaw informacji istnieje uzasadnione bezpieczeństwo informacji, wykonując coroczny przegląd, który obejmuje wszystkich partnerów/dostawców innych firm, od których zależy ich łańcuch dostaw informacji.

**Identyfikator:** 1450.05i2Organizational.2 — 05.i **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Wymuszanie połączenia SSL powinno być włączone dla serwerów baz danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle" przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="cloud-service-providers-design-and-implement-controls-to-mitigate-and-contain-data-security-risks-through-proper-separation-of-duties-role-based-access-and-least-privilege-access-for-all-personnel-within-their-supply-chain"></a>Dostawcy usług w chmurze projektują i wdrażają mechanizmy kontroli w celu zminimalizowania i ograniczenia ryzyka związanego z bezpieczeństwem danych dzięki właściwemu rozdzieleniu obowiązków, dostępu opartego na rolach i dostępu z najmniejszymi uprawnieniami dla wszystkich pracowników w łańcuchu dostaw.

**Identyfikator:** 1451.05iCSPOrganizational.2 — 05.i **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy włączyć tylko bezpieczne Azure Cache for Redis z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączania tylko połączeń za pośrednictwem protokołu SSL do Azure Cache for Redis. Korzystanie z bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="audit-logging"></a>Rejestrowanie inspekcji

### <a name="a-secure-audit-record-is-created-for-all-activities-on-the-system-create-read-update-delete-involving-covered-information"></a>Rekord bezpiecznej inspekcji jest tworzony dla wszystkich działań w systemie (tworzenie, odczyt, aktualizacja, usuwanie) obejmujących objęte informacje.

**Identyfikator:** 1202.09aa1System.1 - 09.aa **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dzienniki zasobów w usłudze Azure Data Lake Store powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć trasy działań do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Należy zainstalować aktualizacje systemu w zestawach skalowania maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Sprawdź, czy nie ma żadnych aktualizacji zabezpieczeń systemu i aktualizacji krytycznych, które należy zainstalować, aby upewnić się, że zestawy skalowania maszyn wirtualnych z systemami Windows i Linux są bezpieczne. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |

### <a name="audit-records-include-the-unique-user-id-unique-data-subject-id-function-performed-and-datetime-the-event-was-performed"></a>Rekordy inspekcji obejmują unikatowy identyfikator użytkownika, unikatowy identyfikator podmiotu danych, wykonaną funkcję i datę/czas wykonania zdarzenia.

**Identyfikator:** 1203.09aa1System.2 — 09.aa **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dzienniki zasobów w Logic Apps powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Inspekcja włączania dzienników zasobów. Umożliwia to odtworzenie śladów aktywności do użycia na potrzeby badania; w przypadku wystąpienia zdarzenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

### <a name="the-activities-of-privileged-users-administrators-operators-etc-include-the-successfailure-of-the-event-time-the-event-occurred-the-account-involved-the-processes-involved-and-additional-information-about-the-event"></a>Działania uprzywilejowanych użytkowników (administratorów, operatorów itp.) obejmują powodzenie/niepowodzenie zdarzenia, czas wystąpienia zdarzenia, konto, w związku z tym procesy i dodatkowe informacje o zdarzeniu.

**Identyfikator:** 1204.09aa1System.3 — 09.aa **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dzienniki zasobów w IoT Hub powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Inspekcja włączania dzienników zasobów. Umożliwia to odtworzenie śladów aktywności do użycia na potrzeby badania; w przypadku wystąpienia zdarzenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

### <a name="logs-of-messages-sent-and-received-are-maintained-including-the-date-time-origin-and-destination-of-the-message-but-not-its-contents"></a>Dzienniki komunikatów wysyłanych i odbieranych są zachowywane, w tym daty, czasu, źródła i miejsca docelowego komunikatu, ale nie jego zawartości.

**Identyfikator:** 1205.09aa2System.1 - 09.aa **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dzienniki zasobów na kontach usługi Batch powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć trasy działań do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |

### <a name="auditing-is-always-available-while-the-system-is-active-and-tracks-key-events-successfailed-data-access-system-security-configuration-changes-privileged-or-utility-use-any-alarms-raised-nbspactivation-and-de-activation-of-protection-systems-eg-av-and-ids-activation-and-deactivation-of-identification-and-authentication-mechanisms-and-creation-and-deletion-of-system-level-objects"></a>Inspekcja jest zawsze dostępna, gdy system jest aktywny i śledzi kluczowe zdarzenia, powodzenie/niepowodzenie dostępu do danych, zmiany konfiguracji zabezpieczeń systemu, uprzywilejowane lub narzędzia, wszelkie alarmy, aktywację i dezaktywowanie systemów ochrony (np. A/V i IDS), aktywację i dezaktywację mechanizmów identyfikacji i uwierzytelniania oraz tworzenie i usuwanie obiektów na poziomie &nbsp; systemu.

**Identyfikator:** 1206.09aa2System.23 - 09.aa **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dzienniki zasobów w Virtual Machine Scale Sets powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Zaleca się włączenie dzienników, aby można było odtworzyć dziennik aktywności, gdy badania są wymagane w przypadku zdarzenia lub naruszenia bezpieczeństwa. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

### <a name="audit-records-are-retained-for-90-days-and-older-audit-records-are-archived-for-one-year"></a>Rekordy inspekcji są przechowywane przez 90 dni, a starsze rekordy inspekcji są archiwizowane przez jeden rok.

**Identyfikator:** 1207.09aa2System.4 — 09.aa **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dzienniki zasobów w Azure Stream Analytics powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Inspekcja włączania dzienników zasobów. Umożliwia to odtworzenie śladów aktywności do użycia na potrzeby badania; w przypadku wystąpienia zdarzenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Dzienniki zasobów w centrum zdarzeń powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Inspekcja włączania dzienników zasobów. Umożliwia to odtworzenie śladów aktywności do użycia na potrzeby badania; w przypadku wystąpienia zdarzenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

### <a name="audit-logs-are-maintained-for-management-activities-system-and-application-startupshutdownerrors-file-changes-and-security-policy-changes"></a>Dzienniki inspekcji są utrzymywane dla działań związanych z zarządzaniem, uruchamiania/zamykania/błędów systemu i aplikacji, zmian plików i zmian zasad zabezpieczeń.

**Identyfikator:** 1208.09aa3System.1 — 09.aa **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dzienniki zasobów w usługach wyszukiwania powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Inspekcja włączania dzienników zasobów. Umożliwia to odtworzenie śladów aktywności do użycia na potrzeby badania; w przypadku wystąpienia zdarzenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Dzienniki zasobów w Service Bus powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć trasy działań do użycia na potrzeby badania. w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

### <a name="the-information-system-generates-audit-records-containing-the-following-detailed-information-i-filename-accessed-ii-program-or-command-used-to-initiate-the-event-and-iii-source-and-destination-addresses"></a>System informacji generuje rekordy inspekcji zawierające następujące szczegółowe informacje: (i) nazwa pliku, do którego uzyskano dostęp; (ii) program lub polecenie użyte do zainicjowania zdarzenia; i (iii) adresy źródłowe i docelowe.

**Identyfikator:** 1209.09aa3System.2 — 09.aa **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dzienniki diagnostyczne w App Services powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Inspekcja włączania dzienników diagnostycznych w aplikacji. Dzięki temu można odtworzyć ślady aktywności na potrzeby badania w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia bezpieczeństwa sieci |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |

### <a name="all-disclosures-of-covered-information-within-or-outside-of-the-organization-are-logged-including-type-of-disclosure-datetime-of-the-event-recipient-and-sender"></a>Wszystkie ujawnione informacje objęte usługą w organizacji lub poza nią są rejestrowane, w tym informacje o typie ujawnienia, dacie/godzinie zdarzenia, odbiorcy i nadawcy.

**Identyfikator:** 1210.09aa3System.3 - 09.aa **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Audit diagnostic setting](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) (Przeprowadzaj inspekcję ustawienia diagnostyki) |Prze audit diagnostic setting for selected resource types (Przesłoń ustawienia diagnostyczne dla wybranych typów zasobów) |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Dzienniki zasobów w Data Lake Analytics powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Inspekcja włączania dzienników zasobów. Umożliwia to odtworzenie śladów aktywności do użycia na potrzeby badania; w przypadku wystąpienia zdarzenia zabezpieczeń lub naruszenia zabezpieczeń sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |

### <a name="the-organization-verifies-every-ninety-90-days-for-each-extract-of-covered-information-recorded-that-the-data-is-erased-or-its-use-is-still-required"></a>Organizacja sprawdza co dziewięćdziesiąt (90) dni dla każdego wyodrębnienia zarejestrowanych informacji objętych usługą, czy dane są usuwane lub czy ich użycie jest nadal wymagane.

**Identyfikator:** 1211.09aa3System.4 — 09.aa **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja na serwerze SQL powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Inspekcja w twojej SQL Server powinna być włączona, aby śledzić działania bazy danych we wszystkich bazach danych na serwerze i zapisywać je w dzienniku inspekcji. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Dzienniki zasobów w zarządzanym Azure Key Vault HSM powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |Aby odtworzyć dzienniki aktywności na potrzeby badania w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia zabezpieczeń sieci, można chcieć ją zweryfikować, włączając dzienniki zasobów w zarządzanych modułach HSM. Postępuj zgodnie z instrukcjami tutaj: [https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](https://docs.microsoft.com/azure/key-vault/managed-hsm/logging) . |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[Dzienniki zasobów w Key Vault powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Inspekcja włączania dzienników zasobów. Dzięki temu można odtworzyć trasy działań do użycia na potrzeby badania w przypadku wystąpienia zdarzenia naruszenia zabezpieczeń lub naruszenia bezpieczeństwa sieci |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

## <a name="monitoring-system-use"></a>Monitorowanie użycia systemu

### <a name="unauthorized-remote-connections-to-the-information-systems-are-monitored-and-reviewed-at-least-quarterly-and-appropriate-action-is-taken-if-an-unauthorized-connection-is-discovered"></a>Nieautoryzowane połączenia zdalne z systemami informacyjnymi są monitorowane i sprawdzane co najmniej co kwartał, a w przypadku odnalezionia nieautoryzowanego połączenia są podejmowane odpowiednie działania.

**Identyfikator:** 1120.09ab3System.9 - 09.ab **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor zbierać dzienniki aktywności ze wszystkich regionów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Te zasady przeprowadza inspekcję profilu Azure Monitor, który nie eksportuje działań ze wszystkich obsługiwanych regionów platformy Azure, w tym z regionów globalnych. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="the-organization-monitors-the-information-system-to-identify-irregularities-or-anomalies-that-are-indicators-of-a-system-malfunction-or-compromise-and-help-confirm-the-system-is-functioning-in-an-optimal-resilient-and-secure-state"></a>Organizacja monitoruje system informacji, aby identyfikować nieprawidłowości lub anomalie, które są wskaźnikami awarii lub naruszenia bezpieczeństwa systemu, i pomaga potwierdzić, że system działa w optymalnym, odpornym i bezpiecznym stanie.

**Identyfikator:** 12100.09ab2System.15 - 09.ab **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Agent usługi Log Analytics powinien być zainstalowany na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Te zasady przeprowadza inspekcję wszystkich maszyn wirtualnych z systemem Windows/Linux, jeśli agent usługi Log Analytics nie został zainstalowany. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-specifies-how-often-audit-logs-are-reviewed-how-the-reviews-are-documented-and-the-specific-roles-and-responsibilities-of-the-personnel-conducting-the-reviews-including-the-professional-certifications-or-other-qualifications-required"></a>Organizacja określa, jak często są przeglądane dzienniki inspekcji, jak są dokumentowane przeglądy oraz konkretne role i obowiązki personelu, który przeprowadza przeglądy, w tym certyfikaty zawodowe lub inne wymagane kwalifikacje.

**Identyfikator:** 12101.09ab1Organizational.3 — 09.ab **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Agent usługi Log Analytics powinien być zainstalowany na Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Te zasady będą przeprowadzać inspekcję wszystkich Virtual Machine Scale Sets systemu Windows/Linux, jeśli agent usługi Log Analytics nie jest zainstalowany. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-shall-periodically-test-its-monitoring-and-detection-processes-remediate-deficiencies-and-improve-its-processes"></a>Organizacja okresowo testuje swoje procesy monitorowania i wykrywania, koryguje braki i ulepsza swoje procesy.

**Identyfikator:** 12102.09ab1Organizational.4 — 09.ab **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, na których agent usługi Log Analytics nie jest połączony zgodnie z oczekiwaniami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli agent nie jest zainstalowany lub jeśli jest zainstalowany, ale obiekt COM AgentConfigManager.MgmtSvcCfg zwraca, że jest zarejestrowany w obszarze roboczym innym niż identyfikator określony w parametrze zasad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="all-applicable-legal-requirements-related-to-monitoring-authorized-access-and-unauthorized-access-attempts-are-met"></a>Spełnione są wszystkie odpowiednie wymagania prawne związane z monitorowaniem autoryzowanego dostępu i nieautoryzowanych prób dostępu.

**Identyfikator:** 1212.09ab1System.1 - 09.ab **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor dziennika powinny zbierać dzienniki dla kategorii "zapis", "usuwanie" i "akcja"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Te zasady zapewniają, że profil dziennika zbiera dzienniki dla kategorii "zapis", "usuwanie" i "akcja" |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="automated-systems-deployed-throughout-the-organizations-environment-are-used-to-monitor-key-events-and-anomalous-activity-and-analyze-system-logs-the-results-of-which-are-reviewed-regularly"></a>Zautomatyzowane systemy wdrożone w całym środowisku organizacji są używane do monitorowania kluczowych zdarzeń i anomalii działań oraz analizowania dzienników systemu, których wyniki są regularnie przeglądane.

**Identyfikator:** 1213.09ab2System.128 - 09.ab **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Automatyczną aprowizowanie agenta usługi Log Analytics należy włączyć w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Aby monitorować luki w zabezpieczeniach i zagrożenia, Azure Security Center zbiera dane z maszyn wirtualnych platformy Azure. Dane są zbierane przez agenta usługi Log Analytics, wcześniej znanego jako Microsoft Monitoring Agent (MMA), który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyny i kopiuje dane do obszaru roboczego usługi Log Analytics w celu analizy. Zalecamy włączenie automatycznego aprowizowania w celu automatycznego wdrożenia agenta na wszystkich obsługiwanych maszyn wirtualnych platformy Azure i wszystkich nowo utworzonych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="monitoring-includes-privileged-operations-authorized-access-or-unauthorized-access-attempts-including-attempts-to-access-deactivated-accounts-and-system-alerts-or-failures"></a>Monitorowanie obejmuje operacje uprzywilejowane, próby autoryzowanego dostępu lub nieautoryzowanego dostępu, w tym próby uzyskania dostępu do zdezaktywowanych kont oraz alerty systemowe lub błędy.

**Identyfikator:** 1214.09ab2System.3456 - 09.ab **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor zbierać dzienniki aktywności ze wszystkich regionów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Te zasady przeprowadza inspekcję profilu Azure Monitor, który nie eksportuje działań ze wszystkich obsługiwanych regionów platformy Azure, w tym z regionów globalnych. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="auditing-and-monitoring-systems-employed-by-the-organization-support-audit-reduction-and-report-generation"></a>Systemy inspekcji i monitorowania stosowane w organizacji obsługują redukcję inspekcji i generowanie raportów.

**Identyfikator:** 1215.09ab2System.7 — 09.ab **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Agent usługi Log Analytics powinien być zainstalowany na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Te zasady przeprowadza inspekcję wszystkich maszyn wirtualnych z systemem Windows/Linux, jeśli agent usługi Log Analytics nie jest zainstalowany. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="automated-systems-are-used-to-review-monitoring-activities-of-security-systems-eg-ipsids-and-system-records-on-a-daily-basis-and-identify-and-document-anomalies"></a>Systemy automatyczne są używane do codziennego przeglądania działań monitorowania systemów zabezpieczeń (np. adresów IP/IDS) i rekordów systemowych oraz identyfikowania i dokumentowania anomalii.

**Identyfikator:** 1216.09ab3System.12 — 09.ab **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Agent usługi Log Analytics powinien być zainstalowany na Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Te zasady będą przeprowadzać inspekcję wszystkich Virtual Machine Scale Sets systemu Windows/Linux, jeśli agent usługi Log Analytics nie jest zainstalowany. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="alerts-are-generated-for-technical-personnel-to-analyze-and-investigate-suspicious-activity-or-suspected-violations"></a>Dla personelu technicznego są generowane alerty w celu analizowania i badania podejrzanych działań lub podejrzanych naruszeń.

**Identyfikator:** 1217.09ab3System.3 — 09.ab **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, na których agent usługi Log Analytics nie jest połączony zgodnie z oczekiwaniami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli agent nie jest zainstalowany lub jeśli jest zainstalowany, ale obiekt COM AgentConfigManager.MgmtSvcCfg zwraca, że jest zarejestrowany w obszarze roboczym innym niż identyfikator określony w parametrze zasad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="the-information-system-is-able-to-automatically-process-audit-records-for-events-of-interest-based-on-selectable-criteria"></a>System informacji może automatycznie przetwarzać rekordy inspekcji zdarzeń, które cię interesują, na podstawie wybranych kryteriów.

**Identyfikator:** 1219.09ab3System.10 — 09.ab **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor dziennika powinny zbierać dzienniki dla kategorii "zapis", "usuwanie" i "akcja"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Te zasady zapewniają, że profil dziennika zbiera dzienniki dla kategorii "zapis", "usuwanie" i "akcja" |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="monitoring-includes-inbound-and-outbound-communications-and-file-integrity-monitoring"></a>Monitorowanie obejmuje komunikację przychodzących i wychodzącą oraz monitorowanie integralności plików.

**Identyfikator:** 1220.09ab3System.56 — 09.ab **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Automatyczne aprowizowanie agenta usługi Log Analytics powinno być włączone w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Aby monitorować luki w zabezpieczeniach i zagrożenia, Azure Security Center zbiera dane z maszyn wirtualnych platformy Azure. Dane są zbierane przez agenta usługi Log Analytics, wcześniej znanego jako Microsoft Monitoring Agent (MMA), który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyny i kopiuje dane do obszaru roboczego usługi Log Analytics w celu analizy. Zalecamy włączenie automatycznego aprowizowania, aby automatycznie wdrożyć agenta na wszystkich obsługiwanych maszyn wirtualnych platformy Azure i wszystkich nowych, które zostały utworzone. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

## <a name="administrator-and-operator-logs"></a>Dzienniki administratora i operatora

### <a name="the-organization-ensures-proper-logging-is-enabled-in-order-to-audit-administrator-activities-and-reviews-system-administrator-and-operator-logs-on-a-regular-basis"></a>Organizacja zapewnia, że odpowiednie rejestrowanie jest włączone w celu inspekcji działań administratora; i regularnie przegląda dzienniki administratorów i operatorów systemu.

**Identyfikator:** 1270.09ad1System.12 — 09.ad **własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Te zasady przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="an-intrusion-detection-system-managed-outside-of-the-control-of-system-and-network-administrators-is-used-to-monitor-system-and-network-administration-activities-for-compliance"></a>System wykrywania włamań zarządzany poza kontrolą administratorów systemu i sieci jest używany do monitorowania działań administracyjnych dotyczących systemu i sieci w celu zapewnienia zgodności.

**Identyfikator:** 1271.09ad1System.1 — 09.ad **własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Te zasady przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

## <a name="segregation-of-duties"></a>Podział obowiązków

### <a name="separation-of-duties-is-used-to-limit-the-risk-of-unauthorized-or-unintentional-modification-of-information-and-systems"></a>Podział obowiązków ogranicza ryzyko nieautoryzowanych lub niezamierzonych modyfikacji informacji i systemów.

**Identyfikator:** 1229.09c1Organizational.1 - 09.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[W usługach Kubernetes Access Control na podstawie ról (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Aby zapewnić szczegółowe filtrowanie akcji, które użytkownicy mogą wykonywać, użyj funkcji Role-Based Access Control (RBAC) do zarządzania uprawnieniami w klastrach usługi Kubernetes Service i skonfigurowania odpowiednich zasad autoryzacji. |Inspekcja, wyłączone |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="no-single-person-is-able-to-access-modify-or-use-information-systems-without-authorization-or-detection"></a>Żadna osoba nie może uzyskać dostępu do systemów informacyjnych, modyfikować ich ani używać ich bez autoryzacji ani wykrywania.

**Identyfikator:** 1230.09c2Organizational.1 - 09.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja użycia niestandardowych reguł RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Inspekcja ról wbudowanych, takich jak "Właściciel, Współtator, Czytelnik" zamiast niestandardowych ról RBAC, które są podatne na błędy. Używanie ról niestandardowych jest traktowane jako wyjątek i wymaga rygorystycznego przeglądu i modelowania zagrożeń |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

### <a name="access-for-individuals-responsible-for-administering-nbspaccess-controls-is-limited-to-the-minimum-necessary-based-upon-each-users-role-and-responsibilities-and-these-individuals-cannot-access-audit-functions-related-to-these-controls"></a>Dostęp dla osób odpowiedzialnych za administrowanie kontrolą dostępu jest ograniczony do niezbędnego minimum w zależności od roli i obowiązków poszczególnych użytkowników, a te osoby nie mogą uzyskać dostępu do funkcji inspekcji związanych z tymi &nbsp; kontrolkami.

**Identyfikator:** 1232.09c3Organizational.12 — 09.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące przypisania praw użytkownika](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe068b215-0026-4354-b347-8fb2766f73a2) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Przypisywanie praw użytkownika", aby zezwalać na logowanie lokalne, RDP, dostęp z sieci i wiele innych działań użytkownika. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_UserRightsAssignment_AINE.json) |

### <a name="security-audit-activities-are-independent"></a>Działania inspekcji zabezpieczeń są niezależne.

**Identyfikator:** 1276.09c2Organizational.2 — 09.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Niestandardowe role właściciela subskrypcji nie powinny istnieć](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Te zasady zapewniają, że role właściciela subskrypcji niestandardowej nie istnieją. |Inspekcja, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

### <a name="the-initiation-of-an-event-is-separated-from-its-authorization-to-reduce-the-possibility-of-collusion"></a>Zainicjowanie zdarzenia jest oddzielone od jego autoryzacji, aby zmniejszyć prawdopodobieństwo zmowy.

**Identyfikator:** 1277.09c2Organizational.4 - 09.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń — kontrola konta użytkownika](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F492a29ed-d143-4f03-b6a4-705ce081b463) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Opcje zabezpieczeń — kontrola konta użytkownika" dla trybu dla administratorów, zachowania monitu o podniesienie uprawnień oraz wirtualizacji błędów zapisu plików i rejestru. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsUserAccountControl_AINE.json) |

### <a name="the-organization-identifies-duties-that-require-separation-and-defines-information-system-access-authorizations-to-support-separation-of-duties-and-incompatible-duties-are-segregated-across-multiple-users-to-minimize-the-opportunity-for-misuse-or-fraud"></a>Organizacja identyfikuje obowiązki, które wymagają rozdzielenia, i definiuje autoryzacje dostępu systemu informacji w celu obsługi rozdzielania obowiązków. i niezgodne obowiązki są rozdzielone między wielu użytkowników, aby zminimalizować ryzyko nieprawidłowego użycia lub oszustwa.

**Identyfikator:** 1278.09c2Organizational.56 - 09.c **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Niestandardowe role właściciela subskrypcji nie powinny istnieć](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Te zasady zapewniają, że role właściciela subskrypcji niestandardowej nie istnieją. |Inspekcja, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="controls-against-malicious-code"></a>Mechanizmy kontroli przed złośliwym kodem

### <a name="anti-virus-and-anti-spyware-are-installed-operating-and-updated-on-all-end-user-devices-to-conduct-periodic-scans-of-the-systems-to-identify-and-remove-unauthorized-software-server-environments-for-which-the-server-software-developer-specifically-recommends-not-installing-host-based-anti-virus-and-anti-spyware-software-may-address-the-requirement-via-a-network-based-malware-detection-nbmd-solution"></a>Oprogramowanie antywirusowe i programy szpiegujące są instalowane, działające i aktualizowane na wszystkich urządzeniach użytkowników końcowych w celu przeprowadzania okresowych skanowań systemów w celu zidentyfikowania i usunięcia nieautoryzowanego oprogramowania. Środowiska serwera, dla których deweloper oprogramowania serwerowego zaleca, aby nie instalować oprogramowania antywirusowego opartego na hoście i chroniącego przed programami szpiegującymi, mogą rozwiązać ten wymóg za pośrednictwem rozwiązania do wykrywania złośliwego oprogramowania opartego na sieci (NBMD).

**Identyfikator:** 0201.09j1Organizational.124 - 09.j **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Na maszynach należy włączyć funkcje adaptacyjnego sterowania aplikacjami do definiowania bezpiecznych aplikacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Włącz kontrolki aplikacji, aby zdefiniować listę znanych bezpiecznych aplikacji uruchomionych na maszynach i powiadamiać o uruchomieniu innych aplikacji. Ułatwia to ochronę maszyn przed złośliwym oprogramowaniem. Aby uprościć proces konfigurowania i obsługi reguł, Security Center uczenia maszynowego analizuje aplikacje uruchomione na poszczególnych maszynach i sugeruje listę znanych bezpiecznych aplikacji. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Wdrażanie domyślnego rozszerzenia IaaSAntimalware firmy Microsoft dla systemu Windows Server](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |Te zasady wdrażają rozszerzenie IaaSAntimalware firmy Microsoft z konfiguracją domyślną, gdy maszyna wirtualna nie jest skonfigurowana przy użyciu rozszerzenia ochrony przed złośliwym oprogramowaniem. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |
|[Rozwiązanie ochrony punktu końcowego powinno być zainstalowane w zestawach skalowania maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Przejmuj istnienie i kondycję rozwiązania ochrony punktu końcowego w zestawach skalowania maszyn wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft Antimalware dla platformy Azure należy skonfigurować do automatycznego aktualizowania sygnatur ochrony](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |Te zasady przeprowadza inspekcję wszystkich maszyn wirtualnych z systemem Windows, które nie zostały skonfigurowane przy użyciu automatycznej aktualizacji Microsoft Antimalware podpisów ochrony. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[Monitorowanie braku programu Endpoint Protection w programie Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Serwery bez zainstalowanego agenta programu Endpoint Protection będą monitorowane przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[Należy zainstalować aktualizacje systemu na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="back-up"></a>Back-up (Zapasowa)

### <a name="backup-copies-of-information-and-software-are-made-and-tests-of-the-media-and-restoration-procedures-are-regularly-performed-at-appropriate-intervals"></a>Kopie zapasowe informacji i oprogramowania są wykonywane, a testy nośników i procedur przywracania są regularnie wykonywane w odpowiednich odstępach czasu.

**Identyfikator:** 1616.09l1Organizational.16 — 09.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Długoterminowe geograficznie nadmiarowe kopie zapasowe powinny być włączone dla baz Azure SQL danych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Te zasady przeprowadzają inspekcję Azure SQL Database, dla których długoterminowa geograficznie nadmiarowa kopia zapasowa nie jest włączona. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="a-formal-definition-of-the-level-of-backup-required-for-each-system-is-defined-and-documented-including-how-each-system-will-be-restored-the-scope-of-data-to-be-imaged-frequency-of-imaging-and-duration-of-retention-based-on-relevant-contractual-legal-regulatory-and-business-requirements"></a>Zdefiniowano i udokumentowano formalną definicję poziomu kopii zapasowej wymaganej dla każdego systemu, w tym sposób przywracania poszczególnych systemów, zakres danych, które mają być obrazowane, częstotliwość tworzenia obrazów oraz czas przechowywania na podstawie odpowiednich wymagań kontraktowych, prawnych, prawnych i biznesowych.

**Identyfikator:** 1617.09l1Organizational.23 - 09.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Można go ustawić na magazyn geograficznie nadmiarowy kopii zapasowych, w którym dane są nie tylko przechowywane w regionie, w którym jest hostowany serwer, ale także są replikowane do sparowanego regionu w celu zapewnienia opcji odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego do tworzenia kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="the-backups-are-stored-in-a-physically-secure-remote-location-at-a-sufficient-distance-to-make-them-reasonably-immune-from-damage-to-data-at-the-primary-site-and-reasonable-physical-and-environmental-controls-are-in-place-to-ensure-their-protection-at-the-remote-location"></a>Kopie zapasowe są przechowywane w fizycznie zabezpieczonej lokalizacji zdalnej, w odpowiedniej odległości, aby można je było odpowiednio zabezpieczyć przed uszkodzeniem danych w lokacji głównej. W celu zapewnienia ochrony w lokalizacji zdalnej są dostępne odpowiednie środki kontroli fizycznej i środowiskowej.

**Identyfikator:** 1618.09l1Organizational.45 — 09.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Można go ustawić na magazyn geograficznie nadmiarowy kopii zapasowych, w którym dane są nie tylko przechowywane w regionie, w którym jest hostowany serwer, ale także są replikowane do sparowanego regionu w celu zapewnienia opcji odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego do tworzenia kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="inventory-records-for-the-backup-copies-including-content-and-current-location-are-maintained"></a>Rekordy spisu kopii zapasowych, w tym zawartość i bieżąca lokalizacja, są zachowywane.

**Identyfikator:** 1619.09l1Organizational.7 — 09.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Można go ustawić na geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane są nie tylko przechowywane w regionie, w którym jest hostowany serwer, ale również są replikowane do sparowanego regionu w celu zapewnienia opcji odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego do tworzenia kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="when-the-backup-service-is-delivered-by-the-third-party-the-service-level-agreement-includes-the-detailed-protections-to-control-confidentiality-integrity-and-availability-of-the-backup-information"></a>Gdy usługa tworzenia kopii zapasowej jest dostarczana przez firmę trzecią, umowa o poziomie usług obejmuje szczegółowe zabezpieczenia w celu kontrolowania poufności, integralności i dostępności informacji o kopii zapasowej.

**Identyfikator:** 1620.09l1Organizational.8 — 09.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Backup należy włączyć dla Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Zapewnij ochronę usługi Azure Virtual Machines, włączając Azure Backup. Azure Backup to bezpieczne i ekonomiczne rozwiązanie do ochrony danych dla platformy Azure. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="automated-tools-are-used-to-track-all-backups"></a>Zautomatyzowane narzędzia są używane do śledzenia wszystkich kopii zapasowych.

**Identyfikator:** 1621.09l2Organizational.1 - 09.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Długoterminowe geograficznie nadmiarowe kopie zapasowe powinny być włączone dla baz Azure SQL danych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Te zasady przeprowadza inspekcję Azure SQL Database z niewłączona długoterminową geograficznie nadmiarową kopią zapasową. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="the-integrity-and-security-of-the-backup-copies-are-maintained-to-ensure-future-availability-and-any-potential-accessibility-problems-with-the-backup-copies-are-identified-and-mitigated-in-the-event-of-an-area-wide-disaster"></a>Integralność i zabezpieczenia kopii zapasowych są zachowywane w celu zapewnienia przyszłej dostępności, a wszelkie potencjalne problemy z dostępnością kopii zapasowych są identyfikowane i ograniczane w przypadku awarii całego obszaru.

**Identyfikator:** 1622.09l2Organizational.23 — 09.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Można go ustawić na magazyn geograficznie nadmiarowy kopii zapasowych, w którym dane są nie tylko przechowywane w regionie, w którym jest hostowany serwer, ale także są replikowane do sparowanego regionu w celu zapewnienia opcji odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego do tworzenia kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="covered-information-is-backed-up-in-an-encrypted-format-to-ensure-confidentiality"></a>Uwzględnione informacje są objęte kopią zapasową w zaszyfrowanym formacie w celu zapewnienia poufności.

**Identyfikator:** 1623.09l2Organizational.4 — 09.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Można go ustawić na geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane są nie tylko przechowywane w regionie, w którym jest hostowany serwer, ale również są replikowane do sparowanego regionu w celu zapewnienia opcji odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego do tworzenia kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-performs-incremental-or-differential-backups-daily-and-full-backups-weekly-to-separate-media"></a>Organizacja wykonuje przyrostowe lub różnicowe kopie zapasowe codziennie i pełne kopie zapasowe co tydzień na oddzielnym nośniku.

**Identyfikator:** 1624.09l3Organizational.12 — 09.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Można go ustawić na geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane są nie tylko przechowywane w regionie, w którym jest hostowany serwer, ale również są replikowane do sparowanego regionu w celu zapewnienia opcji odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego do tworzenia kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="three-3-generations-of-backups-full-plus-all-related-incremental-or-differential-backups-are-stored-off-site-and-both-on-site-and-off-site-backups-are-logged-with-name-date-time-and-action"></a>Trzy (3) generacje kopii zapasowych (pełne i wszystkie powiązane przyrostowe lub różnicowe kopie zapasowe) są przechowywane poza lokacją, a kopie zapasowe zarówno w lokacji, jak i poza nią są rejestrowane przy użyciu nazwy, daty, godziny i akcji.

**Identyfikator:** 1625.09l3Organizational.34 - 09.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Backup należy włączyć dla Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Zapewnij ochronę usługi Azure Virtual Machines, włączając Azure Backup. Azure Backup to bezpieczne i ekonomiczne rozwiązanie do ochrony danych dla platformy Azure. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="the-organization-ensures-a-current-retrievable-copy-of-covered-information-is-available-before-movement-of-servers"></a>Organizacja zapewnia, że przed rozpoczęciem przemieszczania serwerów jest dostępna aktualna, możliwe do pobierania kopia objętych informacji.

**Identyfikator:** 1626.09l3Organizational.5 — 09.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Można go ustawić na magazyn geograficznie nadmiarowy kopii zapasowych, w którym dane są nie tylko przechowywane w regionie, w którym jest hostowany serwer, ale także są replikowane do sparowanego regionu w celu zapewnienia opcji odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego do tworzenia kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-tests-backup-information-following-each-backup-to-verify-media-reliability-and-information-integrity-and-at-least-annually-thereafter"></a>Organizacja testuje informacje o kopii zapasowej po każdej kopii zapasowej, aby zweryfikować niezawodność nośnika i integralność informacji, a następnie co najmniej raz do roku.

**Identyfikator:** 1627.09l3Organizational.6 — 09.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geograficznie nadmiarowa kopia zapasowa powinna być włączona dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Można go ustawić na geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane są nie tylko przechowywane w regionie, w którym jest hostowany serwer, ale również są replikowane do sparowanego regionu w celu zapewnienia opcji odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego do tworzenia kopii zapasowych jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="workforce-members-roles-and-responsibilities-in-the-data-backup-process-are-identified-and-communicated-to-the-workforce-in-particular-bring-your-own-device-byod-users-are-required-to-perform-backups-of-organizational-andor-client-data-on-their-devices"></a>Role i obowiązki członków personelu w procesie tworzenia kopii zapasowej danych są identyfikowane i przekazywane pracownikom. W szczególności użytkownicy "Przynieź własne urządzenie" (BYOD, Bring Your Own Device) są zobowiązani do wykonywania kopii zapasowych danych organizacji i/lub danych klienta na swoich urządzeniach.

**Identyfikator:** 1699.09l1Organizational.10 - 09.l **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Backup należy włączyć dla Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Zapewnij ochronę usługi Azure Virtual Machines, włączając Azure Backup. Azure Backup to bezpieczne i ekonomiczne rozwiązanie do ochrony danych dla platformy Azure. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="network-controls"></a>Kontrolki sieciowe

### <a name="the-organization-monitors-for-all-authorized-and-unauthorized-wireless-access-to-the-information-system-and-prohibits-installation-of-wireless-access-points-waps-unless-explicitly-authorized-in-writing-by-the-cio-or-hisher-designated-representative"></a>Organizacja monitoruje wszystkich autoryzowanych i nieautoryzowanych dostępu bezprzewodowego do systemu informacji i uniemożliwia instalację punktów dostępu bezprzewodowego (WAPs), chyba że jawnie autoryzowane na podstawie zapisu przez CIO lub jego wyznaczony przedstawiciel.

**Identyfikator:** 0858.09m1Organizational.4 — 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Wszystkie porty sieciowe powinny być ograniczone do sieciowych grup zabezpieczeń skojarzonych z maszyną wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center zidentyfikował, że niektóre reguły ruchu przychodzącego sieciowych grup zabezpieczeń są zbyt permisywne. Reguły ruchu przychodzącego nie powinny zezwalać na dostęp z zakresów "Any" lub "Internet". Może to potencjalnie umożliwić atakującym ukierunkowanie zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu do sieci just in time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp just in time (JIT) do sieci będzie monitorowany przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące właściwości Zapory systemu Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35d9882c-993d-44e6-87d2-db66ce21b636) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Właściwości Zapory systemu Windows" dla stanu zapory, połączeń, zarządzania regułami i powiadomień. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsFirewallProperties_AINE.json) |

### <a name="the-organization-ensures-the-security-of-information-in-networks-availability-of-network-services-and-information-services-using-the-network-and-the-protection-of-connected-services-from-unauthorized-access"></a>Organizacja zapewnia bezpieczeństwo informacji w sieciach, dostępność usług sieciowych i informacyjnych korzystających z sieci oraz ochronę połączonych usług przed nieautoryzowanym dostępem.

**Identyfikator:** 0859.09m1Organizational.78 — 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Na maszynach wirtualnych z Dostępem do Internetu należy stosować zalecenia dotyczące adaptacyjnego wzmacniania zabezpieczeń sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych skierowanych do Internetu i udostępnia zalecenia dotyczące reguł sieciowej grupy zabezpieczeń, które zmniejszają ryzyko ataku |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="the-organization-formally-manages-equipment-on-the-network-including-equipment-in-user-areas"></a>Organizacja formalnie zarządza sprzętem w sieci, w tym sprzętem w obszarach użytkowników.

**Identyfikator:** 0860.09m1Organizational.9 - 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Wdrażanie ustawień diagnostycznych dla sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9c29499-c1d1-4195-99bd-2ec9e3a9dc89) |Te zasady automatycznie wdrażają ustawienia diagnostyczne w sieciowych grupach zabezpieczeń. Konto magazynu o nazwie "{storagePrefixParameter}{NSGLocation}" zostanie utworzone automatycznie. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForNSG_Deploy.json) |
|[Service Bus używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Te zasady przeprowadzają inspekcję Service Bus nieskonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

### <a name="to-identify-and-authenticate-devices-on-local-andor-wide-area-networks-including-wireless-networks-nbspthe-information-system-uses-either-a-i-shared-known-information-solution-or-ii-an-organizational-authentication-solution-the-exact-selection-and-strength-of-which-is-dependent-on-the-security-categorization-of-the-information-system"></a>Aby identyfikować i uwierzytelniać urządzenia w sieciach lokalnych i/lub sieciach szerokich, w tym sieciach bezprzewodowych, system informacyjny korzysta z (i) udostępnionego znanego rozwiązania informacyjnego lub (ii) rozwiązania uwierzytelniania organizacyjnego, którego dokładny wybór i siła zależą od kategoryzacji zabezpieczeń systemu &nbsp; informacji.

**Identyfikator:** 0861.09m2Organizational.67 - 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Te zasady przeprowadza inspekcję App Service, które nie zostały skonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń — dostęp do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ff60f98-7fa4-410a-9f7f-0b00f5afdbdd) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Opcje zabezpieczeń — dostęp do sieci", aby zapewnić dostęp dla użytkowników anonimowych, kont lokalnych i dostępu zdalnego do rejestru. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_AINE.json) |

### <a name="the-organization-ensures-information-systems-protect-the-confidentiality-and-integrity-of-transmitted-information-including-during-preparation-for-transmission-and-during-reception"></a>Organizacja zapewnia, że systemy informacyjne chronią poufność i integralność przesyłanych informacji, w tym podczas przygotowywania do transmisji i podczas odbioru.

**Identyfikator:** 0862.09m2Organizational.8 — 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Te zasady przeprowadza inspekcję SQL Server, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |

### <a name="the-organization-builds-a-firewall-configuration-that-restricts-connections-between-un-trusted-networks-and-any-system-components-in-the-covered-information-environment-and-any-changes-to-the-firewall-configuration-are-updated-in-the-network-diagram"></a>Organizacja tworzy konfigurację zapory, która ogranicza połączenia między niezau zaufanymi sieciami i dowolnymi składnikami systemu w środowisku informacji objętych usługą; a wszelkie zmiany konfiguracji zapory są aktualizowane na diagramie sieciowym.

**Identyfikator:** 0863.09m2Organizational.910 - 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Centrum zdarzeń powinno używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Te zasady przeprowadza inspekcję wszystkich centrów zdarzeń, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

### <a name="usage-restrictions-and-implementation-guidance-are-formally-defined-for-voip-including-the-authorization-and-monitoring-of-the-service"></a>Ograniczenia użycia i wskazówki dotyczące implementacji są formalnie zdefiniowane dla usługi VoIP, w tym dotyczące autoryzacji i monitorowania usługi.

**Identyfikator:** 0864.09m2Organizational.12 - 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Cosmos DB używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Te zasady przeprowadzają inspekcję Cosmos DB, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

### <a name="the-organization-i-authorizes-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-through-the-use-of-interconnection-security-agreements-or-other-formal-agreement-ii-documents-each-connection-the-interface-characteristics-security-requirements-and-the-nature-of-the-information-communicated-iii-employs-a-deny-all-permit-by-exception-policy-for-allowing-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-and-iv-applies-a-default-deny-rule-that-drops-all-traffic-via-host-based-firewalls-or-port-filtering-tools-on-its-endpoints-workstations-servers-etc-except-those-services-and-ports-that-are-explicitly-allowed"></a>Organizacja (i) autoryzuje połączenia z systemu informacyjnego do innych systemów informacyjnych poza organizacją za pośrednictwem umów dotyczących zabezpieczeń połączeń lub innych umów formalnych; (ii) dokumentuje każde połączenie, charakterystykę interfejsu, wymagania dotyczące zabezpieczeń oraz charakter informacji, które są przekazywane; (iii) stosuje zasady odmowy wszystkiego, zezwolenia na użycie wyjątków do zezwalania na połączenia z systemu informacyjnego do innych systemów informacyjnych poza organizacją; i (iv) stosuje regułę odmowy domyślnej, która odrzuca cały ruch za pośrednictwem zapór opartych na hoście lub narzędzi filtrowania portów w punktach końcowych (stacjach roboczych, serwerach itp.), z wyjątkiem tych usług i portów, które są jawnie dozwolone.

**Identyfikator:** 0865.09m2Organizational.13 - 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Te zasady przeprowadza inspekcję Key Vault nieskonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |

### <a name="the-organization-describes-the-groups-roles-and-responsibilities-for-the-logical-management-of-network-components-and-ensures-coordination-of-and-consistency-in-the-elements-of-the-network-infrastructure"></a>Organizacja opisuje grupy, role i obowiązki dotyczące logicznego zarządzania składnikami sieci oraz zapewnia koordynację i spójność elementów infrastruktury sieciowej.

**Identyfikator:** 0866.09m3Organizational.1516 — 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Konta magazynu powinny ograniczać dostęp sieciowy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Dostęp sieciowy do kont magazynu powinien być ograniczony. Skonfiguruj reguły sieci, aby tylko aplikacje z dozwolonych sieci mogły uzyskać dostęp do konta magazynu. Aby zezwolić na połączenia z określonego Internetu lub klientów lokalnych, można przyznać dostęp do ruchu z określonych sieci wirtualnych platformy Azure lub z zakresów publicznych internetowych adresów IP |Inspekcja, Odmowa, Wyłączone |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="wireless-access-points-are-placed-in-secure-areas-and-shut-down-when-not-in-use-eg-nights-weekends"></a>Punkty dostępu bezprzewodowego są umieszczane w bezpiecznych obszarach i zamykane, gdy nie są one w użyciu (np. w weekendy).

**Identyfikator:** 0867.09m3Organizational.17 — 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Konta magazynu powinny używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Te zasady przeprowadza inspekcję konta magazynu, które nie jest skonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |

### <a name="the-organization-builds-a-firewall-configuration-to-restrict-inbound-and-outbound-traffic-to-that-which-is-necessary-for-the-covered-data-environment"></a>Organizacja tworzy konfigurację zapory w celu ograniczenia ruchu przychodzącego i wychodzącego do tego, co jest niezbędne dla objętego środowiska danych.

**Identyfikator:** 0868.09m3Organizational.18 — 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Te zasady przeprowadzają inspekcję Container Registry nieskonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="the-router-configuration-files-are-secured-and-synchronized"></a>Pliki konfiguracji routera są zabezpieczone i synchronizowane.

**Identyfikator:** 0869.09m3Organizational.19 — 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Te zasady przeprowadzają inspekcję Container Registry nieskonfigurowane do używania punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="access-to-all-proxies-is-denied-except-for-those-hosts-ports-and-services-that-are-explicitly-required"></a>Dostęp do wszystkich serwerów proxy jest odmawiany, z wyjątkiem tych hostów, portów i usług, które są jawnie wymagane.

**Identyfikator:** 0870.09m3Organizational.20 - 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Te zasady przeprowadza inspekcję Container Registry, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="authoritative-dns-servers-are-segregated-into-internal-and-external-roles"></a>Autorytatywne serwery DNS są podzielone na role wewnętrzne i zewnętrzne.

**Identyfikator:** 0871.09m3Organizational.22 — 09.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry używać punktu końcowego usługi dla sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Te zasady przeprowadza inspekcję Container Registry, które nie zostały skonfigurowane do korzystania z punktu końcowego usługi dla sieci wirtualnej. |Inspekcja, wyłączone |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="security-of-network-services"></a>Zabezpieczenia Network Services

### <a name="agreed-services-provided-by-a-network-service-providermanager-are-formally-managed-and-monitored-to-ensure-they-are-provided-securely"></a>Uzgadniane usługi udostępniane przez dostawcę/menedżera usług sieciowych są formalnie zarządzane i monitorowane w celu zapewnienia ich bezpiecznego świadczenia.

**Identyfikator:** 0835.09n1Organizational.1 — 09.n **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Agent zbierania danych ruchu sieciowego powinien być zainstalowany na maszynach wirtualnych z systemem Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center korzysta z agenta Zależności firmy Microsoft do zbierania danych o ruchu sieciowym z maszyn wirtualnych platformy Azure w celu włączenia zaawansowanych funkcji ochrony sieci, takich jak wizualizacja ruchu na mapie sieci, zalecenia dotyczące wzmacniania zabezpieczeń sieci i określone zagrożenia sieciowe. |AuditIfNotExists, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Maszyny wirtualne powinny być migrowane do nowych Azure Resource Manager zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |Użyj nowych usług Azure Resource Manager dla maszyn wirtualnych, aby zapewnić ulepszenia zabezpieczeń, takie jak: silniejsza kontrola dostępu (RBAC), lepsza inspekcja, wdrażanie i ład oparty na usłudze Azure Resource Manager, dostęp do tożsamości zarządzanych, dostęp do magazynu kluczy dla wpisów tajnych, uwierzytelnianie oparte na usłudze Azure AD oraz obsługa tagów i grup zasobów w celu łatwiejszego zarządzania zabezpieczeniami |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="the-organization-formally-authorizes-and-documents-the-characteristics-of-each-connection-from-an-information-system-to-other-information-systems-outside-the-organization"></a>Organizacja formalnie autoryzuje i dokumentuje charakterystyki każdego połączenia z systemu informacyjnego do innych systemów informacyjnych poza organizacją.

**Identyfikator:** 0836.09.n2Organizational.1 - 09.n **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Agent zbierania danych ruchu sieciowego powinien być zainstalowany na maszynach wirtualnych z systemem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center korzysta z agenta Zależności firmy Microsoft do zbierania danych o ruchu sieciowym z maszyn wirtualnych platformy Azure w celu włączenia zaawansowanych funkcji ochrony sieci, takich jak wizualizacja ruchu na mapie sieci, zalecenia dotyczące wzmacniania zabezpieczeń sieci i określone zagrożenia sieciowe. |AuditIfNotExists, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="formal-agreements-with-external-information-system-providers-include-specific-obligations-for-security-and-privacy"></a>Formalne umowy z zewnętrznymi dostawcami systemu informacji zawierają konkretne zobowiązania dotyczące bezpieczeństwa i prywatności.

**Identyfikator:** 0837.09.n2Organizational.2 - 09.n **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci na platformie Azure, do i z niej. Monitorowanie na poziomie scenariusza umożliwia diagnozowanie problemów w widoku na poziomie sieci. Narzędzia do diagnostyki i wizualizacji sieci Network Watcher pomagają zrozumieć, zdiagnozować i uzyskać wgląd w sieć na platformie Azure. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-reviews-and-updates-the-interconnection-security-agreements-on-an-ongoing-basis-verifying-enforcement-of-security-requirements"></a>Organizacja na bieżąco przegląda i aktualizuje umowy dotyczące zabezpieczeń połączeń, weryfikując wymuszanie wymagań dotyczących zabezpieczeń.

**Identyfikator:** 0885.09n2Organizational.3 — 09.n **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Agent zbierania danych ruchu sieciowego powinien być zainstalowany na maszynach wirtualnych z systemem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center używa agenta Zależności firmy Microsoft do zbierania danych o ruchu sieciowym z maszyn wirtualnych platformy Azure w celu włączenia zaawansowanych funkcji ochrony sieci, takich jak wizualizacja ruchu na mapie sieci, zalecenia dotyczące wzmacniania zabezpieczeń sieci i określone zagrożenia sieciowe. |AuditIfNotExists, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="the-organization-employs-and-documents-in-a-formal-agreement-or-other-document-either-i-allow-all-deny-by-exception-or-ii-deny-all-permit-by-exception-preferred-policy-for-allowing-specific-information-systems-to-connect-to-external-information-systems"></a>Organizacja stosuje dokumenty i w formalnej umowie lub innym dokumencie, i) allow-all, deny-by-exception lub, ii) deny-all, permit-by-exception (preferowane), zasady zezwalania określonym systemom informacyjnym na łączenie się z zewnętrznymi systemami informacji.

**Identyfikator:** 0886.09n2Organizational.4 — 09.n **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie sieci na platformie Azure, do i z niej. Monitorowanie na poziomie scenariusza umożliwia diagnozowanie problemów w widoku end-to-end poziomu sieci. Narzędzia do diagnostyki i wizualizacji sieci Network Watcher pomagają zrozumieć, zdiagnozować i uzyskać wgląd w sieć na platformie Azure. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-requires-externaloutsourced-service-providers-to-identify-the-specific-functions-ports-and-protocols-used-in-the-provision-of-the-externaloutsourced-services"></a>Organizacja wymaga, aby zewnętrzni/zewnętrzni dostawcy usług identyfikowali określone funkcje, porty i protokoły używane podczas udostępniania usług zewnętrznych/zewnętrznych.

**Identyfikator:** 0887.09n2Organizational.5 — 09.n **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Agent zbierania danych ruchu sieciowego powinien być zainstalowany na maszynach wirtualnych z systemem Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center korzysta z agenta Zależności firmy Microsoft do zbierania danych o ruchu sieciowym z maszyn wirtualnych platformy Azure w celu włączenia zaawansowanych funkcji ochrony sieci, takich jak wizualizacja ruchu na mapie sieci, zalecenia dotyczące wzmacniania zabezpieczeń sieci i określone zagrożenia sieciowe. |AuditIfNotExists, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |

### <a name="the-contract-with-the-externaloutsourced-service-provider-includes-the-specification-that-the-service-provider-is-responsible-for-the-protection-of-covered-information-shared"></a>Umowa z zewnętrznym/zewnętrznym dostawcą usług zawiera specyfikację, że dostawca usług jest odpowiedzialny za ochronę udostępnianych informacji objętych usługą.

**Identyfikator:** 0888.09n2Organizational.6 - 09.n **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci na platformie Azure, do i z niej. Monitorowanie na poziomie scenariusza umożliwia diagnozowanie problemów w widoku na poziomie sieci. Narzędzia do diagnostyki i wizualizacji sieci Network Watcher pomagają zrozumieć, zdiagnozować i uzyskać wgląd w sieć na platformie Azure. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="management-of-removable-media"></a>Zarządzanie nośnikami wymiennymi

### <a name="the-organization-based-on-the-data-classification-level-registers-media-including-laptops-prior-to-use-places-reasonable-restrictions-on-how-such-media-be-used-and-provides-an-appropriate-level-of-physical-and-logical-protection-including-encryption-for-media-containing-covered-information-until-properly-destroyed-or-sanitized"></a>Organizacja, na podstawie poziomu klasyfikacji danych, rejestruje nośniki (w tym laptopy) przed użyciem, nakłada uzasadnione ograniczenia dotyczące sposobu używania takich nośników oraz zapewnia odpowiedni poziom ochrony fizycznej i logicznej (w tym szyfrowania) dla multimediów zawierających informacje objęte usługą do momentu prawidłowego zniszczenia lub czydzenia.

**Identyfikator:** 0301.09o1Organizational.123 - 09.o **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Transparent Data Encryption w bazach danych SQL powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Należy włączyć funkcję Transparent Data Encryption, aby chronić dane w spoczynku i spełniać wymagania dotyczące zgodności |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="the-organization-protects-and-controls-media-containing-sensitive-information-during-transport-outside-of-controlled-areas"></a>Organizacja chroni i kontroluje nośniki zawierające poufne informacje podczas transportu poza kontrolowane obszary.

**Identyfikator:** 0302.09o2Organizational.1 - 09.o **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Maszyny wirtualne bez włączonego szyfrowania dysków będą monitorowane przez Azure Security Center zgodnie z zaleceniami. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="digital-and-non-digital-media-requiring-restricted-use-and-the-specific-safeguards-used-to-restrict-their-use-are-identified"></a>Zidentyfikowano nośniki cyfrowe i inne niż cyfrowe wymagające ograniczonego użycia oraz określone zabezpieczenia używane do ograniczenia ich użycia.

**Identyfikator:** 0303.09o2Organizational.2 - 09.o **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Niedołączane dyski powinny być szyfrowane](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Te zasady przeprowadza inspekcję niedołączanych dysków bez włączonego szyfrowania. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="the-organization-restricts-the-use-of-writable-removable-media-and-personally-owned-removable-media-in-organizational-systems"></a>Organizacja ogranicza użycie zapisywalnych nośników wymiennych i osobistych nośników wymiennych w systemach organizacyjnych.

**Identyfikator:** 0304.09o3Organizational.1 - 09.o **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Wymagaj szyfrowania na Data Lake Store kont](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Te zasady zapewniają, że szyfrowanie jest włączone na wszystkich Data Lake Store kont |Odmów |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[Wystąpienia zarządzane SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Zaimplementowanie funkcji Transparent Data Encryption (TDE) przy użyciu własnego klucza zapewnia większą przejrzystość i kontrolę nad funkcji ochrony TDE, zwiększone zabezpieczenia dzięki usłudze zewnętrznej, która jest sterowana przez moduł HSM, oraz podwyższy poziom rozdzielenia obowiązków. To zalecenie dotyczy organizacji, które mają powiązane wymagania dotyczące zgodności. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Serwery SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementacja Transparent Data Encryption (TDE) przy użyciu własnego klucza zapewnia większą przejrzystość i kontrolę nad funkcji ochrony TDE, zwiększone zabezpieczenia za pomocą usługi zewnętrznej z modułem HSM oraz podwyższy poziom rozdzielenia obowiązków. To zalecenie dotyczy organizacji, które mają powiązane wymagania dotyczące zgodności. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="information-exchange-policies-and-procedures"></a>Zasady i procedury wymiany informacji

### <a name="cloud-service-providers-use-an-industry-recognized-virtualization-platform-and-standard-virtualization-formats-eg-open-virtualization-format-ovf-to-help-ensure-interoperability-and-has-documented-custom-changes-made-to-any-hypervisor-in-use-and-all-solution-specific-virtualization-hooks-available-for-customer-review"></a>Dostawcy usług w chmurze używają uznanej w branży platformy wirtualizacji i standardowych formatów wirtualizacji (np. Open Virtualization Format, OVF) w celu zapewnienia współdziałania, a także udokumentowano niestandardowe zmiany wprowadzone do dowolnej funkcji hypervisor w użyciu i wszystkich punktów zaczepienia wirtualizacji specyficznych dla rozwiązania dostępnych do przeglądu przez klientów.

**Identyfikator:** 0662.09sCSPOrganizational.2 — 09.s **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Upewnij się, że dla aplikacji internetowej ustawiono wartość "Certyfikaty klienta (przychodzące certyfikaty klienta)" na wartość "Wł."](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Certyfikaty klienta umożliwiają aplikacji żądanie certyfikatu dla żądań przychodzących. Tylko klienci z prawidłowym certyfikatem będą mogli uzyskać dostęp do aplikacji. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-organization-formally-addresses-multiple-safeguards-before-allowing-the-use-of-information-systems-for-information-exchange"></a>Organizacja formalnie uwzględnia wiele zabezpieczeń przed zezwoleniem na używanie systemów informacyjnych do wymiany informacji.

**Identyfikator:** 0901.09s1Organizational.1 — 09.s **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Mechanizm CORS nie powinien zezwalać na dostęp do aplikacji internetowych ze wszystkich zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Współużytkowania zasobów między źródłami (CORS, Cross-Origin Resource Sharing) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji internetowej. Zezwalaj na interakcję z aplikacją internetową tylko wymaganym domenom. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="remote-external-access-to-the-organizations-information-assets-and-access-to-external-information-assets-for-which-the-organization-has-no-control-is-based-on-clearly-defined-terms-and-conditions"></a>Zdalny (zewnętrzny) dostęp do zasobów informacyjnych organizacji oraz dostęp do zewnętrznych zasobów informacyjnych (do których organizacja nie ma kontroli) jest oparty na jasno zdefiniowanych warunkach i postanowieniach.

**Identyfikator:** 0902.09s2Organizational.13 - 09.s **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Funkcja CORS nie powinna zezwalać każdemu zasobowi na dostęp do aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Współużytkowania zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji funkcji. Zezwalaj na interakcję z aplikacją funkcji tylko wymaganym domenom. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="the-organization-establishes-terms-and-conditions-consistent-with-any-trust-relationship-established-with-other-organizations-owning-operating-andor-maintaining-external-information-systems-allowing-authorized-individuals-to-i-access-the-information-system-from-external-information-systems-and-ii-process-store-or-transmit-organization-controlled-information-using-external-information-systems"></a>Organizacja ustanawia warunki i postanowienia, zgodnie z dowolnymi relacjami zaufania ustanowionych z innymi organizacjami, które są właścicielami, operacyjnymi i/lub konserwami zewnętrznych systemów informacyjnych, umożliwiając autoryzowanym osobom (i) dostęp do systemu informacji z zewnętrznych systemów informacyjnych; oraz (ii) przetwarzać, przechowywać lub przesyłać informacje kontrolowane przez organizację przy użyciu zewnętrznych systemów informacyjnych.

**Identyfikator:** 0911.09s1Organizational.2 — 09.s **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Usługa CORS nie powinna zezwalać każdemu zasobowi na dostęp do aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |Udostępnianie zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji interfejsu API. Zezwalaj na interakcję z aplikacją interfejsu API tylko wymaganym domenom. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |

### <a name="cryptography-is-used-to-protect-the-confidentiality-and-integrity-of-remote-access-sessions-to-the-internal-network-and-to-external-systems"></a>Kryptografia służy do ochrony poufności i integralności sesji dostępu zdalnego do sieci wewnętrznej i systemów zewnętrznych.

**Identyfikator:** 0912.09s1Organizational.4 — 09.s **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zdalne debugowanie powinno być wyłączone dla aplikacji internetowych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Debugowanie zdalne wymaga otwarcia portów przychodzących w aplikacji internetowej. Debugowanie zdalne powinno być wyłączone. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="strong-cryptography-protocols-are-used-to-safeguard-covered-information-during-transmission-over-less-trusted--open-public-networks"></a>Protokoły silnej kryptografii są używane do zabezpieczania informacji objętych podczas transmisji za pośrednictwem mniej zaufanych/otwartych sieci publicznych.

**Identyfikator:** 0913.09s1Organizational.5 — 09.s **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zdalne debugowanie powinno być wyłączone dla aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Debugowanie zdalne wymaga otwarcia portów przychodzących w aplikacjach funkcji. Debugowanie zdalne powinno być wyłączone. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-ensures-that-communication-protection-requirements-including-the-security-of-exchanges-of-information-is-the-subject-of-policy-development-and-compliance-audits"></a>Organizacja zapewnia, że wymagania dotyczące ochrony komunikacji, w tym bezpieczeństwo wymiany informacji, są objęte opracowywaniem zasad i inspekcjami zgodności.

**Identyfikator:** 0914.09s1Organizational.6 - 09.s **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zdalne debugowanie powinno być wyłączone dla API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Debugowanie zdalne wymaga otwarcia portów przychodzących w aplikacjach interfejsu API. Zdalne debugowanie powinno być wyłączone. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-limits-the-use-of-organization-controlled-portable-storage-media-by-authorized-individuals-on-external-information-systems"></a>Organizacja ogranicza użycie kontrolowanego przez organizację przenośnego nośnika magazynu przez autoryzowane osoby w zewnętrznych systemach informacyjnych.

**Identyfikator:** 0915.09s2Organizational.2 - 09.s **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Upewnij się, że dla aplikacji internetowej ustawiono wartość "Certyfikaty klienta (przychodzące certyfikaty klienta)" na wartość "Wł."](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Certyfikaty klienta umożliwiają aplikacji żądanie certyfikatu dla żądań przychodzących. Tylko klienci z prawidłowym certyfikatem będą mogli uzyskać dostęp do aplikacji. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-information-system-prohibits-remote-activation-of-collaborative-computing-devices-and-provides-an-explicit-indication-of-use-to-users-physically-present-at-the-devices"></a>System informacyjny zabrania zdalnej aktywacji urządzeń do wspólnego przetwarzania danych i zapewnia jawne wskazanie użycia dla użytkowników fizycznie obecnych na urządzeniach.

**Identyfikator:** 0916.09s2Organizational.4 — 09.s **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Mechanizm CORS nie powinien zezwalać na dostęp do aplikacji internetowych ze wszystkich zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Udostępnianie zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji internetowej. Zezwalanie na interakcję z aplikacją internetową tylko wymaganym domenom. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="cloud-service-providers-use-secure-eg-non-clear-text-and-authenticated-standardized-network-protocols-for-the-import-and-export-of-data-and-to-manage-the-service-and-make-available-a-document-to-consumers-tenants-detailing-the-relevant-interoperability-and-portability-standards-that-are-involved"></a>Dostawcy usług w chmurze używają bezpiecznych (np. nieczyszonego tekstu i uwierzytelnionych) standardowych protokołów sieciowych do importowania i eksportowania danych oraz do zarządzania usługą, a także do zarządzania usługą, a także do zapewnienia klientom (dzierżawom) dokumentu ze szczegółowymi informacjami na temat odpowiednich standardów współdziałania i przenośności, które są w tym związane.

**Identyfikator:** 0960.09sCSPOrganizational.1 — 09.s **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Funkcja CORS nie powinna zezwalać każdemu zasobowi na dostęp do aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Współużytkowania zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji funkcji. Zezwalaj tylko wymaganym domenom na interakcję z aplikacją funkcji. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="personnel-are-appropriately-trained-on-leading-principles-and-practices-for-all-types-of-information-exchange-oral-paper-and-electronic"></a>Personel jest odpowiednio wytrenowany na podstawie wiodących zasad i praktyk dla wszystkich typów wymiany informacji (na temat artykułów papierowych i elektronicznych).

**Identyfikator:** 1325.09s1Organizational.3 — 09.s **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zdalne debugowanie powinno być wyłączone dla aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Debugowanie zdalne wymaga otwarcia portów przychodzących w aplikacjach funkcji. Zdalne debugowanie powinno być wyłączone. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

## <a name="on-line-transactions"></a>Transakcje online

### <a name="data-involved-in-electronic-commerce-and-online-transactions-is-checked-to-determine-if-it-contains-covered-information"></a>Dane związane z handelem elektronicznym i transakcjami online są sprawdzane w celu ustalenia, czy zawierają objęte informacje.

**Identyfikator:** 0943.09y1Organizational.1 - 09.y **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Wymaganie inspekcji bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza na koncie magazynu akceptowanie żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="protocols-used-to-communicate-between-all-involved-parties-are-secured-using-cryptographic-techniques-eg-ssl"></a>Protokoły używane do komunikacji między wszystkimi zaangażowanymi stronami są zabezpieczone przy użyciu technik kryptograficznych (np. protokołu SSL).

**Identyfikator:** 0945.09y1Organizational.3 — 09.y **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, które nie zawierają określonych certyfikatów w zaufanym katalogu głównym](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F934345e1-4dfb-4c70-90d7-41990dc9608b) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli magazyn zaufanych certyfikatów głównych komputera (Cert:\LocalMachine\Root) nie zawiera co najmniej jednego certyfikatu wymienionego przez parametr zasad. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot_AINE.json) |

### <a name="the-organization-requires-the-use-of-encryption-between-and-the-use-of-electronic-signatures-by-each-of-the-parties-involved-in-the-transaction"></a>Organizacja wymaga szyfrowania między i korzystania z podpisów elektronicznych przez każdą ze stron zaangażowanych w transakcję.

**Identyfikator:** 0946.09y2Organizational.14 — 09.y **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączania tylko połączeń za pośrednictwem protokołu SSL do Azure Cache for Redis. Korzystanie z bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

### <a name="the-organization-ensures-the-storage-of-the-transaction-details-are-located-outside-of-any-publicly-accessible-environments-eg-on-a-storage-platform-existing-on-the-organizations-intranet-and-not-retained-and-exposed-on-a-storage-medium-directly-accessible-from-the-internet"></a>Organizacja zapewnia, że szczegóły transakcji są przechowywane poza dowolnymi publicznie dostępnymi środowiskami (np. na platformie magazynu istniejącej w intranecie organizacji) i nie są zachowywane ani udostępniane na nośniku magazynu dostępnym bezpośrednio z Internetu.

**Identyfikator:** 0947.09y2Organizational.2 — 09.y **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Wymuszanie połączenia SSL powinno być włączone dla serwerów baz danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle" przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, aby protokół SSL był zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="where-a-trusted-authority-is-used-eg-for-the-purposes-of-issuing-and-maintaining-digital-signatures-andor-digital-certificates-security-is-integrated-and-embedded-throughout-the-entire-end-to-end-certificatesignature-management-process"></a>W przypadku, gdy używany jest zaufany urząd (np. w celu wystawiania i obsługi podpisów cyfrowych i/lub certyfikatów cyfrowych), zabezpieczenia są zintegrowane i osadzone w całym całym procesie zarządzania certyfikatami/podpisami.

**Identyfikator:** 0948.09y2Organizational.3 - 09.y **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy włączyć wymuszanie połączenia SSL dla serwerów baz danych MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu Secure Sockets Layer (SSL). Wymuszanie połączeń SSL między serwerem bazy danych i aplikacjami klienckimi pomaga chronić przed atakami "man in the middle", szyfrując strumień danych między serwerem a aplikacją. Ta konfiguracja wymusza, aby protokół SSL był zawsze włączony w celu uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-protocols-used-for-communications-are-enhanced-to-address-any-new-vulnerability-and-the-updated-versions-of-the-protocols-are-adopted-as-soon-as-possible"></a>Protokoły używane do komunikacji są ulepszone w celu rozwiązania wszelkich nowych luk w zabezpieczeniach, a zaktualizowane wersje protokołów są stosowane tak szybko, jak to możliwe.

**Identyfikator:** 0949.09y2Organizational.5 - 09.y **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwały w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[W aplikacji funkcji powinna być używana najnowsza wersja TLS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja TLS powinna być używana w aplikacji internetowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnianie do najnowszej wersji TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="control-of-operational-software"></a>Kontrola nad oprogramowaniem operacyjnym

### <a name="only-authorized-administrators-are-allowed-to-implement-approved-upgrades-to-software-applications-and-program-libraries-based-on-business-requirements-and-the-security-implications-of-the-release"></a>Tylko autoryzowani administratorzy mogą wdrażać zatwierdzone uaktualnienia oprogramowania, aplikacji i bibliotek programów na podstawie wymagań biznesowych i skutków dla bezpieczeństwa wydania.

**Identyfikator:** 0605.10h1System.12 — 10.h **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Serwery, które nie spełniają skonfigurowanego planu bazowego, będą monitorowane przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń — inspekcja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33936777-f2ac-45aa-82ec-07958ec9ade4) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Opcje zabezpieczeń — inspekcja" w celu wymuszywania podkategorii zasad inspekcji i zamknięcia, jeśli nie można rejestrować inspekcji zabezpieczeń. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAudit_AINE.json) |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — zarządzanie kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94d9aca8-3757-46df-aa51-f218c5f11954) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Zasady inspekcji systemu — zarządzanie kontami" dotyczące inspekcji aplikacji, zabezpieczeń i zarządzania użytkownikami oraz innych zdarzeń zarządzania. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesAccountManagement_AINE.json) |

### <a name="applications-and-operating-systems-are-successfully-tested-for-usability-security-and-impact-prior-to-production"></a>Aplikacje i systemy operacyjne zostały pomyślnie przetestowane pod kątem użyteczności, bezpieczeństwa i wpływu przed rozpoczęciem produkcji.

**Identyfikator:** 0606.10h2System.1 - 10.h **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń kontenera](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Przeszukaj luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach z zainstalowaną i wyświetlaną jako rekomendacje platformy Docker w Azure Security Center. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-uses-its-configuration-control-program-to-maintain-control-of-all-implemented-software-and-its-system-documentation-and-archive-prior-versions-of-implemented-software-and-associated-system-documentation"></a>Organizacja korzysta ze swojego programu kontroli konfiguracji, aby zachować kontrolę nad całym wdrożonym oprogramowaniem i dokumentacją systemu oraz archiwizować wcześniejsze wersje wdrożonego oprogramowania i skojarzoną dokumentację systemu.

**Identyfikator:** 0607.10h2System.23 — 10.h **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Na maszynach należy włączyć funkcje adaptacyjnego sterowania aplikacjami do definiowania bezpiecznych aplikacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Włącz kontrolki aplikacji, aby zdefiniować listę znanych bezpiecznych aplikacji uruchomionych na maszynach i powiadamiać o uruchomieniu innych aplikacji. Ułatwia to ochronę maszyn przed złośliwym oprogramowaniem. Aby uprościć proces konfigurowania i obsługi reguł, Security Center uczenia maszynowego analizuje aplikacje uruchomione na poszczególnych maszynach i sugeruje listę znanych bezpiecznych aplikacji. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Przejmij luki w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

## <a name="change-control-procedures"></a>Procedury kontroli zmian

### <a name="managers-responsible-for-application-systems-are-also-responsible-for-the-strict-control-security-of-the-project-or-support-environment-and-ensure-that-all-proposed-system-changes-are-reviewed-to-check-that-they-do-not-compromise-the-security-of-either-the-system-or-the-operating-environment"></a>Menedżerowie odpowiedzialni za systemy aplikacji są również odpowiedzialni za ścisłą kontrolę (bezpieczeństwo) projektu lub środowiska pomocy technicznej i zapewniają, że wszystkie proponowane zmiany systemu są sprawdzane w celu sprawdzenia, czy nie naruszyć bezpieczeństwa systemu lub środowiska operacyjnego.

**Identyfikator:** 0635.10k1Organizational.12 — 10.k **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania "Zasady inspekcji systemu — szczegółowe śledzenie"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Zasady inspekcji systemu — szczegółowe śledzenie" dotyczące inspekcji interfejsu DPAPI, tworzenia/zakończenia procesu, zdarzeń RPC i aktywności PNP. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-formally-addresses-purpose-scope-roles-responsibilities-management-commitment-coordination-among-organizational-entities-and-compliance-for-configuration-management-eg-through-policies-standards-processes"></a>Organizacja formalnie dotyczy przeznaczenia, zakresu, ról, obowiązków, zobowiązania do zarządzania, koordynacji między jednostkami organizacyjnymi oraz zgodności w zakresie zarządzania konfiguracją (np. za pośrednictwem zasad, standardów, procesów).

**Identyfikator:** 0636.10k2Organizational.1 — 10.k **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania "Zasady inspekcji systemu — szczegółowe śledzenie"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Zasady inspekcji systemu — szczegółowe śledzenie" dotyczące inspekcji interfejsu DPAPI, tworzenia/zakończenia procesu, zdarzeń RPC i aktywności PNP. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-has-developed-documented-and-implemented-a-configuration-management-plan-for-the-information-system"></a>Organizacja opracowała, udokumentowała i wdrożyła plan zarządzania konfiguracją dla systemu informacji.

**Identyfikator:** 0637.10k2Organizational.2 — 10.k **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Komputery z systemem Windows powinny mieć określone zasady grupy w kategorii "Zasady inspekcji systemu — szczegółowe śledzenie" dotyczące inspekcji interfejsu DPAPI, tworzenia/zakończenia procesu, zdarzeń RPC i działania PNP. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="changes-are-formally-controlled-documented-and-enforced-in-order-to-minimize-the-corruption-of-information-systems"></a>Zmiany są formalnie kontrolowane, dokumentowane i wymuszane w celu zminimalizowania uszkodzenia systemów informacyjnych.

**Identyfikator:** 0638.10k2Organizational.34569 - 10.k **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Komputery z systemem Windows powinny mieć określone zasady grupy w kategorii "Zasady inspekcji systemu — szczegółowe śledzenie" dotyczące inspekcji interfejsu DPAPI, tworzenia/zakończenia procesu, zdarzeń RPC i działania PNP. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="installation-checklists-and-vulnerability-scans-are-used-to-validate-the-configuration-of-servers-workstations-devices-and-appliances-and-ensure-the-configuration-meets-minimum-standards"></a>Listy kontrolne instalacji i skanowania luk w zabezpieczeniach służą do weryfikowania konfiguracji serwerów, stacji roboczych, urządzeń i urządzeń oraz upewniają się, że konfiguracja spełnia minimalne standardy.

**Identyfikator:** 0639.10k2Organizational.78 - 10.k **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania "Zasady inspekcji systemu — szczegółowe śledzenie"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Zasady inspekcji systemu — szczegółowe śledzenie" dotyczące inspekcji interfejsu DPAPI, tworzenia/zakończenia procesu, zdarzeń RPC i aktywności PNP. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="where-development-is-outsourced-change-control-procedures-to-address-security-are-included-in-the-contracts-and-specifically-require-the-developer-to-track-security-flaws-and-flaw-resolution-within-the-system-component-or-service-and-report-findings-to-organization-defined-personnel-or-roles"></a>Tam, gdzie opracowywanie jest zewnętrzne, procedury kontroli zmian w celu rozwiązania problemów z zabezpieczeniami są uwzględnione w umowach i wymagają od dewelopera śledzenia wad zabezpieczeń i rozwiązywania błędów w systemie, składniku lub usłudze i zgłaszania wyników do pracowników lub ról zdefiniowanych przez organizację.

**Identyfikator:** 0640.10k2Organizational.1012 - 10.k **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania "Zasady inspekcji systemu — szczegółowe śledzenie"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Zasady inspekcji systemu — szczegółowe śledzenie" dotyczące inspekcji interfejsu DPAPI, tworzenia/zakończenia procesu, zdarzeń RPC i aktywności PNP. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-does-not-use-automated-updates-on-critical-systems"></a>Organizacja nie używa automatycznych aktualizacji w systemach krytycznych.

**Identyfikator:** 0641.10k2Organizational.11 — 10.k **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Komputery z systemem Windows powinny mieć określone zasady grupy w kategorii "Zasady inspekcji systemu — szczegółowe śledzenie" dotyczące inspekcji interfejsu DPAPI, tworzenia/zakończenia procesu, zdarzeń RPC i działania PNP. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-develops-documents-and-maintains-under-configuration-control-a-current-baseline-configuration-of-the-information-system-and-reviews-and-updates-the-baseline-as-required"></a>Organizacja opracowuje, dokumentuje i utrzymuje pod kontrolą konfiguracji bieżącą konfigurację odniesienia systemu informacji oraz przegląda i aktualizuje punkt odniesienia zgodnie z potrzebami.

**Identyfikator:** 0642.10k3Organizational.12 — 10.k **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Komputery z systemem Windows powinny mieć określone zasady grupy w kategorii "Zasady inspekcji systemu — szczegółowe śledzenie" dotyczące inspekcji interfejsu DPAPI, tworzenia/zakończenia procesu, zdarzeń RPC i działania PNP. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-i-establishes-and-documents-mandatory-configuration-settings-for-information-technology-products-employed-within-the-information-system-using-the-latest-security-configuration-baselines-ii-identifies-documents-and-approves-exceptions-from-the-mandatory-established-configuration-settings-for-individual-components-based-on-explicit-operational-requirements-and-iii-monitors-and-controls-changes-to-the-configuration-settings-in-accordance-with-organizational-policies-and-procedures"></a>Organizacja (i) ustanawia i dokumentuje obowiązkowe ustawienia konfiguracji produktów technologii informatycznych, które są stosowane w systemie informacyjnym przy użyciu najnowszych linii bazowych konfiguracji zabezpieczeń; (ii) identyfikuje, dokumentuje i zatwierdza wyjątki od obowiązkowych ustalonych ustawień konfiguracji dla poszczególnych składników na podstawie jawnych wymagań operacyjnych; i (iii) monitoruje i kontroluje zmiany ustawień konfiguracji zgodnie z zasadami i procedurami organizacji.

**Identyfikator:** 0643.10k3Organizational.3 — 10.k **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania "Zasady inspekcji systemu — szczegółowe śledzenie"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Zasady inspekcji systemu — szczegółowe śledzenie" dotyczące inspekcji interfejsu DPAPI, tworzenia/zakończenia procesu, zdarzeń RPC i aktywności PNP. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-employs-automated-mechanisms-to-i-centrally-manage-apply-and-verify-configuration-settings-ii-respond-to-unauthorized-changes-to-network-and-system-security-related-configuration-settings-and-iii-enforce-access-restrictions-and-auditing-of-the-enforcement-actions"></a>Organizacja stosuje zautomatyzowane mechanizmy do (i) centralnego zarządzania, stosowania i weryfikowania ustawień konfiguracji; (ii) odpowiadanie na nieautoryzowane zmiany ustawień konfiguracji związanych z zabezpieczeniami sieci i systemu; i (iii) wymuszać ograniczenia dostępu i inspekcję akcji wymuszania.

**Identyfikator:** 0644.10k3Organizational.4 — 10.k **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania "Zasady inspekcji systemu — szczegółowe śledzenie"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Zasady inspekcji systemu — szczegółowe śledzenie" dotyczące inspekcji interfejsu DPAPI, tworzenia/zakończenia procesu, zdarzeń RPC i aktywności PNP. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

## <a name="control-of-technical-vulnerabilities"></a>Kontrola luk w zabezpieczeniach technicznych

### <a name="technical-vulnerabilities-are-identified-evaluated-for-risk-and-corrected-in-a-timely-manner"></a>Luki w zabezpieczeniach technicznych są identyfikowane, oceniane na ryzyko i poprawiane w terminowy sposób.

**Identyfikator:** 0709.10m1Organizational.1 – 10.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Na maszynach wirtualnych należy włączyć rozwiązanie do oceny luk w zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Przeprowadza inspekcję maszyn wirtualnych w celu wykrycia, czy są uruchomione obsługiwane rozwiązanie do oceny luk w zabezpieczeniach. Podstawowym składnikiem każdego programu związanego z ryzykiem cybernetycznym i bezpieczeństwem jest identyfikacja i analiza luk w zabezpieczeniach. Azure Security Center warstwy cenowej standardowa obejmuje skanowanie maszyn wirtualnych w celu wykorzystania luk w zabezpieczeniach bez dodatkowych kosztów. Ponadto Security Center automatycznie wdrożyć to narzędzie. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń kontenera](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Przeszukaj luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach z zainstalowaną i wyświetlaną jako rekomendacje platformy Docker w Azure Security Center. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Serwery, które nie spełniają skonfigurowanego planu bazowego, będą monitorowane przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Przeszukaj luki w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Należy skorygować luki w zabezpieczeniach baz danych SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Monitoruj wyniki skanowania w ramach oceny luk w zabezpieczeniach i zalecenia dotyczące sposobu korygowania luk w zabezpieczeniach bazy danych. |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[Ocena luk w zabezpieczeniach powinna być włączona na SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Przeskanuj SQL Managed Instance, dla których nie włączono cyklicznych skanowań oceny luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może odnajdywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[Na serwerach SQL należy włączyć ocenę luk w zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Inspekcja Azure SQL, które nie mają włączonych cyklicznych skanowań oceny luk w zabezpieczeniach. Ocena luk w zabezpieczeniach umożliwia odnajdywanie, śledzenie i korygowanie potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń — microsoft network server](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcaf2d518-f029-4f6b-833b-d7081702f253) |Maszyny z systemem Windows powinny mieć określone zasady grupy w kategorii "Opcje zabezpieczeń — serwer sieci Microsoft" do wyłączania serwera SMB v1. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_AINE.json) |

### <a name="a-hardened-configuration-standard-exists-for-all-system-and-network-components"></a>Dla wszystkich składników systemu i sieci istnieje standard konfiguracji ze wzmacnianiem zabezpieczeń.

**Identyfikator:** 0710.10m2Organizational.1 - 10.m **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Ocena luk w zabezpieczeniach powinna być włączona na SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Przejmij SQL Managed Instance, które nie mają włączonych cyklicznych skanowań oceny luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może odnajdywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

### <a name="a-technical-vulnerability-management-program-is-in-place-to-monitor-assess-rank-and-remediate-vulnerabilities-identified-in-systems"></a>Istnieje techniczny program do zarządzania lukami w zabezpieczeniach, który umożliwia monitorowanie, ocenianie, klasyfikację i korygowanie luk w zabezpieczeniach zidentyfikowanych w systemach.

**Identyfikator:** 0711.10m2Organizational.23 - 10.m **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Na maszynach wirtualnych należy włączyć rozwiązanie do oceny luk w zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Przeprowadza inspekcję maszyn wirtualnych w celu wykrycia, czy są uruchomione obsługiwane rozwiązanie do oceny luk w zabezpieczeniach. Podstawowym składnikiem każdego programu związanego z ryzykiem cybernetycznym i bezpieczeństwem jest identyfikacja i analiza luk w zabezpieczeniach. Azure Security Center warstwy cenowej standardowa obejmuje skanowanie maszyn wirtualnych w celu wykorzystania luk w zabezpieczeniach bez dodatkowych kosztów. Ponadto Security Center automatycznie wdrożyć to narzędzie. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="patches-are-tested-and-evaluated-before-they-are-installed"></a>Poprawki są testowane i oceniane przed ich zainstalowaniem.

**Identyfikator:** 0713.10m2Organizational.5 — 10.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Serwery, które nie spełniają skonfigurowanego planu bazowego, będą monitorowane przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-technical-vulnerability-management-program-is-evaluated-on-a-quarterly-basis"></a>Program do zarządzania lukami w zabezpieczeniach technicznych jest oceniany co kwartał.

**Identyfikator:** 0714.10m2Organizational.7 — 10.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Przeszukaj luki w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="systems-are-appropriately-hardened-eg-configured-with-only-necessary-and-secure-services-ports-and-protocols-enabled"></a>Systemy są odpowiednio wzmocnione (np. skonfigurowane tylko z włączonymi niezbędnymi i bezpiecznymi usługami, portami i protokołami).

**Identyfikator:** 0715.10m2Organizational.8 – 10.m **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń kontenera](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Przeszukaj luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach z zainstalowaną i wyświetlaną jako rekomendacje platformy Docker w Azure Security Center. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-conducts-an-enterprise-security-posture-review-as-needed-but-no-less-than-once-within-every-three-hundred-sixty-five-365-days-in-accordance-with-organizational-is-procedures"></a>Organizacja przeprowadza przegląd poziomu zabezpieczeń przedsiębiorstwa zgodnie z potrzebami, ale nie rzadziej niż raz na trzysta sześćdziesiąt pięć (365) dni, zgodnie z procedurami organizacji IS.

**Identyfikator:** 0716.10m3Organizational.1 - 10.m **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy skorygować luki w zabezpieczeniach baz danych SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Monitoruj wyniki skanowania w ramach oceny luk w zabezpieczeniach i zalecenia dotyczące sposobu korygowania luk w zabezpieczeniach bazy danych. |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning-tools-include-the-capability-to-readily-update-the-information-system-vulnerabilities-scanned"></a>Narzędzia do skanowania luk w zabezpieczeniach obejmują możliwość aktualizowania skanowanych luk w zabezpieczeniach systemu informacji.

**Identyfikator:** 0717.10m3Organizational.2 — 10.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Przeszukaj luki w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="the-organization-scans-for-vulnerabilities-in-the-information-system-and-hosted-applications-to-determine-the-state-of-flaw-remediation-monthly-automatically-and-again-manually-or-automatically-when-new-vulnerabilities-potentially-affecting-the-systems-and-networked-environments-are-identified-and-reported"></a>Organizacja skanuje w poszukiwaniu luk w zabezpieczeniach systemu informacyjnego i hostowanych aplikacji w celu określenia stanu korygowania błędów co miesiąc (automatycznie) i ponownie (ręcznie lub automatycznie), gdy nowe luki w zabezpieczeniach mogące mieć wpływ na systemy i środowiska sieciowe są identyfikowane i zgłaszane.

**Identyfikator:** 0718.10m3Organizational.34 - 10.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Serwery, które nie spełniają skonfigurowanego planu bazowego, będą monitorowane przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-organization-updates-the-list-of-information-system-vulnerabilities-scanned-within-every-thirty-30-days-or-when-new-vulnerabilities-are-identified-and-reported"></a>Organizacja aktualizuje listę luk w zabezpieczeniach systemu informacji skanowanych co 30 dni lub w przypadku zidentyfikowania i zgłoszonego nowych luk w zabezpieczeniach.

**Identyfikator:** 0719.10m3Organizational.5 — 10.m **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Ocena luk w zabezpieczeniach powinna być włączona na SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Przejmij SQL Managed Instance, które nie mają włączonych cyklicznych skanowań oceny luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może odnajdywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

## <a name="business-continuity-and-risk-assessment"></a>Ciągłość działalności biznesowej i ocena ryzyka

### <a name="the-organization-identifies-the-critical-business-processes-requiring-business-continuity"></a>Organizacja identyfikuje krytyczne procesy biznesowe wymagające ciągłości działania.

**Identyfikator:** 1634.12b1Organizational.1 — 12.b **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja maszyn wirtualnych bez skonfigurowanego odzyskiwania po awarii](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Przejmij maszyny wirtualne, dla których nie skonfigurowano odzyskiwania po awarii. Aby dowiedzieć się więcej na temat odzyskiwania po awarii, odwiedź stronę [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

### <a name="information-security-aspects-of-business-continuity-are-i-based-on-identifying-events-or-sequence-of-events-that-can-cause-interruptions-to-the-organizations-critical-business-processes-eg-equipment-failure-human-errors-theft-fire-natural-disasters-acts-of-terrorism-ii-followed-by-a-risk-assessment-to-determine-the-probability-and-impact-of-such-interruptions-in-terms-of-time-damage-scale-and-recovery-period-iii-based-on-the-results-of-the-risk-assessment-a-business-continuity-strategy-is-developed-to-identify-the-overall-approach-to-business-continuity-and-iv-once-this-strategy-has-been-created-endorsement-is-provided-by-management-and-a-plan-created-and-endorsed-to-implement-this-strategy"></a>Aspekty bezpieczeństwa informacji związane z ciągłością biznesową są (i) oparte na identyfikowaniu zdarzeń (lub sekwencji zdarzeń), które mogą powodować przerwy w krytycznych procesach biznesowych organizacji (np. awarie sprzętu, błędy ludzkie, kradzież, pożary, klęski żywiołowe). (ii) po której następuje ocena ryzyka w celu określenia prawdopodobieństwa i wpływu takich przerw w zakresie czasu, skali szkód i okresu odzyskiwania; (iii) na podstawie wyników oceny ryzyka opracowywana jest strategia ciągłości działalności biznesowej w celu zidentyfikowania ogólnego podejścia do ciągłości działalności biznesowej; i (iv) po utworzeniu tej strategii, zatwierdzanie jest udostępniane przez kierownictwo oraz plan utworzony i zatwierdzony w celu zaimplementowania tej strategii.

**Identyfikator:** 1635.12b1Organizational.2 — 12.b **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Key Vault HSM powinien mieć włączoną ochronę przed przeczyszczaniem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |Złośliwe usunięcie zarządzanego Azure Key Vault HSM może prowadzić do trwałej utraty danych. Złośliwy niejawny program testów w organizacji może potencjalnie usunąć i przeczyścić Azure Key Vault zarządzany moduł HSM. Ochrona przed przeczyszczaniem chroni przed atakami wewnętrznymi przez wymuszenie obowiązkowego okresu przechowywania dla nieużytego usuniętego Azure Key Vault HSM. Nikt wewnątrz organizacji ani firma Microsoft nie będzie mógł przeczyścić zarządzanego modułu HSM Azure Key Vault w okresie przechowywania usuwania nie soft. |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[Magazyny kluczy powinny mieć włączoną ochronę przed przeczyszczaniem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Złośliwe usunięcie magazynu kluczy może prowadzić do trwałej utraty danych. Złośliwy niejawny program testów w organizacji może potencjalnie usuwać i przeczyścić magazyny kluczy. Ochrona przed przeczyszczaniem chroni przed atakami wewnętrznymi, wymuszając obowiązkowy okres przechowywania dla magazynów kluczy bez usunięcia. Nikt wewnątrz organizacji ani firma Microsoft nie będzie mógł przeczyścić Twoich magazynów kluczy w okresie przechowywania usuwania nie soft. |Inspekcja, Odmowa, Wyłączone |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="business-impact-analysis-are-used-to-evaluate-the-consequences-of-disasters-security-failures-loss-of-service-and-service-availability"></a>Analiza wpływu na działalność biznesową służy do oceny konsekwencji awarii, awarii zabezpieczeń, utraty usługi i dostępności usługi.

**Identyfikator:** 1637.12b2Organizational.2 — 12.b **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń — konsola odzyskiwania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff71be03e-e25b-4d0f-b8bc-9b3e309b66c0) |Komputery z systemem Windows powinny mieć określone zasady grupy w kategorii "Opcje zabezpieczeń — konsola odzyskiwania", aby zezwalać na kopiowanie dyskietek i dostęp do wszystkich dysków i folderów. Te zasady wymagają wdrożenia wymagań wstępnych konfiguracji gościa w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsRecoveryconsole_AINE.json) |

### <a name="business-continuity-risk-assessments-i-are-carried-out-annually-with-full-involvement-from-owners-of-business-resources-and-processes-ii-consider-all-business-processes-and-is-not-limited-to-the-information-assets-but-includes-the-results-specific-to-information-security-and-iii-identifies-quantifies-and-prioritizes-risks-against-key-business-objectives-and-criteria-relevant-to-the-organization-including-critical-resources-impacts-of-disruptions-allowable-outage-times-and-recovery-priorities"></a>Oceny ryzyka związanego z ciągłością biznesową (i) są przeprowadzane co roku przy pełnym zaangażowaniu właścicieli zasobów i procesów biznesowych; (ii) uwzględnia wszystkie procesy biznesowe i nie jest ograniczony do zasobów informacyjnych, ale zawiera wyniki specyficzne dla bezpieczeństwa informacji; oraz (iii) identyfikują, kwantyfikują i priorytetyzują zagrożenia w stosunku do kluczowych celów biznesowych i kryteriów odpowiednich dla organizacji, w tym krytycznych zasobów, wpływu zakłóceń, dopuszczalnego czasu przestoju i priorytetów odzyskiwania.

**Identyfikator:** 1638.12b2Organizational.345 - 12.b **Własność**: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja maszyn wirtualnych bez skonfigurowanego odzyskiwania po awarii](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Przejmij maszyny wirtualne, dla których nie skonfigurowano odzyskiwania po awarii. Aby dowiedzieć się więcej na temat odzyskiwania po awarii, odwiedź stronę [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> Dostępność określonych definicji Azure Policy może się różnić w Azure Government i innych chmurach krajowych.

## <a name="next-steps"></a>Następne kroki

Dodatkowe artykuły dotyczące Azure Policy:

- [Omówienie zgodności z przepisami.](../concepts/regulatory-compliance.md)
- Zobacz strukturę [definicji inicjatywy](../concepts/initiative-definition-structure.md).
- Przejrzyj inne przykłady na [Azure Policy przykładów.](./index.md)
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [korygować niezgodne zasoby.](../how-to/remediate-resources.md)
