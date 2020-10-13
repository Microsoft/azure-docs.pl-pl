---
title: Szczegóły zgodności z przepisami dla HIPAA HITRUST 9,2
description: Szczegółowe informacje na temat wbudowanej inicjatywy zgodności z przepisami HIPAA HITRUST 9,2. Każda kontrolka jest zamapowana na co najmniej jedną definicję Azure Policy, która pomaga w ocenie.
ms.date: 10/07/2020
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: ed486a520033b9022abdf1f795e125d1740e3de9
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929266"
---
# <a name="details-of-the-hipaa-hitrust-92-regulatory-compliance-built-in-initiative"></a>Szczegóły dotyczące wbudowanej inicjatywy zgodności z przepisami HIPAA HITRUST 9,2

W poniższym artykule szczegółowo opisano sposób, w jaki Azure Policy wbudowana definicja zgodności z przepisami jest mapowana na domeny i **kontrolki** **zgodności** w HIPAA HiTRUST 9,2.
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html). Aby zrozumieć _własność_, zobacz [Azure Policy definicję zasad](../concepts/definition-structure.md#type) i [współdzieloną odpowiedzialność w chmurze](../../../security/fundamentals/shared-responsibility.md).

Następujące mapowania znajdują się w kontrolkach **HIPAA HITRUST 9,2** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do konkretnej **domeny zgodności**. Wiele kontrolek jest implementowanych z definicją inicjatywy [Azure Policy](../overview.md) . Aby zapoznać się z pełną definicją inicjatywy, Otwórz pozycję **zasady** w Azure Portal a następnie wybierz stronę **definicje** .
Następnie Znajdź i wybierz definicję inicjatywy **HiTRUST/HIPAA** zgodność z przepisami.

Ta inicjatywa wbudowana jest wdrażana w ramach [przykładu planu HIPAA HITRUST 9,2](../../blueprints/samples/hipaa-hitrust-9-2.md).

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../overview.md) .
> Te zasady mogą pomóc w [ocenie zgodności](../how-to/get-compliance-data.md) z kontrolką; jednak często nie jest to jeden lub kompletny odpowiednik między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy dotyczy tylko definicji zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między domenami zgodności, kontrolkami i definicjami Azure Policy dla tego standardu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/HIPAA_HITRUST_audit.json).

## <a name="privilege-management"></a>Zarządzanie uprawnieniami

### <a name="access-to-management-functions-or-administrative-consoles-for-systems-hosting-virtualized-systems-are-restricted-to-personnel-based-upon-the-principle-of-least-privilege-and-supported-through-technical-controls"></a>Dostęp do funkcji zarządzania lub konsol administracyjnych dla systemów obsługujących systemy wirtualne są ograniczone do pracowników opartych na zasadzie najniższych uprawnień i obsługiwanych przez kontrolę techniczną.

**ID**: 11180.01 c3system. 6-01. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp do sieci just in Time (JIT) będzie monitorowany przez Azure Security Center jak zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="privileges-are-formally-authorized-and-controlled-allocated-to-users-on-a-need-to-use-and-event-by-event-basis-for-their-functional-role-eg-user-or-administrator-and-documented-for-each-system-productelement"></a>Uprawnienia są formalnie autoryzowane i kontrolowane, przydzielone do użytkowników w zależności od potrzeb i zdarzeń związanych z ich rolą funkcjonalną (np. użytkownik lub administrator) i udokumentowane dla każdego produktu/elementu systemowego.

**ID**: 1143.01 c1system. 123-01. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Porty zarządzania powinny być zamknięte na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Otwieranie portów zarządzania zdalnego umożliwia wystawianie maszyn wirtualnych na wysokim poziomie ryzyka związanego z atakami internetowymi. Ataki te podejmują próbę nawiązania poświadczeń w celu uzyskania dostępu administratora do komputera. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-explicitly-authorizes-access-to-specific-security-relevant-functions-deployed-in-hardware-software-and-firmware-and-security-relevant-information"></a>Organizacja jawnie autoryzuje dostęp do określonych funkcji związanych z zabezpieczeniami (wdrożonych w sprzęcie, oprogramowaniu i oprogramowaniu układowym) i informacji dotyczących zabezpieczeń.

**ID**: 1144.01 c1system. 4-01. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Zaleca się wyznaczenie do 3 właścicieli subskrypcji, aby zmniejszyć prawdopodobieństwo naruszenia przez zagrożonego właściciela. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="role-based-access-control-is-implemented-and-capable-of-mapping-each-user-to-one-or-more-roles-and-each-role-to-one-or-more-system-functions"></a>Kontrola dostępu oparta na rolach jest implementowana i umożliwia mapowanie poszczególnych użytkowników na jedną lub więcej ról, a każda rola do co najmniej jednej funkcji systemowej.

**ID**: 1145.01 c2system. 1-01. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Do subskrypcji powinien być przypisany więcej niż jeden właściciel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Zaleca się wyznaczenie więcej niż jednego właściciela subskrypcji w celu zapewnienia nadmiarowości dostępu administratora. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="the-organization-promotes-the-development-and-use-of-programs-that-avoid-the-need-to-run-with-elevated-privileges-and-system-routines-to-avoid-the-need-to-grant-privileges-to-users"></a>Organizacja wspiera opracowywanie i używanie programów, które nie wymagają uruchamiania z podwyższonym poziomem uprawnień i procedur systemowych, aby uniknąć konieczności przyznawania uprawnień użytkownikom.

**ID**: 1146.01 c2system. 23-01. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Konta zewnętrzne z uprawnieniami właściciela należy usunąć z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |

### <a name="elevated-privileges-are-assigned-to-a-different-user-id-from-those-used-for-normal-business-use-all-users-access-privileged-services-in-a-single-role-and-such-privileged-access-is-minimized"></a>Podwyższone uprawnienia są przypisywane do innego identyfikatora użytkownika niż te używane do normalnego korzystania z firmy, wszyscy użytkownicy uzyskują dostęp do usług uprzywilejowanych w ramach jednej roli, a taki dostęp uprzywilejowany jest zminimalizowany.

**ID**: 1147.01 c2system. 456-01. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji.  Przestarzałe konta to konta, które zostały zablokowane do logowania. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="the-organization-restricts-access-to-privileged-functions-and-all-security-relevant-information"></a>Organizacja ogranicza dostęp do funkcji uprzywilejowanych i wszystkich informacji związanych z bezpieczeństwem.

**ID**: 1148.01 c2system. 78-01. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja użycia niestandardowych reguł RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Inspekcja wbudowanych ról, takich jak "Owner, współautorzy, czytelnik" zamiast niestandardowych ról RBAC, które są podatne na błędy. Używanie ról niestandardowych jest traktowane jako wyjątek i wymaga rygorystycznego przeglądu i modelowania zagrożeń |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące "opcji zabezpieczeń-Accounts"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee984370-154a-4ee8-9726-19d900e56fc0) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "Opcje zabezpieczeń-Accounts", aby ograniczyć użycie konta lokalnego przez puste hasła i stan konta gościa. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAccounts_AINE.json) |

### <a name="the-organization-facilitates-information-sharing-by-enabling-authorized-users-to-determine-a-business-partners-access-when-discretion-is-allowed-as-defined-by-the-organization-and-by-employing-manual-processes-or-automated-mechanisms-to-assist-users-in-making-information-sharingcollaboration-decisions"></a>Organizacja ułatwia udostępnianie informacji przez umożliwienie autoryzowanym użytkownikom określenia dostępu partnera biznesowego, gdy wartość uznania jest dozwolona zgodnie z definicją w organizacji i przez zastosowanie ręcznych procesów lub zautomatyzowanych mechanizmów, aby pomóc użytkownikom w podejmowaniu decyzji dotyczących udostępniania i współpracy.

**ID**: 1149.01 c2system. 9-01. c **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Access Control oparte na rolach (RBAC) powinny być używane w usługach Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Aby zapewnić szczegółowe filtrowanie działań, które użytkownicy mogą wykonywać, użyj Role-Based Access Control (RBAC) do zarządzania uprawnieniami w klastrach usługi Kubernetes i konfigurowania odpowiednich zasad autoryzacji. |Inspekcja, wyłączona |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="the-access-control-system-for-the-system-components-storing-processing-or-transmitting-covered-information-is-set-with-a-default-quotdeny-allquot-setting"></a>System kontroli dostępu dla składników systemowych przechowujących, przetwarzających lub przesyłających informacje objęte usługą jest ustawiany przy użyciu domyślnego &quot; Ustawienia Odmów — wszystkie &quot; .

**ID**: 1150.01 c2system. 10-01. c, **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Porty zarządzania powinny być zamknięte na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Otwieranie portów zarządzania zdalnego umożliwia wystawianie maszyn wirtualnych na wysokim poziomie ryzyka związanego z atakami internetowymi. Ataki te podejmują próbę nawiązania poświadczeń w celu uzyskania dostępu administratora do komputera. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-limits-authorization-to-privileged-accounts-on-information-systems-to-a-pre-defined-subset-of-users"></a>Organizacja ogranicza autoryzację do uprzywilejowanych kont w systemach informacyjnych do wstępnie zdefiniowanego podzbioru użytkowników.

**ID**: 1151.01 c3system. 1-01. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Zaleca się wyznaczenie do 3 właścicieli subskrypcji, aby zmniejszyć prawdopodobieństwo naruszenia przez zagrożonego właściciela. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-audits-the-execution-of-privileged-functions-on-information-systems-and-ensures-information-systems-prevent-non-privileged-users-from-executing-privileged-functions"></a>Organizacja przeprowadza inspekcję wykonywania uprzywilejowanych funkcji w systemach informacyjnych i zapewnia, że systemy informacyjne uniemożliwiają wykonywanie funkcji uprzywilejowanych przez użytkowników bez uprawnień.

**ID**: 1152.01 c3system. 2-01. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Do subskrypcji powinien być przypisany więcej niż jeden właściciel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Zaleca się wyznaczenie więcej niż jednego właściciela subskrypcji w celu zapewnienia nadmiarowości dostępu administratora. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="all-file-system-access-not-explicitly-required-is-disabled-and-only-authorized-users-are-permitted-access-to-only-that-which-is-expressly-required-for-the-performance-of-the-users-job-duties"></a>Cały dostęp do systemu plików, który nie jest jawnie wymagany, jest wyłączony i dostęp do nich jest możliwy tylko dla autoryzowanych użytkowników.

**ID**: 1153.01 c3system. 35-01. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Access Control oparte na rolach (RBAC) powinny być używane w usługach Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Aby zapewnić szczegółowe filtrowanie działań, które użytkownicy mogą wykonywać, użyj Role-Based Access Control (RBAC) do zarządzania uprawnieniami w klastrach usługi Kubernetes i konfigurowania odpowiednich zasad autoryzacji. |Inspekcja, wyłączona |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="contractors-are-provided-with-minimal-system-and-physical-access-only-after-the-organization-assesses-the-contractors-ability-to-comply-with-its-security-requirements-and-the-contractor-agrees-to-comply"></a>Przedsiębiorcy są dostarczani z minimalnym poziomem dostępu systemowego i fizycznego, gdy organizacja ocenia zdolność wykonawcy do zapewnienia zgodności z wymaganiami dotyczącymi bezpieczeństwa, a wykonawca zgadza się przestrzegać zgodności.

**ID**: 1154.01 c3system. 4-01. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Zaleca się wyznaczenie do 3 właścicieli subskrypcji, aby zmniejszyć prawdopodobieństwo naruszenia przez zagrożonego właściciela. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

## <a name="user-authentication-for-external-connections"></a>Uwierzytelnianie użytkowników dla połączeń zewnętrznych

### <a name="strong-authentication-methods-such-as-multi-factor-radius-or-kerberos-for-privileged-access-and-chap-for-encryption-of-credentials-for-dialup-methods-are-implemented-for-all-external-connections-to-the-organizations-network"></a>Metody silnego uwierzytelniania, takie jak uwierzytelnianie wieloskładnikowe, usługa RADIUS lub Kerberos (dla dostępu uprzywilejowanego) i CHAP (w przypadku szyfrowania poświadczeń dla metod telefonicznych) są implementowane dla wszystkich połączeń zewnętrznych z siecią organizacji.

**ID**: 1116.01 j1organizational. 145-01. j **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami właściciela, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="remote-access-by-vendors-and-business-partners-eg-for-remote-maintenance-is-disableddeactivated-when-not-in-use"></a>Dostęp zdalny według dostawców i partnerów handlowych (np. do konserwacji zdalnej) jest wyłączony/dezaktywowany, gdy nie jest używany.

**ID**: 1117.01 j1organizational. 23-01. j **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="organizations-implement-encryption-eg-nbspvpn-solutions-or-private-lines-and-logs-remote-access-to-the-organizations-network-by-employees-contractors-or-third-party-eg-vendors"></a>Organizacje implementują szyfrowanie (np. &nbsp; rozwiązania sieci VPN lub linie prywatne) i dzienniki dostępu zdalnego do sieci organizacji przez pracowników, wykonawców lub innych firm (np. dostawców).

**ID**: 1118.01 j2organizational. 124-01. j **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do odczytu, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="network-equipment-is-checked-for-unanticipated-dial-up-capabilities"></a>Urządzenie sieciowe jest sprawdzane pod kątem nieoczekiwanych możliwości połączeń telefonicznych.

**ID**: 1119.01 j2organizational. 3-01. j **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp do sieci just in Time (JIT) będzie monitorowany przez Azure Security Center jak zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="remote-administration-sessions-are-authorized-encrypted-and-employ-increased-security-measures"></a>Sesje administracji zdalnej są autoryzowane, szyfrowane i wykorzystują zwiększone środki bezpieczeństwa.

**ID**: 1121.01 j3organizational. 2-01. j **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami właściciela, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="if-encryption-is-not-used-for-dial-up-connections-the-cio-or-hisher-designated-representative-provides-specific-written-authorization"></a>Jeśli szyfrowanie nie jest używane na potrzeby połączeń telefonicznych, CIO lub jego wystawiony przedstawiciel zapewnia konkretną autoryzację.

**ID**: 1173.01 j1organizational. 6-01. j **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="the-organization-protects-wireless-access-to-systems-containing-sensitive-information-by-authenticating-both-users-and-devices"></a>Organizacja chroni dostęp bezprzewodowy do systemów zawierających poufne informacje przez uwierzytelnianie zarówno użytkowników, jak i urządzeń.

**ID**: 1174.01 j1organizational. 7-01. j **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do odczytu, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="remote-access-to-business-information-across-public-networks-only-takes-place-after-successful-identification-and-authentication"></a>Dostęp zdalny do informacji firmowych w sieciach publicznych odbywa się tylko po pomyślnym zidentyfikowaniu i uwierzytelnieniu.

**ID**: 1175.01 j1organizational. 8-01. j **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp do sieci just in Time (JIT) będzie monitorowany przez Azure Security Center jak zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="the-organization-requires-a-callback-capability-with-re-authentication-to-verify-dial-up-connections-from-authorized-locations"></a>Organizacja wymaga możliwości wywołania zwrotnego z ponownym uwierzytelnianiem w celu sprawdzenia połączeń telefonicznych z autoryzowanych lokalizacji.

**ID**: 1176.01 j2organizational. 5-01. j **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami właściciela, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="user-ids-assigned-to-vendors-are-reviewed-in-accordance-with-the-organizations-access-review-policy-at-a-minimum-annually"></a>Identyfikatory użytkowników przypisane do dostawców są analizowane zgodnie z zasadami kontroli dostępu w organizacji, co najmniej raz na rok.

**ID**: 1177.01 j2organizational. 6-01. j **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="node-authentication-including-cryptographic-techniques-eg-machine-certificates-serves-as-an-alternative-means-of-authenticating-groups-of-remote-users-where-they-are-connected-to-a-secure-shared-computer-facility"></a>Uwierzytelnianie węzła, łącznie z technikami kryptograficznymi (np. certyfikatami maszynowymi), służy jako alternatywny sposób uwierzytelniania grup użytkowników zdalnych, gdy są one połączone z bezpiecznym, udostępnionym obiektem komputera.

**ID**: 1178.01 j2organizational. 7-01. j **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do odczytu, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-monitors-and-controls-remote-access-methods"></a>System informacji monitoruje i kontroluje metody dostępu zdalnego.

**ID**: 1179.01 j3organizational. 1-01. j **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp do sieci just in Time (JIT) będzie monitorowany przez Azure Security Center jak zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Zdalna Ochrona danych diagnostycznych i konfiguracji

### <a name="access-to-network-equipment-is-physically-protected"></a>Dostęp do sprzętu sieciowego jest fizycznie chroniony.

**ID**: 1192.01 l1organizational. 1-01. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp do sieci just in Time (JIT) będzie monitorowany przez Azure Security Center jak zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="controls-for-the-access-to-diagnostic-and-configuration-ports-include-the-use-of-a-key-lock-and-the-implementation-of-supporting-procedures-to-control-physical-access-to-the-port"></a>Kontrolki dostępu do portów diagnostycznych i konfiguracyjnych obejmują użycie blokady klucza i implementacji procedur pomocniczych w celu kontrolowania fizycznego dostępu do portu.

**ID**: 1193.01 l2organizational. 13-01. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Porty zarządzania powinny być zamknięte na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Otwieranie portów zarządzania zdalnego umożliwia wystawianie maszyn wirtualnych na wysokim poziomie ryzyka związanego z atakami internetowymi. Ataki te podejmują próbę nawiązania poświadczeń w celu uzyskania dostępu administratora do komputera. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="ports-services-and-similar-applications-installed-on-a-computer-or-network-systems-which-are-not-specifically-required-for-business-functionality-are-disabled-or-removed"></a>Porty, usługi i podobne aplikacje zainstalowane na komputerze lub w systemach sieciowych, które nie są wymagane w celu zapewnienia funkcjonalności biznesowej, są wyłączone lub usuwane.

**ID**: 1194.01 l2organizational. 2-01. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Debugowanie zdalne powinno być wyłączone dla aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Zdalne debugowanie wymaga otwarcia portów przychodzących w aplikacji sieci Web. Debugowanie zdalne powinno zostać wyłączone. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="the-organization-reviews-the-information-system-within-every-three-hundred-and-sixty--five-365-days-to-identify-and-disables-unnecessary-and-non-secure-functions-ports-protocols-andor-services"></a>Organizacja przegląda system informacji w ciągu co 365 (365) dni, aby identyfikować i wyłączać niepotrzebne i niezabezpieczone funkcje, porty, protokoły i/lub usługi.

**ID**: 1195.01 l3organizational. 1-01. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Zdalne debugowanie wymaga otwarcia portów przychodzących w aplikacjach funkcji. Debugowanie zdalne powinno zostać wyłączone. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-identifies-unauthorized-blacklisted-software-on-the-information-system-prevents-program-execution-in-accordance-with-a-list-of-unauthorized-blacklisted-software-programs-employs-an-allow-all-deny-by-exception-policy-to-prohibit-execution-of-known-unauthorized-blacklisted-software-and-reviews-and-updates-the-list-of-unauthorized-blacklisted-software-programs-annually"></a>Organizacja identyfikuje nieautoryzowane (zabronione) oprogramowanie w systemie informacyjnym, zapobiega wykonywaniu przez program zgodnie z listą nieautoryzowanych (zabronionych) programów, stosuje zasady "Zezwalaj na wszystkie, Odmów przez wyjątek", aby uniemożliwić wykonanie znanego nieautoryzowanego (zabronionego) oprogramowania, a następnie sprawdza i aktualizuje listę nieautoryzowanych (zabronionych) programów oprogramowania rocznie.

**ID**: 1196.01 l3organizational. 24-01. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zdalne debugowanie powinno zostać wyłączone dla API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Zdalne debugowanie wymaga otwarcia portów przychodzących w aplikacjach interfejsu API. Debugowanie zdalne powinno zostać wyłączone. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-disables-bluetooth-and-peer-to-peer-networking-protocols-within-the-information-system-determined-to-be-unnecessary-or-non-secure"></a>Organizacja wyłącza protokoły sieciowe Bluetooth i peer-to-peer w systemie informacyjnym, które wynikają z niepotrzebnych lub niezabezpieczonych.

**ID**: 1197.01 l3organizational. 3-01. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Na maszynach powinny być włączone adaptacyjne kontrole aplikacji umożliwiające Definiowanie bezpiecznych aplikacji.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Włącz kontrolki aplikacji, aby zdefiniować listę znanych bezpiecznych aplikacji uruchomionych na maszynach, i Zgłoś alert, gdy inne aplikacje zostaną uruchomione. Pozwala to na zabezpieczanie maszyn przed złośliwym oprogramowaniem. Aby uprościć proces konfigurowania i utrzymywania reguł, Security Center korzysta z uczenia maszynowego do analizowania aplikacji uruchomionych na poszczególnych maszynach i sugerują listę znanych bezpiecznych aplikacji. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="segregation-in-networks"></a>Dzielenie w sieci

### <a name="the-organizations-security-gateways-eg-firewalls-enforce-security-policies-and-are-configured-to-filter-traffic-between-domains-block-unauthorized-access-and-are-used-to-maintain-segregation-between-internal-wired-internal-wireless-and-external-network-segments-eg-the-internet-including-dmzs-and-enforce-access-control-policies-for-each-of-the-domains"></a>Bramy zabezpieczeń organizacji (np. zapory) wymuszają zasady zabezpieczeń i są skonfigurowane do filtrowania ruchu między domenami, blokują nieautoryzowany dostęp i służą do obsługi rozdzielania między wewnętrzną przewodową, wewnętrzną siecią bezprzewodową i zewnętrznymi segmentami sieci (np. Internetem), w tym stref DMZ i wymuszanie zasad kontroli dostępu dla każdej z domen.

**ID**: 0805.01 m1organizational. 12-01. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Ta zasada przeprowadza inspekcję wszelkich App Service nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ta zasada przeprowadza inspekcję wszelkich Container Registry nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ta zasada przeprowadza inspekcję wszelkich Cosmos DB nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Centrum zdarzeń powinno używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Ta zasada przeprowadza inspekcję wszystkich centrów zdarzeń, które nie są skonfigurowane do używania punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Podsieci bramy nie należy konfigurować przy użyciu sieciowej grupy zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Ta zasada powoduje odmowę, jeśli podsieć bramy jest skonfigurowana z sieciową grupą zabezpieczeń. Przypisanie sieciowej grupy zabezpieczeń do podsieci bramy spowoduje, że brama przestanie działać. |pozbawić |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ochrona maszyn wirtualnych przed potencjalnymi zagrożeniami przez ograniczenie dostępu do nich za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). Dowiedz się więcej o kontrolowaniu ruchu z sieciowych grup zabezpieczeń na [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Ta zasada przeprowadza inspekcję wszelkich Key Vault nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Ta zasada przeprowadza inspekcję wszelkich Service Bus nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Ta zasada przeprowadza inspekcję wszelkich SQL Server nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Konta magazynu powinny używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Ta zasada przeprowadza inspekcję wszystkich kont magazynu nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ochrona podsieci przed potencjalnymi zagrożeniami przez ograniczenie dostępu do niej przy użyciu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). Sieciowych grup zabezpieczeń zawierają listę reguł listy kontroli dostępu (ACL) Access Control, które zezwalają na ruch sieciowy lub odmawiają go w podsieci. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych połączonych z siecią wirtualną, która nie jest zatwierdzona. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="the-organizations-network-is-logically-and-physically-segmented-with-a-defined-security-perimeter-and-a-graduated-set-of-controls-including-subnetworks-for-publicly-accessible-system-components-that-are-logically-separated-from-the-internal-network-based-on-organizational-requirements-and-traffic-is-controlled-based-on-functionality-required-and-classification-of-the-datasystems-based-on-a-risk-assessment-and-their-respective-security-requirements"></a>Sieć organizacji jest logicznie i fizycznie dzielona ze zdefiniowanym obwodem zabezpieczeń oraz stopniowanym zestawem kontrolek, w tym podsieciami dla publicznie dostępnych składników systemu, które są logicznie oddzielone od sieci wewnętrznej, w oparciu o wymagania organizacyjne. ruch jest kontrolowany w oparciu o funkcje wymagane i Klasyfikacja danych/systemów w oparciu o ocenę ryzyka i odpowiednie wymagania dotyczące zabezpieczeń.

**ID**: 0806.01 m2organizational. 12356-01. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Ta zasada przeprowadza inspekcję wszelkich App Service nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ta zasada przeprowadza inspekcję wszelkich Container Registry nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ta zasada przeprowadza inspekcję wszelkich Cosmos DB nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Centrum zdarzeń powinno używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Ta zasada przeprowadza inspekcję wszystkich centrów zdarzeń, które nie są skonfigurowane do używania punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Podsieci bramy nie należy konfigurować przy użyciu sieciowej grupy zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Ta zasada powoduje odmowę, jeśli podsieć bramy jest skonfigurowana z sieciową grupą zabezpieczeń. Przypisanie sieciowej grupy zabezpieczeń do podsieci bramy spowoduje, że brama przestanie działać. |pozbawić |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ochrona maszyn wirtualnych przed potencjalnymi zagrożeniami przez ograniczenie dostępu do nich za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). Dowiedz się więcej o kontrolowaniu ruchu z sieciowych grup zabezpieczeń na [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Ta zasada przeprowadza inspekcję wszelkich Key Vault nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Ta zasada przeprowadza inspekcję wszelkich Service Bus nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Ta zasada przeprowadza inspekcję wszelkich SQL Server nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Konta magazynu powinny używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Ta zasada przeprowadza inspekcję wszystkich kont magazynu nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ochrona podsieci przed potencjalnymi zagrożeniami przez ograniczenie dostępu do niej przy użyciu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). Sieciowych grup zabezpieczeń zawierają listę reguł listy kontroli dostępu (ACL) Access Control, które zezwalają na ruch sieciowy lub odmawiają go w podsieci. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych połączonych z siecią wirtualną, która nie jest zatwierdzona. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="networks-are-segregated-from-production-level-networks-when-migrating-physical-servers-applications-or-data-to-virtualized-servers"></a>Sieci są segregowane z sieci na poziomie produkcyjnym podczas migrowania serwerów fizycznych, aplikacji lub danych do serwerów zwirtualizowanych.

**ID**: 0894.01 m2organizational. 7-01. m **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Ta zasada przeprowadza inspekcję wszelkich App Service nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ta zasada przeprowadza inspekcję wszelkich Container Registry nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ta zasada przeprowadza inspekcję wszelkich Cosmos DB nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Wdróż obserwatora sieciowego po utworzeniu sieci wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9b99dd8-06c5-4317-8629-9d86a3c6e7d9) |Te zasady tworzą zasób obserwatora sieci w regionach z sieciami wirtualnymi. Należy upewnić się, że istnieje grupa zasobów o nazwie networkWatcherRG, która zostanie użyta do wdrożenia wystąpień obserwatora sieci. |DeployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |
|[Centrum zdarzeń powinno używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Ta zasada przeprowadza inspekcję wszystkich centrów zdarzeń, które nie są skonfigurowane do używania punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[Podsieci bramy nie należy konfigurować przy użyciu sieciowej grupy zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Ta zasada powoduje odmowę, jeśli podsieć bramy jest skonfigurowana z sieciową grupą zabezpieczeń. Przypisanie sieciowej grupy zabezpieczeń do podsieci bramy spowoduje, że brama przestanie działać. |pozbawić |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ochrona maszyn wirtualnych przed potencjalnymi zagrożeniami przez ograniczenie dostępu do nich za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). Dowiedz się więcej o kontrolowaniu ruchu z sieciowych grup zabezpieczeń na [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Ta zasada przeprowadza inspekcję wszelkich Key Vault nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Ta zasada przeprowadza inspekcję wszelkich Service Bus nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Ta zasada przeprowadza inspekcję wszelkich SQL Server nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[Konta magazynu powinny używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Ta zasada przeprowadza inspekcję wszystkich kont magazynu nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ochrona podsieci przed potencjalnymi zagrożeniami przez ograniczenie dostępu do niej przy użyciu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). Sieciowych grup zabezpieczeń zawierają listę reguł listy kontroli dostępu (ACL) Access Control, które zezwalają na ruch sieciowy lub odmawiają go w podsieci. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych połączonych z siecią wirtualną, która nie jest zatwierdzona. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

## <a name="network-connection-control"></a>Sterowanie połączeniem sieciowym

### <a name="network-traffic-is-controlled-in-accordance-with-the-organizations-access-control-policy-through-firewall-and-other-network-related-restrictions-for-each-network-access-point-or-external-telecommunication-services-managed-interface"></a>Ruch sieciowy jest kontrolowany zgodnie z zasadami kontroli dostępu organizacji przez zaporę i inne ograniczenia związane z siecią dla każdego punktu dostępu do sieci lub z zarządzanym zewnętrznym interfejsem usługi telekomunikacyjnej.

**ID**: 0809.01 n2organizational. 1234-01. n **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych mających dostęp do Internetu i zawiera zalecenia dotyczące reguły sieciowej grupy zabezpieczeń, które zmniejszają potencjalną podatność na ataki |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ochrona maszyn wirtualnych przed potencjalnymi zagrożeniami przez ograniczenie dostępu do nich za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). Dowiedz się więcej o kontrolowaniu ruchu z sieciowych grup zabezpieczeń na [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączenia tylko połączeń za pośrednictwem protokołu SSL do usługi Azure cache for Redis. Użycie bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane z ataków warstwy sieciowej, takich jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Inspekcja wymagania bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ochrona podsieci przed potencjalnymi zagrożeniami przez ograniczenie dostępu do niej przy użyciu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). Sieciowych grup zabezpieczeń zawierają listę reguł listy kontroli dostępu (ACL) Access Control, które zezwalają na ruch sieciowy lub odmawiają go w podsieci. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych połączonych z siecią wirtualną, która nie jest zatwierdzona. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="transmitted-information-is-secured-and-at-a-minimum-encrypted-over-open-public-networks"></a>Przesyłane informacje są zabezpieczone i co najmniej zaszyfrowane przez otwarte sieci publiczne.

**ID**: 0810.01 n2organizational. 5-01. n **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych mających dostęp do Internetu i zawiera zalecenia dotyczące reguły sieciowej grupy zabezpieczeń, które zmniejszają potencjalną podatność na ataki |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ochrona maszyn wirtualnych przed potencjalnymi zagrożeniami przez ograniczenie dostępu do nich za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). Dowiedz się więcej o kontrolowaniu ruchu z sieciowych grup zabezpieczeń na [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączenia tylko połączeń za pośrednictwem protokołu SSL do usługi Azure cache for Redis. Użycie bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane z ataków warstwy sieciowej, takich jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Inspekcja wymagania bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ochrona podsieci przed potencjalnymi zagrożeniami przez ograniczenie dostępu do niej przy użyciu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). Sieciowych grup zabezpieczeń zawierają listę reguł listy kontroli dostępu (ACL) Access Control, które zezwalają na ruch sieciowy lub odmawiają go w podsieci. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych połączonych z siecią wirtualną, która nie jest zatwierdzona. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="exceptions-to-the-traffic-flow-policy-are-documented-with-a-supporting-missionbusiness-need-duration-of-the-exception-and-reviewed-at-least-annually-traffic-flow-policy-exceptions-are-removed-when-no-longer-supported-by-an-explicit-missionbusiness-need"></a>Wyjątki od zasad przepływu ruchu są udokumentowane z obsługą potrzebnych zadań, czasu trwania wyjątku i przeglądem co najmniej raz na rok; wyjątki zasad przepływu ruchu są usuwane, gdy nie są już obsługiwane przez jawną potrzebę firmy.

**ID**: 0811.01 n2organizational. 6-01. n **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych mających dostęp do Internetu i zawiera zalecenia dotyczące reguły sieciowej grupy zabezpieczeń, które zmniejszają potencjalną podatność na ataki |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ochrona maszyn wirtualnych przed potencjalnymi zagrożeniami przez ograniczenie dostępu do nich za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). Dowiedz się więcej o kontrolowaniu ruchu z sieciowych grup zabezpieczeń na [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączenia tylko połączeń za pośrednictwem protokołu SSL do usługi Azure cache for Redis. Użycie bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane z ataków warstwy sieciowej, takich jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Inspekcja wymagania bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ochrona podsieci przed potencjalnymi zagrożeniami przez ograniczenie dostępu do niej przy użyciu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). Sieciowych grup zabezpieczeń zawierają listę reguł listy kontroli dostępu (ACL) Access Control, które zezwalają na ruch sieciowy lub odmawiają go w podsieci. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych połączonych z siecią wirtualną, która nie jest zatwierdzona. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="remote-devices-establishing-a-non-remote-connection-are-not-allowed-to-communicate-with-external-remote-resources"></a>Urządzenia zdalne ustanawiające niezdalne połączenie nie mogą komunikować się z zewnętrznymi (zdalnymi) zasobami.

**ID**: 0812.01 n2organizational. 8-01. n **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych mających dostęp do Internetu i zawiera zalecenia dotyczące reguły sieciowej grupy zabezpieczeń, które zmniejszają potencjalną podatność na ataki |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ochrona maszyn wirtualnych przed potencjalnymi zagrożeniami przez ograniczenie dostępu do nich za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). Dowiedz się więcej o kontrolowaniu ruchu z sieciowych grup zabezpieczeń na [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączenia tylko połączeń za pośrednictwem protokołu SSL do usługi Azure cache for Redis. Użycie bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane z ataków warstwy sieciowej, takich jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Inspekcja wymagania bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ochrona podsieci przed potencjalnymi zagrożeniami przez ograniczenie dostępu do niej przy użyciu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). Sieciowych grup zabezpieczeń zawierają listę reguł listy kontroli dostępu (ACL) Access Control, które zezwalają na ruch sieciowy lub odmawiają go w podsieci. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych połączonych z siecią wirtualną, która nie jest zatwierdzona. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="the-ability-of-users-to-connect-to-the-internal-network-is-restricted-using-a-deny-by-default-and-allow-by-exception-policy-at-managed-interfaces-according-to-the-access-control-policy-and-the-requirements-of-clinical-and-business-applications"></a>Możliwość nawiązywania połączeń z siecią wewnętrzną jest ograniczona przy użyciu zasad Odmów przez domyślne i zezwala na wyjątki w interfejsach zarządzanych zgodnie z zasadami kontroli dostępu oraz wymaganiami dotyczącymi aplikacji klinicznych i firmowych.

**Identyfikator**: 0814.01 n1organizational. 12-01. n **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych mających dostęp do Internetu i zawiera zalecenia dotyczące reguły sieciowej grupy zabezpieczeń, które zmniejszają potencjalną podatność na ataki |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Maszyny wirtualne dostępne z Internetu powinny być chronione za pomocą sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Ochrona maszyn wirtualnych przed potencjalnymi zagrożeniami przez ograniczenie dostępu do nich za pomocą sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). Dowiedz się więcej o kontrolowaniu ruchu z sieciowych grup zabezpieczeń na [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączenia tylko połączeń za pośrednictwem protokołu SSL do usługi Azure cache for Redis. Użycie bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane z ataków warstwy sieciowej, takich jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Inspekcja wymagania bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Ochrona podsieci przed potencjalnymi zagrożeniami przez ograniczenie dostępu do niej przy użyciu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń). Sieciowych grup zabezpieczeń zawierają listę reguł listy kontroli dostępu (ACL) Access Control, które zezwalają na ruch sieciowy lub odmawiają go w podsieci. |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Maszyny wirtualne powinny być połączone z zatwierdzoną siecią wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych połączonych z siecią wirtualną, która nie jest zatwierdzona. |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="user-identification-and-authentication"></a>Identyfikacja i uwierzytelnianie użytkownika

### <a name="the-organization-ensures-that-redundant-user-ids-are-not-issued-to-other-users-and-that-all-users-are-uniquely-identified-and-authenticated-for-both-local-and-remote-access-to-information-systems"></a>Organizacja gwarantuje, że nadmiarowe identyfikatory użytkowników nie są wystawiane innym użytkownikom oraz że wszyscy użytkownicy są jednoznacznie zidentyfikowani i uwierzytelniani w przypadku dostępu lokalnego i zdalnego do systemów informacyjnych.

**ID**: 11109.01 q1organizational. 57-01. q **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami właściciela, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="non-organizational-users-all-information-system-users-other-than-organizational-users-such-as-patients-customers-contractors-or-foreign-nationals-or-processes-acting-on-behalf-of-non-organizational-users-determined-to-need-access-to-information-residing-on-the-organizations-information-systems-are-uniquely-identified-and-authenticated"></a>Użytkownicy niebędący organizacjami (wszyscy użytkownicy systemu informacji, którzy nie są użytkownikami w organizacji, np. pacjentów, klienci, wykonawcy lub zagraniczni cudzoziemcy) lub procesy działające w imieniu użytkowników nienależących do organizacji, którzy mają do nich dostęp do informacji znajdujących się w systemach informatycznych firmy, są jednoznacznie identyfikowane i uwierzytelniane.

**ID**: 11110.01 q1organizational. 6-01. q **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="when-pki-based-authentication-is-used-the-information-system-validates-certificates-by-constructing-and-verifying-a-certification-path-to-an-accepted-trust-anchor-including-checking-certificate-status-information-enforces-access-to-the-corresponding-private-key-maps-the-identity-to-the-corresponding-account-of-the-individual-or-group-and-implements-a-local-cache-of-revocation-data-to-support-path-discovery-and-validation-in-case-of-an-inability-to-access-revocation-information-via-the-network"></a>W przypadku użycia uwierzytelniania opartego na infrastrukturze PKI system informacji weryfikuje certyfikaty przez konstruowanie i zweryfikowanie ścieżki certyfikacji do zaakceptowanej kotwicy zaufania, w tym sprawdzanie informacji o stanie certyfikatu; wymusza dostęp do odpowiedniego klucza prywatnego; mapuje tożsamość na odpowiednie konto osoby lub grupy; i implementuje lokalną pamięć podręczną danych odwołania w celu obsługi odnajdywania ścieżek i weryfikacji w przypadku braku możliwości dostępu do informacji o odwołaniu za pośrednictwem sieci.

**ID**: 11111.01 q2system. 4-01. q **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Multi-Factor Authentication (MFA) powinna być włączona dla wszystkich kont subskrypcji z uprawnieniami do odczytu, aby zapobiec naruszeniu kont lub zasobów. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-employs-replay-resistant-authentication-mechanisms-such-as-nonce-one-time-passwords-or-time-stamps-to-secure-network-access-for-privileged-accounts-and-for-hardware-token-based-authentication-employs-mechanisms-that-satisfy-minimum-token-requirements-discussed-in-nist-sp-800-63-2-electronic-authentication-guideline"></a>System informacji stosuje mechanizmy uwierzytelniania odpornego na powtórzenia, takie jak identyfikator jednorazowy, hasła jednorazowe lub sygnatury czasowe, aby zabezpieczyć dostęp do sieci dla kont uprzywilejowanych; w przypadku uwierzytelniania opartego na tokenach sprzętowych program wykorzystuje mechanizmy spełniające minimalne wymagania dotyczące tokenu omówione w sekcji NIST SP 800-63-2, wytyczne dotyczące uwierzytelniania elektronicznego.

**ID**: 11112.01 q2organizational. 67-01. q **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Zaleca się wyznaczenie do 3 właścicieli subskrypcji, aby zmniejszyć prawdopodobieństwo naruszenia przez zagrożonego właściciela. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-requires-that-electronic-signatures-unique-to-one-individual-cannot-be-reused-by-or-reassigned-to-anyone-else"></a>Organizacja wymaga, aby podpisy elektroniczne, unikatowe dla jednej osoby, nie mogą być ponownie używane przez, ani ponownie przypisane do, inne.

**ID**: 11208.01 q1organizational. 8-01. q **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Do subskrypcji powinien być przypisany więcej niż jeden właściciel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Zaleca się wyznaczenie więcej niż jednego właściciela subskrypcji w celu zapewnienia nadmiarowości dostępu administratora. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="electronic-signatures-and-handwritten-signatures-executed-to-electronic-records-shall-be-linked-to-their-respective-electronic-records"></a>Podpisy elektroniczne i podpisy ręczne wykonywane w rekordach elektronicznych są łączone z odpowiednimi rekordami elektronicznymi.

**ID**: 11210.01 q2organizational. 10-01. q **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, które mają określonych członków w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Wymaga, aby warunki wstępne zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli lokalna grupa administratorów zawiera co najmniej jeden element członkowski wymieniony w parametrze Policy. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="signed-electronic-records-shall-contain-information-associated-with-the-signing-in-human-readable-format"></a>Podpisane rekordy elektroniczne zawierają informacje powiązane z logowaniem w formacie czytelnym dla człowieka.

**ID**: 11211.01 q2organizational. 11-01. q **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, w których brakuje któregokolwiek z określonych członków w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Wymaga, aby warunki wstępne zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny nie są zgodne, jeśli lokalna grupa administratorów nie zawiera co najmniej jednego członka wymienionego w parametrze Policy. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

### <a name="users-who-performed-privileged-functions-eg-system-administration-use-separate-accounts-when-performing-those-privileged-functions"></a>Użytkownicy, którzy wykonali funkcje uprzywilejowane (np. Administracja systemu), używają osobnych kont podczas wykonywania tych funkcji uprzywilejowanych.

**ID**: 1123.01 q1system. 2-01. q **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, które mają dodatkowe konta w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Wymaga, aby warunki wstępne zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny nie są zgodne, jeśli lokalna grupa administratorów zawiera elementy członkowskie, których nie ma na liście parametrów zasad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |

### <a name="multi-factor-authentication-methods-are-used-in-accordance-with-organizational-policy-eg-for-remote-network-access"></a>Metody uwierzytelniania wieloskładnikowego są używane zgodnie z zasadami organizacyjnymi (np. w przypadku dostępu zdalnego do sieci).

**ID**: 1125.01 q2system. 1-01. q **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, które mają określonych członków w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Wymaga, aby warunki wstępne zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli lokalna grupa administratorów zawiera co najmniej jeden element członkowski wymieniony w parametrze Policy. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="where-tokens-are-provided-for-multi-factor-authentication-in-person-verification-is-required-prior-to-granting-access"></a>W przypadku, gdy tokeny są udostępniane na potrzeby uwierzytelniania wieloskładnikowego, weryfikacja osoby jest wymagana przed udzieleniem dostępu.

**ID**: 1127.01 q2system. 3-01. q **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, w których brakuje któregokolwiek z określonych członków w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Wymaga, aby warunki wstępne zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny nie są zgodne, jeśli lokalna grupa administratorów nie zawiera co najmniej jednego członka wymienionego w parametrze Policy. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

## <a name="identification-of-risks-related-to-external-parties"></a>Identyfikacja zagrożeń związanych ze stronami zewnętrznymi

### <a name="access-to-the-organizations-information-and-systems-by-external-parties-is-not-permitted-until-due-diligence-has-been-conducted-the-appropriate-controls-have-been-implemented-and-a-contractagreement-reflecting-the-security-requirements-is-signed-acknowledging-they-understand-and-accept-their-obligations"></a>Dostęp do informacji i systemów organizacji przez podmioty zewnętrzne nie jest dozwolony do momentu przeprowadzenia pomyślnej staranności, zostały zaimplementowane odpowiednie mechanizmy kontroli, a umowa/umowa odzwierciedlająca wymagania dotyczące zabezpieczeń są podpisane potwierdzaniem, że rozumieją one i akceptują swoje zobowiązania.

**ID**: 1401.05 i1organizational. 1239-05. i **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Inspekcja wymagania bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="remote-access-connections-between-the-organization-and-external-parties-are-encrypted"></a>Połączenia dostępu zdalnego między organizacją a zewnętrznymi stronami są szyfrowane.

**ID**: 1402.05 i1organizational. 45-05. i **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |

### <a name="access-granted-to-external-parties-is-limited-to-the-minimum-necessary-and-granted-only-for-the-duration-required"></a>Dostęp przyznawany stronom zewnętrznym jest ograniczony do minimalnych wymagań i udzielanych tylko przez wymagany czas.

**ID**: 1403.05 i1organizational. 67-05. i **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="due-diligence-of-the-external-party-includes-interviews-document-review-checklists-certification-reviews-eg-hitrustnbspor-other-remote-means"></a>Poszanowanie strony zewnętrznej obejmuje rozmowy kwalifikacyjne, przegląd dokumentów, listy kontrolne, przeglądy certyfikacji (np. HITRUST) &nbsp; lub inne zdalne metody.

**ID**: 1404.05 i2organizational. 1-05. i **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |

### <a name="the-identification-of-risks-related-to-external-party-access-takes-into-account-a-minimal-set-of-specifically-defined-issues"></a>Identyfikacja zagrożeń związanych z dostępem podmiotów zewnętrznych polega na uwzględnieniu minimalnego zestawu konkretnych określonych problemów.

**ID**: 1418.05 i1organizational. 8-05. i **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-organization-obtains-satisfactory-assurances-that-reasonable-information-security-exists-across-their-information-supply-chain-by-performing-an-annual-review-which-includes-all-partnersthird-party-providers-upon-which-their-information-supply-chain-depends"></a>Organizacja uzyskuje zadowalające gwarancje, że istnieją odpowiednie zabezpieczenia informacji w łańcuchu dostaw informacji przez przeprowadzenie rocznego przeglądu, w tym wszystkich partnerów/dostawców innych firm, na których zależą ich łańcuch dostaw informacji.

**ID**: 1450.05 i2organizational. 2-05. i **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="cloud-service-providers-design-and-implement-controls-to-mitigate-and-contain-data-security-risks-through-proper-separation-of-duties-role-based-access-and-least-privilege-access-for-all-personnel-within-their-supply-chain"></a>Dostawcy usług w chmurze projektują i implementują kontrole w celu ograniczenia i zawierania zagrożeń bezpieczeństwa danych poprzez odpowiednie rozdzielenie obowiązków, dostęp oparty na rolach i dostęp do najniższych uprawnień dla wszystkich pracowników w łańcuchu dostaw.

**ID**: 1451.05 icsporganizational. 2-05. i **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączenia tylko połączeń za pośrednictwem protokołu SSL do usługi Azure cache for Redis. Użycie bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane z ataków warstwy sieciowej, takich jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="audit-logging"></a>Rejestrowanie inspekcji

### <a name="a-secure-audit-record-is-created-for-all-activities-on-the-system-create-read-update-delete-involving-covered-information"></a>Dla wszystkich działań w systemie tworzony jest bezpieczny rekord inspekcji (tworzenie, odczytywanie, aktualizowanie, usuwanie) obejmujących informacje objęte usługą.

**ID**: 1202.09 aa1system. 1-09. AA **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dzienniki diagnostyczne w Azure Data Lake Store powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Należy sprawdzić, czy istnieją brakujące aktualizacje zabezpieczeń systemu i aktualizacje krytyczne, które należy zainstalować, aby mieć pewność, że zestawy skalowania maszyn wirtualnych z systemami Windows i Linux są bezpieczne. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |

### <a name="audit-records-include-the-unique-user-id-unique-data-subject-id-function-performed-and-datetime-the-event-was-performed"></a>Rekordy inspekcji obejmują unikatowy identyfikator użytkownika, unikatowy identyfikator podmiotu danych, wykonaną funkcję i datę/godzinę wykonania zdarzenia.

**ID**: 1203.09 aa1system. 2-09. AA **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dzienniki diagnostyczne w Logic Apps powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

### <a name="the-activities-of-privileged-users-administrators-operators-etc-include-the-successfailure-of-the-event-time-the-event-occurred-the-account-involved-the-processes-involved-and-additional-information-about-the-event"></a>Działania uprzywilejowanych użytkowników (Administratorzy, operatorzy itp.) obejmują sukces/niepowodzenie zdarzenia, czas wystąpienia zdarzenia, dane konto, uwzględnione procesy i dodatkowe informacje o zdarzeniu.

**ID**: 1204.09 aa1system. 3-09. AA **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dzienniki diagnostyczne w IoT Hub powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

### <a name="logs-of-messages-sent-and-received-are-maintained-including-the-date-time-origin-and-destination-of-the-message-but-not-its-contents"></a>Dzienniki komunikatów wysłanych i odebranych są obsługiwane, w tym data, godzina, źródło i miejsce docelowe wiadomości, ale nie jej zawartość.

**ID**: 1205.09 aa2system. 1-09. AA **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć dzienniki diagnostyczne na kontach wsadowych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |

### <a name="auditing-is-always-available-while-the-system-is-active-and-tracks-key-events-successfailed-data-access-system-security-configuration-changes-privileged-or-utility-use-any-alarms-raised-nbspactivation-and-de-activation-of-protection-systems-eg-av-and-ids-activation-and-deactivation-of-identification-and-authentication-mechanisms-and-creation-and-deletion-of-system-level-objects"></a>Inspekcja jest zawsze dostępna, gdy system jest aktywny i śledzi najważniejsze zdarzenia, dostęp do danych zakończonych sukcesem/zakończonych niepowodzeniem, zmiany konfiguracji zabezpieczeń systemu, uprawnienia uprzywilejowane lub użycie narzędzi, wszelkie alarmy zgłoszone, &nbsp; aktywację i cofnięcie aktywacji systemów ochrony (np. a/V i identyfikatorów), aktywacji i dezaktywacji mechanizmów identyfikacji i uwierzytelniania oraz tworzenia i usuwania obiektów poziomu systemu.

**ID**: 1206.09 aa2system. 23-09. AA **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dzienniki diagnostyczne w Virtual Machine Scale Sets powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Zaleca się włączenie dzienników, aby można było odtworzyć dziennik aktywności w przypadku, gdy w przypadku incydentu jest wymagane badanie lub naruszenie zabezpieczeń. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

### <a name="audit-records-are-retained-for-90-days-and-older-audit-records-are-archived-for-one-year"></a>Rekordy inspekcji są przechowywane przez 90 dni, a starsze rekordy inspekcji są archiwizowane przez jeden rok.

**ID**: 1207.09 aa2system. 4-09. AA **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dzienniki diagnostyczne w Azure Stream Analytics powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

### <a name="audit-logs-are-maintained-for-management-activities-system-and-application-startupshutdownerrors-file-changes-and-security-policy-changes"></a>Dzienniki inspekcji są utrzymywane na potrzeby działań związanych z zarządzaniem, uruchamiania i wyłączania i wyłączania aplikacji oraz zmian w plikach oraz zmian zasad zabezpieczeń.

**ID**: 1208.09 aa3system. 1-09. AA **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dzienniki diagnostyczne w usługach wyszukiwania powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Dzienniki diagnostyczne w Service Bus powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

### <a name="the-information-system-generates-audit-records-containing-the-following-detailed-information-i-filename-accessed-ii-program-or-command-used-to-initiate-the-event-and-iii-source-and-destination-addresses"></a>System informacji generuje rekordy inspekcji zawierające następujące szczegółowe informacje: (i) filename, do których można uzyskać dostęp; (II) program lub polecenie używane do inicjowania zdarzenia; i (III) adresy źródłowe i docelowe.

**ID**: 1209.09 aa3system. 2-09. AA **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dzienniki diagnostyczne w App Services powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Inspekcja włączenia dzienników diagnostycznych w aplikacji. Pozwala to na ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |

### <a name="all-disclosures-of-covered-information-within-or-outside-of-the-organization-are-logged-including-type-of-disclosure-datetime-of-the-event-recipient-and-sender"></a>Wszystkie ujawniane informacje zawarte w organizacji lub poza nią są rejestrowane, w tym typ ujawniania, Data/godzina zdarzenia, odbiorcy i nadawcy.

**ID**: 1210.09 aa3system. 3-09. AA **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Audit diagnostic setting](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) (Przeprowadzaj inspekcję ustawienia diagnostyki) |Inspekcja ustawień diagnostycznych dla wybranych typów zasobów |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Dzienniki diagnostyczne w Data Lake Analytics powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie śladów aktywności do użycia w celach dochodzeniowych; gdy wystąpi zdarzenie związane z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |

### <a name="the-organization-verifies-every-ninety-90-days-for-each-extract-of-covered-information-recorded-that-the-data-is-erased-or-its-use-is-still-required"></a>Organizacja weryfikuje co 90 (90) dni dla każdego wyodrębnienia zarejestrowanej informacji, że dane zostały wymazane lub ich użycie jest nadal wymagane.

**ID**: 1211.09 aa3system. 4-09. AA **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja w programie SQL Server powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Inspekcja w SQL Server powinna być włączona, aby śledzić działania bazy danych we wszystkich bazach danych na serwerze, z wyjątkiem Synapse i zapisywać je w dzienniku inspekcji. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Dzienniki diagnostyczne w Key Vault powinny być włączone](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Inspekcja włączenia dzienników diagnostycznych. Pozwala to na ponowne utworzenie końcowych działań do użycia w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub w przypadku naruszenia bezpieczeństwa sieci |AuditIfNotExists, wyłączone |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

## <a name="monitoring-system-use"></a>Monitorowanie użycia systemu

### <a name="unauthorized-remote-connections-to-the-information-systems-are-monitored-and-reviewed-at-least-quarterly-and-appropriate-action-is-taken-if-an-unauthorized-connection-is-discovered"></a>Nieautoryzowane połączenia zdalne z systemami informacyjnymi są monitorowane i przeglądane co najmniej raz na kwartał i podejmowane są odpowiednie działania w przypadku odnalezienia nieautoryzowanego połączenia.

**ID**: 1120.09 ab3system. 9-09. AB **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor powinna zbierać dzienniki aktywności ze wszystkich regionów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Ta zasada przeprowadza inspekcję profilu dziennika Azure Monitor, który nie eksportuje działań ze wszystkich regionów obsługiwanych przez platformę Azure, w tym globalnego. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="the-organization-monitors-the-information-system-to-identify-irregularities-or-anomalies-that-are-indicators-of-a-system-malfunction-or-compromise-and-help-confirm-the-system-is-functioning-in-an-optimal-resilient-and-secure-state"></a>Organizacja monitoruje system informacji w celu zidentyfikowania nieprawidłowości lub anomalii, które są wskaźnikami wadliwych usterek systemu lub naruszenia bezpieczeństwa, a także pomaga upewnić się, że system działa w optymalnym, odpornym i bezpiecznym stanie.

**ID**: 12100.09 ab2system. 15-09., **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Na maszynach wirtualnych należy zainstalować agenta Log Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych z systemem Windows/Linux, jeśli Agent Log Analytics nie jest zainstalowany. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-specifies-how-often-audit-logs-are-reviewed-how-the-reviews-are-documented-and-the-specific-roles-and-responsibilities-of-the-personnel-conducting-the-reviews-including-the-professional-certifications-or-other-qualifications-required"></a>Organizacja określa, jak często są przeglądane dzienniki inspekcji, jak są dokumentowane przeglądy oraz określone role i obowiązki personelu wykonującego przeglądy, w tym certyfikaty profesjonalne lub inne wymagane kwalifikacje.

**ID**: 12101.09 ab1organizational. 3-09 **. AB:** klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Agenta Log Analytics należy zainstalować na Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Ta zasada przeprowadza inspekcję wszystkich Virtual Machine Scale Sets systemu Windows/Linux, jeśli Agent Log Analytics nie jest zainstalowany. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-shall-periodically-test-its-monitoring-and-detection-processes-remediate-deficiencies-and-improve-its-processes"></a>Organizacja okresowo bada procesy monitorowania i wykrywania, koryguje braki i ulepsza procesy.

**ID**: 12102.09 ab1organizational. 4-09. AB **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, na których Agent Log Analytics nie jest połączony zgodnie z oczekiwaniami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Wymaga, aby warunki wstępne zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli Agent nie jest zainstalowany lub jest zainstalowany, ale obiekt COM AgentConfigManager. MgmtSvcCfg zwraca, że jest zarejestrowany w obszarze roboczym innym niż identyfikator określony w parametrze Policy. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="all-applicable-legal-requirements-related-to-monitoring-authorized-access-and-unauthorized-access-attempts-are-met"></a>Spełnione są wszystkie odpowiednie wymagania prawne dotyczące monitorowania autoryzowanego dostępu i nieautoryzowanych prób dostępu.

**ID**: 1212.09 ab1system. 1-09. AB **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Profil dziennika Azure Monitor powinien zbierać dzienniki dla kategorii "Write", "Delete" i "Action"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Te zasady zapewniają, że profil dziennika zbiera dzienniki dla kategorii "zapis", "Usuń" i "Akcja" |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="automated-systems-deployed-throughout-the-organizations-environment-are-used-to-monitor-key-events-and-anomalous-activity-and-analyze-system-logs-the-results-of-which-are-reviewed-regularly"></a>Zautomatyzowane systemy wdrożone w środowisku organizacji są używane do monitorowania najważniejszych zdarzeń i nietypowej aktywności oraz analizowania dzienników systemowych, których wyniki są regularnie przeglądane.

**ID**: 1213.09 ab2system. 128-09, **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Automatyczna obsługa administracyjna agenta monitorowania Log Analytics powinna być włączona w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Włącz automatyczną obsługę administracyjną agenta monitorowania Log Analytics w celu zbierania danych zabezpieczeń |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="monitoring-includes-privileged-operations-authorized-access-or-unauthorized-access-attempts-including-attempts-to-access-deactivated-accounts-and-system-alerts-or-failures"></a>Monitorowanie obejmuje operacje uprzywilejowane, autoryzowany dostęp lub próby nieautoryzowanego dostępu, w tym próby dostępu do zdezaktywowanych kont, a także alerty lub błędy systemowe.

**ID**: 1214.09 ab2system. 3456-09. AB **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Monitor powinna zbierać dzienniki aktywności ze wszystkich regionów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Ta zasada przeprowadza inspekcję profilu dziennika Azure Monitor, który nie eksportuje działań ze wszystkich regionów obsługiwanych przez platformę Azure, w tym globalnego. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="auditing-and-monitoring-systems-employed-by-the-organization-support-audit-reduction-and-report-generation"></a>Systemy inspekcji i monitorowania wykorzystywane przez organizację obsługują redukcję inspekcji i generowanie raportów.

**ID**: 1215.09 ab2system. 7-09. AB **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Na maszynach wirtualnych należy zainstalować agenta Log Analytics](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych z systemem Windows/Linux, jeśli Agent Log Analytics nie jest zainstalowany. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="automated-systems-are-used-to-review-monitoring-activities-of-security-systems-eg-ipsids-and-system-records-on-a-daily-basis-and-identify-and-document-anomalies"></a>Zautomatyzowane systemy są używane do codziennego przeglądania działań monitorowania systemów zabezpieczeń (np. adresów IP/identyfikatorów) i rekordów systemowych oraz identyfikowania i dokumentowania anomalii.

**ID**: 1216.09 ab3system. 12-09 **. AB:** klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Agenta Log Analytics należy zainstalować na Virtual Machine Scale Sets](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Ta zasada przeprowadza inspekcję wszystkich Virtual Machine Scale Sets systemu Windows/Linux, jeśli Agent Log Analytics nie jest zainstalowany. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="alerts-are-generated-for-technical-personnel-to-analyze-and-investigate-suspicious-activity-or-suspected-violations"></a>Alerty są generowane dla personelu technicznego w celu analizowania i badania podejrzanych działań lub podejrzanych naruszeń.

**ID**: 1217.09 ab3system. 3-09 **. AB:** klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, na których Agent Log Analytics nie jest połączony zgodnie z oczekiwaniami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Wymaga, aby warunki wstępne zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli Agent nie jest zainstalowany lub jest zainstalowany, ale obiekt COM AgentConfigManager. MgmtSvcCfg zwraca, że jest zarejestrowany w obszarze roboczym innym niż identyfikator określony w parametrze Policy. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="the-information-system-is-able-to-automatically-process-audit-records-for-events-of-interest-based-on-selectable-criteria"></a>System informacji jest w stanie automatycznie przetwarzać rekordy inspekcji pod kątem zdarzeń interesujących na podstawie wybranych kryteriów.

**ID**: 1219.09 ab3system. 10-09. **dbwłasność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Profil dziennika Azure Monitor powinien zbierać dzienniki dla kategorii "Write", "Delete" i "Action"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Te zasady zapewniają, że profil dziennika zbiera dzienniki dla kategorii "zapis", "Usuń" i "Akcja" |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="monitoring-includes-inbound-and-outbound-communications-and-file-integrity-monitoring"></a>Monitorowanie obejmuje komunikację z ruchem przychodzącym i wychodzącym oraz monitorowanie integralności plików.

**ID**: 1220.09 ab3system. 56-09, **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Automatyczna obsługa administracyjna agenta monitorowania Log Analytics powinna być włączona w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Włącz automatyczną obsługę administracyjną agenta monitorowania Log Analytics w celu zbierania danych zabezpieczeń |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

## <a name="administrator-and-operator-logs"></a>Dzienniki administratorów i operatorów

### <a name="the-organization-ensures-proper-logging-is-enabled-in-order-to-audit-administrator-activities-and-reviews-system-administrator-and-operator-logs-on-a-regular-basis"></a>Organizacja zapewnia, że w celu inspekcji działań administratorów jest włączone odpowiednie rejestrowanie; i regularnie przegląda dzienniki i administratorów systemu.

**ID**: 1270.09 ad1system. 12-09.AD **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ta zasada przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="an-intrusion-detection-system-managed-outside-of-the-control-of-system-and-network-administrators-is-used-to-monitor-system-and-network-administration-activities-for-compliance"></a>System wykrywania dostępu intruzów zarządzany poza kontrolą administratorów systemu i sieci jest używany do monitorowania działań związanych z administracją systemu i sieci w celu zapewnienia zgodności.

**ID**: 1271.09 ad1system. 1-09.AD **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Ta zasada przeprowadza inspekcję określonych operacji administracyjnych bez skonfigurowanych alertów dziennika aktywności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

## <a name="segregation-of-duties"></a>Rozdzielenie obowiązków

### <a name="separation-of-duties-is-used-to-limit-the-risk-of-unauthorized-or-unintentional-modification-of-information-and-systems"></a>Rozdzielenie obowiązków jest stosowane w celu ograniczenia ryzyka nieautoryzowanej lub przypadkowej modyfikacji informacji i systemów.

**ID**: 1229.09 c1organizational. 1-09. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Access Control oparte na rolach (RBAC) powinny być używane w usługach Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Aby zapewnić szczegółowe filtrowanie działań, które użytkownicy mogą wykonywać, użyj Role-Based Access Control (RBAC) do zarządzania uprawnieniami w klastrach usługi Kubernetes i konfigurowania odpowiednich zasad autoryzacji. |Inspekcja, wyłączona |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="no-single-person-is-able-to-access-modify-or-use-information-systems-without-authorization-or-detection"></a>Żadna osoba nie może uzyskać dostępu do systemów informacyjnych ani ich modyfikować ani korzystać z nich bez autoryzacji i wykrywania.

**ID**: 1230.09 c2organizational. 1-09. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja użycia niestandardowych reguł RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Inspekcja wbudowanych ról, takich jak "Owner, współautorzy, czytelnik" zamiast niestandardowych ról RBAC, które są podatne na błędy. Używanie ról niestandardowych jest traktowane jako wyjątek i wymaga rygorystycznego przeglądu i modelowania zagrożeń |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

### <a name="access-for-individuals-responsible-for-administering-nbspaccess-controls-is-limited-to-the-minimum-necessary-based-upon-each-users-role-and-responsibilities-and-these-individuals-cannot-access-audit-functions-related-to-these-controls"></a>Dostęp do osób odpowiedzialnych za administrowanie &nbsp; kontrolami dostępu jest ograniczony do minimalnych potrzeb opartych na rolach i obowiązkach poszczególnych użytkowników, a te osoby nie mogą uzyskać dostępu do funkcji inspekcji związanych z tymi kontrolkami.

**ID**: 1232.09 c3organizational. 12-09. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące "przypisywania praw użytkownika"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe068b215-0026-4354-b347-8fb2766f73a2) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "przypisanie praw użytkownika", aby umożliwić logowanie lokalne, RDP, dostęp z sieci i wiele innych działań użytkownika. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_UserRightsAssignment_AINE.json) |

### <a name="security-audit-activities-are-independent"></a>Działania inspekcji zabezpieczeń są niezależne.

**ID**: 1276.09 c2organizational. 2-09. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Role niestandardowego właściciela subskrypcji nie powinny istnieć](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Te zasady zapewniają, że nie istnieją żadne role niestandardowego właściciela subskrypcji. |Inspekcja, wyłączona |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

### <a name="the-initiation-of-an-event-is-separated-from-its-authorization-to-reduce-the-possibility-of-collusion"></a>Inicjacja zdarzenia jest oddzielona od jego autoryzacji, aby zmniejszyć prawdopodobieństwo zmowy.

**ID**: 1277.09 c2organizational. 4-09. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń "Kontrola konta użytkownika"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F492a29ed-d143-4f03-b6a4-705ce081b463) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "Opcje zabezpieczeń-Kontrola konta użytkownika" dla administratorów, zachowanie monitu o podniesienie uprawnień oraz Wirtualizacja błędów zapisu plików i rejestru. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsUserAccountControl_AINE.json) |

### <a name="the-organization-identifies-duties-that-require-separation-and-defines-information-system-access-authorizations-to-support-separation-of-duties-and-incompatible-duties-are-segregated-across-multiple-users-to-minimize-the-opportunity-for-misuse-or-fraud"></a>Organizacja identyfikuje obowiązki, które wymagają separacji i definiuje autoryzacje dostępu do systemu informacji w celu obsługi separacji obowiązków. i niezgodne cła są segregowane przez wielu użytkowników w celu zminimalizowania możliwości nieprawidłowego użycia lub oszustwa.

**ID**: 1278.09 c2organizational. 56-09. c **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Role niestandardowego właściciela subskrypcji nie powinny istnieć](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Te zasady zapewniają, że nie istnieją żadne role niestandardowego właściciela subskrypcji. |Inspekcja, wyłączona |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="controls-against-malicious-code"></a>Kontrolki przed złośliwym kodem

### <a name="anti-virus-and-anti-spyware-are-installed-operating-and-updated-on-all-end-user-devices-to-conduct-periodic-scans-of-the-systems-to-identify-and-remove-unauthorized-software-server-environments-for-which-the-server-software-developer-specifically-recommends-not-installing-host-based-anti-virus-and-anti-spyware-software-may-address-the-requirement-via-a-network-based-malware-detection-nbmd-solution"></a>Programy antywirusowe i antywirusowe są instalowane, działają i aktualizowane na wszystkich urządzeniach użytkowników końcowych w celu przeprowadzania okresowego skanowania systemów w celu identyfikowania i usuwania nieautoryzowanego oprogramowania. Środowiska serwera, dla których deweloper oprogramowania serwera zaleca, aby nie instalować oprogramowania antywirusowego i chroniącego przed oprogramowaniem szpiegującym, może rozwiązać ten problem za pośrednictwem rozwiązania do wykrywania złośliwego oprogramowania (NBMD).

**ID**: 0201.09 j1organizational. 124-09. j **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Na maszynach powinny być włączone adaptacyjne kontrole aplikacji umożliwiające Definiowanie bezpiecznych aplikacji.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Włącz kontrolki aplikacji, aby zdefiniować listę znanych bezpiecznych aplikacji uruchomionych na maszynach, i Zgłoś alert, gdy inne aplikacje zostaną uruchomione. Pozwala to na zabezpieczanie maszyn przed złośliwym oprogramowaniem. Aby uprościć proces konfigurowania i utrzymywania reguł, Security Center korzysta z uczenia maszynowego do analizowania aplikacji uruchomionych na poszczególnych maszynach i sugerują listę znanych bezpiecznych aplikacji. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Wdróż domyślne rozszerzenie Microsoft IaaSAntimalware dla systemu Windows Server](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |Te zasady wdrażają rozszerzenie Microsoft IaaSAntimalware z konfiguracją domyślną, gdy maszyna wirtualna nie jest skonfigurowana przy użyciu rozszerzenia ochrony przed złośliwym kodem. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |
|[Rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Przeprowadź inspekcję istnienia i kondycji rozwiązania Endpoint Protection w zestawach skalowania maszyn wirtualnych, aby chronić je przed zagrożeniami i lukami w zabezpieczeniach. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Program Microsoft chroniący przed złośliwym kodem dla platformy Azure powinien zostać skonfigurowany do automatycznego aktualizowania sygnatur ochrony](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |Ta zasada przeprowadza inspekcję wszystkich maszyn wirtualnych systemu Windows, które nie są skonfigurowane z automatyczną aktualizacją sygnatur ochrony przed złośliwym oprogramowaniem firmy Microsoft. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[Monitoruj brakujące Endpoint Protection w Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Serwery bez zainstalowanego agenta Endpoint Protection będą monitorowane przez Azure Security Center jako zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[Należy zainstalować aktualizacje systemu na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center jak zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="back-up"></a>Tworzenie kopii zapasowej

### <a name="backup-copies-of-information-and-software-are-made-and-tests-of-the-media-and-restoration-procedures-are-regularly-performed-at-appropriate-intervals"></a>Kopie zapasowe informacji i oprogramowania są tworzone, a testy dotyczące nośników i procedur przywracania są regularnie wykonywane w odpowiednich odstępach czasu.

**ID**: 1616.09 l1organizational. 16-09. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Długoterminowa geograficznie nadmiarowa kopia zapasowa powinna być włączona dla baz danych Azure SQL Database](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Ta zasada przeprowadza inspekcję wszelkich Azure SQL Database z długoterminową geograficznie nadmiarową kopią zapasową, która nie jest włączona. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="a-formal-definition-of-the-level-of-backup-required-for-each-system-is-defined-and-documented-including-how-each-system-will-be-restored-the-scope-of-data-to-be-imaged-frequency-of-imaging-and-duration-of-retention-based-on-relevant-contractual-legal-regulatory-and-business-requirements"></a>Formalna definicja poziomu kopii zapasowej wymaganego dla każdego systemu jest zdefiniowana i udokumentowana, w tym sposób przywracania każdego systemu, zakres danych do obrazu, częstotliwość tworzenia obrazów oraz czas przechowywania w oparciu o odpowiednie wymagania umowne, prawne, obowiązujące i biznesowe.

**ID**: 1617.09 l1organizational. 23-09. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Może być ustawiony na Geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane nie są przechowywane tylko w regionie, w którym znajduje się serwer, ale również są replikowane w sparowanym regionie, aby zapewnić opcję odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego dla kopii zapasowej jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="the-backups-are-stored-in-a-physically-secure-remote-location-at-a-sufficient-distance-to-make-them-reasonably-immune-from-damage-to-data-at-the-primary-site-and-reasonable-physical-and-environmental-controls-are-in-place-to-ensure-their-protection-at-the-remote-location"></a>Kopie zapasowe są przechowywane w fizycznie zabezpieczonej lokalizacji zdalnej, w odpowiedniej odległości, aby nie były one w stanie odporne na uszkodzenia danych w lokacji głównej, a odpowiednie środki kontroli fizycznej i środowiska mają zapewnić ochronę w lokalizacji zdalnej.

**ID**: 1618.09 l1organizational. 45-09. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Może być ustawiony na Geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane nie są przechowywane tylko w regionie, w którym znajduje się serwer, ale również są replikowane w sparowanym regionie, aby zapewnić opcję odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego dla kopii zapasowej jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="inventory-records-for-the-backup-copies-including-content-and-current-location-are-maintained"></a>Obsługiwane są rekordy spisu kopii zapasowych, w tym zawartość i bieżąca lokalizacja.

**ID**: 1619.09 l1organizational. 7-09. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Może być ustawiony na Geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane nie są przechowywane tylko w regionie, w którym znajduje się serwer, ale również są replikowane w sparowanym regionie, aby zapewnić opcję odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego dla kopii zapasowej jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="when-the-backup-service-is-delivered-by-the-third-party-the-service-level-agreement-includes-the-detailed-protections-to-control-confidentiality-integrity-and-availability-of-the-backup-information"></a>Gdy usługa tworzenia kopii zapasowych jest dostarczana przez inną firmę, umowa dotycząca poziomu usług obejmuje szczegółową ochronę, która kontroluje poufność, integralność i dostępność informacji o kopii zapasowej.

**ID**: 1620.09 l1organizational. 8-09. l **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Backup powinna być włączona dla Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Aby zapewnić ochronę Virtual Machines platformy Azure, należy włączyć Azure Backup. Azure Backup to bezpieczne i ekonomiczne rozwiązanie do ochrony danych dla systemu Azure. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="automated-tools-are-used-to-track-all-backups"></a>Narzędzia automatyczne służą do śledzenia wszystkich kopii zapasowych.

**ID**: 1621.09 l2organizational. 1-09. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Długoterminowa geograficznie nadmiarowa kopia zapasowa powinna być włączona dla baz danych Azure SQL Database](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Ta zasada przeprowadza inspekcję wszelkich Azure SQL Database z długoterminową geograficznie nadmiarową kopią zapasową, która nie jest włączona. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="the-integrity-and-security-of-the-backup-copies-are-maintained-to-ensure-future-availability-and-any-potential-accessibility-problems-with-the-backup-copies-are-identified-and-mitigated-in-the-event-of-an-area-wide-disaster"></a>Integralność i bezpieczeństwo kopii zapasowych są zachowywane w celu zapewnienia przyszłej dostępności, a wszelkie potencjalne problemy z dostępnością kopii zapasowych są identyfikowane i zmniejszane w przypadku awarii całego obszaru.

**ID**: 1622.09 l2organizational. 23-09. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Może być ustawiony na Geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane nie są przechowywane tylko w regionie, w którym znajduje się serwer, ale również są replikowane w sparowanym regionie, aby zapewnić opcję odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego dla kopii zapasowej jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="covered-information-is-backed-up-in-an-encrypted-format-to-ensure-confidentiality"></a>Informacje objęte usługą są archiwizowane w zaszyfrowanym formacie w celu zapewnienia poufności.

**ID**: 1623.09 l2organizational. 4-09. l **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Może być ustawiony na Geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane nie są przechowywane tylko w regionie, w którym znajduje się serwer, ale również są replikowane w sparowanym regionie, aby zapewnić opcję odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego dla kopii zapasowej jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-performs-incremental-or-differential-backups-daily-and-full-backups-weekly-to-separate-media"></a>Organizacja wykonuje przyrostowe lub różnicowe kopie zapasowe codziennie i pełnymi kopiami zapasowymi na osobnym nośniku.

**ID**: 1624.09 l3organizational. 12-09. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Może być ustawiony na Geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane nie są przechowywane tylko w regionie, w którym znajduje się serwer, ale również są replikowane w sparowanym regionie, aby zapewnić opcję odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego dla kopii zapasowej jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="three-3-generations-of-backups-full-plus-all-related-incremental-or-differential-backups-are-stored-off-site-and-both-on-site-and-off-site-backups-are-logged-with-name-date-time-and-action"></a>Trzy (3) generacja kopii zapasowych (pełne i powiązane przyrostowe kopie zapasowe) są przechowywane poza lokacją, a kopie zapasowe w lokacji i poza lokacjami są rejestrowane przy użyciu nazwy, daty, godziny i akcji.

**ID**: 1625.09 l3organizational. 34-09. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Backup powinna być włączona dla Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Aby zapewnić ochronę Virtual Machines platformy Azure, należy włączyć Azure Backup. Azure Backup to bezpieczne i ekonomiczne rozwiązanie do ochrony danych dla systemu Azure. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="the-organization-ensures-a-current-retrievable-copy-of-covered-information-is-available-before-movement-of-servers"></a>Organizacja gwarantuje, że przed przemieszczeniem serwerów dostępne są bieżące, możliwe do pobierania informacje o podanych informacjach.

**ID**: 1626.09 l3organizational. 5-09. l **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Może być ustawiony na Geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane nie są przechowywane tylko w regionie, w którym znajduje się serwer, ale również są replikowane w sparowanym regionie, aby zapewnić opcję odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego dla kopii zapasowej jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-tests-backup-information-following-each-backup-to-verify-media-reliability-and-information-integrity-and-at-least-annually-thereafter"></a>Organizacja testuje informacje o kopii zapasowej po każdej kopii zapasowej, aby zweryfikować niezawodność i integralność informacji oraz co najmniej raz na rok.

**ID**: 1627.09 l3organizational. 6-09. l **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB umożliwia wybranie opcji nadmiarowości dla serwera bazy danych. Może być ustawiony na Geograficznie nadmiarowy magazyn kopii zapasowych, w którym dane nie są przechowywane tylko w regionie, w którym znajduje się serwer, ale również są replikowane w sparowanym regionie, aby zapewnić opcję odzyskiwania w przypadku awarii regionu. Konfigurowanie magazynu geograficznie nadmiarowego dla kopii zapasowej jest dozwolone tylko podczas tworzenia serwera. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="workforce-members-roles-and-responsibilities-in-the-data-backup-process-are-identified-and-communicated-to-the-workforce-in-particular-bring-your-own-device-byod-users-are-required-to-perform-backups-of-organizational-andor-client-data-on-their-devices"></a>Role i obowiązki członków pracowników w procesie tworzenia kopii zapasowych danych są identyfikowane i przekazywane do pracowników; w szczególności należy przystąpić do wykonywania kopii zapasowych danych organizacji i/lub klientów na swoich urządzeniach (BYOD).

**ID**: 1699.09 l1organizational. 10-09. l **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Backup powinna być włączona dla Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Aby zapewnić ochronę Virtual Machines platformy Azure, należy włączyć Azure Backup. Azure Backup to bezpieczne i ekonomiczne rozwiązanie do ochrony danych dla systemu Azure. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="network-controls"></a>Kontrolki sieci

### <a name="the-organization-monitors-for-all-authorized-and-unauthorized-wireless-access-to-the-information-system-and-prohibits-installation-of-wireless-access-points-waps-unless-explicitly-authorized-in-writing-by-the-cio-or-hisher-designated-representative"></a>Organizacja monitoruje wszystkie autoryzowane i nieautoryzowany dostęp bezprzewodowy do systemu informacji i zabrania instalacji punktów dostępu bezprzewodowego (WAPs), chyba że jest to wyraźnie autoryzowane przez CIO lub jego pełnomocnika.

**ID**: 0858.09 m1organizational. 4-09. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Wszystkie porty sieciowe powinny być ograniczone do sieciowych grup zabezpieczeń skojarzonych z maszyną wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center zidentyfikował zbyt ograniczającą niektóre reguły ruchu przychodzącego grup zabezpieczeń sieci. Reguły ruchu przychodzącego nie powinny zezwalać na dostęp z zakresów "any" lub "Internet". Może to potencjalnie umożliwić osobom atakującym kierowanie Twoich zasobów. |AuditIfNotExists, wyłączone |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp do sieci just in Time (JIT) będzie monitorowany przez Azure Security Center jak zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące właściwości zapory systemu Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35d9882c-993d-44e6-87d2-db66ce21b636) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "właściwości zapory systemu Windows" dla stanu zapory, połączeń, zarządzania regułami i powiadomień. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsFirewallProperties_AINE.json) |

### <a name="the-organization-ensures-the-security-of-information-in-networks-availability-of-network-services-and-information-services-using-the-network-and-the-protection-of-connected-services-from-unauthorized-access"></a>Organizacja gwarantuje bezpieczeństwo informacji w sieciach, dostępność usług sieciowych i usług informacyjnych przy użyciu sieci oraz ochronę połączonych usług przed nieautoryzowanym dostępem.

**ID**: 0859.09 m1organizational. 78-09. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych mających dostęp do Internetu i zawiera zalecenia dotyczące reguły sieciowej grupy zabezpieczeń, które zmniejszają potencjalną podatność na ataki |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="the-organization-formally-manages-equipment-on-the-network-including-equipment-in-user-areas"></a>Organizacja zarządza sprzętem w sieci, w tym sprzętem w obszarach użytkownika.

**ID**: 0860.09 m1organizational. 9-09. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Wdróż ustawienia diagnostyczne dla sieciowych grup zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9c29499-c1d1-4195-99bd-2ec9e3a9dc89) |Te zasady automatycznie wdrażają ustawienia diagnostyczne do sieciowych grup zabezpieczeń. Konto magazynu o nazwie "{storagePrefixParameter} {NSGLocation}" zostanie utworzone automatycznie. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForNSG_Deploy.json) |
|[Service Bus powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Ta zasada przeprowadza inspekcję wszelkich Service Bus nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

### <a name="to-identify-and-authenticate-devices-on-local-andor-wide-area-networks-including-wireless-networks-nbspthe-information-system-uses-either-a-i-shared-known-information-solution-or-ii-an-organizational-authentication-solution-the-exact-selection-and-strength-of-which-is-dependent-on-the-security-categorization-of-the-information-system"></a>Aby identyfikować i uwierzytelniać urządzenia w sieciach lokalnych i/lub rozległych, w tym w sieciach bezprzewodowych, &nbsp; system informacji korzysta z (i) udostępnionego rozwiązania informacji lub (II) rozwiązania do uwierzytelniania w organizacji, dokładnego wyboru i siły, które są zależne od kategoryzacji zabezpieczeń systemu informacji.

**ID**: 0861.09 m2organizational. 67-09. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[App Service powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Ta zasada przeprowadza inspekcję wszelkich App Service nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń "dostęp do sieci"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ff60f98-7fa4-410a-9f7f-0b00f5afdbdd) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "Opcje zabezpieczeń-dostęp do sieci", w tym dostęp użytkowników anonimowych, kont lokalnych i dostępu zdalnego do rejestru. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_AINE.json) |

### <a name="the-organization-ensures-information-systems-protect-the-confidentiality-and-integrity-of-transmitted-information-including-during-preparation-for-transmission-and-during-reception"></a>Organizacja zapewnia ochronę poufności i integralności przesyłanych informacji, w tym podczas przygotowania do przesyłania i podczas odbioru.

**ID**: 0862.09 m2organizational. 8-09. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[SQL Server powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Ta zasada przeprowadza inspekcję wszelkich SQL Server nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |

### <a name="the-organization-builds-a-firewall-configuration-that-restricts-connections-between-un-trusted-networks-and-any-system-components-in-the-covered-information-environment-and-any-changes-to-the-firewall-configuration-are-updated-in-the-network-diagram"></a>Organizacja tworzy konfigurację zapory, która ogranicza połączenia między niezaufanymi sieciami i wszelkimi składnikami systemowymi w środowisku informacji objętych usługą. wszystkie zmiany w konfiguracji zapory są aktualizowane na diagramie sieciowym.

**ID**: 0863.09 m2organizational. 910-09. m **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Centrum zdarzeń powinno używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Ta zasada przeprowadza inspekcję wszystkich centrów zdarzeń, które nie są skonfigurowane do używania punktu końcowego usługi sieci wirtualnej. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

### <a name="usage-restrictions-and-implementation-guidance-are-formally-defined-for-voip-including-the-authorization-and-monitoring-of-the-service"></a>Ograniczenia dotyczące użycia i wskazówki dotyczące implementacji są definiowane jako informacje o technologii VoIP, w tym o autoryzacji i monitorowaniu usługi.

**ID**: 0864.09 m2organizational. 12-09. m **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Cosmos DB powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ta zasada przeprowadza inspekcję wszelkich Cosmos DB nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

### <a name="the-organization-i-authorizes-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-through-the-use-of-interconnection-security-agreements-or-other-formal-agreement-ii-documents-each-connection-the-interface-characteristics-security-requirements-and-the-nature-of-the-information-communicated-iii-employs-a-deny-all-permit-by-exception-policy-for-allowing-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-and-iv-applies-a-default-deny-rule-that-drops-all-traffic-via-host-based-firewalls-or-port-filtering-tools-on-its-endpoints-workstations-servers-etc-except-those-services-and-ports-that-are-explicitly-allowed"></a>Organizacja (i) autoryzuje połączenia od systemu informacji do innych systemów informacyjnych poza organizacją przy użyciu umów dotyczących zabezpieczeń połączeń lub innych umów formalnych; (II) dokumentuje każde połączenie, charakterystykę interfejsu, wymagania dotyczące zabezpieczeń oraz charakter przekazywanych informacji; (III) stosuje zasadę Odmów wszystkim, zezwól przez zasady wyjątku, aby zezwalać na połączenia z systemu informacji do innych systemów informacyjnych poza organizacją; i (IV) stosuje domyślną regułę odmowy, która odrzuca cały ruch za pośrednictwem zapór opartych na hoście lub narzędzi filtrowania portów w punktach końcowych (stacjach roboczych, serwerach itp.), z wyjątkiem tych usług i portów, które są jawnie dozwolone.

**ID**: 0865.09 m2organizational. 13-09. m **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Ta zasada przeprowadza inspekcję wszelkich Key Vault nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |

### <a name="the-organization-describes-the-groups-roles-and-responsibilities-for-the-logical-management-of-network-components-and-ensures-coordination-of-and-consistency-in-the-elements-of-the-network-infrastructure"></a>Organizacja opisuje grupy, role i obowiązki dla logicznego zarządzania składnikami sieci oraz zapewnia koordynację i spójność elementów infrastruktury sieciowej.

**ID**: 0866.09 m3organizational. 1516-09. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konta magazynu powinny ograniczać dostęp do sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Dostęp sieciowy do kont magazynu powinien być ograniczony. Skonfiguruj reguły sieciowe, tak aby tylko aplikacje z dozwolonych sieci mogły uzyskiwać dostęp do konta magazynu. Aby zezwolić na połączenia z określonych klientów internetowych lub lokalnych, można udzielić dostępu do ruchu z określonych sieci wirtualnych platformy Azure lub do publicznych zakresów adresów IP w Internecie |Inspekcja, Odmów, wyłączone |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="wireless-access-points-are-placed-in-secure-areas-and-shut-down-when-not-in-use-eg-nights-weekends"></a>Punkty dostępu bezprzewodowego są umieszczane w bezpiecznych obszarach i zamykane, gdy nie są używane (np. nocy, weekendy).

**ID**: 0867.09 m3organizational. 17-09. m **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Konta magazynu powinny używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Ta zasada przeprowadza inspekcję wszystkich kont magazynu nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |

### <a name="the-organization-builds-a-firewall-configuration-to-restrict-inbound-and-outbound-traffic-to-that-which-is-necessary-for-the-covered-data-environment"></a>Organizacja tworzy konfigurację zapory w celu ograniczenia ruchu przychodzącego i wychodzącego do programu, który jest niezbędny dla środowiska danych objętych usługą.

**ID**: 0868.09 m3organizational. 18-09. m **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ta zasada przeprowadza inspekcję wszelkich Container Registry nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="the-router-configuration-files-are-secured-and-synchronized"></a>Pliki konfiguracji routera są zabezpieczone i synchronizowane.

**ID**: 0869.09 m3organizational. 19-09. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ta zasada przeprowadza inspekcję wszelkich Container Registry nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="access-to-all-proxies-is-denied-except-for-those-hosts-ports-and-services-that-are-explicitly-required"></a>Odmowa dostępu do wszystkich serwerów proxy, z wyjątkiem tych hostów, portów i usług, które są jawnie wymagane.

**ID**: 0870.09 m3organizational. 20-09. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ta zasada przeprowadza inspekcję wszelkich Container Registry nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="authoritative-dns-servers-are-segregated-into-internal-and-external-roles"></a>Autorytatywne serwery DNS są podzielone na role wewnętrzne i zewnętrzne.

**ID**: 0871.09 m3organizational. 22-09. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Container Registry powinien używać punktu końcowego usługi sieci wirtualnej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Ta zasada przeprowadza inspekcję wszelkich Container Registry nieskonfigurowanych do korzystania z punktu końcowego usługi sieci wirtualnej. |Inspekcja, wyłączona |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="security-of-network-services"></a>Bezpieczeństwo Network Services

### <a name="agreed-services-provided-by-a-network-service-providermanager-are-formally-managed-and-monitored-to-ensure-they-are-provided-securely"></a>Zatwierdzone usługi udostępniane przez dostawcę/Menedżera usług sieciowych są zarządzane i monitorowane w celu zapewnienia, że są one bezpieczne.

**ID**: 0835.09 n1organizational. 1-09. n **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Agent zbierania danych o ruchu w sieci powinien być zainstalowany na maszynach wirtualnych z systemem Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center używa programu Microsoft Dependency Agent do zbierania danych o ruchu sieciowym z maszyn wirtualnych platformy Azure w celu włączenia zaawansowanych funkcji ochrony sieci, takich jak wizualizacja ruchu na mapie sieci, zalecenia dotyczące ograniczania funkcjonalności sieci i określone zagrożenia sieci. |AuditIfNotExists, wyłączone |[1.0.1 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |Użyj nowych Azure Resource Manager dla maszyn wirtualnych, aby zapewnić lepsze zabezpieczenia, takie jak: silniejsza kontrola dostępu (RBAC), lepsza Inspekcja, oparte na Azure Resource Manager wdrożenia i zarządzanie, dostęp do tożsamości zarządzanych, dostęp do magazynu kluczy dla wpisów tajnych, uwierzytelnianie oparte na usłudze Azure AD i obsługa tagów i grup zasobów w celu łatwiejszego zarządzania zabezpieczeniami |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="the-organization-formally-authorizes-and-documents-the-characteristics-of-each-connection-from-an-information-system-to-other-information-systems-outside-the-organization"></a>Organizacja formalnie autoryzuje i dokumentuje cechy poszczególnych połączeń z systemu informacji do innych systemów informacyjnych poza organizacją.

**Identyfikator**: 0836.09. n2Organizational. 1-09. n **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Agent zbierania danych o ruchu w sieci powinien być zainstalowany na maszynach wirtualnych z systemem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center używa programu Microsoft Dependency Agent do zbierania danych o ruchu sieciowym z maszyn wirtualnych platformy Azure w celu włączenia zaawansowanych funkcji ochrony sieci, takich jak wizualizacja ruchu na mapie sieci, zalecenia dotyczące ograniczania funkcjonalności sieci i określone zagrożenia sieci. |AuditIfNotExists, wyłączone |[1.0.1 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="formal-agreements-with-external-information-system-providers-include-specific-obligations-for-security-and-privacy"></a>Formalne umowy z zewnętrznymi dostawcami systemów informacyjnych obejmują określone zobowiązania w zakresie zabezpieczeń i ochrony prywatności.

**Identyfikator**: 0837.09. n2Organizational. 2-09. n **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Network Watcher powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci w, do i z platformy Azure. Monitorowanie poziomu scenariusza umożliwia diagnozowanie problemów w widoku na poziomie sieci. Narzędzia do diagnostyki i wizualizacji sieci dostępne w Network Watcher pomagają zrozumieć, zdiagnozować i uzyskiwać wgląd w sieć na platformie Azure. |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-reviews-and-updates-the-interconnection-security-agreements-on-an-ongoing-basis-verifying-enforcement-of-security-requirements"></a>Organizacja przegląda i aktualizuje umowy dotyczące zabezpieczeń połączeń, które regularnie sprawdzają, czy zostały spełnione wymagania dotyczące zabezpieczeń.

**Identyfikator**: 0885.09 n2organizational. 3-09. n **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Agent zbierania danych o ruchu w sieci powinien być zainstalowany na maszynach wirtualnych z systemem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center używa programu Microsoft Dependency Agent do zbierania danych o ruchu sieciowym z maszyn wirtualnych platformy Azure w celu włączenia zaawansowanych funkcji ochrony sieci, takich jak wizualizacja ruchu na mapie sieci, zalecenia dotyczące ograniczania funkcjonalności sieci i określone zagrożenia sieci. |AuditIfNotExists, wyłączone |[1.0.1 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="the-organization-employs-and-documents-in-a-formal-agreement-or-other-document-either-i-allow-all-deny-by-exception-or-ii-deny-all-permit-by-exception-preferred-policy-for-allowing-specific-information-systems-to-connect-to-external-information-systems"></a>Organizacja stosuje i dokumenty w formalnej umowie lub innym dokumencie, a i) Zezwalaj na wszystkie, Odmów na wyjątek lub, II) Odmów-wszystkie, Zezwalaj na wyjątek (preferowany), zasady zezwalające określonym systemom informacyjnym na łączenie się z zewnętrznymi systemami informacji.

**ID**: 0886.09 n2organizational. 4-09. n **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Network Watcher powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci w, do i z platformy Azure. Monitorowanie poziomu scenariusza umożliwia diagnozowanie problemów w widoku na poziomie sieci. Narzędzia do diagnostyki i wizualizacji sieci dostępne w Network Watcher pomagają zrozumieć, zdiagnozować i uzyskiwać wgląd w sieć na platformie Azure. |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-requires-externaloutsourced-service-providers-to-identify-the-specific-functions-ports-and-protocols-used-in-the-provision-of-the-externaloutsourced-services"></a>Organizacja wymaga dostawców usług zewnętrznych/niezwiązanych ze źródłem, aby identyfikować określone funkcje, porty i protokoły używane w ramach udostępniania usług zewnętrznych/z zewnątrz.

**ID**: 0887.09 n2organizational. 5-09. n **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Agent zbierania danych o ruchu w sieci powinien być zainstalowany na maszynach wirtualnych z systemem Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center używa programu Microsoft Dependency Agent do zbierania danych o ruchu sieciowym z maszyn wirtualnych platformy Azure w celu włączenia zaawansowanych funkcji ochrony sieci, takich jak wizualizacja ruchu na mapie sieci, zalecenia dotyczące ograniczania funkcjonalności sieci i określone zagrożenia sieci. |AuditIfNotExists, wyłączone |[1.0.1 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |

### <a name="the-contract-with-the-externaloutsourced-service-provider-includes-the-specification-that-the-service-provider-is-responsible-for-the-protection-of-covered-information-shared"></a>Umowa z dostawcą usług zewnętrznych/zewnętrznymi zawiera specyfikację, którą dostawca usług odpowiada za ochronę udostępnionej informacji.

**Identyfikator**: 0888.09 n2organizational. 6-09. n **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Network Watcher powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci w, do i z platformy Azure. Monitorowanie poziomu scenariusza umożliwia diagnozowanie problemów w widoku na poziomie sieci. Narzędzia do diagnostyki i wizualizacji sieci dostępne w Network Watcher pomagają zrozumieć, zdiagnozować i uzyskiwać wgląd w sieć na platformie Azure. |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="management-of-removable-media"></a>Zarządzanie nośnikiem wymiennym

### <a name="the-organization-based-on-the-data-classification-level-registers-media-including-laptops-prior-to-use-places-reasonable-restrictions-on-how-such-media-be-used-and-provides-an-appropriate-level-of-physical-and-logical-protection-including-encryption-for-media-containing-covered-information-until-properly-destroyed-or-sanitized"></a>W organizacji, na podstawie poziomu klasyfikacji danych, rejestruje nośniki (w tym laptopy) przed użyciem, umieszcza uzasadnione ograniczenia dotyczące tego nośnika, a także zapewnia odpowiedni poziom ochrony fizycznej i logicznej (w tym szyfrowania) na nośniku zawierającym informacje objęte usługą, dopóki nie zostaną odpowiednio zniszczone lub oczyszczone.

**ID**: 0301.09 o1organizational. 123-09. o **własności**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć Transparent Data Encryption baz danych SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Należy włączyć funkcję przezroczystego szyfrowania danych w celu ochrony danych w czasie spoczynku i spełnienia wymagań dotyczących zgodności. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="the-organization-protects-and-controls-media-containing-sensitive-information-during-transport-outside-of-controlled-areas"></a>Organizacja chroni i kontroluje multimedia zawierające informacje poufne podczas transportu poza obszary kontrolowane.

**ID**: 0302.09 o2organizational. 1-09. o **własności**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Maszyny wirtualne bez włączonego szyfrowania dysków będą monitorowane przez Azure Security Center jako zalecenia. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="digital-and-non-digital-media-requiring-restricted-use-and-the-specific-safeguards-used-to-restrict-their-use-are-identified"></a>Nośniki cyfrowe i niecyfrowe wymagające ograniczonego użycia i określonych zabezpieczeń, aby ograniczyć ich użycie.

**Identyfikator**: 0303.09 o2organizational. 2-09. o **własności**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Niedołączone dyski powinny być szyfrowane](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Ta zasada przeprowadza inspekcję wszelkich niedołączonych dysków bez włączonego szyfrowania. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="the-organization-restricts-the-use-of-writable-removable-media-and-personally-owned-removable-media-in-organizational-systems"></a>Organizacja ogranicza korzystanie z zapisywalnych nośników wymiennych i prywatnych nośników wymiennych w systemach organizacji.

**ID**: 0304.09 o3organizational. 1-09. o **własności**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Wymagaj szyfrowania na kontach Data Lake Store](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Te zasady zapewniają, że szyfrowanie jest włączone na wszystkich kontach Data Lake Store |pozbawić |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[Funkcja ochrony TDE wystąpienia zarządzanego SQL powinna być szyfrowana przy użyciu własnego klucza](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Transparent Data Encryption (TDE) z własną kluczową obsługą zapewnia zwiększoną przejrzystość i kontrolę nad ochroną TDE, zwiększoną ochronę za pomocą usługi zewnętrznej z obsługą modułu HSM i promocją rozdzielenia obowiązków. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Funkcja ochrony programu SQL Server TDE powinna być szyfrowana przy użyciu własnego klucza](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Transparent Data Encryption (TDE) z własną kluczową obsługą zapewnia zwiększoną przejrzystość i kontrolę nad ochroną TDE, zwiększoną ochronę za pomocą usługi zewnętrznej z obsługą modułu HSM i promocją rozdzielenia obowiązków. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="information-exchange-policies-and-procedures"></a>Zasady i procedury wymiany informacji

### <a name="cloud-service-providers-use-an-industry-recognized-virtualization-platform-and-standard-virtualization-formats-eg-open-virtualization-format-ovf-to-help-ensure-interoperability-and-has-documented-custom-changes-made-to-any-hypervisor-in-use-and-all-solution-specific-virtualization-hooks-available-for-customer-review"></a>Dostawcy usług w chmurze wykorzystują platformę wirtualizacji rozpoznaną przez branżę i standardowe formaty wirtualizacji (np. Open Virtualization Format, OVF), aby pomóc w zapewnieniu współdziałania i udokumentowania niestandardowymi zmianami wprowadzonymi do dowolnych funkcji hypervisor i wszystkich punktów zaczepienia wirtualizacji określonych dla danego rozwiązania.

**ID**: 0662.09 scsporganizational. 2-09. s **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Upewnij się, że aplikacja sieci WEB ma "certyfikaty klienta (przychodzące certyfikaty klienta") "on"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Certyfikaty klienta umożliwiają aplikacji żądanie certyfikatu dla żądań przychodzących. Tylko klienci z prawidłowym certyfikatem będą mogli uzyskać dostęp do aplikacji. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-organization-formally-addresses-multiple-safeguards-before-allowing-the-use-of-information-systems-for-information-exchange"></a>Organizacja w sposób niezgodny z wieloma zabezpieczeniami przed zezwoleniem na korzystanie z systemów informacyjnych w celu wymiany informacji.

**ID**: 0901.09 s1organizational. 1-09. s **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Mechanizm CORS nie powinien zezwalać na dostęp do aplikacji internetowych ze wszystkich zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Współużytkowanie zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji sieci Web. Zezwalaj na współpracującie z aplikacją sieci Web tylko wymaganych domen. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="remote-external-access-to-the-organizations-information-assets-and-access-to-external-information-assets-for-which-the-organization-has-no-control-is-based-on-clearly-defined-terms-and-conditions"></a>Dostęp zdalny (zewnętrzny) do zasobów informacji organizacji i dostęp do zewnętrznych zasobów informacji (dla których organizacja nie ma kontroli) jest oparty na jasno określonych warunkach.

**ID**: 0902.09 s2organizational. 13-09. s — **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Współużytkowanie zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji funkcji. Zezwalaj na współdziałanie z aplikacją funkcji tylko dla wymaganych domen. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="the-organization-establishes-terms-and-conditions-consistent-with-any-trust-relationship-established-with-other-organizations-owning-operating-andor-maintaining-external-information-systems-allowing-authorized-individuals-to-i-access-the-information-system-from-external-information-systems-and-ii-process-store-or-transmit-organization-controlled-information-using-external-information-systems"></a>Organizacja ustanawia warunki i postanowienia, które są spójne z jakąkolwiek relacją zaufania ustanowioną z innymi organizacjami, które są właścicielami, operacyjnymi i/lub obsługą zewnętrznych systemów informacyjnych, umożliwiając autoryzowanym osobom dostęp do systemu informacji z zewnętrznych systemów informacyjnych. i (II) Przetwarzaj, przechowuj lub przesyłaj informacje kontrolowane przez organizację przy użyciu zewnętrznych systemów informacyjnych.

**ID**: 0911.09 s1organizational. 2-09. s **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |Współużytkowanie zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji interfejsu API. Zezwalaj na współpracującie z aplikacją interfejsu API tylko wymagane domeny. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |

### <a name="cryptography-is-used-to-protect-the-confidentiality-and-integrity-of-remote-access-sessions-to-the-internal-network-and-to-external-systems"></a>Kryptografia jest używana do ochrony poufności i integralności sesji dostępu zdalnego do sieci wewnętrznej i systemów zewnętrznych.

**ID**: 0912.09 s1organizational. 4-09. s — **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Debugowanie zdalne powinno być wyłączone dla aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Zdalne debugowanie wymaga otwarcia portów przychodzących w aplikacji sieci Web. Debugowanie zdalne powinno zostać wyłączone. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="strong-cryptography-protocols-are-used-to-safeguard-covered-information-during-transmission-over-less-trusted--open-public-networks"></a>Mocne protokoły kryptograficzne są używane do ochrony informacji uwzględnionych podczas przesyłania przez mniej zaufane/otwarte sieci publiczne.

**ID**: 0913.09 s1organizational. 5-09. s **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Zdalne debugowanie wymaga otwarcia portów przychodzących w aplikacjach funkcji. Debugowanie zdalne powinno zostać wyłączone. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-ensures-that-communication-protection-requirements-including-the-security-of-exchanges-of-information-is-the-subject-of-policy-development-and-compliance-audits"></a>Organizacja gwarantuje, że wymagania dotyczące ochrony komunikacji, w tym bezpieczeństwo wymiany informacji, są przedmiotem kontroli nad projektowaniem zasad i zgodnością.

**ID**: 0914.09 s1organizational. 6-09. s — **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zdalne debugowanie powinno zostać wyłączone dla API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Zdalne debugowanie wymaga otwarcia portów przychodzących w aplikacjach interfejsu API. Debugowanie zdalne powinno zostać wyłączone. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-limits-the-use-of-organization-controlled-portable-storage-media-by-authorized-individuals-on-external-information-systems"></a>Organizacja ogranicza użycie przenośnych nośników magazynu chronionych przez organizację przez autoryzowanych użytkowników w zewnętrznych systemach informacyjnych.

**Identyfikator**: 0915.09 s2organizational. 2-09 **. s:** nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Upewnij się, że aplikacja sieci WEB ma "certyfikaty klienta (przychodzące certyfikaty klienta") "on"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Certyfikaty klienta umożliwiają aplikacji żądanie certyfikatu dla żądań przychodzących. Tylko klienci z prawidłowym certyfikatem będą mogli uzyskać dostęp do aplikacji. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-information-system-prohibits-remote-activation-of-collaborative-computing-devices-and-provides-an-explicit-indication-of-use-to-users-physically-present-at-the-devices"></a>System informacji zabrania zdalnej aktywacji współpracujących urządzeń komputerowych i zapewnia jawne wskazanie użycia użytkownikom fizycznie na urządzeniach.

**Identyfikator**: 0916.09 s2organizational. 4-09 **. s:** nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Mechanizm CORS nie powinien zezwalać na dostęp do aplikacji internetowych ze wszystkich zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Współużytkowanie zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji sieci Web. Zezwalaj na współpracującie z aplikacją sieci Web tylko wymaganych domen. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="cloud-service-providers-use-secure-eg-non-clear-text-and-authenticated-standardized-network-protocols-for-the-import-and-export-of-data-and-to-manage-the-service-and-make-available-a-document-to-consumers-tenants-detailing-the-relevant-interoperability-and-portability-standards-that-are-involved"></a>Dostawcy usług w chmurze używają zabezpieczeń (np. niezwykły tekst i uwierzytelniany) standardowych protokołów sieciowych do importowania i eksportowania danych oraz zarządzania usługą, a następnie udostępniają użytkownikom (dzierżawców) szczegółowe informacje dotyczące odpowiednich współdziałań i standardów przenoszenia.

**ID**: 0960.09 scsporganizational. 1-09. s **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |Współużytkowanie zasobów między źródłami (CORS) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji funkcji. Zezwalaj na współdziałanie z aplikacją funkcji tylko dla wymaganych domen. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="personnel-are-appropriately-trained-on-leading-principles-and-practices-for-all-types-of-information-exchange-oral-paper-and-electronic"></a>Personel jest odpowiednio szkolony w przypadku najważniejszych zasad i praktyk dla wszystkich rodzajów wymiany informacji (doustnie, papier i elektroniczny).

**ID**: 1325.09 s1organizational. 3-09. s — **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Zdalne debugowanie wymaga otwarcia portów przychodzących w aplikacjach funkcji. Debugowanie zdalne powinno zostać wyłączone. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

## <a name="on-line-transactions"></a>Transakcje online

### <a name="data-involved-in-electronic-commerce-and-online-transactions-is-checked-to-determine-if-it-contains-covered-information"></a>Dane związane z handlem elektronicznym i transakcjami online są sprawdzane w celu ustalenia, czy zawierają informacje objęte usługą.

**ID**: 0943.09 y1organizational. 1-09. y **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Inspekcja wymagania bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="protocols-used-to-communicate-between-all-involved-parties-are-secured-using-cryptographic-techniques-eg-ssl"></a>Protokoły używane do komunikacji między wszystkimi zainteresowanymi stronami są zabezpieczone przy użyciu technik kryptograficznych (np. SSL).

**ID**: 0945.09 y1organizational. 3-09. y **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja maszyn z systemem Windows, które nie zawierają określonych certyfikatów w zaufanym katalogu głównym](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F934345e1-4dfb-4c70-90d7-41990dc9608b) |Wymaga, aby warunki wstępne zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli magazyn zaufanych certyfikatów komputera (CERT: \ LocalMachine\Root) nie zawiera co najmniej jednego certyfikatu wymienionego przez parametr Policy. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot_AINE.json) |

### <a name="the-organization-requires-the-use-of-encryption-between-and-the-use-of-electronic-signatures-by-each-of-the-parties-involved-in-the-transaction"></a>Organizacja wymaga użycia szyfrowania między, a korzystanie z podpisów elektronicznych przez, każda ze stron uwzględnionych w transakcji.

**ID**: 0946.09 y2organizational. 14-09. y **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć tylko bezpieczne połączenia z usługą Azure cache for Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączenia tylko połączeń za pośrednictwem protokołu SSL do usługi Azure cache for Redis. Użycie bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane z ataków warstwy sieciowej, takich jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmów, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

### <a name="the-organization-ensures-the-storage-of-the-transaction-details-are-located-outside-of-any-publicly-accessible-environments-eg-on-a-storage-platform-existing-on-the-organizations-intranet-and-not-retained-and-exposed-on-a-storage-medium-directly-accessible-from-the-internet"></a>Organizacja gwarantuje przechowywanie szczegółowych informacji o transakcji poza wszelkimi publicznie dostępnymi środowiskami (np. na platformie magazynów istniejących w intranecie organizacji) i nie są przechowywane i udostępniane na nośniku magazynującym bezpośrednio dostępnym z Internetu.

**ID**: 0947.09 y2organizational. 2-09. y **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL obsługuje łączenie serwera Azure Database for PostgreSQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="where-a-trusted-authority-is-used-eg-for-the-purposes-of-issuing-and-maintaining-digital-signatures-andor-digital-certificates-security-is-integrated-and-embedded-throughout-the-entire-end-to-end-certificatesignature-management-process"></a>W przypadku korzystania z zaufanego urzędu (np. w celu wystawiania i utrzymywania podpisów cyfrowych i/lub certyfikatów cyfrowych) zabezpieczenia są zintegrowane i wbudowane w całym całym procesie zarządzania certyfikatami/sygnaturami.

**ID**: 0948.09 y2organizational. 3-09. y **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL obsługuje łączenie serwera Azure Database for MySQL z aplikacjami klienckimi przy użyciu SSL (SSL). Wymuszanie połączeń SSL między serwerem bazy danych a aplikacjami klienckimi pomaga chronić przed atakami typu man-in-the-Middle przez szyfrowanie strumienia danych między serwerem a aplikacją. Ta konfiguracja wymusza, że protokół SSL jest zawsze włączony do uzyskiwania dostępu do serwera bazy danych. |Inspekcja, wyłączona |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-protocols-used-for-communications-are-enhanced-to-address-any-new-vulnerability-and-the-updated-versions-of-the-protocols-are-adopted-as-soon-as-possible"></a>Protokoły używane do komunikacji zostały udoskonalone w celu rozwiązania wszelkich nowych luk w zabezpieczeniach, a zaktualizowane wersje protokołów są wdrażane tak szybko, jak to możliwe.

**ID**: 0949.09 y2organizational. 5-09. y **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacja funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Najnowsza wersja protokołu TLS powinna być używana w aplikacji sieci Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Uaktualnij do najnowszej wersji protokołu TLS |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Użycie protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami polegającymi na podsłuchu w warstwie sieciowej. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="control-of-operational-software"></a>Kontrola oprogramowania operacyjnego

### <a name="only-authorized-administrators-are-allowed-to-implement-approved-upgrades-to-software-applications-and-program-libraries-based-on-business-requirements-and-the-security-implications-of-the-release"></a>Tylko autoryzowani administratorzy mogą wdrażać zatwierdzone uaktualnienia do oprogramowania, aplikacji i bibliotek programów w zależności od wymagań firmy i skutków związanych z bezpieczeństwem wersji.

**Identyfikator**: 0605.10 h1system. 12-10. h **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Serwery, które nie spełniają skonfigurowanej linii bazowej, będą monitorowane przez Azure Security Center jako zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń-Audit](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33936777-f2ac-45aa-82ec-07958ec9ade4) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "Opcje zabezpieczeń-Audit" dla wymuszania podkategorii zasad inspekcji i zamykania, jeśli nie można rejestrować inspekcji zabezpieczeń. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAudit_AINE.json) |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — zarządzanie kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94d9aca8-3757-46df-aa51-f218c5f11954) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "zasady inspekcji systemu — Zarządzanie kontem" do inspekcji aplikacji, zabezpieczeń i zarządzania grupami użytkowników oraz innych zdarzeń zarządzania. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesAccountManagement_AINE.json) |

### <a name="applications-and-operating-systems-are-successfully-tested-for-usability-security-and-impact-prior-to-production"></a>Aplikacje i systemy operacyjne zostały pomyślnie przetestowane pod kątem użyteczności, bezpieczeństwa i wpływu przed środowiskiem produkcyjnym.

**ID**: 0606.10 h2system. 1-10. h **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Inspekcja luk w zabezpieczeniach na maszynach z zainstalowanym systemem Docker i wyświetlanie ich jako rekomendacji w Azure Security Center. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-uses-its-configuration-control-program-to-maintain-control-of-all-implemented-software-and-its-system-documentation-and-archive-prior-versions-of-implemented-software-and-associated-system-documentation"></a>Organizacja używa programu kontroli konfiguracji w celu zapewnienia kontroli nad wszystkimi zaimplementowanym oprogramowaniem i jego dokumentacją systemową oraz archiwizowania wcześniejszych wersji zaimplementowanego oprogramowania i dokumentacji systemu.

**Identyfikator**: 0607.10 h2system. 23-10. h **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Na maszynach powinny być włączone adaptacyjne kontrole aplikacji umożliwiające Definiowanie bezpiecznych aplikacji.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Włącz kontrolki aplikacji, aby zdefiniować listę znanych bezpiecznych aplikacji uruchomionych na maszynach, i Zgłoś alert, gdy inne aplikacje zostaną uruchomione. Pozwala to na zabezpieczanie maszyn przed złośliwym oprogramowaniem. Aby uprościć proces konfigurowania i utrzymywania reguł, Security Center korzysta z uczenia maszynowego do analizowania aplikacji uruchomionych na poszczególnych maszynach i sugerują listę znanych bezpiecznych aplikacji. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Przeprowadź inspekcję luk w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

## <a name="change-control-procedures"></a>Procedury kontroli zmian

### <a name="managers-responsible-for-application-systems-are-also-responsible-for-the-strict-control-security-of-the-project-or-support-environment-and-ensure-that-all-proposed-system-changes-are-reviewed-to-check-that-they-do-not-compromise-the-security-of-either-the-system-or-the-operating-environment"></a>Menedżerowie odpowiedzialni za systemy aplikacji są również odpowiedzialni za ścisłą kontrolę (bezpieczeństwo) projektu lub środowiska pomocy technicznej i gwarantują, że wszystkie proponowane zmiany systemu są sprawdzane w celu sprawdzenia, czy nie naruszają zabezpieczeń systemu ani środowiska operacyjnego.

**Identyfikator**: 0635.10 k1organizational. 12-10. k **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "zasady inspekcji systemu — szczegółowe śledzenie" na potrzeby inspekcji DPAPI, tworzenia procesów/kończenia, zdarzeń zdalnego wywoływania procedur oraz działania PNP. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-formally-addresses-purpose-scope-roles-responsibilities-management-commitment-coordination-among-organizational-entities-and-compliance-for-configuration-management-eg-through-policies-standards-processes"></a>Organizacja tworzy adresy, zakres, role, obowiązki, zobowiązania dotyczące zarządzania, koordynację między jednostkami organizacyjnymi i zgodność z zarządzaniem konfiguracją (np. za pomocą zasad, standardów, procesów).

**ID**: 0636.10 k2organizational. 1-10. k **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "zasady inspekcji systemu — szczegółowe śledzenie" na potrzeby inspekcji DPAPI, tworzenia procesów/kończenia, zdarzeń zdalnego wywoływania procedur oraz działania PNP. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-has-developed-documented-and-implemented-a-configuration-management-plan-for-the-information-system"></a>Organizacja opracowała, udokumentować i zaimplementował plan zarządzania konfiguracją dla systemu informacji.

**ID**: 0637.10 k2organizational. 2-10. k **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "zasady inspekcji systemu — szczegółowe śledzenie" na potrzeby inspekcji DPAPI, tworzenia procesów/kończenia, zdarzeń zdalnego wywoływania procedur oraz działania PNP. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="changes-are-formally-controlled-documented-and-enforced-in-order-to-minimize-the-corruption-of-information-systems"></a>Zmiany są formalnie kontrolowane, udokumentowane i wymuszane w celu zminimalizowania uszkodzenia systemów informacyjnych.

**ID**: 0638.10 k2organizational. 34569-10. k **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "zasady inspekcji systemu — szczegółowe śledzenie" na potrzeby inspekcji DPAPI, tworzenia procesów/kończenia, zdarzeń zdalnego wywoływania procedur oraz działania PNP. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="installation-checklists-and-vulnerability-scans-are-used-to-validate-the-configuration-of-servers-workstations-devices-and-appliances-and-ensure-the-configuration-meets-minimum-standards"></a>Listy kontrolne instalacji i skanowania w poszukiwaniu luk w zabezpieczeniach są używane do sprawdzania poprawności konfiguracji serwerów, stacji roboczych i urządzeń oraz zapewnienia, że konfiguracja spełnia minimalne standardy.

**ID**: 0639.10 k2organizational. 78-10. k **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "zasady inspekcji systemu — szczegółowe śledzenie" na potrzeby inspekcji DPAPI, tworzenia procesów/kończenia, zdarzeń zdalnego wywoływania procedur oraz działania PNP. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="where-development-is-outsourced-change-control-procedures-to-address-security-are-included-in-the-contracts-and-specifically-require-the-developer-to-track-security-flaws-and-flaw-resolution-within-the-system-component-or-service-and-report-findings-to-organization-defined-personnel-or-roles"></a>W przypadku, gdy programowanie jest źródłem, procedury kontroli zmian dotyczące zabezpieczeń są zawarte w umowach i wymagają, aby deweloper śledził wady zabezpieczeń i rozwiązywać wady w ramach systemu, składnika lub usługi, a także zgłaszać wyniki do personelu lub ról zdefiniowanych przez organizację.

**ID**: 0640.10 k2organizational. 1012-10. y **własność**: nie dotyczy

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "zasady inspekcji systemu — szczegółowe śledzenie" na potrzeby inspekcji DPAPI, tworzenia procesów/kończenia, zdarzeń zdalnego wywoływania procedur oraz działania PNP. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-does-not-use-automated-updates-on-critical-systems"></a>Organizacja nie korzysta z automatycznych aktualizacji w systemach krytycznych.

**ID**: 0641.10 k2organizational. 11-10. k **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "zasady inspekcji systemu — szczegółowe śledzenie" na potrzeby inspekcji DPAPI, tworzenia procesów/kończenia, zdarzeń zdalnego wywoływania procedur oraz działania PNP. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-develops-documents-and-maintains-under-configuration-control-a-current-baseline-configuration-of-the-information-system-and-reviews-and-updates-the-baseline-as-required"></a>Organizacja opracowuje, dokumentuje i utrzymuje w ramach kontroli konfiguracji, bieżącą konfigurację linii bazowej systemu informacji oraz przegląda i aktualizuje linię bazową zgodnie z wymaganiami.

**Identyfikator**: 0642.10 k3organizational. 12-10. k **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "zasady inspekcji systemu — szczegółowe śledzenie" na potrzeby inspekcji DPAPI, tworzenia procesów/kończenia, zdarzeń zdalnego wywoływania procedur oraz działania PNP. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-i-establishes-and-documents-mandatory-configuration-settings-for-information-technology-products-employed-within-the-information-system-using-the-latest-security-configuration-baselines-ii-identifies-documents-and-approves-exceptions-from-the-mandatory-established-configuration-settings-for-individual-components-based-on-explicit-operational-requirements-and-iii-monitors-and-controls-changes-to-the-configuration-settings-in-accordance-with-organizational-policies-and-procedures"></a>Organizacja (i) ustanawia i dokumentuje obowiązkowe ustawienia konfiguracji dla produktów technologii informatycznych używanych w systemie informacyjnym przy użyciu najnowszych linii bazowych konfiguracji zabezpieczeń. (II) identyfikuje, dokumenty i zatwierdza wyjątki od obowiązkowych ustanowionych ustawień konfiguracji dla poszczególnych składników na podstawie jawnych wymagań operacyjnych; i (III) monitoruje i kontroluje zmiany ustawień konfiguracji zgodnie z zasadami i procedurami organizacyjnymi.

**ID**: 0643.10 k3organizational. 3-10. k **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "zasady inspekcji systemu — szczegółowe śledzenie" na potrzeby inspekcji DPAPI, tworzenia procesów/kończenia, zdarzeń zdalnego wywoływania procedur oraz działania PNP. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-employs-automated-mechanisms-to-i-centrally-manage-apply-and-verify-configuration-settings-ii-respond-to-unauthorized-changes-to-network-and-system-security-related-configuration-settings-and-iii-enforce-access-restrictions-and-auditing-of-the-enforcement-actions"></a>Organizacja używa zautomatyzowanych mechanizmów do (i) centralnie zarządzać, stosować i weryfikować ustawienia konfiguracji; (II) Odpowiedz na nieautoryzowane zmiany ustawień konfiguracji związanych z zabezpieczeniami sieci i systemu; i (III) Wymuś ograniczenia dostępu i inspekcję działań wymuszania.

**ID**: 0644.10 k3organizational. 4-10. k **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — szczegółowe śledzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "zasady inspekcji systemu — szczegółowe śledzenie" na potrzeby inspekcji DPAPI, tworzenia procesów/kończenia, zdarzeń zdalnego wywoływania procedur oraz działania PNP. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

## <a name="control-of-technical-vulnerabilities"></a>Kontrola nad lukami w zabezpieczeniach

### <a name="technical-vulnerabilities-are-identified-evaluated-for-risk-and-corrected-in-a-timely-manner"></a>Luki w zabezpieczeniach są identyfikowane, oceniane pod kątem ryzyka i korygowane w odpowiednim czasie.

**ID**: 0709.10 m1organizational. 1-10. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rozwiązanie do oceny luk w zabezpieczeniach powinno być włączone na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Przeprowadza inspekcję maszyn wirtualnych w celu wykrycia, czy są uruchomione obsługiwane rozwiązanie do oceny luk w zabezpieczeniach. Podstawowym składnikiem każdego cybernetycznymi ryzyka i programu zabezpieczeń jest identyfikacja i analiza luk w zabezpieczeniach. Standardowa warstwa cenowa Azure Security Center obejmuje skanowanie w poszukiwaniu luk w zabezpieczeniach maszyn wirtualnych bez dodatkowych kosztów. Ponadto Security Center może automatycznie wdrożyć to narzędzie. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Inspekcja luk w zabezpieczeniach na maszynach z zainstalowanym systemem Docker i wyświetlanie ich jako rekomendacji w Azure Security Center. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Serwery, które nie spełniają skonfigurowanej linii bazowej, będą monitorowane przez Azure Security Center jako zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Przeprowadź inspekcję luk w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Luki w zabezpieczeniach baz danych SQL należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Monitoruj wyniki skanowania oceny luk w zabezpieczeniach i zalecenia dotyczące sposobu korygowania luk w zabezpieczeniach bazy danych. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Wykrywa luki w zabezpieczeniach wykryte przez rozwiązanie do oceny luk w zabezpieczeniach i maszyny wirtualne bez rozwiązania do oceny luk w zabezpieczeniach Azure Security Center jak zalecenia. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |
|[Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniu zarządzanym SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Przeprowadź inspekcję każdego wystąpienia zarządzanego SQL, które nie ma włączonych skanów oceny luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może wykrywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Inspekcja serwerów SQL platformy Azure, które nie mają włączonej cyklicznej oceny ocen luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może wykrywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń — serwer sieci firmy Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcaf2d518-f029-4f6b-833b-d7081702f253) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "Opcje zabezpieczeń — serwer sieci firmy Microsoft", aby wyłączyć serwer SMB v1. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_AINE.json) |

### <a name="a-hardened-configuration-standard-exists-for-all-system-and-network-components"></a>Istnieje zaostrzony Standard konfiguracji dla wszystkich składników systemowych i sieciowych.

**ID**: 0710.10 m2organizational. 1-10. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniu zarządzanym SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Przeprowadź inspekcję każdego wystąpienia zarządzanego SQL, które nie ma włączonych skanów oceny luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może wykrywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

### <a name="a-technical-vulnerability-management-program-is-in-place-to-monitor-assess-rank-and-remediate-vulnerabilities-identified-in-systems"></a>Program zarządzania lukami w zabezpieczeniach jest gotowy do monitorowania, oceniania, określania rangi i korygowania luk w zabezpieczeniach.

**ID**: 0711.10 m2organizational. 23-10. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Rozwiązanie do oceny luk w zabezpieczeniach powinno być włączone na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Przeprowadza inspekcję maszyn wirtualnych w celu wykrycia, czy są uruchomione obsługiwane rozwiązanie do oceny luk w zabezpieczeniach. Podstawowym składnikiem każdego cybernetycznymi ryzyka i programu zabezpieczeń jest identyfikacja i analiza luk w zabezpieczeniach. Standardowa warstwa cenowa Azure Security Center obejmuje skanowanie w poszukiwaniu luk w zabezpieczeniach maszyn wirtualnych bez dodatkowych kosztów. Ponadto Security Center może automatycznie wdrożyć to narzędzie. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="internal-and-external-vulnerability-assessments-of-covered-information-systems-virtualized-environments-and-networked-environments-including-both-network--and-application-layer-tests-are-performed-by-a-qualified-individual-on-a-quarterly-basis-or-after-significant-changes"></a>Podstawowe i zewnętrzne oceny luk w zabezpieczeniach objętych usługą, zwirtualizowane środowiska i środowiska sieciowe, w tym testy warstwowe i warstwy aplikacji, są wykonywane przez zakwalifikowanych co kwartał lub po znaczących zmianach.

**ID**: 0712.10 m2organizational. 4-10. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Wykrywa luki w zabezpieczeniach wykryte przez rozwiązanie do oceny luk w zabezpieczeniach i maszyny wirtualne bez rozwiązania do oceny luk w zabezpieczeniach Azure Security Center jak zalecenia. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

### <a name="patches-are-tested-and-evaluated-before-they-are-installed"></a>Poprawki są testowane i oceniane przed ich zainstalowaniem.

**ID**: 0713.10 m2organizational. 5-10. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Serwery, które nie spełniają skonfigurowanej linii bazowej, będą monitorowane przez Azure Security Center jako zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-technical-vulnerability-management-program-is-evaluated-on-a-quarterly-basis"></a>Program zarządzania lukami w zabezpieczeniach jest oceniany kwartalnie.

**ID**: 0714.10 m2organizational. 7-10. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Przeprowadź inspekcję luk w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="systems-are-appropriately-hardened-eg-configured-with-only-necessary-and-secure-services-ports-and-protocols-enabled"></a>Systemy są odpowiednio zaostrzone (na przykład skonfigurowane z użyciem tylko niezbędnych i zabezpieczonych usług, portów i protokołów).

**ID**: 0715.10 m2organizational. 8-10. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Inspekcja luk w zabezpieczeniach na maszynach z zainstalowanym systemem Docker i wyświetlanie ich jako rekomendacji w Azure Security Center. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-conducts-an-enterprise-security-posture-review-as-needed-but-no-less-than-once-within-every-three-hundred-sixty-five-365-days-in-accordance-with-organizational-is-procedures"></a>Organizacja przeprowadza przegląd stan zabezpieczeń przedsiębiorstwa w razie konieczności, ale nie mniej niż raz w ciągu 365 (365) dni, zgodnie z procedurą organizacyjną.

**ID**: 0716.10 m3organizational. 1-10. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Luki w zabezpieczeniach baz danych SQL należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Monitoruj wyniki skanowania oceny luk w zabezpieczeniach i zalecenia dotyczące sposobu korygowania luk w zabezpieczeniach bazy danych. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning-tools-include-the-capability-to-readily-update-the-information-system-vulnerabilities-scanned"></a>Narzędzia do skanowania luk w zabezpieczeniach obejmują możliwość łatwego aktualizowania skanowanych luk w zabezpieczeniach systemu informacji.

**ID**: 0717.10 m3organizational. 2-10. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Przeprowadź inspekcję luk w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami. |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="the-organization-scans-for-vulnerabilities-in-the-information-system-and-hosted-applications-to-determine-the-state-of-flaw-remediation-monthly-automatically-and-again-manually-or-automatically-when-new-vulnerabilities-potentially-affecting-the-systems-and-networked-environments-are-identified-and-reported"></a>Organizacja skanuje w poszukiwaniu luk w zabezpieczeniach w systemie informacyjnym i hostowanych aplikacjach, aby określić stan usterki (automatyczne) i ponownie (ręcznie lub automatycznie) w przypadku zidentyfikowania i zgłoszenia nowych luk w zabezpieczeniach systemów i środowisk sieciowych.

**ID**: 0718.10 m3organizational. 34-10. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Serwery, które nie spełniają skonfigurowanej linii bazowej, będą monitorowane przez Azure Security Center jako zalecenia |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-organization-updates-the-list-of-information-system-vulnerabilities-scanned-within-every-thirty-30-days-or-when-new-vulnerabilities-are-identified-and-reported"></a>Organizacja aktualizuje listę luk w zabezpieczeniach systemu informacji skanowanych w ciągu co 30 (30) dni lub po zidentyfikowaniu i zgłoszeniu nowych luk w zabezpieczeniach.

**ID**: 0719.10 m3organizational. 5-10. m **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniu zarządzanym SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Przeprowadź inspekcję każdego wystąpienia zarządzanego SQL, które nie ma włączonych skanów oceny luk w zabezpieczeniach. Ocena luk w zabezpieczeniach może wykrywać, śledzić i pomagać w korygowaniu potencjalnych luk w zabezpieczeniach bazy danych. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

## <a name="business-continuity-and-risk-assessment"></a>Ciągłość działania i Ocena ryzyka

### <a name="the-organization-identifies-the-critical-business-processes-requiring-business-continuity"></a>Organizacja identyfikuje krytyczne procesy biznesowe wymagające ciągłości biznesowej.

**Identyfikator**: 1634.12 b1organizational. 1-12. b **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja maszyn wirtualnych bez skonfigurowanego odzyskiwania po awarii](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Inspekcja maszyn wirtualnych, dla których nie skonfigurowano odzyskiwania po awarii. Aby dowiedzieć się więcej na temat odzyskiwania po awarii, odwiedź stronę [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

### <a name="information-security-aspects-of-business-continuity-are-i-based-on-identifying-events-or-sequence-of-events-that-can-cause-interruptions-to-the-organizations-critical-business-processes-eg-equipment-failure-human-errors-theft-fire-natural-disasters-acts-of-terrorism-ii-followed-by-a-risk-assessment-to-determine-the-probability-and-impact-of-such-interruptions-in-terms-of-time-damage-scale-and-recovery-period-iii-based-on-the-results-of-the-risk-assessment-a-business-continuity-strategy-is-developed-to-identify-the-overall-approach-to-business-continuity-and-iv-once-this-strategy-has-been-created-endorsement-is-provided-by-management-and-a-plan-created-and-endorsed-to-implement-this-strategy"></a>Aspekty bezpieczeństwa informacji o ciągłości biznesowej są (i) oparte na identyfikowaniu zdarzeń (lub sekwencji zdarzeń), które mogą spowodować zakłócenia w przypadku krytycznych procesów firmy w organizacji (np. awaria sprzętu, błędy ludzkie, kradzież, pożar, naturalne katastrofy żywiołowe); (II) następuje Ocena ryzyka, aby określić prawdopodobieństwo i wpływ takich przerw w odniesieniu do czasu, awarii i okresu odzyskiwania. (III) w oparciu o wyniki oceny ryzyka opracowano strategię zapewnienia ciągłości działania w celu ustalenia ogólnego podejścia do ciągłości działania firmy; i (IV) po utworzeniu tej strategii zatwierdzenie jest udzielane przez zarządzanie, a plan został utworzony i zatwierdzony do wdrożenia tej strategii.

**Identyfikator**: 1635.12 b1organizational. 2-12. b **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Key Vault obiektów powinno być możliwe do odzyskania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Ta zasada przeprowadza inspekcję w przypadku braku możliwości odzyskania obiektów magazynu kluczy. Funkcja usuwania nietrwałego pomaga efektywnie przechowywać zasoby dla danego okresu przechowywania (90 dni) nawet po operacji usuwania, jednocześnie zapewniając, że obiekt jest usuwany. Gdy jest włączona ochrona przed czyszczeniem, nie można wyczyścić magazynu lub obiektu w stanie usuniętym, dopóki nie zostanie przekroczony okres przechowywania 90 dni. Te magazyny i obiekty nadal mogą być odzyskiwane, dzięki czemu klienci będą przestrzegani zasad przechowywania. |Inspekcja, wyłączona |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="business-impact-analysis-are-used-to-evaluate-the-consequences-of-disasters-security-failures-loss-of-service-and-service-availability"></a>Analiza wpływu na działalność biznesową służy do analizowania konsekwencji awarii, błędów zabezpieczeń, utraty usługi i dostępności usług.

**Identyfikator**: 1637.12 b2organizational. 2-12. b **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń — Konsola odzyskiwania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff71be03e-e25b-4d0f-b8bc-9b3e309b66c0) |Maszyny z systemem Windows powinny mieć określone ustawienia zasady grupy w kategorii "Opcje zabezpieczeń — Konsola odzyskiwania", aby umożliwić kopiowanie dyskietek i dostęp do wszystkich dysków i folderów. Te zasady wymagają, aby wymagania wstępne dotyczące konfiguracji gościa zostały wdrożone w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |AuditIfNotExists, wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsRecoveryconsole_AINE.json) |

### <a name="business-continuity-risk-assessments-i-are-carried-out-annually-with-full-involvement-from-owners-of-business-resources-and-processes-ii-consider-all-business-processes-and-is-not-limited-to-the-information-assets-but-includes-the-results-specific-to-information-security-and-iii-identifies-quantifies-and-prioritizes-risks-against-key-business-objectives-and-criteria-relevant-to-the-organization-including-critical-resources-impacts-of-disruptions-allowable-outage-times-and-recovery-priorities"></a>Oceny ryzyka związanego z ciągłością biznesową (i) są przeprowadzane corocznie z pełnymi zaangażowaniem właścicieli zasobów i procesów firmy. (II) Rozważ wszystkie procesy biznesowe i nie jest ograniczony do zasobów informacji, ale zawiera wyniki specyficzne dla bezpieczeństwa informacji; i (III) identyfikują i ustalają priorytety ryzyka związane z kluczowymi celami biznesowymi i kryteriami, które są odpowiednie dla organizacji, w tym o najważniejszych zasobach, wpływie zakłóceń, czasie przestojów oraz priorytetów odzyskiwania.

**ID**: 1638.12 b2organizational. 345-12. b **własność**: klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja maszyn wirtualnych bez skonfigurowanego odzyskiwania po awarii](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Inspekcja maszyn wirtualnych, dla których nie skonfigurowano odzyskiwania po awarii. Aby dowiedzieć się więcej na temat odzyskiwania po awarii, odwiedź stronę [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> Dostępność określonych definicji Azure Policy może się różnić w zależności od Azure Government i innych chmur krajowych.

## <a name="next-steps"></a>Następne kroki

Dodatkowe artykuły dotyczące Azure Policy:

- Przegląd [zgodności z przepisami](../concepts/regulatory-compliance.md) .
- Zapoznaj się ze [strukturą definicji inicjatywy](../concepts/initiative-definition-structure.md).
- Zapoznaj się z innymi przykładami w [Azure Policy Samples](./index.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).
