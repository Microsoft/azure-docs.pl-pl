---
title: Szczegóły zgodności z przepisami dla NIST SP 800-53 R4
description: Szczegóły wbudowanej inicjatywy NIST SP 800-53 R4 Regulatory Compliance. Każda kontrolka jest mapowana na co najmniej jedną Azure Policy, które pomagają w ocenie.
ms.date: 04/14/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 0fe88525bc574219729a7f90fba1bf82803e5854
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505347"
---
# <a name="details-of-the-nist-sp-800-53-r4-regulatory-compliance-built-in-initiative"></a>Szczegóły wbudowanej inicjatywy NIST SP 800-53 R4 Regulatory Compliance

W poniższym artykule szczegółowo opisano sposób mapowania wbudowanej definicji  inicjatywy  Azure Policy zgodność z przepisami na domeny zgodności i mechanizmy kontroli w NIST SP 800-53 R4.
Aby uzyskać więcej informacji na temat tego standardu zgodności, zobacz [NIST SP 800-53 R4.](https://nvd.nist.gov/800-53) Aby zrozumieć _własność_, [zobacz Azure Policy definicji zasad i](../concepts/definition-structure.md#type) [Wspólna odpowiedzialność w chmurze.](../../../security/fundamentals/shared-responsibility.md)

Następujące mapowania są na formanty **NIST SP 800-53 R4.** Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonej **domeny zgodności.** Wiele kontrolek jest implementowych z Azure Policy [definicji](../overview.md) inicjatywy. Aby przejrzeć pełną definicję inicjatywy, **otwórz** pozycję Zasady w Azure Portal i wybierz **stronę Definicje.**
Następnie znajdź i wybierz wbudowaną definicję inicjatywy **NIST SP 800-53 R4** Regulatory Compliance.

Ta wbudowana inicjatywa jest wdrażana w ramach przykładu strategii [NIST SP 800-53 R4.](../../blueprints/samples/nist-sp-800-53-r4.md)

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co [najmniej jedną Azure Policy](../overview.md) definicją.
> Te zasady mogą ułatwić [ocenę zgodności](../how-to/get-compliance-data.md) z kontrolą. Jednak często nie występuje relacja jeden do jednego lub pełne dopasowanie między kontrolą a co najmniej jedną zasadą. W związku z **tym zgodność** w Azure Policy odnosi się tylko do samych definicji zasad; Nie gwarantuje to pełnej zgodności ze wszystkimi wymaganiami kontrolki. Ponadto standard zgodności zawiera kontrolki, które nie są obecnie Azure Policy przez żadne definicje zgodności. Dlatego zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między domenami zgodności, kontrolkami i Azure Policy dla tego standardu zgodności mogą zmieniać się w czasie. Aby wyświetlić historię zmian, zobacz [GitHub Commit History (Historia zatwierdzania w usłudze GitHub).](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/NIST80053_audit.json)

## <a name="access-control"></a>Kontrola dostępu

### <a name="access-control-policy-and-procedures"></a>Access Control i procedury

**Identyfikator:** Własność NIST SP 800-53 R4 AC-1: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1000 — Access Control i procedury](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ef3cc79-733e-48ed-ab6f-7bf439e9b406) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1000.json) |
|[Microsoft Managed Control 1001 — Access Control i procedury](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e26f8c3-4bf3-4191-b8fc-d888805101b7) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1001.json) |

### <a name="account-management"></a>Zarządzanie kontem

**Identyfikator:** Własność NIST SP 800-53 R4 AC-2: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Przestarzałe konta powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |Przestarzałe konta powinny zostać usunięte z subskrypcji.  Przestarzałe konta to konta, których logowanie zostało zablokowane. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji.  Przestarzałe konta to konta, których logowanie zostało zablokowane. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Konta zewnętrzne z uprawnieniami właściciela należy usunąć z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji, aby zapobiec niemonitorowanemu dostępowi. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |
|[Microsoft Managed Control 1002 — Zarządzanie kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F632024c2-8079-439d-a7f6-90af1d78cc65) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1002.json) |
|[Microsoft Managed Control 1003 — Zarządzanie kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b68b179-3704-4ff7-b51d-7d65374d165d) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1003.json) |
|[Microsoft Managed Control 1004 — Zarządzanie kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc17822dc-736f-4eb4-a97d-e6be662ff835) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1004.json) |
|[Microsoft Managed Control 1005 — Zarządzanie kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b626abc-26d4-4e22-9de8-3831818526b1) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1005.json) |
|[Microsoft Managed Control 1006 — Zarządzanie kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faae8d54c-4bce-4c04-b3aa-5b65b67caac8) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1006.json) |
|[Microsoft Managed Control 1007 — Zarządzanie kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17200329-bf6c-46d8-ac6d-abf4641c2add) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1007.json) |
|[Microsoft Managed Control 1008 — Zarządzanie kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8356cfc6-507a-4d20-b818-08038011cd07) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1008.json) |
|[Microsoft Managed Control 1009 — zarządzanie kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb26f8610-e615-47c2-abd6-c00b2b0b503a) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1009.json) |
|[Microsoft Managed Control 1010 — zarządzanie kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F784663a8-1eb0-418a-a98c-24d19bc1bb62) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1010.json) |
|[Microsoft Managed Control 1011 — Zarządzanie kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7e6a54f3-883f-43d5-87c4-172dfd64a1f5) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1011.json) |
|[Microsoft Managed Control 1012 — Account Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefd7b9ae-1db6-4eb6-b0fe-87e6565f9738) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1012.json) |

### <a name="account-management--automated-system-account-management"></a>Zarządzanie kontami | Automatyczne zarządzanie kontami systemowymi

**Identyfikator:** NIST SP 800-53 R4 AC-2 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1013 — Account Management \| Automated System Account Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fd7b917-d83b-4379-af60-51e14e316c61) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1013.json) |

### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Zarządzanie kontami | Usuwanie kont tymczasowych/awaryjnych

**Identyfikator:** NIST SP 800-53 R4 AC-2 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1014 — usuwanie zarządzania kontami \| tymczasowymi/awaryjnymi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5dee936c-8037-4df1-ab35-6635733da48c) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1014.json) |

### <a name="account-management--disable-inactive-accounts"></a>Zarządzanie kontami | Wyłączanie nieaktywnych kont

**Identyfikator:** NIST SP 800-53 R4 AC-2 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1015 — zarządzanie kontami \| — wyłączanie nieaktywnych kont](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F544a208a-9c3f-40bc-b1d1-d7e144495c14) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1015.json) |

### <a name="account-management--automated-audit-actions"></a>Zarządzanie kontami | Zautomatyzowane akcje inspekcji

**Identyfikator:** NIST SP 800-53 R4 AC-2 (4) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1016 — zautomatyzowane akcje inspekcji \| zarządzania kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8b43277-512e-40c3-ab00-14b3b6e72238) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1016.json) |

### <a name="account-management--inactivity-logout"></a>Zarządzanie kontami | Wyloguj nieaktywność

**Identyfikator:** NIST SP 800-53 R4 AC-2 (5) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1017 — Account Management Inactivity Logout (Microsoft Managed Control 1017 — wyloguj \| nieaktywność zarządzania kontami)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fc3db37-e59a-48c1-84e9-1780cedb409e) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1017.json) |

### <a name="account-management--role-based-schemes"></a>Zarządzanie kontami | Role-Based schematów

**Identyfikator:** NIST SP 800-53 R4 AC-2 (7) **Własność: Udostępnione**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Administrator Azure Active Directory powinien być aprowstrowany dla serwerów SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Przejmij aprowizowanie konta Azure Active Directory dla serwera SQL, aby włączyć uwierzytelnianie usługi Azure AD. Uwierzytelnianie usługi Azure AD umożliwia uproszczone zarządzanie uprawnieniami i scentralizowane zarządzanie tożsamościami użytkowników bazy danych i innych usługi firmy Microsoft |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[Inspekcja użycia niestandardowych reguł kontroli RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Inspekcja ról wbudowanych, takich jak "Właściciel, Współtator, Czytelnik" zamiast niestandardowych ról RBAC, które są podatne na błędy. Używanie ról niestandardowych jest traktowane jako wyjątek i wymaga rygorystycznego przeglądu i modelowania zagrożeń |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Microsoft Managed Control 1018 — schematy zarządzania \| Role-Based kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9121abf-e698-4ee9-b1cf-71ee528ff07f) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1018.json) |
|[Microsoft Managed Control 1019 — schematy zarządzania \| Role-Based kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a3ee9b2-3977-459c-b8ce-2db583abd9f7) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1019.json) |
|[Microsoft Managed Control 1020 — schematy zarządzania \| Role-Based kontami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b291ee8-3140-4cad-beb7-568c077c78ce) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1020.json) |
|[Service Fabric klastry powinny używać tylko Azure Active Directory do uwierzytelniania klienta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Inspekcja użycia uwierzytelniania klienta tylko za pośrednictwem Azure Active Directory w Service Fabric |Inspekcja, Odmowa, Wyłączone |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Zarządzanie kontami | Ograniczenia dotyczące korzystania z kont udostępnionych/grupowych

**Identyfikator:** NIST SP 800-53 R4 AC-2 (9) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1021 — ograniczenia zarządzania \| kontami dotyczące korzystania z kont udostępnionych/grupowych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a3eb0a3-428d-4669-baff-20a14eb4b551) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1021.json) |

### <a name="account-management--shared--group-account-credential-termination"></a>Zarządzanie kontami | Zakończenie poświadczeń konta udostępnionego/grupy

**Identyfikator:** NIST SP 800-53 R4 AC-2 (10) **Własność: Udostępnione**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1022 — Account Management Shared /Group Account Termination (Kontrola zarządzana przez firmę Microsoft 1022 — zakończenie poświadczeń konta \| udostępnionego/konta grupy)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F411f7e2d-9a0b-4627-a0b9-1700432db47d) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1022.json) |

### <a name="account-management--usage-conditions"></a>Zarządzanie kontami | Warunki użytkowania

**Identyfikator:** NIST SP 800-53 R4 AC-2 (11) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1023 — warunki użycia \| zarządzania kontem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe55698b6-3dea-4aa9-99b9-d8218c6ab6e5) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1023.json) |

### <a name="account-management--account-monitoring--atypical-usage"></a>Zarządzanie kontami | Monitorowanie konta / Nietypowe użycie

**Identyfikator:** NIST SP 800-53 R4 AC-2 (12) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu do sieci just in time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp just in time (JIT) do sieci będzie monitorowany przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft Managed Control 1024 — account management account monitoring / Atypical Usage (Kontrola zarządzana przez firmę Microsoft 1024 — monitorowanie konta zarządzania \| kontem/ nietypowe użycie)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84914fb4-12da-4c53-a341-a9fd463bed10) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1024.json) |
|[Microsoft Managed Control 1025 — monitorowanie konta zarządzania \| kontem / nietypowe użycie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fadfe020d-0a97-45f4-a39c-696ef99f3a95) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1025.json) |

### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Zarządzanie kontami | Wyłączanie kont dla High-Risk indywidualnych

**Identyfikator:** NIST SP 800-53 R4 AC-2 (13) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1026 — account management Disable Accounts for High-Risk Individuals (Zarządzanie kontami — wyłączanie \| kont dla High-Risk indywidualnych)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55419419-c597-4cd4-b51e-009fd2266783) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1026.json) |

### <a name="access-enforcement"></a>Wymuszanie dostępu

**Identyfikator:** Własność NIST SP 800-53 R4 AC-3: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1027 — Access Enforcement (Kontrola zarządzana przez firmę Microsoft 1027 — wymuszanie dostępu)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa76ca9b0-3f4a-4192-9a38-b25e4f8ae48c) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1027.json) |

### <a name="information-flow-enforcement"></a>Wymuszanie przepływu informacji

**Identyfikator:** Własność NIST SP 800-53 R4 AC-4: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Mechanizm CORS nie powinien zezwalać na dostęp do aplikacji internetowych ze wszystkich zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |Współużytkowania zasobów między źródłami (CORS, Cross-Origin Resource Sharing) nie powinno zezwalać wszystkim domenom na dostęp do aplikacji internetowej. Zezwalaj na interakcję z aplikacją internetową tylko wymaganym domenom. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[Microsoft Managed Control 1028 — wymuszanie przepływu informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff171df5c-921b-41e9-b12b-50801c315475) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1028.json) |

### <a name="information-flow-enforcement--security-policy-filters"></a>Wymuszanie przepływu informacji | Filtry zasad zabezpieczeń

**Identyfikator:** NIST SP 800-53 R4 AC-4 (8) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1029 — filtry zasad zabezpieczeń wymuszania \| przepływu informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53ac8f8e-c2b5-4d44-8a2d-058e9ced9b69) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1029.json) |

### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Wymuszanie przepływu informacji | Fizyczne/logiczne rozdzielenie przepływów informacji

**Identyfikator:** NIST SP 800-53 R4 AC-4 (21) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1030 — wymuszanie fizycznego/logicznego rozdzielania przepływów informacji za \| pomocą przepływów przepływów informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3531453-b869-4606-9122-29c1cd6e7ed1) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1030.json) |

### <a name="separation-of-duties"></a>Separacja obowiązków

**Identyfikator:** NIST SP 800-53 R4 AC-5 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[W ramach subskrypcji należy wyznaczyć maksymalnie 3 właścicieli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Zaleca się wyznaczenie maksymalnie 3 właścicieli subskrypcji w celu ograniczenia ryzyka naruszenia zabezpieczeń przez naruszonego właściciela. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[Inspekcja maszyn z systemem Windows, na których brakuje dowolnego z określonych członków w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli lokalna grupa Administratorów nie zawiera co najmniej jednego członka wymienionego w parametrze zasad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Inspekcja maszyn z systemem Windows, które mają określonych członków w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli lokalna grupa Administratorzy zawiera co najmniej jeden z elementów członkowskich wymienionych w parametrze zasad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Microsoft Managed Control 1031 — Separacja obowiązków](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b93a801-fe25-4574-a60d-cb22acffae00) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1031.json) |
|[Microsoft Managed Control 1032 — separacja obowiązków](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5aa85661-d618-46b8-a20f-ca40a86f0751) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1032.json) |
|[Microsoft Managed Control 1033 — Separacja obowiązków](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48540f01-fc11-411a-b160-42807c68896e) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1033.json) |
|[Do subskrypcji powinien być przypisany więcej niż jeden właściciel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Zaleca się wyznaczenie więcej niż jednego właściciela subskrypcji w celu zapewnienia nadmiarowości dostępu administratora. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege"></a>Najmniejsze uprawnienia

**Identyfikator:** Własność NIST SP 800-53 R4 AC-6: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1034 — najmniejsze uprawnienia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02a5ed00-6d2e-4e97-9a98-46c32c057329) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1034.json) |

### <a name="least-privilege--authorize-access-to-security-functions"></a>Najmniejsze uprawnienia | Autoryzowanie dostępu do funkcji zabezpieczeń

**Identyfikator:** NIST SP 800-53 R4 AC-6 (1) **Własność: Udostępnione**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1035 — autoryzacja dostępu \| z najmniejszymi uprawnieniami do funkcji zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca94b046-45e2-444f-a862-dc8ce262a516) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1035.json) |

### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Najmniejsze uprawnienia | Non-Privileged Access for Nonsecurity Functions

**Identyfikator:** NIST SP 800-53 R4 AC-6 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1036 - Least Privileged Access for Nonsecurity Functions (Kontrola zarządzana przez firmę Microsoft 1036 — dostęp z najmniejszymi uprawnieniami bez uprawnień \| dla funkcji bez zabezpieczeń)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a16d673-8cf0-4dcf-b1d5-9b3e114fef71) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1036.json) |

### <a name="least-privilege--network-access-to-privileged-commands"></a>Najmniejsze uprawnienia | Dostęp sieciowy do poleceń uprzywilejowanych

**Identyfikator:** NIST SP 800-53 R4 AC-6 (3) **Własność: Udostępnione**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1037 — dostęp \| sieciowy z najmniejszymi uprawnieniami do poleceń uprzywilejowanych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa4c2a3d-1294-41a3-9ada-0e540471e9fb) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1037.json) |

### <a name="least-privilege--privileged-accounts"></a>Najmniejsze uprawnienia | Konta uprzywilejowane

**Identyfikator:** NIST SP 800-53 R4 AC-6 (5) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1038 — konta z \| najmniejszymi uprawnieniami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26692e88-71b7-4a5f-a8ac-9f31dd05bd8e) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1038.json) |

### <a name="least-privilege--review-of-user-privileges"></a>Najmniejsze uprawnienia | Przegląd uprawnień użytkownika

**Identyfikator:** NIST SP 800-53 R4 AC-6 (7) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[W ramach subskrypcji należy wyznaczyć maksymalnie 3 właścicieli](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Zaleca się wyznaczenie maksymalnie 3 właścicieli subskrypcji w celu ograniczenia ryzyka naruszenia zabezpieczeń przez naruszonego właściciela. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[Inspekcja maszyn z systemem Windows, na których brakuje dowolnego z określonych członków w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli lokalna grupa Administratorów nie zawiera co najmniej jednego członka wymienionego w parametrze zasad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Inspekcja maszyn z systemem Windows, które mają określonych członków w grupie Administratorzy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli lokalna grupa Administratorzy zawiera co najmniej jeden z elementów członkowskich wymienionych w parametrze zasad. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Microsoft Managed Control 1039 - Least Privilege Review of User Privileges (Kontrola zarządzana przez firmę Microsoft 1039 — przegląd \| uprawnień użytkownika z najmniejszymi uprawnieniami)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a7b9de4-a8a2-4672-914d-c5f6752aa7f9) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1039.json) |
|[Microsoft Managed Control 1040 - Least Privilege Review of User Privileges (Kontrola zarządzana przez firmę Microsoft 1040 — przegląd uprawnień użytkownika \| z najmniejszymi uprawnieniami)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F54205576-cec9-463f-ba44-b4b3f5d0a84c) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1040.json) |
|[Do Subskrypcji powinien być przypisany więcej niż jeden właściciel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Zaleca się wyznaczenie więcej niż jednego właściciela subskrypcji w celu zapewnienia nadmiarowości dostępu administratora. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege--privilege-levels-for-code-execution"></a>Najmniejsze uprawnienia | Poziomy uprawnień do wykonywania kodu

**Identyfikator:** NIST SP 800-53 R4 AC-6 (8) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1041 — najmniejsze \| poziomy uprawnień do wykonywania kodu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb3d8d15b-627a-4219-8c96-4d16f788888b) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1041.json) |

### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Najmniejsze uprawnienia | Inspekcja użycia funkcji uprzywilejowanych

**Identyfikator:** NIST SP 800-53 R4 AC-6 (9) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1042 — \| inspekcja najmniejszych uprawnień korzysta z funkcji uprzywilejowanych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F319dc4f0-0fed-4ac9-8fc3-7aeddee82c07) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1042.json) |

### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Najmniejsze uprawnienia | Zabraniaj użytkownikom z uprawnieniami wykonywania funkcji uprzywilejowanych

**Identyfikator:** NIST SP 800-53 R4 AC-6 (10) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1043 - Least Privilege Prohibit Non-Privileged Users from Executing Privileged Functions (Kontrola zarządzana przez firmę Microsoft 1043 — najmniejsze uprawnienia zabraniają użytkownikom bez uprawnień \| wykonywania funkcji uprzywilejowanych)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F361a77f6-0f9c-4748-8eec-bc13aaaa2455) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1043.json) |

### <a name="unsuccessful-logon-attempts"></a>Nieudane próby logowania

**Identyfikator:** NIST SP 800-53 R4 AC-7 Ownership : Shared (Własność NIST SP 800-53 R4 AC-7): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1044 — nieudane próby logowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0abbac52-57cf-450d-8408-1208d0dd9e90) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1044.json) |
|[Microsoft Managed Control 1045 — nieudane próby logowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F554d2dd6-f3a8-4ad5-b66f-5ce23bd18892) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1045.json) |

### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Nieudane próby logowania | Przeczyszczanie/czyszczenie urządzenia przenośnego

**Identyfikator:** NIST SP 800-53 R4 AC-7 (2) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1046 — automatyczne przeczyszczanie/czyszczenie \| blokady konta urządzenia przenośnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b1aa965-7502-41f9-92be-3e2fe7cc392a) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1046.json) |

### <a name="system-use-notification"></a>Powiadomienie o użyciu systemu

**Identyfikator:** Własność NIST SP 800-53 R4 AC-8: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1047 — powiadomienie o użyciu systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1ff6d62-a55c-41ab-90ba-90bb5b7b6f62) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1047.json) |
|[Microsoft Managed Control 1048 — powiadomienie o użyciu systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F483e7ca9-82b3-45a2-be97-b93163a0deb7) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1048.json) |
|[Microsoft Managed Control 1049 — powiadomienie o użyciu systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9adf7ba7-900a-4f35-8d57-9f34aafc405c) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1049.json) |

### <a name="concurrent-session-control"></a>Współbieżna kontrola sesji

**Identyfikator:** NIST SP 800-53 R4 AC-10 **Ownership**: Shared (Własność NIST SP 800-53 R4 AC-10): Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1050 — współbieżna kontrola sesji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd20184c-b4ec-4ce5-8db6-6e86352d183f) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1050.json) |

### <a name="session-lock"></a>Blokada sesji

**Identyfikator:** Własność NIST SP 800-53 R4 AC-11: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1051 — blokada sesji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7cac6ee9-b58b-40c8-a5ce-f0efc3d9b339) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1051.json) |
|[Microsoft Managed Control 1052 — blokada sesji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F027cae1c-ec3e-4492-9036-4168d540c42a) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1052.json) |

### <a name="session-lock--pattern-hiding-displays"></a>Blokada sesji | Pattern-Hiding ekranów

**Identyfikator:** NIST SP 800-53 R4 AC-11 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1053 — blokady \| sesji Pattern-Hiding wyświetlane](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7582b19c-9dba-438e-aed8-ede59ac35ba3) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1053.json) |

### <a name="session-termination"></a>Zakończenie sesji

**Identyfikator:** Własność NIST SP 800-53 R4 AC-12: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1054 — zakończenie sesji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5807e1b4-ba5e-4718-8689-a0ca05a191b2) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1054.json) |

### <a name="session-termination--user-initiated-logouts--message-displays"></a>Zakończenie sesji | User-Initiated wylogowy/wyświetlane wiadomości

**Identyfikator:** NIST SP 800-53 R4 AC-12 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1055 — zakończenie \| sesji User-Initiated wylogowy/ wyświetlane wiadomości](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F769efd9b-3587-4e22-90ce-65ddcd5bd969) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1055.json) |
|[Microsoft Managed Control 1056 - Session Termination \| User-Initiated Logouts / Message Displays](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac43352f-df83-4694-8738-cfce549fd08d) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1056.json) |

### <a name="permitted-actions-without-identification-or-authentication"></a>Dozwolone akcje bez identyfikacji lub uwierzytelniania

**Identyfikator:** Własność NIST SP 800-53 R4 AC-14: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1057 — dozwolone akcje bez identyfikacji lub uwierzytelniania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78255758-6d45-4bf0-a005-7016bc03b13c) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1057.json) |
|[Microsoft Managed Control 1058 — dozwolone akcje bez identyfikacji lub uwierzytelniania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76e85d08-8fbb-4112-a1c1-93521e6a9254) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1058.json) |

### <a name="security-attributes"></a>Atrybuty zabezpieczeń

**Identyfikator:** Własność NIST SP 800-53 R4 AC-16: Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zaawansowane zabezpieczenia danych powinny być włączone na SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Inspekcja poszczególnych SQL Managed Instance danych bez zaawansowanego zabezpieczeń danych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na serwerach SQL należy włączyć zaawansowane zabezpieczenia danych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Inspekcja serwerów SQL bez Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="remote-access"></a>Dostęp zdalny

**Identyfikator:** Własność NIST SP 800-53 R4 AC-17: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1059 — dostęp zdalny](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa29b5d9f-4953-4afe-b560-203a6410b6b4) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1059.json) |
|[Microsoft Managed Control 1060 — dostęp zdalny](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a987fd-2003-45de-a120-014956581f2b) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1060.json) |

### <a name="remote-access--automated-monitoring--control"></a>Dostęp zdalny | Automatyczne monitorowanie/kontrola

**Identyfikator:** NIST SP 800-53 R4 AC-17 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dodawanie tożsamości zarządzanej przypisanej przez system w celu włączenia przypisań konfiguracji gościa na maszynach wirtualnych bez tożsamości](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Te zasady dodają przypisaną przez system tożsamość zarządzaną do maszyn wirtualnych hostowanych na platformie Azure, które są obsługiwane przez konfigurację gościa, ale nie mają żadnych tożsamości zarządzanych. Tożsamość zarządzana przypisana przez system jest wymaganiem wstępnym dla wszystkich przypisań konfiguracji gościa i musi zostać dodana do maszyn przed użyciem jakichkolwiek definicji zasad konfiguracji gościa. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Modyfikowanie |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Dodawanie tożsamości zarządzanej przypisanej przez system w celu włączenia przypisań konfiguracji gościa na maszynach wirtualnych z tożsamością przypisaną przez użytkownika](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Te zasady dodają przypisaną przez system tożsamość zarządzaną do maszyn wirtualnych hostowanych na platformie Azure, które są obsługiwane przez konfigurację gościa i mają co najmniej jedną tożsamość przypisaną przez użytkownika, ale nie mają przypisanej przez system tożsamości zarządzanej. Tożsamość zarządzana przypisana przez system jest wymaganiem wstępnym dla wszystkich przypisań konfiguracji gościa i musi zostać dodana do maszyn przed użyciem definicji zasad konfiguracji gościa. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Modyfikowanie |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Inspekcja maszyn z systemem Linux, które zezwalają na połączenia zdalne z kont bez haseł](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny nie są zgodne, jeśli maszyny z systemem Linux zezwalają na połączenia zdalne z kont bez haseł |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[Wdrażanie rozszerzenia konfiguracji gościa systemu Linux w celu włączenia przypisań konfiguracji gościa na maszynach wirtualnych z systemem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Te zasady wdrażają rozszerzenie konfiguracji gościa systemu Linux na maszynach wirtualnych z systemem Linux hostowanych na platformie Azure, które są obsługiwane przez konfigurację gościa. Rozszerzenie konfiguracji gościa systemu Linux jest wymaganie wstępne dla wszystkich przypisań konfiguracji gościa systemu Linux i musi zostać wdrożone na maszynach przed użyciem dowolnej definicji zasad konfiguracji gościa systemu Linux. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Wdrażanie rozszerzenia konfiguracji gościa systemu Windows w celu włączenia przypisań konfiguracji gościa na maszynach wirtualnych z systemem Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Te zasady wdrażają rozszerzenie Konfiguracja gościa systemu Windows na maszynach wirtualnych z systemem Windows hostowanych na platformie Azure, które są obsługiwane przez konfigurację gościa. Rozszerzenie Konfiguracja gościa systemu Windows jest wymaganie wstępne dla wszystkich przypisań konfiguracji gościa systemu Windows i musi zostać wdrożone na maszynach przed użyciem dowolnej definicji zasad konfiguracji gościa systemu Windows. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft Managed Control 1061 — zautomatyzowane \| monitorowanie/kontrola dostępu zdalnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ac22808-a2e8-41c4-9d46-429b50738914) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1061.json) |
|[Zdalne debugowanie powinno być wyłączone dla API Apps](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |Debugowanie zdalne wymaga otwarcia portów przychodzących w aplikacjach interfejsu API. Debugowanie zdalne powinno być wyłączone. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[Zdalne debugowanie powinno być wyłączone dla aplikacji funkcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |Debugowanie zdalne wymaga otwarcia portów przychodzących w aplikacjach funkcji. Debugowanie zdalne powinno być wyłączone. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[Zdalne debugowanie powinno być wyłączone dla aplikacji internetowych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |Debugowanie zdalne wymaga otwarcia portów przychodzących w aplikacji internetowej. Debugowanie zdalne powinno być wyłączone. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[Konta magazynu powinny ograniczać dostęp sieciowy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Dostęp sieciowy do kont magazynu powinien być ograniczony. Skonfiguruj reguły sieci, aby tylko aplikacje z dozwolonych sieci mogły uzyskać dostęp do konta magazynu. Aby zezwolić na połączenia z określonego Internetu lub klientów lokalnych, można przyznać dostęp do ruchu z określonych sieci wirtualnych platformy Azure lub do zakresów publicznych internetowych adresów IP |Inspekcja, Odmowa, Wyłączone |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Dostęp zdalny | Ochrona poufności/integralności przy użyciu szyfrowania

**Identyfikator:** NIST SP 800-53 R4 AC-17 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1062 — Remote Access Protection of Confidentiality/Integrity Using Encryption (Ochrona dostępu zdalnego \| poufności/integralności przy użyciu szyfrowania)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4708723f-e099-4af1-bbf9-b6df7642e444) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1062.json) |

### <a name="remote-access--managed-access-control-points"></a>Dostęp zdalny | Zarządzane Access Control punktowe

**Identyfikator:** NIST SP 800-53 R4 AC-17 (3) **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1063 - Remote Access \| Managed Access Control Points](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F593ce201-54b2-4dd0-b34f-c308005d7780) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1063.json) |

### <a name="remote-access--privileged-commands--access"></a>Dostęp zdalny | Uprzywilejowane polecenia / dostęp

**Identyfikator:** NIST SP 800-53 R4 AC-17 (4) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1064 — uprzywilejowane \| polecenia dostępu zdalnego / dostęp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb4d9508-cbf0-4a3c-bb5c-6c95b159f3fb) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1064.json) |
|[Microsoft Managed Control 1065 — uprzywilejowane \| polecenia dostępu zdalnego / dostęp](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff87b8085-dca9-4cf1-8f7b-9822b997797c) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1065.json) |

### <a name="remote-access--disconnect--disable-access"></a>Dostęp zdalny | Rozłącz/wyłącz dostęp

**Identyfikator:** NIST SP 800-53 R4 AC-17 (9) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1066 - Remote Access Disconnect /Disable Access (Kontrola zarządzana firmy Microsoft 1066 — \| rozłączanie dostępu zdalnego/wyłączanie dostępu)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4455c2e8-c65d-4acf-895e-304916f90b36) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1066.json) |

### <a name="wireless-access"></a>Dostęp bezprzewodowy

**Identyfikator:** NIST SP 800-53 R4 AC-18 **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1067 — dostęp bezprzewodowy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5e54f6-0127-44d0-8b61-f31dc8dd6190) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1067.json) |
|[Microsoft Managed Control 1068 — dostęp bezprzewodowy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d045bca-a0fd-452e-9f41-4ec33769717c) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1068.json) |

### <a name="wireless-access--authentication-and-encryption"></a>Dostęp bezprzewodowy | Uwierzytelnianie i szyfrowanie

**Identyfikator:** NIST SP 800-53 R4 AC-18 (1) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1069 — uwierzytelnianie i szyfrowanie dostępu \| bezprzewodowego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F91c97b44-791e-46e9-bad7-ab7c4949edbb) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1069.json) |

### <a name="wireless-access--disable-wireless-networking"></a>Dostęp bezprzewodowy | Wyłącz sieć bezprzewodową

**Identyfikator:** NIST SP 800-53 R4 AC-18 (3) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1070 — dostęp bezprzewodowy \| wyłącz sieć bezprzewodową](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68f837d0-8942-4b1e-9b31-be78b247bda8) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1070.json) |

### <a name="wireless-access--restrict-configurations-by-users"></a>Dostęp bezprzewodowy | Ograniczanie konfiguracji przez użytkowników

**Identyfikator:** NIST SP 800-53 R4 AC-18 (4) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1071 — ograniczanie dostępu \| bezprzewodowego konfiguracji przez użytkowników](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a437f5b-9ad6-4f28-8861-de404d511ae4) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1071.json) |

### <a name="wireless-access--antennas--transmission-power-levels"></a>Dostęp bezprzewodowy | Poziomy zasilania transmisji/chłoniaki

**Identyfikator:** NIST SP 800-53 R4 AC-18 (5) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1072 — dostęp \| bezprzewodowy — poziomy zasilania/transmisji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ca29e41-34ec-4e70-aba9-6248aca18c31) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1072.json) |

### <a name="access-control-for-mobile-devices"></a>Access Control dla urządzeń przenośnych

**Identyfikator:** Własność NIST SP 800-53 R4 AC-19: Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1073 — Access Control dla urządzeń przenośnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fab55cdb0-c7dd-4bd8-ae22-a7cea7594e9c) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1073.json) |
|[Microsoft Managed Control 1074 — Access Control dla urządzeń przenośnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F27a69937-af92-4198-9b86-08d355c7e59a) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1074.json) |

### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Access Control dla urządzeń przenośnych | Pełne szyfrowanie urządzenia/Container-Based danych

**Identyfikator:** NIST SP 800-53 R4 AC-19 (5) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1075 — Access Control for Mobile Devices \| Full Device /Container-Based Encryption](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc933d22-04df-48ed-8f87-22a3773d4309) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1075.json) |

### <a name="use-of-external-information-systems"></a>Korzystanie z zewnętrznych systemów informacyjnych

**Identyfikator:** NIST SP 800-53 R4 AC-20 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1076 — korzystanie z zewnętrznych systemów informacyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98a4bd5f-6436-46d4-ad00-930b5b1dfed4) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1076.json) |
|[Microsoft Managed Control 1077 — korzystanie z zewnętrznych systemów informacyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2dad3668-797a-412e-a798-07d3849a7a79) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1077.json) |

### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Korzystanie z zewnętrznych systemów informacyjnych | Limity dotyczące autoryzowanego użycia

**Identyfikator:** NIST SP 800-53 R4 AC-20 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1078 — korzystanie z limitów zewnętrznych systemów informacji \| przy autoryzowanym użyciu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb25faf85-8a16-4f28-8e15-d05c0072d64d) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1078.json) |
|[Microsoft Managed Control 1079 — korzystanie z limitów zewnętrznych systemów informacji \| przy autoryzowanym użyciu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F85c32733-7d23-4948-88da-058e2c56b60f) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1079.json) |

### <a name="use-of-external-information-systems--portable-storage-devices"></a>Korzystanie z zewnętrznych systemów informacji | Przenośne urządzenia magazynu

**Identyfikator:** NIST SP 800-53 R4 AC-20 (2) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1080 — korzystanie z przenośnych urządzeń magazynujących zewnętrznych \| systemów informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F852981b4-a380-4704-aa1e-2e52d63445e5) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1080.json) |

### <a name="information-sharing"></a>Udostępnianie informacji

**Identyfikator:** Własność NIST SP 800-53 R4 AC-21: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1081 — information sharing (Kontrola zarządzana przez firmę Microsoft 1081 — udostępnianie informacji)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3867f2a9-23bb-4729-851f-c3ad98580caf) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1081.json) |
|[Microsoft Managed Control 1082 — information sharing (Microsoft Managed Control 1082 — udostępnianie informacji)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24d480ef-11a0-4b1b-8e70-4e023bf2be23) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1082.json) |

### <a name="publicly-accessible-content"></a>Publicznie dostępna zawartość

**Identyfikator:** Własność NIST SP 800-53 R4 AC-22: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1083 — publicznie dostępna zawartość](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e319cb6-2ca3-4a58-ad75-e67f484e50ec) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1083.json) |
|[Microsoft Managed Control 1084 — publicznie dostępna zawartość](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0eb15db-dd1c-4d1d-b200-b12dd6cd060c) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1084.json) |
|[Microsoft Managed Control 1085 — publicznie dostępna zawartość](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d117e0-38b0-4bbb-aaab-563be5dd10ba) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1085.json) |
|[Microsoft Managed Control 1086 — publicznie dostępna zawartość](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb321e6f-16a0-4be3-878f-500956e309c5) |Firma Microsoft implementuje tę Access Control kontroli |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1086.json) |

## <a name="awareness-and-training"></a>Świadomość i szkolenia

### <a name="security-awareness-and-training-policy-and-procedures"></a>Zasady i procedury dotyczące świadomości zabezpieczeń i szkolenia

**Identyfikator:** Własność NIST SP 800-53 R4 AT-1: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1087 — Security Awareness and Training Policy and Procedures (Zasady i procedury dotyczące szkolenia i świadomości zabezpieczeń zarządzane przez firmę Microsoft)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F100c82ba-42e9-4d44-a2ba-94b209248583) |Firma Microsoft implementuje tę kontrolę świadomość i szkolenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1087.json) |
|[Microsoft Managed Control 1088 — Security Awareness and Training Policy and Procedures (Zasady i procedury dotyczące szkolenia i świadomości zabezpieczeń zarządzanej przez firmę Microsoft)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d50f99d-1356-49c0-934a-45f742ba7783) |Firma Microsoft implementuje tę kontrolę świadomość i szkolenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1088.json) |

### <a name="security-awareness-training"></a>Security Awareness Training

**Identyfikator:** Własność NIST SP 800-53 R4 AT-2: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1089 — Security Awareness Training](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef080e67-0d1a-4f76-a0c5-fb9b0358485e) |Firma Microsoft implementuje tę kontrolę świadomości i szkolenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1089.json) |
|[Microsoft Managed Control 1090 — Security Awareness Training](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fb740e5-cbc7-4d10-8686-d1bf826652b1) |Firma Microsoft implementuje tę kontrolę świadomość i trenowanie |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1090.json) |
|[Microsoft Managed Control 1091 — Security Awareness Training](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb23bd715-5d1c-4e5c-9759-9cbdf79ded9d) |Firma Microsoft implementuje tę kontrolę świadomość i trenowanie |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1091.json) |

### <a name="security-awareness-training--insider-threat"></a>Security Awareness Training | Wewnętrzne zagrożenie

**Identyfikator:** NIST SP 800-53 R4 AT-2 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1092 — Security Awareness Training \| Insider Threat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8a29d47b-8604-4667-84ef-90d203fcb305) |Firma Microsoft implementuje tę kontrolę świadomość i trenowanie |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1092.json) |

### <a name="role-based-security-training"></a>Role-Based Security Training

**Identyfikator:** Własność NIST SP 800-53 R4 AT-3: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1093 — Role-Based Security Training](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a0bdeeb-15f4-47e8-a1da-9f769f845fdf) |Firma Microsoft implementuje tę kontrolę świadomość i trenowanie |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1093.json) |
|[Microsoft Managed Control 1094 — Role-Based Security Training](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b1853e0-8973-446b-b567-09d901d31a09) |Firma Microsoft implementuje tę kontrolę świadomość i szkolenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1094.json) |
|[Microsoft Managed Control 1095 — Role-Based Security Training](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc3f6f7a-057b-433e-9834-e8c97b0194f6) |Firma Microsoft implementuje tę kontrolę świadomości i szkolenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1095.json) |

### <a name="role-based-security-training--practical-exercises"></a>Role-Based Security Training | Ćwiczenia praktyczne

**Identyfikator:** NIST SP 800-53 R4 AT-3 (3) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1096 — Role-Based ćwiczenia praktyczne dotyczące zabezpieczeń \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F420c1477-aa43-49d0-bd7e-c4abdd9addff) |Firma Microsoft implementuje tę kontrolę świadomość i szkolenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1096.json) |

### <a name="role-based-security-training--suspicious-communications-and-anomalous-system-behavior"></a>Role-Based Security Training | Podejrzana komunikacja i anomalie zachowanie systemu

**Identyfikator:** NIST SP 800-53 R4 AT-3 (4) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1097 — Role-Based podejrzaną komunikację i anomalie zachowanie systemu w zakresie \| trenowania zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3e4836-f19e-47eb-a8cd-c3ca150452c0) |Firma Microsoft implementuje tę kontrolę świadomość i trenowanie |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1097.json) |

### <a name="security-training-records"></a>Rekordy trenowania zabezpieczeń

**Identyfikator:** Własność NIST SP 800-53 R4 AT-4: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1098 — rekordy trenowania zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84363adb-dde3-411a-9fc1-36b56737f822) |Firma Microsoft implementuje tę kontrolę świadomość i trenowanie |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1098.json) |
|[Microsoft Managed Control 1099 — rekordy trenowania zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01910bab-8639-4bd0-84ef-cc53b24d79ba) |Firma Microsoft implementuje tę kontrolę świadomość i trenowanie |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1099.json) |

## <a name="audit-and-accountability"></a>Inspekcja i odpowiedzialność

### <a name="audit-and-accountability-policy-and-procedures"></a>Zasady i procedury dotyczące inspekcji i odpowiedzialności

**Identyfikator:** Własność NIST SP 800-53 R4 AU-1: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1100 — zasady i procedury dotyczące inspekcji i odpowiedzialności](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4057863c-ca7d-47eb-b1e0-503580cba8a4) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1100.json) |
|[Microsoft Managed Control 1101 — zasady i procedury dotyczące inspekcji i odpowiedzialności](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7327b708-f0e0-457d-9d2a-527fcc9c9a65) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1101.json) |

### <a name="audit-events"></a>Zdarzenia inspekcji

**Identyfikator:** NIST SP 800-53 R4 AU-2 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1102 — zdarzenia inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9943c16a-c54c-4b4a-ad28-bfd938cdbf57) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1102.json) |
|[Microsoft Managed Control 1103 — zdarzenia inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16feeb31-6377-437e-bbab-d7f73911896d) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1103.json) |
|[Microsoft Managed Control 1104 — zdarzenia inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcdd8d244-18b2-4306-a1d1-df175ae0935f) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1104.json) |
|[Microsoft Managed Control 1105 — zdarzenia inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b73f57b-587d-4470-a344-0b0ae805f459) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1105.json) |

### <a name="audit-events--reviews-and-updates"></a>Inspekcja zdarzeń | Przeglądy i aktualizacje

**Identyfikator:** NIST SP 800-53 R4 AU-2 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1106 — inspekcja przeglądów \| i aktualizacji zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd2b4feae-61ab-423f-a4c5-0e38ac4464d8) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1106.json) |

### <a name="content-of-audit-records"></a>Zawartość rekordów inspekcji

**Identyfikator:** NIST SP 800-53 R4 AU-3 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1107 — zawartość rekordów inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb29ed931-8e21-4779-8458-27916122a904) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1107.json) |

### <a name="content-of-audit-records--additional-audit-information"></a>Zawartość rekordów inspekcji | Dodatkowe informacje o inspekcji

**Identyfikator:** NIST SP 800-53 R4 AU-3 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1108 — zawartość rekordów inspekcji — \| dodatkowe informacje o inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9ad559e-c12d-415e-9a78-e50fdd7da7ba) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1108.json) |

### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Zawartość rekordów inspekcji | Scentralizowane zarządzanie zawartością rekordów planowanej inspekcji

**Identyfikator:** NIST SP 800-53 R4 AU-3 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja obszaru roboczego usługi Log Analytics dla maszyny wirtualnej — niezgodność raportu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Raportuje maszyny wirtualne jako niezgodne, jeśli nie logują się do obszaru roboczego usługi Log Analytics określonego w przypisaniu zasad/inicjatywy. |Inspekcji |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Agent usługi Log Analytics powinien być włączony dla obrazów maszyn wirtualnych na liście](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Raportuje maszyny wirtualne jako niezgodne, jeśli obraz maszyny wirtualnej nie znajduje się na zdefiniowanej liście, a agent nie jest zainstalowany. |AuditIfNotExists, Disabled |[2.0.0 —wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Agenta usługi Log Analytics należy włączyć w zestawach skalowania maszyn wirtualnych dla wymienionych obrazów maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Zgłasza zestawy skalowania maszyn wirtualnych jako niezgodne, jeśli obraz maszyny wirtualnej nie znajduje się na liście zdefiniowanych i agent nie jest zainstalowany. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Microsoft Managed Control 1109 — zawartość rekordów inspekcji \| scentralizowane zarządzanie zawartością rekordu planowanej inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d9ffa23-ad92-4d0d-b1f4-7db274cc2aec) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1109.json) |

### <a name="audit-storage-capacity"></a>Inspekcja pojemności magazynu

**Identyfikator:** Własność NIST SP 800-53 R4 AU-4: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1110 — inspekcja pojemności magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6182bfa7-0f2a-43f5-834a-a2ddf31c13c7) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1110.json) |

### <a name="response-to-audit-processing-failures"></a>Odpowiedź na błędy przetwarzania inspekcji

**Identyfikator:** NIST SP 800-53 R4 AU-5 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zaawansowane zabezpieczenia danych powinny być włączone na SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Przejmij każdą SQL Managed Instance bez zaawansowanego zabezpieczeń danych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na serwerach SQL należy włączyć zaawansowane zabezpieczenia danych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Inspekcja serwerów SQL bez Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Audit diagnostic setting](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) (Przeprowadzaj inspekcję ustawienia diagnostyki) |Prze audit diagnostic setting for selected resource types (Przesłoń ustawienia diagnostyczne dla wybranych typów zasobów) |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Należy włączyć inspekcję na serwerze SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Należy włączyć inspekcję SQL Server, aby śledzić działania bazy danych we wszystkich bazach danych na serwerze i zapisywać je w dzienniku inspekcji. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Microsoft Managed Control 1111 — odpowiedź na błędy przetwarzania inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21de687c-f15e-4e51-bf8d-f35c8619965b) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1111.json) |
|[Microsoft Managed Control 1112 — odpowiedź na błędy przetwarzania inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd530aad8-4ee2-45f4-b234-c061dae683c0) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1112.json) |

### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Odpowiedź na błędy przetwarzania inspekcji | Inspekcja pojemności magazynu

**Identyfikator:** NIST SP 800-53 R4 AU-5 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1113 — response to Audit Processing Failures Audit Storage Capacity (Kontrola zarządzana przez firmę Microsoft 1113 — odpowiedź na błędy przetwarzania inspekcji \| Przejmij pojemność magazynu)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F562afd61-56be-4313-8fe4-b9564aa4ba7d) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1113.json) |

### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Odpowiedź na błędy przetwarzania inspekcji | Real-Time alerty

**Identyfikator:** NIST SP 800-53 R4 AU-5 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1114 — reagowanie na błędy przetwarzania inspekcji Real-Time \| alerty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c090801-59bc-4454-bb33-e0455133486a) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1114.json) |

### <a name="audit-review-analysis-and-reporting"></a>Przegląd inspekcji, analiza i raportowanie

**Identyfikator:** Własność NIST SP 800-53 R4 AU-6: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1115 — inspekcja — przegląd, analiza i raportowanie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b653845-2ad9-4e09-a4f3-5a7c1d78353d) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1115.json) |
|[Microsoft Managed Control 1116 — inspekcja — przegląd, analiza i raportowanie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e47bc51-35d1-44b8-92af-e2f2d8b67635) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1116.json) |

### <a name="audit-review-analysis-and-reporting--process-integration"></a>Inspekcja przeglądu, analizy i raportowania | Integracja procesów

**Identyfikator:** NIST SP 800-53 R4 AU-6 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1117 — integracja procesu przeglądu inspekcji, analizy i \| raportowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fbfe680-6dbb-4037-963c-a621c5635902) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1117.json) |

### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Inspekcja przeglądu, analizy i raportowania | Korelowanie repozytoriów inspekcji

**Identyfikator:** NIST SP 800-53 R4 AU-6 (3) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1118 — Audit Review, Analysis, and Reporting Correlate Audit Repositories (Kontrola zarządzana przez firmę Microsoft 1118 — inspekcja — przegląd, analiza i \| raportowanie korelowanie repozytoriów inspekcji)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96f743d-a195-420d-983a-08aa06bc441e) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1118.json) |

### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Inspekcja przeglądu, analizy i raportowania | Centralny przegląd i analiza

**Identyfikator:** NIST SP 800-53 R4 AU-6 (4) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja obszaru roboczego usługi Log Analytics dla maszyny wirtualnej — niezgodność raportów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Raportuje maszyny wirtualne jako niezgodne, jeśli nie logują się do obszaru roboczego usługi Log Analytics określonego w przypisaniu zasad/inicjatywy. |Inspekcji |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Agent usługi Log Analytics powinien być włączony dla obrazów maszyn wirtualnych na liście](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Raportuje maszyny wirtualne jako niezgodne, jeśli obraz maszyny wirtualnej nie znajduje się na liście zdefiniowanych i agent nie jest zainstalowany. |AuditIfNotExists, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Agenta usługi Log Analytics należy włączyć w zestawach skalowania maszyn wirtualnych dla obrazów maszyn wirtualnych na liście](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Raportuje zestawy skalowania maszyn wirtualnych jako niezgodne, jeśli obraz maszyny wirtualnej nie znajduje się na zdefiniowanej liście i agent nie jest zainstalowany. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Microsoft Managed Control 1119 — przegląd inspekcji, analiza i centralne raportowanie — \| przegląd i analiza](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F845f6359-b764-4b40-b579-657aefe23c44) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1119.json) |

### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Przegląd inspekcji, analiza i raportowanie | Możliwości integracji/skanowania i monitorowania

**Identyfikator:** NIST SP 800-53 R4 AU-6 (5) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1120 — audit review, analysis, and reporting integration /Scanning and Monitoring Capabilities (Kontrola zarządzana przez firmę Microsoft 1120 — integracja z przeglądem inspekcji, analizą i \| raportowaniem/ możliwości skanowania i monitorowania)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc69b870e-857b-458b-af02-bb234f7a00d3) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1120.json) |

### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Inspekcja przeglądu, analizy i raportowania | Korelacja z monitorowaniem fizycznym

**Identyfikator:** NIST SP 800-53 R4 AU-6 (6) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1121 — audit review, analysis, and reporting correlation with physical monitoring (Kontrola zarządzana przez firmę Microsoft 1121 — korelacja przeglądu, analizy i \| raportowania z monitorowaniem fizycznym)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc72b0eb9-1fc2-44e5-a866-e7cb0532f7c1) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1121.json) |

### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Inspekcja przeglądu, analizy i raportowania | Dozwolone akcje

**Identyfikator:** NIST SP 800-53 R4 AU-6 (7) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1122 — inspekcja dozwolonych akcji przeglądu, analizy \| i raportowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F243ec95e-800c-49d4-ba52-1fdd9f6b8b57) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1122.json) |

### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Inspekcja przeglądu, analizy i raportowania | Korekta poziomu inspekcji

**Identyfikator:** NIST SP 800-53 R4 AU-6 (10) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1123 — Audit Review, Analysis, and Reporting Audit Level Adjustment (Kontrola zarządzana przez firmę Microsoft 1123 — korekta poziomu inspekcji, analizy \| i raportowania)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03996055-37a4-45a5-8b70-3f1caa45f87d) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1123.json) |

### <a name="audit-reduction-and-report-generation"></a>Redukcja inspekcji i generowanie raportów

**Identyfikator:** Własność NIST SP 800-53 R4 AU-7: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1124 — redukcja inspekcji i generowanie raportów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10152dd-78f8-4335-ae2d-ad92cc028da4) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1124.json) |
|[Microsoft Managed Control 1125 — redukcja inspekcji i generowanie raportów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6ce745a-670e-47d3-a6c4-3cfe5ef00c10) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1125.json) |

### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Redukcja inspekcji i generowanie | Przetwarzanie automatyczne

**Identyfikator:** NIST SP 800-53 R4 AU-7 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1126 — redukcja inspekcji i automatyczne \| przetwarzanie generowania raportów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f37f71b-420f-49bf-9477-9c0196974ecf) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1126.json) |

### <a name="time-stamps"></a>Sygnatury czasowe

**Identyfikator:** NIST SP 800-53 R4 AU-8 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1127 — sygnatury czasowe](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ce328db-aef3-48ed-9f81-2ab7cf839c66) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1127.json) |
|[Microsoft Managed Control 1128 — sygnatury czasowe](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef212163-3bc4-4e86-bcf8-705127086393) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1128.json) |

### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Sygnatury | Synchronizacja ze źródłem czasu autorytatywnego

**Identyfikator:** NIST SP 800-53 R4 AU-8 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1129 — synchronizacja sygnatur czasu \| z autorytatywnym źródłem czasu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71bb965d-4047-4623-afd4-b8189a58df5d) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1129.json) |
|[Microsoft Managed Control 1130 — synchronizacja sygnatur czasu \| z autorytatywnym źródłem czasu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd7c4c1d-51ee-4349-9dab-89a7f8c8d102) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1130.json) |

### <a name="protection-of-audit-information"></a>Ochrona informacji inspekcji

**Identyfikator:** Własność NIST SP 800-53 R4 AU-9: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1131 — ochrona informacji inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb472a17e-c2bc-493f-b50b-42d55a346962) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1131.json) |

### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Ochrona informacji inspekcji | Inspekcja kopii zapasowej oddzielnych systemów fizycznych/składników

**Identyfikator:** NIST SP 800-53 R4 AU-9 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1132 — Ochrona kopii zapasowej inspekcji informacji inspekcji w \| oddzielnych systemach fizycznych/składnikach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05938e10-cdbd-4a54-9b2b-1cbcfc141ad0) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1132.json) |

### <a name="protection-of-audit-information--cryptographic-protection"></a>Ochrona informacji inspekcji | Ochrona kryptograficzna

**Identyfikator:** NIST SP 800-53 R4 AU-9 (3) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1133 — Protection of Audit Information \| Cryptographic Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90b60a09-133d-45bc-86ef-b206a6134bbe) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1133.json) |

### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Ochrona informacji inspekcji | Dostęp według podzbioru użytkowników uprzywilejowanych

**Identyfikator:** NIST SP 800-53 R4 AU-9 (4) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1134 — Protection of Audit Information Access by Subset of Privileged Users (Kontrola zarządzana przez firmę Microsoft 1134 — ochrona dostępu do informacji inspekcji według \| podzbioru uprzywilejowanych użytkowników)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e95f70e-181c-4422-9da2-43079710c789) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1134.json) |

### <a name="non-repudiation"></a>Niemożność wyparcia się

**Identyfikator:** NIST SP 800-53 R4 AU-10 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1135 — non-Repudiation](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c308b6b-2429-4b97-86cf-081b8e737b04) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1135.json) |

### <a name="audit-record-retention"></a>Inspekcja przechowywania rekordów

**Identyfikator:** NIST SP 800-53 R4 AU-11 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1136 — Inspekcja przechowywania rekordów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97ed5bac-a92f-4f6d-a8ed-dc094723597c) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1136.json) |

### <a name="audit-generation"></a>Generowanie inspekcji

**Identyfikator:** NIST SP 800-53 R4 AU-12 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zaawansowane zabezpieczenia danych powinny być włączone na SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Inspekcja poszczególnych SQL Managed Instance bez zaawansowanych zabezpieczeń danych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na serwerach SQL należy włączyć zaawansowane zabezpieczenia danych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Inspekcja serwerów SQL bez Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Audit diagnostic setting](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) (Przeprowadzaj inspekcję ustawienia diagnostyki) |Prze audit diagnostic setting for selected resource types (Przesłoń ustawienia diagnostyczne dla wybranych typów zasobów) |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Inspekcja obszaru roboczego usługi Log Analytics dla maszyny wirtualnej — niezgodność raportu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Raportuje maszyny wirtualne jako niezgodne, jeśli nie logują się do obszaru roboczego usługi Log Analytics określonego w przypisaniu zasad/inicjatywy. |Inspekcji |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Inspekcja na serwerze SQL powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Należy włączyć inspekcję SQL Server, aby śledzić działania bazy danych we wszystkich bazach danych na serwerze i zapisywać je w dzienniku inspekcji. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Agent usługi Log Analytics powinien być włączony dla obrazów maszyn wirtualnych na liście](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Raportuje maszyny wirtualne jako niezgodne, jeśli obraz maszyny wirtualnej nie znajduje się na zdefiniowanej liście i agent nie jest zainstalowany. |AuditIfNotExists, Disabled |[2.0.0 —wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Agenta usługi Log Analytics należy włączyć w zestawach skalowania maszyn wirtualnych dla obrazów maszyn wirtualnych na liście](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Zgłasza zestawy skalowania maszyn wirtualnych jako niezgodne, jeśli obraz maszyny wirtualnej nie znajduje się na liście zdefiniowanych i agent nie jest zainstalowany. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Microsoft Managed Control 1137 — generowanie inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4344df62-88ab-4637-b97b-bcaf2ec97e7c) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1137.json) |
|[Microsoft Managed Control 1138 — generowanie inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c284fc0-268a-4f29-af44-3c126674edb4) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1138.json) |
|[Microsoft Managed Control 1139 — generowanie inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ed62522-de00-4dda-9810-5205733d2f34) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1139.json) |

### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Inspekcja generowania | System-Wide/ Time-Correlated inspekcji

**Identyfikator:** NIST SP 800-53 R4 AU-12 (1) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1140 — dziennik inspekcji \| generowania System-Wide/ Time-Correlated inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90d8b8ad-8ee3-4db7-913f-2a53fcff5316) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1140.json) |

### <a name="audit-generation--changes-by-authorized-individuals"></a>Inspekcja generowania | Zmiany według autoryzowanych osób

**Identyfikator:** NIST SP 800-53 R4 AU-12 (3) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1141 — inspekcja zmian \| generowania przez autoryzowane osoby](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fdefbf4-93e7-4513-bc95-c1858b7093e0) |Firma Microsoft implementuje tę kontrolę inspekcji i odpowiedzialności |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1141.json) |

## <a name="security-assessment-and-authorization"></a>Ocena zabezpieczeń i autoryzacja

### <a name="security-assessment-and-authorization-policy-and-procedures"></a>Zasady i procedury oceny zabezpieczeń i autoryzacji

**Identyfikator:** Własność NIST SP 800-53 R4 CA-1: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1142 — security assessment and authorization policy and procedures (Kontrola zarządzana przez firmę Microsoft 1142 — procedury i zasady oceny zabezpieczeń oraz autoryzacji)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01524fa8-4555-48ce-ba5f-c3b8dcef5147) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1142.json) |
|[Microsoft Managed Control 1143 — procedury i zasady oceny zabezpieczeń oraz autoryzacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c6de11b-5f51-4f7c-8d83-d2467c8a816e) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1143.json) |

### <a name="security-assessments"></a>Oceny zabezpieczeń

**Identyfikator:** Własność NIST SP 800-53 R4 CA-2: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1144 — oceny zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fa15ff1-a693-4ee4-b094-324818dc9a51) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1144.json) |
|[Microsoft Managed Control 1145 — oceny zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0724970-9c75-4a64-a225-a28002953f28) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1145.json) |
|[Microsoft Managed Control 1146 — oceny zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd83410c-ecb6-4547-8f14-748c3cbdc7ac) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1146.json) |
|[Microsoft Managed Control 1147 — oceny zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fef824a-29a8-4a4c-88fc-420a39c0d541) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1147.json) |

### <a name="security-assessments--independent-assessors"></a>Oceny zabezpieczeń | Niezależni oceniacze

**Identyfikator:** NIST SP 800-53 R4 CA-2 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1148 — niezależni oceniani \| ocen zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e62650-c7c2-4786-bdfa-17edc1673902) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1148.json) |

### <a name="security-assessments--specialized-assessments"></a>Oceny zabezpieczeń | Wyspecjalizowane oceny

**Identyfikator:** NIST SP 800-53 R4 CA-2 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1149 — oceny zabezpieczeń — \| wyspecjalizowane oceny](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e1b855b-a013-481a-aeeb-2bcb129fd35d) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1149.json) |

### <a name="security-assessments--external-organizations"></a>Oceny zabezpieczeń | Organizacje zewnętrzne

**Identyfikator:** NIST SP 800-53 R4 CA-2 (3) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1150 — oceny zabezpieczeń \| w organizacjach zewnętrznych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd630429d-e763-40b1-8fba-d20ba7314afb) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1150.json) |

### <a name="system-interconnections"></a>Wzajemne połączenia systemowe

**Identyfikator:** Własność NIST SP 800-53 R4 CA-3: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1151 — wzajemne połączenia systemów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F347e3b69-7fb7-47df-a8ef-71a1a7b44bca) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1151.json) |
|[Microsoft Managed Control 1152 — wzajemne połączenia systemów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbeff0acf-7e67-40b2-b1ca-1a0e8205cf1b) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1152.json) |
|[Microsoft Managed Control 1153 — wzajemne połączenia systemów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61cf3125-142c-4754-8a16-41ab4d529635) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1153.json) |

### <a name="system-interconnections--unclassified-non-national-security-system-connections"></a>Połączenia systemowe | Niesklasyfikowane połączenia nienarodowego systemu zabezpieczeń

**Identyfikator:** NIST SP 800-53 R4 CA-3 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1154 — wzajemne połączenia systemu niesklasyfikowane nieklasyfikowane \| nienarodowe połączenia z systemem zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe757ceb9-93b3-45fe-a4f4-f43f64f1ac5a) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1154.json) |

### <a name="system-interconnections--restrictions-on-external-system-connections"></a>Połączenia systemowe | Ograniczenia dotyczące połączeń systemu zewnętrznego

**Identyfikator:** NIST SP 800-53 R4 CA-3 (5) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1155 - System Connectionss Restrictions On External System Connections (Kontrola zarządzana przez firmę Microsoft 1155 — ograniczenia połączeń \| systemowych dla połączeń z systemami zewnętrznymi)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d33f9f1-12d0-46ad-9fbd-8f8046694977) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1155.json) |

### <a name="plan-of-action-and-milestones"></a>Plan działania i kamienie milowe

**Identyfikator:** NIST SP 800-53 R4 CA-5 Ownership : Shared (Własność NIST SP 800-53 R4 CA-5): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1156 — plan działania i kamienie milowe](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d52e864-9a3b-41ee-8f03-520815fe5378) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1156.json) |
|[Microsoft Managed Control 1157 — plan działania i kamienie milowe](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F15495367-cf68-464c-bbc3-f53ca5227b7a) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1157.json) |

### <a name="security-authorization"></a>Autoryzacja zabezpieczeń

**Identyfikator:** Własność NIST SP 800-53 R4 CA-6: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1158 — autoryzacja zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffff50cf2-28eb-45b4-b378-c99412688907) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1158.json) |
|[Microsoft Managed Control 1159 — autoryzacja zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0925f098-7877-450b-8ba4-d1e55f2d8795) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1159.json) |
|[Microsoft Managed Control 1160 — autoryzacja zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e797ca6-2aa8-4333-b335-7036f1110c05) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1160.json) |

### <a name="continuous-monitoring"></a>Ciągłe monitorowanie

**Identyfikator:** Własność NIST SP 800-53 R4 CA-7: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1161 — ciągłe monitorowanie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2f8f6c6-dde4-436b-a79d-bc50e129eb3a) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1161.json) |
|[Microsoft Managed Control 1162 — continuous monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5770f3d6-8c2b-4f6f-bf0e-c8c8fc36d592) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1162.json) |
|[Microsoft Managed Control 1163 — ciągłe monitorowanie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F961663a1-8a91-4e59-b6f5-1eee57c0f49c) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1163.json) |
|[Microsoft Managed Control 1164 — continuous monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fb8d3ce-9e96-481c-9c68-88d4e3019310) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1164.json) |
|[Microsoft Managed Control 1165 — ciągłe monitorowanie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47e10916-6c9e-446b-b0bd-ff5fd439d79d) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1165.json) |
|[Microsoft Managed Control 1166 — continuous monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb02733d-3cc5-4bb0-a6cd-695ba2c2272e) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1166.json) |
|[Microsoft Managed Control 1167 — continuous monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcbb2be76-4891-430b-95a7-ca0b0a3d1300) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1167.json) |

### <a name="continuous-monitoring--independent-assessment"></a>Monitorowanie ciągłe | Niezależna ocena

**Identyfikator:** NIST SP 800-53 R4 CA-7 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1168 — ciągła ocena \| niezależna od monitorowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82409f9e-1f32-4775-bf07-b99d53a91b06) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1168.json) |

### <a name="continuous-monitoring--trend-analyses"></a>Ciągłe monitorowanie | Analizy trendów

**Identyfikator:** NIST SP 800-53 R4 CA-7 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1169 — analiza \| trendu ciągłego monitorowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7ba2cb3-5675-4468-8b50-8486bdd998a5) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1169.json) |

### <a name="penetration-testing"></a>Testy penetracyjne

**Identyfikator:** NIST SP 800-53 R4 CA-8 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1170 — testy penetracyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b78b9b3-ee3c-48e0-a243-ed6dba5b7a12) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1170.json) |

### <a name="penetration-testing--independent-penetration-agent-or-team"></a>Testy penetracyjnych | Niezależny agent penetrujący lub zespół

**Identyfikator:** NIST SP 800-53 R4 CA-8 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1171 — test penetrujący niezależny agent lub \| zespół penetrujący](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d4820bc-8b61-4982-9501-2123cb776c00) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1171.json) |

### <a name="internal-system-connections"></a>Wewnętrzne połączenia systemowe

**Identyfikator:** Własność NIST SP 800-53 R4 CA-9: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1172 — wewnętrzne połączenia systemowe](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb43e946e-a4c8-4b92-8201-4a39331db43c) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1172.json) |
|[Microsoft Managed Control 1173 — wewnętrzne połączenia systemowe](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4aff9e7-2e60-46fa-86be-506b79033fc5) |Firma Microsoft implementuje tę kontrolę oceny zabezpieczeń i autoryzacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1173.json) |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

### <a name="configuration-management-policy-and-procedures"></a>Zasady i procedury zarządzania konfiguracją

**Identyfikator:** NIST SP 800-53 R4 CM-1 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1174 — zasady i procedury zarządzania konfiguracją](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42a9a714-8fbb-43ac-b115-ea12d2bd652f) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1174.json) |
|[Microsoft Managed Control 1175 — zasady i procedury zarządzania konfiguracją](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6dab4254-c30d-4bb7-ae99-1d21586c063c) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1175.json) |

### <a name="baseline-configuration"></a>Konfiguracja linii bazowej

**Identyfikator:** Własność NIST SP 800-53 R4 CM-2: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1176 — konfiguracja linii bazowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc30690a5-7bf3-467f-b0cd-ef5c7c7449cd) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1176.json) |

### <a name="baseline-configuration--reviews-and-updates"></a>Konfiguracja linii bazowej | Przeglądy i aktualizacje

**Identyfikator:** NIST SP 800-53 R4 CM-2 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1177 — przeglądy i aktualizacje konfiguracji linii bazowej \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63dbc7a8-e20b-4d38-b857-a7f6c0cd94bc) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1177.json) |
|[Microsoft Managed Control 1178 — przeglądy i aktualizacje konfiguracji odniesienia \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7818b8f4-47c6-441a-90ae-12ce04e99893) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1178.json) |
|[Microsoft Managed Control 1179 — przeglądy i aktualizacje konfiguracji odniesienia \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f9ce557-c8ab-4e6c-bb2c-9b8ed002c46c) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1179.json) |

### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Konfiguracja linii bazowej | Obsługa automatyzacji dokładności/waluty

**Identyfikator:** NIST SP 800-53 R4 CM-2 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1180 — obsługa automatyzacji konfiguracji linii bazowej \| dla dokładności/waluty](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F874e7880-a067-42a7-bcbe-1a340f54c8cc) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1180.json) |

### <a name="baseline-configuration--retention-of-previous-configurations"></a>Konfiguracja linii bazowej | Przechowywanie poprzednich konfiguracji

**Identyfikator:** NIST SP 800-53 R4 CM-2 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1181 — przechowywanie konfiguracji odniesienia \| dla poprzednich konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21839937-d241-4fa5-95c6-b669253d9ab9) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1181.json) |

### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Konfiguracja linii bazowej | Konfigurowanie systemów, składników lub urządzeń dla High-Risk obszarów

**Identyfikator:** NIST SP 800-53 R4 CM-2 (7) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1182 — konfiguracja linii bazowej \| — konfigurowanie systemów, składników lub urządzeń dla High-Risk obszarów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f34f554-da4b-4786-8d66-7915c90893da) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1182.json) |
|[Microsoft Managed Control 1183 — konfiguracja linii bazowej \| — konfigurowanie systemów, składników lub urządzeń dla High-Risk obszarów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5352e3e0-e63a-452e-9e5f-9c1d181cff9c) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1183.json) |

### <a name="configuration-change-control"></a>Kontrolka zmiany konfiguracji

**Identyfikator:** NIST SP 800-53 R4 CM-3 Ownership : Shared (Własność NIST SP 800-53 R4 CM-3): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1184 — kontrola zmiany konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13579d0e-0ab0-4b26-b0fb-d586f6d7ed20) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1184.json) |
|[Microsoft Managed Control 1185 — kontrola zmian konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6420cd73-b939-43b7-9d99-e8688fea053c) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1185.json) |
|[Microsoft Managed Control 1186 — kontrola zmian konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb95ba3bd-4ded-49ea-9d10-c6f4b680813d) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1186.json) |
|[Microsoft Managed Control 1187 — kontrola zmian konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9f2b2f9e-4ba6-46c3-907f-66db138b6f85) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1187.json) |
|[Microsoft Managed Control 1188 — kontrola zmian konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb20548a-c926-4e4d-855c-bcddc6faf95e) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1188.json) |
|[Microsoft Managed Control 1189 — kontrola zmiany konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee45e02a-4140-416c-82c4-fecfea660b9d) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1189.json) |
|[Microsoft Managed Control 1190 — kontrola zmiany konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc66a3d1e-465b-4f28-9da5-aef701b59892) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1190.json) |

### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Kontrolka zmiany konfiguracji | Zautomatyzowany dokument/ powiadomienie / chyłanie zmian

**Identyfikator:** NIST SP 800-53 R4 CM-3 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1191 — automatyczny dokument/powiadomienie dotyczące kontroli zmian konfiguracji \| / zmiana zmian](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f26a61b-a74d-467c-99cf-63644db144f7) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1191.json) |
|[Microsoft Managed Control 1192 — automatyczny dokument/powiadomienie dotyczące kontroli zmian konfiguracji \| / zmiana zmian](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ebd97f7-b105-4f50-8daf-c51465991240) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1192.json) |
|[Microsoft Managed Control 1193 — automatyczny dokument/powiadomienie dotyczące kontroli zmian konfiguracji \| / zmiana zmian](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5fd629f-3075-4cae-ab53-bad65495a4ac) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1193.json) |
|[Microsoft Managed Control 1194 — automatyczny dokument/ powiadomienie / zmiana kontroli \| konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc34667f-397e-4a65-9b72-d0358f0b6b09) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1194.json) |
|[Microsoft Managed Control 1195 — automatyczne zarządzanie kontrolą zmian \| konfiguracji — dokument / powiadomienie / zmiana zmian](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1e1d65c-1013-4484-bd54-991332e6a0d2) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1195.json) |
|[Microsoft Managed Control 1196 — automatyczny dokument kontroli zmian \| konfiguracji / powiadomienie / zmiana zmian](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e7f4ea4-dd62-44f6-8886-ac6137cf52b0) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1196.json) |

### <a name="configuration-change-control--test--validate--document-changes"></a>Kontrolka zmiany konfiguracji | Testowanie/weryfikowanie/zmiany dokumentu

**Identyfikator:** NIST SP 800-53 R4 CM-3 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1197 — test kontroli zmian \| konfiguracji / weryfikacja / zmiany dokumentu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa20d2eaa-88e2-4907-96a2-8f3a05797e5c) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1197.json) |

### <a name="configuration-change-control--security-representative"></a>Kontrolka zmiany konfiguracji | Przedstawiciel ds. zabezpieczeń

**Identyfikator:** NIST SP 800-53 R4 CM-3 (4) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1198 — przedstawiciel zabezpieczeń kontroli zmian \| konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff56be5c3-660b-4c61-9078-f67cf072c356) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1198.json) |

### <a name="configuration-change-control--cryptography-management"></a>Kontrolka zmiany konfiguracji | Zarządzanie kryptografią

**Identyfikator:** NIST SP 800-53 R4 CM-3 (6) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1199 — zarządzanie kryptografią przy kontroli zmian \| konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9a08d1c-09b1-48f1-90ea-029bbdf7111e) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1199.json) |

### <a name="security-impact-analysis"></a>Analiza wpływu na zabezpieczenia

**Identyfikator:** NIST SP 800-53 R4 CM-4 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1200 — Analiza wpływu na zabezpieczenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe98fe9d7-2ed3-44f8-93b7-24dca69783ff) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1200.json) |

### <a name="security-impact-analysis--separate-test-environments"></a>Analiza wpływu na zabezpieczenia | Oddzielne środowiska testowe

**Identyfikator:** NIST SP 800-53 R4 CM-4 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1201 — Security Impact Analysis \| Separate Test Environments](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7daef997-fdd3-461b-8807-a608a6dd70f1) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1201.json) |

### <a name="access-restrictions-for-change"></a>Ograniczenia dostępu do zmiany

**Identyfikator:** NIST SP 800-53 R4 CM-5 Ownership : Shared (Własność NIST SP 800-53 R4 CM-5): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1202 — ograniczenia dostępu do zmiany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40a2a83b-74f2-4c02-ae65-f460a5d2792a) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1202.json) |

### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Ograniczenia dostępu dotyczące zmian | Automatyczne wymuszanie dostępu/ inspekcja

**Identyfikator:** NIST SP 800-53 R4 CM-5 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1203 — ograniczenia dostępu do automatycznego wymuszania/inspekcji dostępu do \| zmiany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9012d14-e3e6-4d7b-b926-9f37b5537066) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1203.json) |

### <a name="access-restrictions-for-change--review-system-changes"></a>Ograniczenia dostępu dotyczące zmian | Przegląd zmian systemu

**Identyfikator:** NIST SP 800-53 R4 CM-5 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1204 — ograniczenia dostępu dotyczące zmian \| w systemie przeglądu zmian](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f4f6750-d1ab-4a4c-8dfd-af3237682665) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1204.json) |

### <a name="access-restrictions-for-change--signed-components"></a>Ograniczenia dostępu dotyczące zmian | Podpisane składniki

**Identyfikator:** NIST SP 800-53 R4 CM-5 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1205 — ograniczenia dostępu dla zmiany \| podpisanych składników](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b070cab-0fb8-4e48-ad29-fc90b4c2797c) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1205.json) |

### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Ograniczenia dostępu dotyczące zmian | Ograniczanie uprawnień produkcyjnych/operacyjnych

**Identyfikator:** NIST SP 800-53 R4 CM-5 (5) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1206 — access restrictions for change limit production/operational privileges (Kontrola zarządzana przez firmę Microsoft 1206 — ograniczenia dostępu do limitu zmian \| — uprawnienia produkcyjne/operacyjne)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0de232d-02a0-4652-872d-88afb4ae5e91) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1206.json) |
|[Microsoft Managed Control 1207 — ograniczenia dostępu do limitu zmian \| — uprawnienia produkcyjne/operacyjne](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8713a0ed-0d1e-4d10-be82-83dffb39830e) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1207.json) |

### <a name="configuration-settings"></a>Ustawienia konfiguracji

**Identyfikator:** NIST SP 800-53 R4 CM-6 Ownership : Shared (Własność NIST SP 800-53 R4 CM-6): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1208 — ustawienia konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5ea87673-d06b-456f-a324-8abcee5c159f) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1208.json) |
|[Microsoft Managed Control 1209 — ustawienia konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fce669c31-9103-4552-ae9c-cdef4e03580d) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1209.json) |
|[Microsoft Managed Control 1210 — ustawienia konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3502c968-c490-4570-8167-1476f955e9b8) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1210.json) |
|[Microsoft Managed Control 1211 — ustawienia konfiguracji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a8b9dc8-6b00-4701-aa96-bba3277ebf50) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1211.json) |

### <a name="configuration-settings--automated-central-management--application--verification"></a>Ustawienia konfiguracji | Zautomatyzowane centralne zarządzanie/aplikacja/weryfikacja

**Identyfikator:** NIST SP 800-53 R4 CM-6 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1212 — Configuration Settings \| Automated Central Management / Application / Verification](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56d970ee-4efc-49c8-8a4e-5916940d784c) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1212.json) |

### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Ustawienia konfiguracji | Reagowanie na nieautoryzowane zmiany

**Identyfikator:** NIST SP 800-53 R4 CM-6 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1213 — ustawienia konfiguracji \| reagują na nieautoryzowane zmiany](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81f11e32-a293-4a58-82cd-134af52e2318) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1213.json) |

### <a name="least-functionality"></a>Najmniejsza funkcjonalność

**Identyfikator:** NIST SP 800-53 R4 CM-7 Ownership : Shared (Własność NIST SP 800-53 R4 CM-7): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1214 — najmniejsza funkcjonalność](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff714a4e2-b580-47b6-ae8c-f2812d3750f3) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1214.json) |
|[Microsoft Managed Control 1215 — najmniejsza funkcjonalność](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88fc93e8-4745-4785-b5a5-b44bb92c44ff) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1215.json) |

### <a name="least-functionality--periodic-review"></a>Najmniejsza funkcjonalność | Okresowy przegląd

**Identyfikator:** NIST SP 800-53 R4 CM-7 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1216 — okresowy przegląd \| najmniejszych funkcjonalności](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7894fe6a-f5cb-44c8-ba90-c3f254ff9484) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1216.json) |
|[Microsoft Managed Control 1217 — okresowy przegląd \| najmniejszych funkcjonalności](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedea4f20-b02c-4115-be75-86c080e5c0ed) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1217.json) |

### <a name="least-functionality--prevent-program-execution"></a>Najmniejsza funkcjonalność | Zapobieganie wykonywaniu programu

**Identyfikator:** NIST SP 800-53 R4 CM-7 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Na maszynach należy włączyć funkcje adaptacyjnego sterowania aplikacjami do definiowania bezpiecznych aplikacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Włącz kontrolki aplikacji, aby zdefiniować listę znanych bezpiecznych aplikacji uruchomionych na maszynach i powiadamiać o uruchomieniu innych aplikacji. Ułatwia to ochronę maszyn przed złośliwym oprogramowaniem. Aby uprościć proces konfigurowania i obsługi reguł, program Security Center uczenia maszynowego do analizowania aplikacji uruchomionych na poszczególnych maszynach i sugerowania listy znanych bezpiecznych aplikacji. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Microsoft Managed Control 1218 — najmniejsza funkcjonalność \| uniemożliwia wykonywanie programu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a1d0394-b9f5-493e-9e83-563fd0ac4df8) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1218.json) |

### <a name="least-functionality--authorized-software--whitelisting"></a>Najmniejsza funkcjonalność | Autoryzowane oprogramowanie/lista dozwolonych

**Identyfikator:** NIST SP 800-53 R4 CM-7 (5) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Na maszynach należy włączyć funkcje adaptacyjnego sterowania aplikacjami do definiowania bezpiecznych aplikacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Włącz kontrolki aplikacji, aby zdefiniować listę znanych bezpiecznych aplikacji uruchomionych na maszynach i powiadamiać o uruchomieniu innych aplikacji. Ułatwia to ochronę maszyn przed złośliwym oprogramowaniem. Aby uprościć proces konfigurowania i obsługi reguł, program Security Center uczenia maszynowego do analizowania aplikacji uruchomionych na poszczególnych maszynach i sugerowania listy znanych bezpiecznych aplikacji. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Microsoft Managed Control 1219 — najmniejsza funkcjonalność \| autoryzowanego oprogramowania/lista dozwolonych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a39ac75-622b-4c88-9a3f-45b7373f7ef7) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1219.json) |
|[Microsoft Managed Control 1220 — najmniejsza \| autoryzowana funkcjonalność oprogramowania / lista dozwolonych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc40f31a7-81e1-4130-99e5-a02ceea2a1d6) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1220.json) |
|[Microsoft Managed Control 1221 — najmniejsza \| autoryzowana funkcjonalność oprogramowania / lista dozwolonych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22589a07-0007-486a-86ca-95355081ae2a) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1221.json) |

### <a name="information-system-component-inventory"></a>Spis składników systemu informacji

**Identyfikator:** NIST SP 800-53 R4 CM-8 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1222 — Information System Component Inventory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb39e62f-6bda-4558-8088-ec03d5670914) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1222.json) |
|[Microsoft Managed Control 1223 — Information System Component Inventory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a1bb01-ad5a-49c1-aad3-b0c893b2ec3a) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1223.json) |

### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Spis składników systemu informacji | Aktualizacje podczas instalacji/usuwania

**Identyfikator:** NIST SP 800-53 R4 CM-8 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1224 — aktualizacje spisu składników systemu informacji \| podczas instalacji/usuwania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28cfa30b-7f72-47ce-ba3b-eed26c8d2c82) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1224.json) |

### <a name="information-system-component-inventory--automated-maintenance"></a>Spis składników systemu informacji | Automatyczna konserwacja

**Identyfikator:** NIST SP 800-53 R4 CM-8 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1225 — automatyczna konserwacja spisu składników systemu \| informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d096fe0-f510-4486-8b4d-d17dc230980b) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1225.json) |

### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Spis składników systemu informacji | Automatyczne wykrywanie nieautoryzowanych składników

**Identyfikator:** NIST SP 800-53 R4 CM-8 (3) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1226 — Information System Component Inventory \| Automated Unauthorized Component Detection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc158eb1c-ae7e-4081-8057-d527140c4e0c) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1226.json) |
|[Microsoft Managed Control 1227 — Information System Component Inventory \| Automated Unauthorized Component Detection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03b78f5e-4877-4303-b0f4-eb6583f25768) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1227.json) |

### <a name="information-system-component-inventory--accountability-information"></a>Informacje o spisie składników systemu | Informacje o odpowiedzialności

**Identyfikator:** NIST SP 800-53 R4 CM-8 (4) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1228 — informacje o odpowiedzialności spisu składników \| systemu informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F39c54140-5902-4079-8bb5-ad31936fe764) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1228.json) |

### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Spis składników systemu informacji | Brak zduplikowanego ewidencjonowania składników

**Identyfikator:** NIST SP 800-53 R4 CM-8 (5) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1229 — information system component inventory no duplicate accounting of Components (Kontrola zarządzana przez firmę Microsoft 1229 — spis składników systemu informacji bez \| zduplikowanego ewidencjonowania składników)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03752212-103c-4ab8-a306-7e813022ca9d) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1229.json) |

### <a name="configuration-management-plan"></a>Plan zarządzania konfiguracją

**Identyfikator:** Własność NIST SP 800-53 R4 CM-9: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1230 — plan zarządzania konfiguracją](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11158848-f679-4e9b-aa7b-9fb07d945071) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1230.json) |
|[Microsoft Managed Control 1231 — Plan zarządzania konfiguracją](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F244e0c05-cc45-4fe7-bf36-42dcf01f457d) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1231.json) |
|[Microsoft Managed Control 1232 — Configuration Management Plan (Microsoft Managed Control 1232 — plan zarządzania konfiguracją)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F396ba986-eac1-4d6d-85c4-d3fda6b78272) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1232.json) |
|[Microsoft Managed Control 1233 — Configuration Management Plan (Microsoft Managed Control 1233 — plan zarządzania konfiguracją)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d79001f-95fe-45d0-8736-f217e78c1f57) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1233.json) |

### <a name="software-usage-restrictions"></a>Ograniczenia użycia oprogramowania

**Identyfikator:** NIST SP 800-53 R4 CM-10 Ownership : Shared (Własność NIST SP 800-53 R4 CM-10): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1234 — ograniczenia użycia oprogramowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb293f881-361c-47ed-b997-bc4e2296bc0b) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1234.json) |
|[Microsoft Managed Control 1235 — ograniczenia użycia oprogramowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc49c610b-ece4-44b3-988c-2172b70d6e46) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1235.json) |
|[Microsoft Managed Control 1236 — ograniczenia użycia oprogramowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ba3ed84-c768-4e18-b87c-34ef1aff1b57) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1236.json) |

### <a name="software-usage-restrictions--open-source-software"></a>Ograniczenia użycia oprogramowania | Oprogramowanie open source

**Identyfikator:** NIST SP 800-53 R4 CM-10 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1237 — ograniczenia użycia oprogramowania \| typu open source](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe80b6812-0bfa-4383-8223-cdd86a46a890) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1237.json) |

### <a name="user-installed-software"></a>User-Installed Software

**Identyfikator:** Własność NIST SP 800-53 R4 CM-11: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Na maszynach należy włączyć funkcje adaptacyjnego sterowania aplikacjami do definiowania bezpiecznych aplikacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Włącz kontrolki aplikacji, aby zdefiniować listę znanych bezpiecznych aplikacji uruchomionych na maszynach i powiadamiać o uruchomieniu innych aplikacji. Ułatwia to ochronę maszyn przed złośliwym oprogramowaniem. Aby uprościć proces konfigurowania i obsługi reguł, program Security Center uczenia maszynowego do analizowania aplikacji uruchomionych na poszczególnych maszynach i sugerowania listy znanych bezpiecznych aplikacji. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Microsoft Managed Control 1238 — User-Installed Software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa36cedd4-3ffd-4b1f-8b18-aa71d8d87ce1) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1238.json) |
|[Microsoft Managed Control 1239 — User-Installed Software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0be51298-f643-4556-88af-d7db90794879) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1239.json) |
|[Microsoft Managed Control 1240 — User-Installed Software](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F129eb39f-d79a-4503-84cd-92f036b5e429) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1240.json) |

### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>User-Installed Software | Alerty dotyczące nieautoryzowanych instalacji

**Identyfikator:** NIST SP 800-53 R4 CM-11 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1241 — User-Installed \| alerty dotyczące oprogramowania w przypadku nieautoryzowanych instalacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feca4d7b2-65e2-4e04-95d4-c68606b063c3) |Firma Microsoft implementuje tę kontrolkę zarządzania konfiguracją |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1241.json) |

## <a name="contingency-planning"></a>Planowanie awaryjne

### <a name="contingency-planning-policy-and-procedures"></a>Zasady i procedury planowania awaryjnego

**Identyfikator:** Własność NIST SP 800-53 R4 CP-1: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1242 — zasady i procedury planowania awaryjnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3b3293-667a-445e-a722-fa0b0afc0958) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1242.json) |
|[Microsoft Managed Control 1243 — zasady i procedury planowania awaryjnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca9a4469-d6df-4ab2-a42f-1213c396f0ec) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1243.json) |

### <a name="contingency-plan"></a>Plan awaryjny

**Identyfikator:** Własność NIST SP 800-53 R4 CP-2: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1244 — plan awaryjny](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a13a8f8-c163-4b1b-8554-d63569dab937) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1244.json) |
|[Microsoft Managed Control 1245 — plan awaryjny](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0e45314-57b8-4623-80cd-bbb561f59516) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1245.json) |
|[Microsoft Managed Control 1246 — plan awaryjny](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F398eb61e-8111-40d5-a0c9-003df28f1753) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1246.json) |
|[Microsoft Managed Control 1247 — plan awaryjny](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e666db5-b2ef-4b06-aac6-09bfce49151b) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1247.json) |
|[Microsoft Managed Control 1248 — plan awaryjny](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50fc602d-d8e0-444b-a039-ad138ee5deb0) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1248.json) |
|[Microsoft Managed Control 1249 — plan awaryjny](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3bf4251-0818-42db-950b-afd5b25a51c2) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1249.json) |
|[Microsoft Managed Control 1250 — plan awaryjny](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8de614d8-a8b7-4f70-a62a-6d37089a002c) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1250.json) |

### <a name="contingency-plan--coordinate-with-related-plans"></a>Plan awaryjny | Koordynuj z powiązanymi planami

**Identyfikator:** NIST SP 800-53 R4 CP-2 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1251 — koordynowanie planu \| awaryjnego z powiązanymi planami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e2b3730-8c14-4081-8893-19dbb5de7348) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1251.json) |

### <a name="contingency-plan--capacity-planning"></a>Plan awaryjny | Planowanie pojemności

**Identyfikator:** NIST SP 800-53 R4 CP-2 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1252 — planowanie pojemności planu \| awaryjnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa328fd72-8ff5-4f96-8c9c-b30ed95db4ab) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1252.json) |

### <a name="contingency-plan--resume-essential-missions--business-functions"></a>Plan awaryjny | Wznawianie podstawowych misji/funkcji biznesowych

**Identyfikator:** NIST SP 800-53 R4 CP-2 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1253 — Plan awaryjny wznawia niezbędne \| misje/funkcje biznesowe](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0afce0b3-dd9f-42bb-af28-1e4284ba8311) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1253.json) |

### <a name="contingency-plan--resume-all-missions--business-functions"></a>Plan awaryjny | Wznawianie wszystkich misji/funkcji biznesowych

**Identyfikator:** NIST SP 800-53 R4 CP-2 (4) **Własność: Udostępnione**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1254 — plan awaryjny wznawia \| wszystkie misje/funkcje biznesowe](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F704e136a-4fe0-427c-b829-cd69957f5d2b) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1254.json) |

### <a name="contingency-plan--continue--essential-missions--business-functions"></a>Plan awaryjny | Kontynuacja podstawowych misji/funkcji biznesowych

**Identyfikator:** NIST SP 800-53 R4 CP-2 (5) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1255 — Plan awaryjny Kontynuuj \| — ważne misje/funkcje biznesowe](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff3793f5e-937f-44f7-bfba-40647ef3efa0) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1255.json) |

### <a name="contingency-plan--identify-critical-assets"></a>Plan awaryjny | Identyfikowanie krytycznych zasobów

**Identyfikator:** NIST SP 800-53 R4 CP-2 (8) **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1256 — plan awaryjny \| Identyfikowanie krytycznych zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F232ab24b-810b-4640-9019-74a7d0d6a980) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1256.json) |

### <a name="contingency-training"></a>Trenowania awaryjnego

**Identyfikator:** Własność NIST SP 800-53 R4 CP-3: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1257 — szkolenie awaryjne](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb958b241-4245-4bd6-bd2d-b8f0779fb543) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1257.json) |
|[Microsoft Managed Control 1258 — szkolenie awaryjne](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7814506c-382c-4d33-a142-249dd4a0dbff) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1258.json) |
|[Microsoft Managed Control 1259 — szkolenie awaryjne](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9e18f7-bad9-4d30-8806-a0c9d5e26208) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1259.json) |

### <a name="contingency-training--simulated-events"></a>Szkolenia awaryjne | Symulowane zdarzenia

**Identyfikator:** NIST SP 800-53 R4 CP-3 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1260 — symulowane zdarzenia trenowania \| awaryjnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42254fc4-2738-4128-9613-72aaa4f0d9c3) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1260.json) |

### <a name="contingency-plan-testing"></a>Testowanie planu awaryjnego

**Identyfikator:** Własność NIST SP 800-53 R4 CP-4: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1261 — testowanie planu awaryjnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65aeceb5-a59c-4cb1-8d82-9c474be5d431) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1261.json) |
|[Microsoft Managed Control 1262 — testowanie planu awaryjnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F831e510e-db41-4c72-888e-a0621ab62265) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1262.json) |
|[Microsoft Managed Control 1263 — testowanie planu awaryjnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41472613-3b05-49f6-8fe8-525af113ce17) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1263.json) |

### <a name="contingency-plan-testing--coordinate-with-related-plans"></a>Testowanie planu awaryjnego | Koordynuj z powiązanymi planami

**Identyfikator:** NIST SP 800-53 R4 CP-4 (1) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1264 — koordynowanie testowania planu awaryjnego \| z powiązanymi planami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd280d4b-50a1-42fb-a479-ece5878acf19) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1264.json) |

### <a name="contingency-plan-testing--alternate-processing-site"></a>Testowanie planu awaryjnego | Alternatywna lokacja przetwarzania

**Identyfikator:** NIST SP 800-53 R4 CP-4 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1265 — alternatywna witryna przetwarzania w planie \| awaryjnym](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa18adb5b-1db6-4a5b-901a-7d3797d12972) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1265.json) |
|[Microsoft Managed Control 1266 — testowania planu awaryjnego \| — alternatywna witryna przetwarzania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b4a3eb2-c25d-40bf-ad41-5094b6f59cee) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1266.json) |

### <a name="alternate-storage-site"></a>Alternatywna lokacja magazynu

**Identyfikator:** Własność NIST SP 800-53 R4 CP-6: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1267 — alternatywna witryna magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e97ba1d-be5d-4953-8da4-0cccf28f4805) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1267.json) |
|[Microsoft Managed Control 1268 — alternatywna witryna magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F23f6e984-3053-4dfc-ab48-543b764781f5) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1268.json) |

### <a name="alternate-storage-site--separation-from-primary-site"></a>Alternatywne lokacje magazynu | Separacja od lokacji głównej

**Identyfikator:** NIST SP 800-53 R4 CP-6 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1269 — alternatywne rozdzielenie lokacji \| magazynu od lokacji głównej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F19b9439d-865d-4474-b17d-97d2702fdb66) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1269.json) |

### <a name="alternate-storage-site--recovery-time--point-objectives"></a>Alternatywny magazyn lokacji | Cel punktu/czasu odzyskiwania

**Identyfikator:** NIST SP 800-53 R4 CP-6 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1270 — cel \| punktu/czasu odzyskiwania alternatywnej lokacji magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53c76a39-2097-408a-b237-b279f7b4614d) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1270.json) |

### <a name="alternate-storage-site--accessibility"></a>Alternatywny magazyn lokacji | Dostępności

**Identyfikator:** NIST SP 800-53 R4 CP-6 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1271 — dostępność alternatywnej lokacji \| magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3bfb53-9c46-4010-b3db-a7ba1296dada) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1271.json) |

### <a name="alternate-processing-site"></a>Alternatywna lokacja przetwarzania

**Identyfikator:** Własność NIST SP 800-53 R4 CP-7: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Inspekcja maszyn wirtualnych bez skonfigurowanego odzyskiwania po awarii](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Przejmij inspekcję maszyn wirtualnych, dla których nie skonfigurowano odzyskiwania po awarii. Aby dowiedzieć się więcej na temat odzyskiwania po awarii, odwiedź stronę [https://aka.ms/asr-doc](https://aka.ms/asr-doc) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Microsoft Managed Control 1272 — alternatywna witryna przetwarzania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae46cf7a-e3fd-427b-9b91-44bc78e2d9d8) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1272.json) |
|[Microsoft Managed Control 1273 — alternatywna witryna przetwarzania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe77fcbf2-a1e8-44f1-860e-ed6583761e65) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1273.json) |
|[Microsoft Managed Control 1274 — alternatywna witryna przetwarzania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2aee175f-cd16-4825-939a-a85349d96210) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1274.json) |

### <a name="alternate-processing-site--separation-from-primary-site"></a>Alternatywne przetwarzanie lokacji | Separacja od lokacji głównej

**Identyfikator:** NIST SP 800-53 R4 CP-7 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1275 — separacja lokacji \| przetwarzania alternatywnego od lokacji głównej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa23d9d53-ad2e-45ef-afd5-e6d10900a737) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1275.json) |

### <a name="alternate-processing-site--accessibility"></a>Alternatywne przetwarzanie lokacji | Dostępności

**Identyfikator:** NIST SP 800-53 R4 CP-7 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1276 — ułatwienia dostępu do alternatywnej witryny \| przetwarzania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe214e563-1206-4a43-a56b-ac5880c9c571) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1276.json) |

### <a name="alternate-processing-site--priority-of-service"></a>Alternatywne przetwarzanie lokacji | Priorytet usługi

**Identyfikator:** NIST SP 800-53 R4 CP-7 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1277 — alternatywny priorytet \| lokacji przetwarzania usługi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc43e829-3d50-4a0a-aa0f-428d551862aa) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1277.json) |

### <a name="alternate-processing-site--preparation-for-use"></a>Alternatywne przetwarzanie lokacji | Przygotowanie do użycia

**Identyfikator:** NIST SP 800-53 R4 CP-7 (4) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1278 — przygotowanie alternatywnej witryny \| przetwarzania do użycia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e5ef485-9e16-4c53-a475-fbb8107eac59) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1278.json) |

### <a name="telecommunications-services"></a>Usługi telekomunikacyjne

**Identyfikator:** NIST SP 800-53 R4 CP-8 **Ownership**(Własność NIST SP 800-53 R4 CP-8): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1279 — Usługi telekomunikacyjne](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d00bcd6-963d-4c02-ad8e-b45fa50bf3b0) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1279.json) |

### <a name="telecommunications-services--priority-of-service-provisions"></a>Usługi telekomunikacyjne | Priorytet a priori postanowień usługi

**Identyfikator:** NIST SP 800-53 R4 CP-8 (1) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1280 — priorytet usług \| telekomunikacyjnych dla a priori](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa108498-b3a8-4ffb-9e79-1107e76afad3) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1280.json) |
|[Microsoft Managed Control 1281 — priorytet usług \| telekomunikacyjnych dla a priori](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dc459b3-0e77-45af-8d71-cfd8c9654fe2) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1281.json) |

### <a name="telecommunications-services--single-points-of-failure"></a>Usługi telekomunikacyjne | Pojedyncze punkty awarii

**Identyfikator:** NIST SP 800-53 R4 CP-8 (2) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1282 — pojedyncze \| punkty awarii usług telekomunikacyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34042a97-ec6d-4263-93d2-8c1c46823b2a) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1282.json) |

### <a name="telecommunications-services--separation-of-primary--alternate-providers"></a>Usługi telekomunikacyjne | Separacja dostawców podstawowych/alternatywnych

**Identyfikator:** NIST SP 800-53 R4 CP-8 (3) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1283 — Separacja usług telekomunikacyjnych \| głównych/alternatywnych dostawców](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9172e76-7f56-46e9-93bf-75d69bdb5491) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1283.json) |

### <a name="telecommunications-services--provider-contingency-plan"></a>Usługi telekomunikacyjne | Plan awaryjny dostawcy

**Identyfikator:** NIST SP 800-53 R4 CP-8 (4) **Własność: Udostępnione**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1284 — plan awaryjny dostawcy usług \| telekomunikacyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F942b3e97-6ae3-410e-a794-c9c999b97c0b) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1284.json) |
|[Microsoft Managed Control 1285 — plan awaryjny dostawcy usług \| telekomunikacyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01f7726b-db54-45c2-bcb5-9bd7a43796ee) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1285.json) |
|[Microsoft Managed Control 1286 — plan awaryjny dostawcy usług \| telekomunikacyjnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4f9b47a-2116-4e6f-88db-4edbf22753f1) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1286.json) |

### <a name="information-system-backup"></a>Kopia zapasowa systemu informacji

**Identyfikator:** NIST SP 800-53 R4 CP-9 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1287 — information system backup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F819dc6da-289d-476e-8500-7e341ef8677d) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1287.json) |
|[Microsoft Managed Control 1288 — information system backup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d854c3b-a3e6-4ec9-9f0c-c7274dbaeb2f) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1288.json) |
|[Microsoft Managed Control 1289 — information system backup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a724864-956a-496c-b778-637cb1d762cf) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1289.json) |
|[Microsoft Managed Control 1290 — information system backup](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F92f85ce9-17b7-49ea-85ee-ea7271ea6b82) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1290.json) |

### <a name="information-system-backup--testing-for-reliability--integrity"></a>Kopia zapasowa systemu informacji | Testowanie niezawodności/integralności

**Identyfikator:** NIST SP 800-53 R4 CP-9 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1291 — Information System Backup Testing for Reliability / Integrity (Kontrola zarządzana przez firmę Microsoft 1291 — testowanie kopii zapasowej systemu informacji pod celu \| zapewnienia niezawodności/integralności)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8fd073-9c85-4ee2-a9d0-2e4ec9eb8912) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1291.json) |

### <a name="information-system-backup--test-restoration-using-sampling"></a>Informacje o systemie kopii | Przywracanie testów przy użyciu próbkowania

**Identyfikator:** NIST SP 800-53 R4 CP-9 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1292 — przywracanie testów kopii zapasowej systemu informacji \| przy użyciu próbkowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd03516cf-0293-489f-9b32-a18f2a79f836) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1292.json) |

### <a name="information-system-backup--separate-storage-for-critical-information"></a>Informacje o systemie kopii | Oddzielny magazyn dla informacji krytycznych

**Identyfikator:** NIST SP 800-53 R4 CP-9 (3) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1293 — Information System Backup \| Separate Storage for Critical Information](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87f7cd82-2e45-4d0f-9e2f-586b0962d142) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1293.json) |

### <a name="information-system-backup--transfer-to-alternate-storage-site"></a>Informacje o systemie tworzenia kopii zapasowych | Transfer do alternatywnej lokacji magazynu

**Identyfikator:** NIST SP 800-53 R4 CP-9 (5) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1294 — transfer kopii zapasowej systemu informacji \| do alternatywnej lokacji magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49dbe627-2c1e-438c-979e-dd7a39bbf81d) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1294.json) |

### <a name="information-system-recovery-and-reconstitution"></a>Informacje Odzyskiwanie systemu i skondycja

**Identyfikator:** NIST SP 800-53 R4 CP-10 **Ownership**: Customer

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1295 — informacje Odzyskiwanie systemu i skonsercja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa895fbdb-204d-4302-9689-0a59dc42b3d9) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1295.json) |

### <a name="information-system-recovery-and-reconstitution--transaction-recovery"></a>Informacje Odzyskiwanie systemu i | Odzyskiwanie transakcji

**Identyfikator:** NIST SP 800-53 R4 CP-10 (2) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1296 — odzyskiwanie Odzyskiwanie systemu informacji i odzyskiwania \| transakcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe57b98a0-a011-4956-a79d-5d17ed8b8e48) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1296.json) |

### <a name="information-system-recovery-and-reconstitution--restore-within-time-period"></a>Informacje Odzyskiwanie systemu i | Przywracanie w okresie

**Identyfikator:** NIST SP 800-53 R4 CP-10 (4) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1297 — informacje Odzyskiwanie systemu przywracania i \| przywracania zabezpieczeń w okresie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93fd8af1-c161-4bae-9ba9-f62731f76439) |Firma Microsoft implementuje tę kontrolę planowania awaryjnego |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1297.json) |

## <a name="identification-and-authentication"></a>Identyfikacja i uwierzytelnianie

### <a name="identification-and-authentication-policy-and-procedures"></a>Zasady i procedury identyfikacji i uwierzytelniania

**Identyfikator:** NIST SP 800-53 R4 IA-1 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1298 — zasady i procedury identyfikacji i uwierzytelniania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1dc784b5-4895-4d27-9d40-a06b032bd1ee) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1298.json) |
|[Microsoft Managed Control 1299 — zasady i procedury identyfikacji i uwierzytelniania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4e54f7-9ab0-4bae-b6cc-457809948a89) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1299.json) |

### <a name="identification-and-authentication-organizational-users"></a>Identyfikacja i uwierzytelnianie (użytkownicy organizacji)

**Identyfikator:** NIST SP 800-53 R4 IA-2 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1300 — identyfikacja i uwierzytelnianie (użytkownicy w organizacji)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99deec7d-5526-472e-b07c-3645a792026a) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1300.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identyfikacja i uwierzytelnianie (użytkownicy organizacji) | Dostęp sieciowy do uprzywilejowanych kont

**Identyfikator:** NIST SP 800-53 R4 IA-2 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do zapisu, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami właściciela w subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami właściciela, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[Microsoft Managed Control 1301 — identyfikacja i uwierzytelnianie (organizacja. użytkownicy) \| dostęp sieciowy do uprzywilejowanych kont](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8e0cc-ac23-468b-abe4-a8a1cc6d7a08) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1301.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identyfikacja i uwierzytelnianie (użytkownicy organizacyjni) | Dostęp sieciowy do kont bez uprawnień

**Identyfikator:** NIST SP 800-53 R4 IA-2 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uwierzytelniania wieloskładnikowego należy włączyć na kontach z uprawnieniami do odczytu w ramach subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Należy włączyć usługę Multi-Factor Authentication (MFA) dla wszystkich kont subskrypcji z uprawnieniami do odczytu, aby zapobiec naruszeniu zabezpieczeń kont lub zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Microsoft Managed Control 1302 - Identification and Authentication (Org. Users) Network access to non-Privileged Accounts (Kontrola zarządzana przez firmę Microsoft 1302 — dostęp sieciowy do kont bez uprawnień) i \| uwierzytelnianie (organizacja. użytkownicy)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09828c65-e323-422b-9774-9d5c646124da) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1302.json) |

### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Identyfikacja i uwierzytelnianie (użytkownicy organizacyjni) | Dostęp lokalny do kont uprzywilejowanych

**Identyfikator:** NIST SP 800-53 R4 IA-2 (3) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1303 — identyfikacja i uwierzytelnianie (organizacja. użytkownicy) dostęp \| lokalny do uprzywilejowanych kont](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F80ca0a27-918a-4604-af9e-723a27ee51e8) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1303.json) |

### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Identyfikacja i uwierzytelnianie (użytkownicy organizacji) | Dostęp lokalny do kont bez uprawnień

**Identyfikator:** NIST SP 800-53 R4 IA-2 (4) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1304 — identyfikacja i uwierzytelnianie (organizacja. użytkownicy) dostęp lokalny \| do kont niezauwilejnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6ca71be3-16cb-4d39-8b50-7f8fd5e2f11b) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1304.json) |

### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Identyfikacja i uwierzytelnianie (użytkownicy organizacji) | Uwierzytelnianie grupy

**Identyfikator:** NIST SP 800-53 R4 IA-2 (5) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1305 — Uwierzytelnianie grupowe identyfikacji i uwierzytelniania (organizacja. \| użytkownicy)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9166a8-1722-4b8f-847c-2cf3f2618b3d) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1305.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Identyfikacja i uwierzytelnianie (użytkownicy organizacyjni) | Dostęp sieciowy do uprzywilejowanych kont — odporny na powtarzanie

**Identyfikator:** NIST SP 800-53 R4 IA-2 (8) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1306 — sieć do identyfikacji i uwierzytelniania (użytkownicy \| organizacji). Dostęp do aplikacji Priv. Accts. — powtarzanie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcafc6c3c-5fc5-4c5e-a99b-a0ccb1d34eff) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1306.json) |

### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Identyfikacja i uwierzytelnianie (użytkownicy organizacyjni) | Dostęp sieciowy do kont bez uprawnień — odporny na powtarzanie

**Identyfikator:** NIST SP 800-53 R4 IA-2 (9) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1307 — sieć do identyfikacji i uwierzytelniania (użytkownicy \| organizacji). Dostęp do aplikacji innych niż Priv. Accts. — Powtarzanie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84e622c8-4bed-417c-84c6-b2fb0dd73682) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1307.json) |

### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Identyfikacja i uwierzytelnianie (użytkownicy organizacyjni) | Dostęp zdalny — osobne urządzenie

**Identyfikator:** NIST SP 800-53 R4 IA-2 (11) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1308 — dostęp zdalny do identyfikacji i uwierzytelniania (organizacja. użytkownicy) \| — osobne urządzenie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81817e1c-5347-48dd-965a-40159d008229) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1308.json) |

### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Identyfikacja i uwierzytelnianie (użytkownicy organizacji) | Akceptacja poświadczeń piv

**Identyfikator:** Własność NIST SP 800-53 R4 IA-2 (12) : Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1309 — identyfikacja i uwierzytelnianie (organizacja. użytkownicy) \| akceptowanie poświadczeń piv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff355d62b-39a8-4ba3-abf7-90f71cb3b000) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1309.json) |

### <a name="device-identification-and-authentication"></a>Identyfikacja i uwierzytelnianie urządzeń

**Identyfikator:** NIST SP 800-53 R4 IA-3 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1310 — identyfikacja i uwierzytelnianie urządzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F450d7ede-823d-4931-a99d-57f6a38807dc) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1310.json) |

### <a name="identifier-management"></a>Zarządzanie identyfikatorami

**Identyfikator:** NIST SP 800-53 R4 IA-4 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1311 — Zarządzanie identyfikatorami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7568697-0c9e-4ea3-9cec-9e567d14f3c6) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1311.json) |
|[Microsoft Managed Control 1312 — Identifier Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d6a5968-9eef-4c18-8534-376790ab7274) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1312.json) |
|[Microsoft Managed Control 1313 — Zarządzanie identyfikatorami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36220f5b-79a1-4cdb-8c74-2d2449f9a510) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1313.json) |
|[Microsoft Managed Control 1314 — Zarządzanie identyfikatorami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef0c8530-efd9-45b8-b753-f03083d06295) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1314.json) |
|[Microsoft Managed Control 1315 — Identifier Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87116-f1a1-4edb-bfbf-14e036f8d454) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1315.json) |

### <a name="identifier-management--identify-user-status"></a>Zarządzanie identyfikatorami | Identyfikowanie stanu użytkownika

**Identyfikator:** NIST SP 800-53 R4 IA-4 (4) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1316 — zarządzanie identyfikatorami \| — identyfikowanie stanu użytkownika](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8ce14753-66e5-465d-9841-26ef55c09c0d) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1316.json) |

### <a name="authenticator-management"></a>Authenticator Management

**Identyfikator:** NIST SP 800-53 R4 IA-5 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dodawanie tożsamości zarządzanej przypisanej przez system w celu włączenia przypisań konfiguracji gościa na maszynach wirtualnych bez tożsamości](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Te zasady dodają przypisaną przez system tożsamość zarządzaną do maszyn wirtualnych hostowanych na platformie Azure, które są obsługiwane przez konfigurację gościa, ale nie mają żadnych tożsamości zarządzanych. Tożsamość zarządzana przypisana przez system jest wymaganiem wstępnym dla wszystkich przypisań konfiguracji gościa i musi zostać dodana do maszyn przed użyciem definicji zasad konfiguracji gościa. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Modyfikowanie |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Dodawanie tożsamości zarządzanej przypisanej przez system w celu włączenia przypisań konfiguracji gościa na maszyny wirtualne z tożsamością przypisaną przez użytkownika](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Te zasady dodają przypisaną przez system tożsamość zarządzaną do maszyn wirtualnych hostowanych na platformie Azure, które są obsługiwane przez konfigurację gościa i mają co najmniej jedną tożsamość przypisaną przez użytkownika, ale nie mają przypisanej przez system tożsamości zarządzanej. Tożsamość zarządzana przypisana przez system jest wymaganiem wstępnym dla wszystkich przypisań konfiguracji gościa i musi zostać dodana do maszyn przed użyciem definicji zasad konfiguracji gościa. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Modyfikowanie |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Inspekcja maszyn z systemem Linux, które nie mają uprawnień pliku passwd ustawionych na 0644](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6955644-301c-44b5-a4c4-528577de6861) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny nie są zgodne, jeśli maszyny z systemem Linux, które nie mają uprawnień do pliku passwd ustawione na 0644 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword121_AINE.json) |
|[Inspekcja maszyn z systemem Linux, które mają konta bez haseł](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6ec09a3-78bf-4f8f-99dc-6c77182d0f99) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny nie są zgodne, jeśli maszyny z systemem Linux mają konta bez haseł |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword232_AINE.json) |
|[Inspekcja maszyn z systemem Windows, które nie przechowują haseł przy użyciu szyfrowania odwracalnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli maszyny z systemem Windows, które nie przechowują haseł przy użyciu szyfrowania odwracalnego |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Wdrażanie rozszerzenia konfiguracji gościa systemu Linux w celu włączenia przypisań konfiguracji gościa na maszynach wirtualnych z systemem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Te zasady wdrażają rozszerzenie konfiguracji gościa systemu Linux na maszynach wirtualnych z systemem Linux hostowanych na platformie Azure, które są obsługiwane przez konfigurację gościa. Rozszerzenie konfiguracji gościa systemu Linux jest wymaganie wstępne dla wszystkich przypisań konfiguracji gościa systemu Linux i musi zostać wdrożone na maszynach przed użyciem dowolnej definicji zasad konfiguracji gościa systemu Linux. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Wdrażanie rozszerzenia Konfiguracja gościa systemu Windows w celu włączenia przypisań konfiguracji gościa na komputerach wirtualnych z systemem Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Te zasady wdrażają rozszerzenie Konfiguracja gościa systemu Windows na maszynach wirtualnych z systemem Windows hostowanych na platformie Azure, które są obsługiwane przez konfigurację gościa. Rozszerzenie Konfiguracja gościa systemu Windows jest wymaganie wstępne dla wszystkich przypisań konfiguracji gościa systemu Windows i musi zostać wdrożone na maszynach przed użyciem dowolnej definicji zasad konfiguracji gościa systemu Windows. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft Managed Control 1317 — Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8877f519-c166-47b7-81b7-8a8eb4ff3775) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1317.json) |
|[Microsoft Managed Control 1318 — Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffced5fda-3bdb-4d73-bfea-0e2c80428b66) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1318.json) |
|[Microsoft Managed Control 1319 — Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F66f7ae57-5560-4fc5-85c9-659f204e7a42) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1319.json) |
|[Microsoft Managed Control 1320 — Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f54c732-71d4-4f93-a696-4e373eca3a77) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1320.json) |
|[Microsoft Managed Control 1321 — Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb627cc6-3a9d-46b5-96b7-5fca49178a37) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1321.json) |
|[Microsoft Managed Control 1322 — Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d1d971e-467e-4278-9633-c74c3d4fecc4) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1322.json) |
|[Microsoft Managed Control 1323 — Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabe8f70b-680f-470c-9b86-a7edfb664ecc) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1323.json) |
|[Microsoft Managed Control 1324 — Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cfea2b3-7f77-497e-ac20-0752f2ff6eee) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1324.json) |
|[Microsoft Managed Control 1325 — Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1845796a-7581-49b2-ae20-443121538e19) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1325.json) |
|[Microsoft Managed Control 1326 — Authenticator Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8605fc00-1bf5-4fb3-984e-c95cec4f231d) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1326.json) |

### <a name="authenticator-management--password-based-authentication"></a>Authenticator Management | Password-Based uwierzytelniania

**Identyfikator:** NIST SP 800-53 R4 IA-5 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Dodawanie tożsamości zarządzanej przypisanej przez system w celu włączenia przypisań konfiguracji gościa na maszynach wirtualnych bez tożsamości](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |Te zasady dodają przypisaną przez system tożsamość zarządzaną do maszyn wirtualnych hostowanych na platformie Azure, które są obsługiwane przez konfigurację gościa, ale nie mają żadnych tożsamości zarządzanych. Tożsamość zarządzana przypisana przez system jest wymaganiem wstępnym dla wszystkich przypisań konfiguracji gościa i musi zostać dodana do maszyn przed użyciem definicji zasad konfiguracji gościa. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Modyfikowanie |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[Dodawanie tożsamości zarządzanej przypisanej przez system w celu włączenia przypisań konfiguracji gościa na maszyny wirtualne z tożsamością przypisaną przez użytkownika](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |Te zasady dodają przypisaną przez system tożsamość zarządzaną do maszyn wirtualnych hostowanych na platformie Azure, które są obsługiwane przez konfigurację gościa i mają co najmniej jedną tożsamość przypisaną przez użytkownika, ale nie mają przypisanej przez system tożsamości zarządzanej. Tożsamość zarządzana przypisana przez system jest wymaganiem wstępnym dla wszystkich przypisań konfiguracji gościa i musi zostać dodana do maszyn przed użyciem definicji zasad konfiguracji gościa. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |Modyfikowanie |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[Inspekcja maszyn z systemem Windows, które umożliwiają ponowne użycie poprzednich 24 haseł](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b054a0d-39e2-4d53-bea3-9734cad2c69b) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli maszyny z systemem Windows zezwalają na ponowne użycie poprzednich 24 haseł |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEnforce_AINE.json) |
|[Inspekcja maszyn z systemem Windows, które nie mają maksymalnego wieku hasła 70 dni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ceb8dc2-559c-478b-a15b-733fbf1e3738) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli maszyny z systemem Windows, które nie mają maksymalnego wieku hasła 70 dni |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMaximumPassword_AINE.json) |
|[Inspekcja maszyn z systemem Windows, które nie mają minimalnego wieku hasła 1 dnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237b38db-ca4d-4259-9e47-7882441ca2c0) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli maszyny z systemem Windows, które nie mają minimalnego wieku hasła 1 dnia |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMinimumPassword_AINE.json) |
|[Inspekcja maszyn z systemem Windows, które nie mają włączonego ustawienia złożoności hasła](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf16e0bb-31e1-4646-8202-60a235cc7e74) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny nie są zgodne, jeśli maszyny z systemem Windows, które nie mają włączonego ustawienia złożoności hasła |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordComplexity_AINE.json) |
|[Inspekcja maszyn z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2d0e922-65d0-40c4-8f87-ea6da2d307a2) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny nie są zgodne, jeśli maszyny z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordLength_AINE.json) |
|[Inspekcja maszyn z systemem Windows, które nie przechowują haseł przy użyciu szyfrowania odwracalnego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |Wymaga wdrożenia wymagań wstępnych w zakresie przypisania zasad. Aby uzyskać szczegółowe informacje, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . Maszyny są niezgodne, jeśli maszyny z systemem Windows, które nie przechowują haseł przy użyciu szyfrowania odwracalnego |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Wdrażanie rozszerzenia konfiguracji gościa systemu Linux w celu włączenia przypisań konfiguracji gościa na komputerach wirtualnych z systemem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |Te zasady wdrażają rozszerzenie konfiguracji gościa systemu Linux na maszynach wirtualnych z systemem Linux hostowanych na platformie Azure, które są obsługiwane przez konfigurację gościa. Rozszerzenie konfiguracji gościa systemu Linux jest wymaganiem wstępnym dla wszystkich przypisań konfiguracji gościa systemu Linux i musi zostać wdrożone na maszynach przed użyciem dowolnej definicji zasad konfiguracji gościa systemu Linux. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Wdrażanie rozszerzenia Konfiguracja gościa systemu Windows w celu włączenia przypisań konfiguracji gościa na komputerach wirtualnych z systemem Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |Te zasady wdrażają rozszerzenie Konfiguracja gościa systemu Windows na maszynach wirtualnych z systemem Windows hostowanych na platformie Azure, które są obsługiwane przez konfigurację gościa. Rozszerzenie Konfiguracja gościa systemu Windows jest wymaganie wstępne dla wszystkich przypisań konfiguracji gościa systemu Windows i musi zostać wdrożone na maszynach przed użyciem dowolnej definicji zasad konfiguracji gościa systemu Windows. Aby uzyskać więcej informacji na temat konfiguracji gościa, odwiedź stronę [https://aka.ms/gcpol](https://aka.ms/gcpol) . |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft Managed Control 1327 — Authenticator Management \| Password-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03188d8f-1ae5-4fe1-974d-2d7d32ef937d) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1327.json) |
|[Microsoft Managed Control 1328 — Authenticator Management \| Password-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5c66fdc-3d02-4034-9db5-ba57802609de) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1328.json) |
|[Microsoft Managed Control 1329 — Authenticator Management \| Password-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F498f6234-3e20-4b6a-a880-cbd646d973bd) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1329.json) |
|[Microsoft Managed Control 1330 — Authenticator Management \| Password-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff75cedb2-5def-4b31-973e-b69e8c7bd031) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1330.json) |
|[Microsoft Managed Control 1331 — Authenticator Management \| Password-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05460fe2-301f-4ed1-8174-d62c8bb92ff4) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1331.json) |
|[Microsoft Managed Control 1332 — Authenticator Management \| Password-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068260be-a5e6-4b0a-a430-cd27071c226a) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1332.json) |

### <a name="authenticator-management--pki-based-authentication"></a>Authenticator Management | Pki-Based uwierzytelniania

**Identyfikator:** NIST SP 800-53 R4 IA-5 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1333 — Authenticator Management \| Pki-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3298d6bf-4bc6-4278-a95d-f7ef3ac6e594) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1333.json) |
|[Microsoft Managed Control 1334 — Authenticator Management \| Pki-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44bfdadc-8c2e-4c30-9c99-f005986fabcd) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1334.json) |
|[Microsoft Managed Control 1335 — Authenticator Management \| Pki-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F382016f3-d4ba-4e15-9716-55077ec4dc2a) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1335.json) |
|[Microsoft Managed Control 1336 — Authenticator Management \| Pki-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77f56280-e367-432a-a3b9-8ca2aa636a26) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1336.json) |

### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Authenticator Management | rejestracja osobiście lub zaufana rejestracja innej firmy

**Identyfikator:** NIST SP 800-53 R4 IA-5 (3) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1337 — authenticator management \| In-Person lub trusted third-party registration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F463e5220-3f79-4e24-a63f-343e4096cd22) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1337.json) |

### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Authenticator Management | Zautomatyzowana obsługa określania siły hasła

**Identyfikator:** NIST SP 800-53 R4 IA-5 (4) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1338 — Authenticator Management Automated Support for Password Strength Determination (Zarządzana przez firmę Microsoft 1338 — zautomatyzowana obsługa zarządzania wystawcą uwierzytelnianą \| na rzecz określania siły hasła)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c59a207-6aed-41dc-83a2-e1ff66e4a4db) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1338.json) |

### <a name="authenticator-management--protection-of-authenticators"></a>Authenticator Management | Ochrona wystawcy uwierzytelnień

**Identyfikator:** NIST SP 800-53 R4 IA-5 (6) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1339 — Authenticator Management Protection of Authenticators (Kontrola zarządzana przez firmę Microsoft 1339 — ochrona \| wystawcy uwierzytelnień w zarządzaniu wystawcami uwierzytelnień)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F367ae386-db7f-4167-b672-984ff86277c0) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1339.json) |

### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Authenticator Management | Brak osadzonych niezaszyfrowanych statycznych wystawcy uwierzytelnień

**Identyfikator:** NIST SP 800-53 R4 IA-5 (7) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1340 — Authenticator Management No Embedded Unencrypted Static Authenticators (Zarządzanie wystawcą uwierzytelnień bez osadzonych \| niezaszyfrowanych statycznych wystawcy uwierzytelnień)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe51ff84b-e5ea-408f-b651-2ecc2933e4c6) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1340.json) |

### <a name="authenticator-management--multiple-information-system-accounts"></a>Authenticator Management | Wiele kont systemowych informacji

**Identyfikator:** NIST SP 800-53 R4 IA-5 (8) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1341 — zarządzanie wieloma kontami systemu informacji w programie Authenticator \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34cb7e92-fe4c-4826-b51e-8cd203fa5d35) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1341.json) |

### <a name="authenticator-management--hardware-token-based-authentication"></a>Authenticator Management | Uwierzytelnianie Token-Based sprzętu

**Identyfikator:** NIST SP 800-53 R4 IA-5 (11) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1342 — Authenticator Management \| Hardware Token-Based Authentication](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F283a4e29-69d5-4c94-b99e-29acf003c899) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1342.json) |

### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Authenticator Management | Wygaśnięcie buforowanych wystawcy uwierzytelnień

**Identyfikator:** NIST SP 800-53 R4 IA-5 (13) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1343 — wygaśnięcie zarządzania wystawcą uwierzytelnień w \| pamięci podręcznej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c251a55-31eb-4e53-99c6-e9c43c393ac2) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1343.json) |

### <a name="authenticator-feedback"></a>Opinie dotyczące wystawcy uwierzytelniań

**Identyfikator:** NIST SP 800-53 R4 IA-6 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1344 — opinie dotyczące aplikacji Authenticator](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c895fe7-2d8e-43a2-838c-3a533a5b355e) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1344.json) |

### <a name="cryptographic-module-authentication"></a>Uwierzytelnianie modułu kryptograficznego

**Identyfikator:** NIST SP 800-53 R4 IA-7 Ownership : Shared (Własność NIST SP 800-53 R4 IA-7): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1345 — Uwierzytelnianie modułów kryptograficznych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff86aa129-7c07-4aa4-bbf5-792d93ffd9ea) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1345.json) |

### <a name="identification-and-authentication-non-organizational-users"></a>Identyfikacja i uwierzytelnianie (użytkownicy niebędący użytkownikami organizacyjnymi)

**Identyfikator:** NIST SP 800-53 R4 IA-8 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1346 — identyfikacja i uwierzytelnianie (użytkownicy niebędący użytkownikami organizacyjnymi)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F464dc8ce-2200-4720-87a5-dc5952924cc6) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1346.json) |

### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Identyfikacja i uwierzytelnianie (użytkownicy niebędący użytkownikami organizacyjnymi) | Akceptacja poświadczeń Piv z innych agencji

**Identyfikator:** NIST SP 800-53 R4 IA-8 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1347 — identyfikacja i uwierzytelnianie (spoza organizacji). Użytkownicy) \| Akceptacja poszybków PIV. Z innej firmy Agys.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F131a2706-61e9-4916-a164-00e052056462) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1347.json) |

### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Identyfikacja i uwierzytelnianie (użytkownicy niebędący użytkownikami organizacyjnymi) | Akceptacja poświadczeń innych firm

**Identyfikator:** NIST SP 800-53 R4 IA-8 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1348 — identyfikacja i uwierzytelnianie (inne niż organizacja. Użytkownicy) \| Akceptacja poświadczeń innych firm](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F855ced56-417b-4d74-9d5f-dd1bc81e22d6) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1348.json) |

### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Identyfikacja i uwierzytelnianie (użytkownicy niebędący użytkownikami organizacyjnymi) | Korzystanie z Ficam-Approved Products

**Identyfikator:** NIST SP 800-53 R4 IA-8 (3) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1349 — identyfikacja i uwierzytelnianie (spoza organizacji). Użytkownicy) \| Korzystanie z FICAM-Approved produktów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17641f70-94cd-4a5d-a613-3d1143e20e34) |Firma Microsoft implementuje tę kontrolkę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1349.json) |

### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Identyfikacja i uwierzytelnianie (użytkownicy niebędący użytkownikami organizacyjnymi) | Korzystanie z Ficam-Issued sieciowych

**Identyfikator:** NIST SP 800-53 R4 IA-8 (4) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1350 — identyfikacja i uwierzytelnianie (spoza organizacji). Użytkownicy) \| Korzystanie z FICAM-Issued aplikacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd77fd943-6ba6-4a21-ba07-22b03e347cc4) |Firma Microsoft implementuje tę kontrolę identyfikacji i uwierzytelniania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1350.json) |

## <a name="incident-response"></a>Reagowanie na zdarzenia

### <a name="incident-response-policy-and-procedures"></a>Zasady i procedury reagowania na zdarzenia

**Identyfikator:** Własność NIST SP 800-53 R4 IR-1: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1351 — zasady i procedury reagowania na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbcfb6683-05e5-4ce6-9723-c3fbe9896bdd) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1351.json) |
|[Microsoft Managed Control 1352 — zasady i procedury reagowania na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F518cb545-bfa8-43f8-a108-3b7d5037469a) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1352.json) |

### <a name="incident-response-training"></a>Szkolenie reagowania na zdarzenia

**Identyfikator:** Własność NIST SP 800-53 R4 IR-2: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1353 — szkolenie dotyczące reagowania na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc785ad59-f78f-44ad-9a7f-d1202318c748) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1353.json) |
|[Microsoft Managed Control 1354 — szkolenie dotyczące reagowania na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9fd92c17-163a-4511-bb96-bbb476449796) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1354.json) |
|[Microsoft Managed Control 1355 — szkolenie dotyczące reagowania na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90e01f69-3074-4de8-ade7-0fef3e7d83e0) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1355.json) |

### <a name="incident-response-training--simulated-events"></a>Szkolenia w zakresie reagowania na zdarzenia | Symulowane zdarzenia

**Identyfikator:** NIST SP 800-53 R4 IR-2 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1356 — symulowane zdarzenia trenowania \| odpowiedzi na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8829f8f5-e8be-441e-85c9-85b72a5d0ef3) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1356.json) |

### <a name="incident-response-training--automated-training-environments"></a>Szkolenia w zakresie reagowania na zdarzenia | Zautomatyzowane środowiska szkoleniowe

**Identyfikator:** NIST SP 800-53 R4 IR-2 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1357 — zautomatyzowane środowiska szkoleniowe dotyczące reagowania \| na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe4213689-05e8-4241-9d4e-8dd1cdafd105) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1357.json) |

### <a name="incident-response-testing"></a>Testowanie odpowiedzi na zdarzenia

**Identyfikator:** Własność NIST SP 800-53 R4 IR-3: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1358 — testowanie odpowiedzi na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feffbaeef-5bf4-400d-895e-ef8cbc0e64c7) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1358.json) |

### <a name="incident-response-testing--coordination-with-related-plans"></a>Testowanie odpowiedzi na zdarzenia | Koordynacja z powiązanymi planami

**Identyfikator:** NIST SP 800-53 R4 IR-3 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1359 — koordynacja testowania odpowiedzi na zdarzenia \| z powiązanymi planami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47bc7ea0-7d13-4f7c-a154-b903f7194253) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1359.json) |

### <a name="incident-handling"></a>Obsługa zdarzeń

**Identyfikator:** Własność NIST SP 800-53 R4 IR-4: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1360 — obsługa zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbe5b05e7-0b82-4ebc-9eda-25e447b1a41e) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1360.json) |
|[Microsoft Managed Control 1361 — Incident Handling (Zarządzana kontrola firmy Microsoft 1361 — obsługa zdarzeń)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ed3be1-7276-4452-9a5d-e4168565ac67) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1361.json) |
|[Microsoft Managed Control 1362 — Incident Handling (Zarządzana kontrola firmy Microsoft 1362 — obsługa zdarzeń)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5d169442-d6ef-439b-8dca-46c2c3248214) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1362.json) |

### <a name="incident-handling--automated-incident-handling-processes"></a>Obsługa | Zautomatyzowane procesy obsługi zdarzeń

**Identyfikator:** NIST SP 800-53 R4 IR-4 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1363 — Incident Handling Automated Incident Handling Processes (Kontrola zarządzana przez firmę Microsoft 1363 — zautomatyzowane \| procesy obsługi zdarzeń)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea3e8156-89a1-45b1-8bd6-938abc79fdfd) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1363.json) |

### <a name="incident-handling--dynamic-reconfiguration"></a>Obsługa | Rekonfiguracja dynamiczna

**Identyfikator:** NIST SP 800-53 R4 IR-4 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1364 — dynamiczna rekonfiguracja obsługi \| zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c615c2a-dc83-4dda-8220-abce7b50c9bc) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1364.json) |

### <a name="incident-handling--continuity-of-operations"></a>Obsługa | Ciągłość działania

**Identyfikator:** NIST SP 800-53 R4 IR-4 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1365 — ciągłość obsługi zdarzeń \|](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4116891d-72f7-46ee-911c-8056cc8dcbd5) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1365.json) |

### <a name="incident-handling--information-correlation"></a>Obsługa | Korelacja informacji

**Identyfikator:** NIST SP 800-53 R4 IR-4 (4) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1366 — Incident Handling Information Correlation (Zarządzana kontrola firmy Microsoft 1366 — korelacja informacji \| dotyczących obsługi zdarzeń)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06c45c30-ae44-4f0f-82be-41331da911cc) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1366.json) |

### <a name="incident-handling--insider-threats---specific-capabilities"></a>Obsługa | Wewnętrzne zagrożenia — określone możliwości

**Identyfikator:** NIST SP 800-53 R4 IR-4 (6) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1367 — Incident Handling Insider Threats - Specific Capabilities (Kontrola zarządzana przez firmę Microsoft 1367 — obsługa zdarzeń \| w przypadku wewnętrznych zagrożeń — określone możliwości)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F435b2547-6374-4f87-b42d-6e8dbe6ae62a) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1367.json) |

### <a name="incident-handling--correlation-with-external-organizations"></a>Obsługa | Korelacja z organizacjami zewnętrznymi

**Identyfikator:** NIST SP 800-53 R4 IR-4 (8) **Własność: Udostępnione**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1368 — Incident Handling Correlation with External Organizations (Kontrola zarządzana przez firmę Microsoft 1368 — korelacja \| obsługi zdarzeń z organizacjami zewnętrznymi)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F465f32da-0ace-4603-8d1b-7be5a3a702de) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1368.json) |

### <a name="incident-monitoring"></a>Monitorowanie zdarzeń

**Identyfikator:** Własność NIST SP 800-53 R4 IR-5: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1369 — monitorowanie zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18cc35ed-a429-486d-8d59-cb47e87304ed) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1369.json) |

### <a name="incident-monitoring--automated-tracking--data-collection--analysis"></a>Monitorowanie zdarzeń | Zautomatyzowane śledzenie/zbieranie danych/analiza

**Identyfikator:** NIST SP 800-53 R4 IR-5 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1370 — zautomatyzowane śledzenie monitorowania \| zdarzeń/ zbieranie danych/analiza](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F924e1b2d-c502-478f-bfdb-a7e09a0d5c01) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1370.json) |

### <a name="incident-reporting"></a>Raportowanie zdarzeń

**Identyfikator:** Własność NIST SP 800-53 R4 IR-6: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1371 — raportowanie zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9447f354-2c85-4700-93b3-ecdc6cb6a417) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1371.json) |
|[Microsoft Managed Control 1372 — raportowanie zdarzeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F25b96717-c912-4c00-9143-4e487f411726) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1372.json) |

### <a name="incident-reporting--automated-reporting"></a>Raportowanie | Zautomatyzowane raportowanie

**Identyfikator:** NIST SP 800-53 R4 IR-6 (1) **Własność: Udostępnione**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1373 — raportowanie zdarzeń \| — zautomatyzowane raportowanie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4cca950f-c3b7-492a-8e8f-ea39663c14f9) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1373.json) |

### <a name="incident-response-assistance"></a>Pomoc w reagowaniu na zdarzenia

**Identyfikator:** Własność NIST SP 800-53 R4 IR-7: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1374 — pomoc w reagowaniu na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc5c8616-52ef-4e5e-8000-491634ed9249) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1374.json) |

### <a name="incident-response-assistance--automation-support-for-availability-of-information--support"></a>Pomoc w zakresie reagowania na zdarzenia | Obsługa automatyzacji w zakresie dostępności informacji/pomocy technicznej

**Identyfikator:** NIST SP 800-53 R4 IR-7 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1375 — obsługa automatyzacji pomocy w reagowaniu na zdarzenia \| w zakresie dostępności informacji/pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F00379355-8932-4b52-b63a-3bc6daf3451a) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1375.json) |

### <a name="incident-response-assistance--coordination-with-external-providers"></a>Pomoc w reagowaniu na zdarzenia | Koordynacja z dostawcami zewnętrznymi

**Identyfikator:** NIST SP 800-53 R4 IR-7 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1376 — koordynacja pomocy w reagowaniu na zdarzenia \| z dostawcami zewnętrznymi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F493a95f3-f2e3-47d0-af02-65e6d6decc2f) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1376.json) |
|[Microsoft Managed Control 1377 — koordynacja pomocy w reagowaniu na zdarzenia \| z dostawcami zewnętrznymi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68434bd1-e14b-4031-9edb-a4adf5f84a67) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1377.json) |

### <a name="incident-response-plan"></a>Plan reagowania na zdarzenia

**Identyfikator:** Własność NIST SP 800-53 R4 IR-8: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1378 — plan reagowania na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97fceb70-6983-42d0-9331-18ad8253184d) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1378.json) |
|[Microsoft Managed Control 1379 — plan reagowania na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9442dd2c-a07f-46cd-b55a-553b66ba47ca) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1379.json) |
|[Microsoft Managed Control 1380 — plan reagowania na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4319b7e-ea8d-42ff-8a67-ccd462972827) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1380.json) |
|[Microsoft Managed Control 1381 — plan reagowania na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe5368258-9684-4567-8126-269f34e65eab) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1381.json) |
|[Microsoft Managed Control 1382 — plan reagowania na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F841392b3-40da-4473-b328-4cde49db67b3) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1382.json) |
|[Microsoft Managed Control 1383 — plan reagowania na zdarzenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4558451-e16a-4d2d-a066-fe12a6282bb9) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1383.json) |

### <a name="information-spillage-response"></a>Odpowiedź na rozlanie informacji

**Identyfikator:** Własność NIST SP 800-53 R4 IR-9: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1384 — odpowiedź na rozlanie informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79fbc228-461c-4a45-9004-a865ca0728a7) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1384.json) |
|[Microsoft Managed Control 1385 — odpowiedź na rozlanie informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e495e65-8663-49ca-9b38-9f45e800bc58) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1385.json) |
|[Microsoft Managed Control 1386 — information rozlanie odpowiedzi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5120193e-91fd-4f9d-bc6d-194f94734065) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1386.json) |
|[Microsoft Managed Control 1387 — information rozlanie odpowiedzi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3007185-3857-43a9-8237-06ca94f1084c) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1387.json) |
|[Microsoft Managed Control 1388 — odpowiedź na rozlanie informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c7c575a-d4c5-4f6f-bd49-dee97a8cba55) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1388.json) |
|[Microsoft Managed Control 1389 — odpowiedź na rozlew informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39e6fda-ae70-4891-a739-be7bba6d1062) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1389.json) |

### <a name="information-spillage-response--responsible-personnel"></a>Odpowiedź na rozlew informacji | Odpowiedzialny personel

**Identyfikator:** NIST SP 800-53 R4 IR-9 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1390 — reagowanie na rozlew informacji \| Odpowiedzialny personel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3b65b63-09ec-4cb5-8028-7dd324d10eb0) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1390.json) |

### <a name="information-spillage-response--training"></a>Odpowiedź na rozlew informacji | Szkolenia

**Identyfikator:** NIST SP 800-53 R4 IR-9 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1391 — szkolenie dotyczące odpowiedzi na rozlew \| informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd6ac1a1-660e-4810-baa8-74e868e2ed47) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1391.json) |

### <a name="information-spillage-response--post-spill-operations"></a>Odpowiedź na rozlanie informacji | Operacje po rozlaniu

**Identyfikator:** NIST SP 800-53 R4 IR-9 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1392 — information rozlanie odpowiedzi \| po rozlanie operacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86dc819f-15e1-43f9-a271-41ae58d4cecc) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1392.json) |

### <a name="information-spillage-response--exposure-to-unauthorized-personnel"></a>Odpowiedź na rozlanie informacji | Narażenie na nieautoryzowany personel

**Identyfikator:** NIST SP 800-53 R4 IR-9 (4) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1393 — information rozlanie odpowiedzi \| na narażenie na nieautoryzowany personel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F731856d8-1598-4b75-92de-7d46235747c0) |Firma Microsoft implementuje tę kontrolkę reagowania na zdarzenia |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1393.json) |

## <a name="maintenance"></a>Konserwacja

### <a name="system-maintenance-policy-and-procedures"></a>Zasady i procedury konserwacji systemu

**Identyfikator:** Własność NIST SP 800-53 R4 MA-1: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1394 — zasady i procedury konserwacji systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4db56f68-3f50-45ab-88f3-ca46f5379a94) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1394.json) |
|[Microsoft Managed Control 1395 — zasady i procedury konserwacji systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7207a023-a517-41c5-9df2-09d4c6845a05) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1395.json) |

### <a name="controlled-maintenance"></a>Kontrolowana konserwacja

**Identyfikator:** Własność NIST SP 800-53 R4 MA-2: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1396 — kontrolowana konserwacja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F276af98f-4ff9-4e69-99fb-c9b2452fb85f) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1396.json) |
|[Microsoft Managed Control 1397 — kontrolowana konserwacja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391af4ab-1117-46b9-b2c7-78bbd5cd995b) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1397.json) |
|[Microsoft Managed Control 1398 — kontrolowana konserwacja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F443e8f3d-b51a-45d8-95a7-18b0e42f4dc4) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1398.json) |
|[Microsoft Managed Control 1399 — kontrolowana konserwacja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2256e638-eb23-480f-9e15-6cf1af0a76b3) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1399.json) |
|[Microsoft Managed Control 1400 — kontrolowana konserwacja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96d5098-a604-4cdf-90b1-ef6449a27424) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1400.json) |
|[Microsoft Managed Control 1401 — kontrolowana konserwacja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb78ee928-e3c1-4569-ad97-9f8c4b629847) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1401.json) |

### <a name="controlled-maintenance--automated-maintenance-activities"></a>Kontrolowane | Zautomatyzowane działania konserwacyjne

**Identyfikator:** NIST SP 800-53 R4 MA-2 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1402 — zautomatyzowane działania konserwacji \| kontrolowanej konserwacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a560d32-8075-4fec-9615-9f7c853f4ea9) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1402.json) |
|[Microsoft Managed Control 1403 — zautomatyzowane działania konserwacji \| kontrolowanej konserwacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F57149289-d52b-4f40-9fe6-5233c1ef80f7) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1403.json) |

### <a name="maintenance-tools"></a>Narzędzia konserwacji

**Identyfikator:** NIST SP 800-53 R4 MA-3 Ownership : Shared (Własność NIST SP 800-53 R4 MA-3): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1404 — narzędzia do konserwacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d8f903-0cd6-449f-a172-50f6579c182b) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1404.json) |

### <a name="maintenance-tools--inspect-tools"></a>Narzędzia konserwacji | Narzędzia inspekcji

**Identyfikator:** NIST SP 800-53 R4 MA-3 (1) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1405 — narzędzia do inspekcji \| narzędzi do konserwacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe1a0bf3-409a-4b00-b60d-0b1f917f7e7b) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1405.json) |

### <a name="maintenance-tools--inspect-media"></a>Narzędzia konserwacji | Inspekcja nośnika

**Identyfikator:** NIST SP 800-53 R4 MA-3 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1406 — narzędzia do konserwacji \| — inspekcja nośnika](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0f5339c-9292-43aa-a0bc-d27c6b8e30aa) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1406.json) |

### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Narzędzia konserwacji | Zapobieganie nieautoryzowanemu usuwaniu

**Identyfikator:** NIST SP 800-53 R4 MA-3 (3) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1407 — narzędzia do konserwacji zapobiegają \| nieautoryzowanemu usuwaniu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fff9fbd83-1d8d-4b41-aac2-94cb44b33976) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1407.json) |
|[Microsoft Managed Control 1408 — narzędzia do konserwacji zapobiegają \| nieautoryzowanemu usuwaniu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5f56ac6-4bb2-4086-bc41-ad76344ba2c2) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1408.json) |
|[Microsoft Managed Control 1409 — narzędzia do konserwacji zapobiegają \| nieautoryzowanemu usuwaniu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1880188-e51a-4772-b2ab-68f5e8bd27f6) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1409.json) |
|[Microsoft Managed Control 1410 — narzędzia do konserwacji zapobiegają \| nieautoryzowanemu usuwaniu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2596a9f-e59f-420d-9625-6e0b536348be) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1410.json) |

### <a name="nonlocal-maintenance"></a>Konserwacja nielokalna

**Identyfikator:** NIST SP 800-53 R4 MA-4 Ownership : Shared (Własność NIST SP 800-53 R4 MA-4): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1411 — konserwacja nielokalna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F898d4fe8-f743-4333-86b7-0c9245d93e7d) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1411.json) |
|[Microsoft Managed Control 1412 — konserwacja nielokalna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3492d949-0dbb-4589-88b3-7b59601cc764) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1412.json) |
|[Microsoft Managed Control 1413 — konserwacja nielokalna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faeedddb6-6bc0-42d5-809b-80048033419d) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1413.json) |
|[Microsoft Managed Control 1414 — konserwacja nielokalna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce63a52-e47b-4ae2-adbb-6e40d967f9e6) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1414.json) |
|[Microsoft Managed Control 1415 — konserwacja nielokalna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61a1dd98-b259-4840-abd5-fbba7ee0da83) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1415.json) |

### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Nielokalne | Dokumentowanie konserwacji nielokału

**Identyfikator:** NIST SP 800-53 R4 MA-4 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1416 — nielokalna konserwacja \| dokumentu konserwacji nielokału](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38dfd8a3-5290-4099-88b7-4081f4c4d8ae) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1416.json) |

### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Nielokalne | Porównywalne zabezpieczenia/sanityzacja

**Identyfikator:** NIST SP 800-53 R4 MA-4 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1417 — nielokalna konserwacja \| porównywalne zabezpieczenia/sanityzacja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7522ed84-70d5-4181-afc0-21e50b1b6d0e) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1417.json) |
|[Microsoft Managed Control 1418 — nielokalna konserwacja \| porównywalne zabezpieczenia/sanityzacja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e633fd-284e-4ea7-88b4-02ca157ed713) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1418.json) |

### <a name="nonlocal-maintenance--cryptographic-protection"></a>Nielokalna konserwacja | Ochrona kryptograficzna

**Identyfikator:** NIST SP 800-53 R4 MA-4 (6) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1419 — ochrona kryptograficzna \| konserwacji nielokału](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6747bf9-2b97-45b8-b162-3c8becb9937d) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1419.json) |

### <a name="maintenance-personnel"></a>Personel odpowiedzialny za konserwację

**Identyfikator:** NIST SP 800-53 R4 MA-5 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1420 — personel obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05ae08cc-a282-413b-90c7-21a2c60b8404) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1420.json) |
|[Microsoft Managed Control 1421 — personel obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe539caaa-da8c-41b8-9e1e-449851e2f7a6) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1421.json) |
|[Microsoft Managed Control 1422 — personel obsługi technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea556850-838d-4a37-8ce5-9d7642f95e11) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1422.json) |

### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Pracownicy | Osoby bez odpowiedniego dostępu

**Identyfikator:** NIST SP 800-53 R4 MA-5 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1423 — pracownicy obsługi technicznej \| bez odpowiedniego dostępu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7741669e-d4f6-485a-83cb-e70ce7cbbc20) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1423.json) |
|[Microsoft Managed Control 1424 — pracownicy obsługi technicznej \| bez odpowiedniego dostępu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf55fc87-48e1-4676-a2f8-d9a8cf993283) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1424.json) |

### <a name="timely-maintenance"></a>Terminowa konserwacja

**Identyfikator:** NIST SP 800-53 R4 MA-6 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1425 — terminowa konserwacja](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5983d99c-f39b-4c32-a3dc-170f19f6941b) |Firma Microsoft implementuje tę kontrolkę konserwacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1425.json) |

## <a name="media-protection"></a>Ochrona multimediów

### <a name="media-protection-policy-and-procedures"></a>Zasady i procedury ochrony multimediów

**Identyfikator:** Własność NIST SP 800-53 R4 MP-1: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1426 — zasady i procedury ochrony multimediów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21f639bc-f42b-46b1-8f40-7a2a389c291a) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1426.json) |
|[Microsoft Managed Control 1427 — zasady i procedury ochrony multimediów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc90e44f-d83f-4bdf-900f-3d5eb4111b31) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1427.json) |

### <a name="media-access"></a>Dostęp do multimediów

**Identyfikator:** Własność NIST SP 800-53 R4 MP-2: Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1428 — dostęp do multimediów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a77fcc7-b8d8-451a-ab52-56197913c0c7) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1428.json) |

### <a name="media-marking"></a>Oznaczanie multimediów

**Identyfikator:** własność NIST SP 800-53 R4 MP-3: Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1429 — oznaczanie multimediów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb07c9b24-729e-4e85-95fc-f224d2d08a80) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1429.json) |
|[Microsoft Managed Control 1430 — oznaczanie multimediów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f559588-5e53-4b14-a7c4-85d28ebc2234) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1430.json) |

### <a name="media-storage"></a>Magazyn multimediów

**Identyfikator:** NIST SP 800-53 R4 MP-4 **Ownership**(Własność NIST SP 800-53 R4 MP-4): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1431 — magazyn multimediów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7173c52-2b99-4696-a576-63dd5f970ef4) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1431.json) |
|[Microsoft Managed Control 1432 — Magazyn multimediów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1140e542-b80d-4048-af45-3f7245be274b) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1432.json) |

### <a name="media-transport"></a>Transport multimediów

**Identyfikator:** Własność NIST SP 800-53 R4 MP-5: Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1433 — Media Transport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b879b41-2728-41c5-ad24-9ee2c37cbe65) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1433.json) |
|[Microsoft Managed Control 1434 — transport multimediów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c18f06b-a68d-41c3-8863-b8cd3acb5f8f) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1434.json) |
|[Microsoft Managed Control 1435 — Media Transport](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa8d221b-d130-4637-ba16-501e666628bb) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1435.json) |
|[Microsoft Managed Control 1436 — transport multimediów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28aab8b4-74fd-4b7c-9080-5a7be525d574) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1436.json) |

### <a name="media-transport--cryptographic-protection"></a>Media Transport | Ochrona kryptograficzna

**Identyfikator:** NIST SP 800-53 R4 MP-5 (4) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1437 — ochrona kryptograficzna \| transportu multimediów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d1eb6ed-bf13-4046-b993-b9e2aef0f76c) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1437.json) |

### <a name="media-sanitization"></a>Sanityzacja multimediów

**Identyfikator:** NIST SP 800-53 R4 MP-6 **Ownership**(Własność NIST SP 800-53 R4 MP-6): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1438 — media sanitization](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40fcc635-52a2-4dbc-9523-80a1f4aa1de6) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1438.json) |
|[Microsoft Managed Control 1439 — media sanitization](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdce72873-c5f1-47c3-9b4f-6b8207fd5a45) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1439.json) |

### <a name="media-sanitization--review--approve--track--document--verify"></a>Urządzenia do sanityzacji | Przeglądanie/zatwierdzanie/śledzenie/dokument/weryfikacja

**Identyfikator:** NIST SP 800-53 R4 MP-6 (1) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1440 - Media Sanitization \| Review / Approve / Track / Document / Verify](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F881299bf-2a5b-4686-a1b2-321d33679953) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1440.json) |

### <a name="media-sanitization--equipment-testing"></a>Urządzenia do sanityzacji | Testowanie sprzętu

**Identyfikator:** NIST SP 800-53 R4 MP-6 (2) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1441 — testowanie sprzętu do sanityzacji \| multimediów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6519d7f3-e8a2-4ff3-a935-9a9497152ad7) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1441.json) |

### <a name="media-sanitization--nondestructive-techniques"></a>Urządzenia do sanityzacji | Techniki niedestruktywne

**Identyfikator:** NIST SP 800-53 R4 MP-6 (3) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1442 — techniki niedestruktywne do \| sanityzacji multimediów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f26049b-2c5a-4841-9ff3-d48a26aae475) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1442.json) |

### <a name="media-use"></a>Korzystanie z nośników

**Identyfikator:** własność NIST SP 800-53 R4 MP-7: Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1443 — używanie multimediów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd0ec6fa-a2e7-4361-aee4-a8688659a9ed) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1443.json) |

### <a name="media-use--prohibit-use-without-owner"></a>Korzystanie z | Zabraniaj użycia bez właściciela

**Identyfikator:** NIST SP 800-53 R4 MP-7 (1) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1444 — używanie multimediów \| zabronione bez właściciela](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F666143df-f5e0-45bd-b554-135f0f93e44e) |Firma Microsoft implementuje tę kontrolkę usługi Media Protection |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1444.json) |

## <a name="physical-and-environmental-protection"></a>Ochrona fizyczna i środowiskowa

### <a name="physical-and-environmental-protection-policy-and-procedures"></a>Zasady i procedury ochrony fizycznej i środowiskowej

**Identyfikator:** Własność NIST SP 800-53 R4 PE-1: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1445 — zasady i procedury ochrony fizycznej i środowiskowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32d07d59-2716-4972-b37b-214a67ac4a37) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1445.json) |
|[Microsoft Managed Control 1446 — zasady i procedury ochrony fizycznej i środowiskowej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf6850fe-abba-468e-9ef4-d09ec7d983cd) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1446.json) |

### <a name="physical-access-authorizations"></a>Autoryzacje dostępu fizycznego

**Identyfikator:** NIST SP 800-53 R4 PE-2 **Ownership**(Własność NIST SP 800-53 R4 PE-2): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1447 — Autoryzacje dostępu fizycznego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9783a99-98fe-4a95-873f-29613309fe9a) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1447.json) |
|[Microsoft Managed Control 1448 — autoryzacje dostępu fizycznego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F825d6494-e583-42f2-a3f2-6458e6f0004f) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1448.json) |
|[Microsoft Managed Control 1449 — autoryzacje dostępu fizycznego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff784d3b0-5f2b-49b7-b9f3-00ba8653ced5) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1449.json) |
|[Microsoft Managed Control 1450 — autoryzacje dostępu fizycznego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F134d7a13-ba3e-41e2-b236-91bfcfa24e01) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1450.json) |

### <a name="physical-access-control"></a>Właściwości Access Control

**Identyfikator:** NIST SP 800-53 R4 PE-3 **Ownership**(Własność NIST SP 800-53 R4 PE-3): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1451 — physical Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3f1e5a3-25c1-4476-8cb6-3955031f8e65) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1451.json) |
|[Microsoft Managed Control 1452 — physical Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82c76455-4d3f-4e09-a654-22e592107e74) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1452.json) |
|[Microsoft Managed Control 1453 — Physical Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9693b564-3008-42bc-9d5d-9c7fe198c011) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1453.json) |
|[Microsoft Managed Control 1454 — Physical Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad58985d-ab32-4f99-8bd3-b7e134c90229) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1454.json) |
|[Microsoft Managed Control 1455 — Physical Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068a88d4-e520-434e-baf0-9005a8164e6a) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1455.json) |
|[Microsoft Managed Control 1456 — Physical Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F733ba9e3-9e7c-440a-a7aa-6196a90a2870) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1456.json) |
|[Microsoft Managed Control 1457 — Physical Access Control](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff2d9d3e6-8886-4305-865d-639163e5c305) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1457.json) |

### <a name="physical-access-control--information-system-access"></a>Stan Access Control | Dostęp do systemu informacji

**Identyfikator:** NIST SP 800-53 R4 PE-3 (1) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1458 — dostęp do Access Control \| informacji fizycznych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c19ceb7-56e9-4488-8ddb-b1eb3aa6d203) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1458.json) |

### <a name="access-control-for-transmission-medium"></a>Access Control dla nośnika transmisji

**Identyfikator:** NIST SP 800-53 R4 PE-4 **Ownership**(Własność PE-4 NIST SP 800-53 R4): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1459 — Access Control dla nośnika transmisji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75cc73c7-5cdb-479d-a06f-7b4d0dbb1da0) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1459.json) |

### <a name="access-control-for-output-devices"></a>Access Control dla urządzeń wyjściowych

**Identyfikator:** NIST SP 800-53 R4 PE-5 **Ownership**(Własność NIST SP 800-53 R4 PE-5): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1460 — Access Control dla urządzeń wyjściowych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f3ce1bb-4f77-4695-8355-70b08d54fdda) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1460.json) |

### <a name="monitoring-physical-access"></a>Monitorowanie dostępu fizycznego

**Identyfikator:** NIST SP 800-53 R4 PE-6 **Ownership**(Własność NIST SP 800-53 R4 PE-6): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1461 — Monitoring Physical Access (Kontrola zarządzana przez firmę Microsoft 1461 — monitorowanie dostępu fizycznego)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faafef03e-fea8-470b-88fa-54bd1fcd7064) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1461.json) |
|[Microsoft Managed Control 1462 — Monitoring Physical Access (Kontrola zarządzana przez firmę Microsoft 1462 — monitorowanie dostępu fizycznego)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9b1f3a9a-13a1-4b40-8420-36bca6fd8c02) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1462.json) |
|[Microsoft Managed Control 1463 — Monitorowanie dostępu fizycznego](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F59721f87-ae25-4db0-a2a4-77cc5b25d495) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1463.json) |

### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>Monitorowanie dostępu fizycznego | Alarmy włamań/sprzęt nadzoru

**Identyfikator:** NIST SP 800-53 R4 PE-6 (1) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1464 — Monitoring Physical Access Intrusion Alarms / Monitoring Equipment (Kontrola zarządzana przez firmę Microsoft 1464 — monitorowanie alarmów włamań do dostępu \| fizycznego/sprzętu nadzoru)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41256567-1795-4684-b00b-a1308ce43cac) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1464.json) |

### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>Monitorowanie dostępu fizycznego | Monitorowanie fizycznego dostępu do systemów informacyjnych

**Identyfikator:** NIST SP 800-53 R4 PE-6 (4) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1465 — Monitoring Physical Access Monitoring Physical Access To Information Systems (Kontrola zarządzana przez firmę Microsoft 1465 — monitorowanie fizycznego dostępu \| do monitorowania fizycznego dostępu do systemów informacyjnych)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6e41554-86b5-4537-9f7f-4fc41a1d1640) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1465.json) |

### <a name="visitor-access-records"></a>Rekordy dostępu gościa

**Identyfikator:** NIST SP 800-53 R4 PE-8 **Ownership**(Własność NIST SP 800-53 R4 PE-8): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1466 — rekordy dostępu gości](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d943a9c-a6f1-401f-a792-740cdb09c451) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1466.json) |
|[Microsoft Managed Control 1467 — rekordy dostępu gościa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5350cbf9-8bdd-4904-b22a-e88be84ca49d) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1467.json) |

### <a name="visitor-access-records--automated-records-maintenance--review"></a>Rekordy dostępu gościa | Konserwacja/przegląd automatycznych rekordów

**Identyfikator:** NIST SP 800-53 R4 PE-8 (1) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1468 - Visitor Access Records \| Automated Records Maintenance /Review](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75603f96-80a1-4757-991d-5a1221765ddd) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1468.json) |

### <a name="power-equipment-and-cabling"></a>Sprzęt zasilający i okablowanie

**Identyfikator:** NIST SP 800-53 R4 PE-9 **Ownership**(Własność NIST SP 800-53 R4 PE-9): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1469 — zasilanie i okablowanie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff509c5b6-0de0-4a4e-9b2e-cd9cbf3a58fd) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1469.json) |

### <a name="emergency-shutoff"></a>Zamykanie awaryjne

**Identyfikator:** NIST SP 800-53 R4 PE-10 **Ownership**(Własność NIST SP 800-53 R4 PE-10): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1470 — zamykanie awaryjne](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc89ba09f-2e0f-44d0-8095-65b05bd151ef) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1470.json) |
|[Microsoft Managed Control 1471 — zamykanie awaryjne](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7dd0e9ce-1772-41fb-a50a-99977071f916) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1471.json) |
|[Microsoft Managed Control 1472 — zamykanie awaryjne](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef869332-921d-4c28-9402-3be73e6e50c8) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1472.json) |

### <a name="emergency-power"></a>Zasilanie awaryjne

**Identyfikator:** NIST SP 800-53 R4 PE-11 **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1473 — awaryjne zasilanie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd7047705-d719-46a7-8bb0-76ad233eba71) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1473.json) |

### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>Awaryjne zasilanie | Long-Term zasilania — minimalna możliwość operacyjna

**Identyfikator:** NIST SP 800-53 R4 PE-11 (1) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1474 - Emergency Power Long-Term Alternate Power Supply - Minimal Operational Capability (Sterowanie awaryjne przy Long-Term zasilania — \| minimalna możliwość operacyjna)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ad326e-d7a1-44b1-9a76-e17492efc9e4) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1474.json) |

### <a name="emergency-lighting"></a>Oświetlenie awaryjne

**Identyfikator:** NIST SP 800-53 R4 PE-12 **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1475 — oświetlenie awaryjne](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a63848-30cf-4081-937e-ce1a1c885501) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1475.json) |

### <a name="fire-protection"></a>Ochrona przed pożarem

**Identyfikator:** NIST SP 800-53 R4 PE-13 **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1476 — Fire Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f3c4ac2-3e35-4906-a80b-473b12a622d7) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1476.json) |

### <a name="fire-protection--detection-devices--systems"></a>Ochrona przed | Urządzenia/systemy wykrywania

**Identyfikator:** NIST SP 800-53 R4 PE-13 (1) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1477 — \| urządzenia/systemy wykrywania ochrony przed pożarem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4862a63c-6c74-4a9d-a221-89af3c374503) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1477.json) |

### <a name="fire-protection--suppression-devices--systems"></a>Ochrona przed | Urządzenia/systemy pomijania

**Identyfikator:** NIST SP 800-53 R4 PE-13 (2) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1478 — urządzenia/systemy do \| gaszenia pożarów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff997df46-cfbb-4cc8-aac8-3fecdaf6a183) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1478.json) |

### <a name="fire-protection--automatic-fire-suppression"></a>Ochrona przed | Automatyczne gaszenia pożaru

**Identyfikator:** NIST SP 800-53 R4 PE-13 (3) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1479 — automatyczna \| gaszenia pożaru w funkcji ochrony przed pożarami](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe327b072-281d-4f75-9c28-4216e5d72f26) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1479.json) |

### <a name="temperature-and-humidity-controls"></a>Kontrolki temperatury i wilgotności

**Identyfikator:** NIST SP 800-53 R4 PE-14 **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1480 — kontrolki temperatury i wilgotności](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18a767cc-1947-4338-a240-bc058c81164f) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1480.json) |
|[Kontrolka zarządzana przez firmę Microsoft 1481 — kontrolki temperatury i wilgotności](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F717a1c78-a267-4f56-ac58-ee6c54dc4339) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1481.json) |

### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>Kontrolki temperatury i wilgotności | Monitorowanie za pomocą alarmów/powiadomień

**Identyfikator:** NIST SP 800-53 R4 PE-14 (2) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1482 — monitorowanie temperatury i wilgotności za \| pomocą alarmów/powiadomień](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9df4277e-8c88-4d5c-9b1a-541d53d15d7b) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1482.json) |

### <a name="water-damage-protection"></a>Ochrona przed uszkodzeniem wody

**Identyfikator:** NIST SP 800-53 R4 PE-15 **Ownership**(Własność NIST SP 800-53 R4 PE-15): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1483 — ochrona przed uszkodzeniem wody](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5cb81060-3c8a-4968-bcdc-395a1801f6c1) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1483.json) |

### <a name="water-damage-protection--automation-support"></a>Ochrona przed uszkodzeniem | Obsługa automatyzacji

**Identyfikator:** NIST SP 800-53 R4 PE-15 (1) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1484 — obsługa automatyzacji ochrony przed uszkodzeniem \| wody](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F486b006a-3653-45e8-b41c-a052d3e05456) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1484.json) |

### <a name="delivery-and-removal"></a>Dostarczanie i usuwanie

**Identyfikator:** NIST SP 800-53 R4 PE-16 **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1485 — dostarczanie i usuwanie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50301354-95d0-4a11-8af5-8039ecf6d38b) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1485.json) |

### <a name="alternate-work-site"></a>Alternatywna lokacja służbowa

**Identyfikator:** NIST SP 800-53 R4 PE-17 **Ownership**(Własność NIST SP 800-53 R4 PE-17): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1486 — alternatywna witryna służbowa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb790345-a51f-43de-934e-98dbfaf9dca5) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1486.json) |
|[Microsoft Managed Control 1487 — alternatywna witryna służbowa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c3371d-c30c-4f58-abd9-30b8a8199571) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1487.json) |
|[Microsoft Managed Control 1488 — alternatywna witryna służbowa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8ef30eb-a44f-47af-8524-ac19a36d41d2) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1488.json) |

### <a name="location-of-information-system-components"></a>Lokalizacja składników systemu informacji

**Identyfikator:** NIST SP 800-53 R4 PE-18 **Ownership**(Własność NIST SP 800-53 R4 PE-18): Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1489 — lokalizacja składników systemu informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0a794f-1444-4c96-9534-e35fc8c39c91) |Firma Microsoft implementuje tę kontrolę ochrony fizycznej i środowiskowej |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1489.json) |

## <a name="planning"></a>Planowanie

### <a name="security-planning-policy-and-procedures"></a>Zasady i procedury planowania zabezpieczeń

**Identyfikator:** Własność NIST SP 800-53 R4 PL-1: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1490 — zasady i procedury planowania zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e61da80-0957-4892-b70c-609d5eaafb6b) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1490.json) |
|[Microsoft Managed Control 1491 — zasady i procedury planowania zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1571dd40-dafc-4ef4-8f55-16eba27efc7b) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1491.json) |

### <a name="system-security-plan"></a>Plan zabezpieczeń systemu

**Identyfikator:** Własność NIST SP 800-53 R4 PL-2 : **Udostępnione**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1492 — Plan zabezpieczeń systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ad5f307-e045-46f7-8214-5bdb7e973737) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1492.json) |
|[Microsoft Managed Control 1493 — plan zabezpieczeń systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22b469b3-fccf-42da-aa3b-a28e6fb113ce) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1493.json) |
|[Microsoft Managed Control 1494 — plan zabezpieczeń systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed09d84-3311-4853-8b67-2b55dfa33d09) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1494.json) |
|[Microsoft Managed Control 1495 — plan zabezpieczeń systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4978d0e-a596-48e7-9f8c-bbf52554ce8d) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1495.json) |
|[Microsoft Managed Control 1496 — plan zabezpieczeń systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ca96127-2f87-46ab-a4fc-0d2a786df1c8) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1496.json) |

### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>System Security Plan | Planowanie/koordynowanie z innymi jednostkami organizacyjnymi

**Identyfikator:** NIST SP 800-53 R4 PL-2 (3) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1497 — plan zabezpieczeń \| systemu / koordynowanie z innymi jednostkami organizacyjnymi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e3c5583-1729-4d36-8771-59c32f090a22) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1497.json) |

### <a name="rules-of-behavior"></a>Reguły zachowania

**Identyfikator:** Własność NIST SP 800-53 R4 PL-4: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1498 — reguły zachowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F633988b9-cf2f-4323-8394-f0d2af9cd6e1) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1498.json) |
|[Microsoft Managed Control 1499 — reguły zachowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe59671ab-9720-4ee2-9c60-170e8c82251e) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1499.json) |
|[Microsoft Managed Control 1500 — reguły zachowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9dd5b241-03cb-47d3-a5cd-4b89f9c53c92) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1500.json) |
|[Microsoft Managed Control 1501 — reguły zachowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88817b58-8472-4f6c-81fa-58ce42b67f51) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1501.json) |

### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>Reguły zachowania | Ograniczenia dotyczące mediów społecznościowych i sieci

**Identyfikator:** NIST SP 800-53 R4 PL-4 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1502 — reguły zachowania ograniczeń \| mediów społecznościowych i sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe901375c-8f01-4ac8-9183-d5312f47fe63) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1502.json) |

### <a name="information-security-architecture"></a>Architektura zabezpieczeń informacji

**Identyfikator**: NIST SP 800-53 R4 PL-8 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1503 — architektura zabezpieczeń informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc1fa9c2f-d439-4ab9-8b83-81fb1934f81d) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1503.json) |
|[Microsoft Managed Control 1504 — architektura zabezpieczeń informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e7c35d0-12d4-4e0c-80a2-8a352537aefd) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1504.json) |
|[Microsoft Managed Control 1505 — architektura zabezpieczeń informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F813a10a7-3943-4fe3-8678-00dc52db5490) |Firma Microsoft implementuje tę kontrolę planowania |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1505.json) |

## <a name="personnel-security"></a>Bezpieczeństwo personelu

### <a name="personnel-security-policy-and-procedures"></a>Zasady i procedury dotyczące zabezpieczeń personelu

**Identyfikator:** Własność NIST SP 800-53 R4 PS-1: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1506 — zasady i procedury dotyczące zabezpieczeń personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d2ff17-d604-4dd9-b607-9ecf63f28ad2) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1506.json) |
|[Microsoft Managed Control 1507 — zasady i procedury dotyczące zabezpieczeń personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ccd1bf-e7ad-4851-93ce-6ec817469c1e) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1507.json) |

### <a name="position-risk-designation"></a>Oznaczenie ryzyka pozycji

**Identyfikator:** Własność NIST SP 800-53 R4 PS-2: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1508 — oznaczenie ryzyka pozycji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76f500cc-4bca-4583-bda1-6d084dc21086) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1508.json) |
|[Microsoft Managed Control 1509 — oznaczenie ryzyka pozycji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70792197-9bfc-4813-905a-bd33993e327f) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1509.json) |
|[Microsoft Managed Control 1510 — oznaczenie ryzyka pozycji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79da5b09-0e7e-499e-adda-141b069c7998) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1510.json) |

### <a name="personnel-screening"></a>Kontrola personelu

**Identyfikator:** Własność NIST SP 800-53 R4 PS-3: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1511 — kontrola personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9eae324-d327-4539-9293-b48e122465f8) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1511.json) |
|[Microsoft Managed Control 1512 — kontrola personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5a8324ad-f599-429b-aaed-f9c6e8c987a8) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1512.json) |

### <a name="personnel-screening--information-with-special-protection-measures"></a>Kontrola | Informacje ze specjalnymi środkami ochrony

**Identyfikator:** NIST SP 800-53 R4 PS-3 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1513 — informacje dotyczące kontroli personelu \| ze specjalnymi środkami ochrony](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc416970d-b12b-49eb-8af4-fb144cd7c290) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1513.json) |
|[Microsoft Managed Control 1514 — informacje dotyczące kontroli personelu \| ze specjalnymi środkami ochrony](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed5ca00-0e43-434e-a018-7aab91461ba7) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1514.json) |

### <a name="personnel-termination"></a>Zakończenie pracy personelu

**Identyfikator:** Własność NIST SP 800-53 R4 PS-4: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1515 — zakończenie pracy personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02dd141a-a2b2-49a7-bcbd-ca31142f6211) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1515.json) |
|[Microsoft Managed Control 1516 — zakończenie pracy personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3cd269-156f-435b-b472-c3af34c032ed) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1516.json) |
|[Microsoft Managed Control 1517 — zakończenie pracy personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8f5ad423-50d6-4617-b058-69908f5586c9) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1517.json) |
|[Microsoft Managed Control 1518 — zakończenie pracy personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d58f734-c052-40e9-8b2f-a1c2bff0b815) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1518.json) |
|[Microsoft Managed Control 1519 — zakończenie pracy personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f13915a-324c-4ab8-b45c-2eefeeefb098) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1519.json) |
|[Microsoft Managed Control 1520 — zakończenie pracy personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f2c513b-eb16-463b-b469-c10e5fa94f0a) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1520.json) |

### <a name="personnel-termination--automated-notification"></a>Zakończenie pracy | Automatyczne powiadomienia

**Identyfikator:** NIST SP 800-53 R4 PS-4 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1521 — automatyczne powiadomienie o \| zakończeniu działania personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cbddf9c-a3aa-4330-a0f5-4c0c1f1862e5) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1521.json) |

### <a name="personnel-transfer"></a>Transfer personelu

**Identyfikator:** NIST SP 800-53 R4 PS-5 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1522 — Transfer personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38b470cc-f939-4a15-80e0-9f0c74f2e2c9) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1522.json) |
|[Microsoft Managed Control 1523 — Transfer personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5577a310-2551-49c8-803b-36e0d5e55601) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1523.json) |
|[Microsoft Managed Control 1524 — Transfer personelu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72f1cb4e-2439-4fe8-88ea-b8671ce3c268) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1524.json) |
|[Microsoft Managed Control 1525 — Transfer pracowników](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9be2f688-7a61-45e3-8230-e1ec93893f66) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1525.json) |

### <a name="access-agreements"></a>Umowy dostępu

**Identyfikator:** Własność NIST SP 800-53 R4 PS-6: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1526 — umowy dostępu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F953e6261-a05a-44fd-8246-000e1a3edbb9) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1526.json) |
|[Microsoft Managed Control 1527 — umowy dostępu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2823de66-332f-4bfd-94a3-3eb036cd3b67) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1527.json) |
|[Microsoft Managed Control 1528 — umowy dostępu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdeb9797c-22f8-40e8-b342-a84003c924e6) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1528.json) |

### <a name="third-party-personnel-security"></a>Bezpieczeństwo personelu innych firm

**Identyfikator:** Własność NIST SP 800-53 R4 PS-7: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1529 — bezpieczeństwo personelu innych firm](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd74fdc92-1cb8-4a34-9978-8556425cd14c) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1529.json) |
|[Microsoft Managed Control 1530 — bezpieczeństwo personelu innych firm](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e8f9566-29f1-49cd-b61f-f8628a3cf993) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1530.json) |
|[Microsoft Managed Control 1531 — zabezpieczenia personelu innych firm](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0643e0c-eee5-4113-8684-c608d05c5236) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1531.json) |
|[Microsoft Managed Control 1532 — bezpieczeństwo personelu innych firm](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2c66299-9017-4d95-8040-8bdbf7901d52) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1532.json) |
|[Microsoft Managed Control 1533 — bezpieczeństwo personelu innych firm](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbba2a036-fb3b-4261-b1be-a13dfb5fbcaa) |Firma Microsoft implementuje tę kontrolę zabezpieczeń personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1533.json) |

### <a name="personnel-sanctions"></a>Personal Sanctions (Personal Sanctions)

**Identyfikator:** NIST SP 800-53 R4 PS-8 Ownership : Shared (Własność NIST SP 800-53 R4 PS-8): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1534 - Personnel Sanctions](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b2b263e-cd05-4488-bcbf-4debec7a17d9) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1534.json) |
|[Microsoft Managed Control 1535 - Personnel Sanctions](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9a165d2-967d-4733-8399-1074270dae2e) |Firma Microsoft implementuje tę kontrolę zabezpieczeń dla personelu |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1535.json) |

## <a name="risk-assessment"></a>Ocena ryzyka

### <a name="risk-assessment-policy-and-procedures"></a>Zasady i procedury oceny ryzyka

**Identyfikator:** NIST SP 800-53 R4 RA-1 Ownership : Shared (Własność NIST SP 800-53 R4 RA-1): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1536 — zasady i procedury oceny ryzyka](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e40d9de-2ad4-4cb5-8945-23143326a502) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1536.json) |
|[Microsoft Managed Control 1537 — zasady i procedury oceny ryzyka](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb19454ca-0d70-42c0-acf5-ea1c1e5726d1) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1537.json) |

### <a name="security-categorization"></a>Kategoryzacja zabezpieczeń

**Identyfikator:** NIST SP 800-53 R4 RA-2 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1538 — Kategoryzacja zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d7658b2-e827-49c3-a2ae-6d2bd0b45874) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1538.json) |
|[Microsoft Managed Control 1539 — Kategoryzacja zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faabb155f-e7a5-4896-a767-e918bfae2ee0) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1539.json) |
|[Microsoft Managed Control 1540 — Kategoryzacja zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff771f8cb-6642-45cc-9a15-8a41cd5c6977) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1540.json) |

### <a name="risk-assessment"></a>Ocena ryzyka

**Identyfikator:** NIST SP 800-53 R4 RA-3 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1541 — Risk Assessment](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70f6af82-7be6-44aa-9b15-8b9231b2e434) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1541.json) |
|[Microsoft Managed Control 1542 — Risk Assessment](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feab340d0-3d55-4826-a0e5-feebfeb0131d) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1542.json) |
|[Microsoft Managed Control 1543 — Risk Assessment](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd00b778-b5b5-49c0-a994-734ea7bd3624) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1543.json) |
|[Microsoft Managed Control 1544 — Risk Assessment](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43ced7c9-cd53-456b-b0da-2522649a4271) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1544.json) |
|[Microsoft Managed Control 1545 — Risk Assessment](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f4b171a-a56b-4328-8112-32cf7f947ee1) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1545.json) |

### <a name="vulnerability-scanning"></a>Skanowanie luk w zabezpieczeniach

**Identyfikator:** NIST SP 800-53 R4 RA-5 Ownership : Shared (Własność NIST SP 800-53 R4 RA-5): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Na maszynach wirtualnych należy włączyć rozwiązanie do oceny luk w zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Przeprowadza inspekcję maszyn wirtualnych w celu wykrycia, czy są uruchomione obsługiwane rozwiązanie do oceny luk w zabezpieczeniach. Podstawowym składnikiem każdego programu związanego z ryzykiem cybernetycznym i bezpieczeństwem jest identyfikacja i analiza luk w zabezpieczeniach. Azure Security Center warstwy cenowej standardowa obejmuje skanowanie maszyn wirtualnych w celu wykorzystania luk w zabezpieczeniach bez dodatkowych kosztów. Ponadto Security Center automatycznie wdrożyć to narzędzie. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Zaawansowane zabezpieczenia danych powinny być włączone na SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Przejmij każdą SQL Managed Instance bez zaawansowanego zabezpieczeń danych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na serwerach SQL należy włączyć zaawansowane zabezpieczenia danych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Inspekcja serwerów SQL bez Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Microsoft Managed Control 1546 — skanowanie luk w zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce1ea7e-4038-4e53-82f4-63e8859333c1) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1546.json) |
|[Microsoft Managed Control 1547 — skanowanie luk w zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58abf9b8-c6d4-4b4b-bfb9-fe98fe295f52) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1547.json) |
|[Microsoft Managed Control 1548 — skanowanie luk w zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3afe6c78-6124-4d95-b85c-eb8c0c9539cb) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1548.json) |
|[Microsoft Managed Control 1549 — skanowanie luk w zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd6976a08-d969-4df2-bb38-29556c2eb48a) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1549.json) |
|[Microsoft Managed Control 1550 — skanowanie luk w zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F902908fb-25a8-4225-a3a5-5603c80066c9) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1550.json) |
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Serwery, które nie spełniają skonfigurowanego planu bazowego, będą monitorowane przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Przeszukaj luki w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Należy skorygować luki w zabezpieczeniach baz danych SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Monitoruj wyniki skanowania w ramach oceny luk w zabezpieczeniach i zalecenia dotyczące sposobu korygowania luk w zabezpieczeniach bazy danych. |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning--update-tool-capability"></a>Skanowanie luk w zabezpieczeniach | Możliwość aktualizowania narzędzi

**Identyfikator:** NIST SP 800-53 R4 RA-5 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1551 — możliwość narzędzia do aktualizowania skanowania luk w \| zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bbda922-0172-4095-89e6-5b4a0bf03af7) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1551.json) |

### <a name="vulnerability-scanning--update-by-frequency--prior-to-new-scan--when-identified"></a>Skanowanie luk w zabezpieczeniach | Aktualizuj według częstotliwości / przed nowym skanowaniem / Po zidentyfikowaniu

**Identyfikator:** NIST SP 800-53 R4 RA-5 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1552 — aktualizacja skanowania luk w zabezpieczeniach według częstotliwości/ przed nowym skanowaniem \| / Po zidentyfikowaniu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43684572-e4f1-4642-af35-6b933bc506da) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1552.json) |

### <a name="vulnerability-scanning--breadth--depth-of-coverage"></a>Skanowanie luk w zabezpieczeniach | Zakres/głębokość pokrycia

**Identyfikator:** NIST SP 800-53 R4 RA-5 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1553 — zakres skanowania luk w zabezpieczeniach \| / głębia pokrycia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e5225fe-cdfb-4fce-9aec-0fe20dd53b62) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1553.json) |

### <a name="vulnerability-scanning--discoverable-information"></a>Skanowanie luk w zabezpieczeniach | Informacje wykrywalne

**Identyfikator:** NIST SP 800-53 R4 RA-5 (4) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1554 — skanowanie luk w zabezpieczeniach — informacje \| wykrywalne](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10984b4e-c93e-48d7-bf20-9c03b04e9eca) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1554.json) |

### <a name="vulnerability-scanning--privileged-access"></a>Skanowanie luk w zabezpieczeniach | Dostęp uprzywilejowany

**Identyfikator:** NIST SP 800-53 R4 RA-5 (5) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1555 — skanowanie luk w zabezpieczeniach \| uprzywilejowanego dostępu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5afa8cab-1ed7-4e40-884c-64e0ac2059cc) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1555.json) |

### <a name="vulnerability-scanning--automated-trend-analyses"></a>Skanowanie luk w zabezpieczeniach | Zautomatyzowane analizy trendów

**Identyfikator:** NIST SP 800-53 R4 RA-5 (6) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1556 — zautomatyzowane analizy trendów skanowania luk w \| zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391ff8b3-afed-405e-9f7d-ef2f8168d5da) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1556.json) |

### <a name="vulnerability-scanning--review-historic-audit-logs"></a>Skanowanie luk w zabezpieczeniach | Przeglądanie dzienników inspekcji historycznych

**Identyfikator:** NIST SP 800-53 R4 RA-5 (8) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1557 — skanowanie luk w zabezpieczeniach — \| przejrzyj historyczne dzienniki inspekcji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36fbe499-f2f2-41b6-880e-52d7ea1d94a5) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1557.json) |

### <a name="vulnerability-scanning--correlate-scanning-information"></a>Skanowanie luk w zabezpieczeniach | Korelowanie informacji skanowania

**Identyfikator:** NIST SP 800-53 R4 RA-5 (10) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1558 — skanowanie luk w zabezpieczeniach \| skorelowanie informacji skanowania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65592b16-4367-42c5-a26e-d371be450e17) |Firma Microsoft implementuje tę kontrolę oceny ryzyka |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1558.json) |

## <a name="system-and-services-acquisition"></a>Pozyskiwanie systemów i usług

### <a name="system-and-services-acquisition-policy-and-procedures"></a>Zasady i procedury pozyskiwania systemu i usług

**Identyfikator:** Własność NIST SP 800-53 R4 SA-1: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1559 — zasady i procedury pozyskiwania systemów i usług](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45692294-f074-42bd-ac54-16f1a3c07554) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1559.json) |
|[Microsoft Managed Control 1560 — zasady i procedury pozyskiwania systemów i usług](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe29e0915-5c2f-4d09-8806-048b749ad763) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1560.json) |

### <a name="allocation-of-resources"></a>Alokacja zasobów

**Identyfikator:** NIST SP 800-53 R4 SA-2 Ownership : Shared (Własność NIST SP 800-53 R4 SA-2): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1561 — alokacja zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40364c3f-c331-4e29-b1e3-2fbe998ba2f5) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1561.json) |
|[Microsoft Managed Control 1562 — alokacja zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4142013-7964-4163-a313-a900301c2cef) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1562.json) |
|[Microsoft Managed Control 1563 — alokacja zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9afe2edf-232c-4fdf-8e6a-e867a5c525fd) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1563.json) |

### <a name="system-development-life-cycle"></a>Cykl życia tworzenia systemu

**Identyfikator:** Własność NIST SP 800-53 R4 SA-3: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1564 — cykl życia tworzenia systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F157f0ef9-143f-496d-b8f9-f8c8eeaad801) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1564.json) |
|[Microsoft Managed Control 1565 — cykl życia tworzenia systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45ce2396-5c76-4654-9737-f8792ab3d26b) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1565.json) |
|[Microsoft Managed Control 1566 — cykl życia opracowywania systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50ad3724-e2ac-4716-afcc-d8eabd97adb9) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1566.json) |
|[Microsoft Managed Control 1567 — cykl życia opracowywania systemu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe72edbf6-aa61-436d-a227-0f32b77194b3) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1567.json) |

### <a name="acquisition-process"></a>Proces pozyskiwania

**Identyfikator:** NIST SP 800-53 R4 SA-4 Ownership : Shared (Własność NIST SP 800-53 R4 SA-4): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1568 — proces pozyskiwania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8eae8-9854-495a-ac82-d2cd3eac02a6) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1568.json) |
|[Microsoft Managed Control 1569 — proces pozyskiwania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad2f8e61-a564-4dfd-8eaa-816f5be8cb34) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1569.json) |
|[Microsoft Managed Control 1570 — proces pozyskiwania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7fcf38d-bb09-4600-be7d-825046eb162a) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1570.json) |
|[Microsoft Managed Control 1571 — proces pozyskiwania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb11c985b-f2cd-4bd7-85f4-b52426edf905) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1571.json) |
|[Microsoft Managed Control 1572 — proces pozyskiwania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04f5fb00-80bb-48a9-a75b-4cb4d4c97c36) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1572.json) |
|[Microsoft Managed Control 1573 — proces pozyskiwania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58c93053-7b98-4cf0-b99f-1beb985416c2) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1573.json) |
|[Microsoft Managed Control 1574 — proces pozyskiwania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f935dab-83d6-47b8-85ef-68b8584161b9) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1574.json) |

### <a name="acquisition-process--functional-properties-of-security-controls"></a>Proces pozyskiwania | Właściwości funkcjonalne kontrolek zabezpieczeń

**Identyfikator:** NIST SP 800-53 R4 SA-4 (1) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1575 — właściwości funkcjonalne procesu \| pozyskiwania kontrolek zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e1bb73-1b08-4dbe-9c62-8e2e92e7ec41) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1575.json) |

### <a name="acquisition-process--design--implementation-information-for-security-controls"></a>Proces pozyskiwania | Informacje o projektowaniu/implementacji kontrolek zabezpieczeń

**Identyfikator:** NIST SP 800-53 R4 SA-4 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1576 — Acquisition Process Design/Implementation Information for Security Controls (Kontrola zarządzana przez firmę Microsoft 1576 — informacje o projekcie \| procesu pozyskiwania/implementacji mechanizmu kontroli zabezpieczeń)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f18c885-ade3-48c5-80b1-8f9216019c18) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1576.json) |

### <a name="acquisition-process--continuous-monitoring-plan"></a>Proces pozyskiwania | Plan ciągłego monitorowania

**Identyfikator:** NIST SP 800-53 R4 SA-4 (8) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1577 — plan \| ciągłego monitorowania procesu pozyskiwania](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd922484a-8cfc-4a6b-95a4-77d6a685407f) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1577.json) |

### <a name="acquisition-process--functions--ports--protocols--services-in-use"></a>Proces pozyskiwania | Funkcje / Porty / Protokoły / Usługi w użyciu

**Identyfikator:** NIST SP 800-53 R4 SA-4 (9) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1578 — Funkcje procesu pozyskiwania \| / Porty / Protokoły / Używane usługi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45b7b644-5f91-498e-9d89-7402532d3645) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1578.json) |

### <a name="acquisition-process--use-of-approved-piv-products"></a>Proces pozyskiwania | Korzystanie z zatwierdzonych produktów Piv

**Identyfikator:** NIST SP 800-53 R4 SA-4 (10) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1579 — użycie procesu \| pozyskiwania zatwierdzonych produktów Piv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e54c7ef-7457-430b-9a3e-ef8881d4a8e0) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1579.json) |

### <a name="information-system-documentation"></a>Dokumentacja systemu informacji

**Identyfikator:** NIST SP 800-53 R4 SA-5 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1580 — dokumentacja systemu informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F854db8ac-6adf-42a0-bef3-b73f764f40b9) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1580.json) |
|[Microsoft Managed Control 1581 — dokumentacja systemu informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F742b549b-7a25-465f-b83c-ea1ffb4f4e0e) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1581.json) |
|[Microsoft Managed Control 1582 — dokumentacja systemu informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd9e2f38-259b-462c-bfad-0ad7ab4e65c5) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1582.json) |
|[Microsoft Managed Control 1583 — dokumentacja systemu informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0882d488-8e80-4466-bc0f-0cd15b6cb66d) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1583.json) |
|[Microsoft Managed Control 1584 — dokumentacja systemu informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5864522b-ff1d-4979-a9f8-58bee1fb174c) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1584.json) |

### <a name="security-engineering-principles"></a>Zasady inżynierii zabezpieczeń

**Identyfikator:** NIST SP 800-53 R4 SA-8 Ownership : Shared (Własność NIST SP 800-53 R4 SA-8): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1585 — Zasady inżynierii zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd57f8732-5cdc-4cda-8d27-ab148e1f3a55) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1585.json) |

### <a name="external-information-system-services"></a>Zewnętrzne usługi systemu informacji

**Identyfikator:** Własność NIST SP 800-53 R4 SA-9: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1586 — External Information System Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e3b2fbd-8f37-4766-a64d-3f37703dcb51) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1586.json) |
|[Microsoft Managed Control 1587 — External Information System Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32820956-9c6d-4376-934c-05cd8525be7c) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1587.json) |
|[Microsoft Managed Control 1588 — External Information System Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68ebae26-e0e0-4ecb-8379-aabf633b51e9) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1588.json) |

### <a name="external-information-system-services--risk-assessments--organizational-approvals"></a>Usługi zewnętrznego systemu informacji | Oceny ryzyka/Zatwierdzenia organizacyjne

**Identyfikator:** NIST SP 800-53 R4 SA-9 (1) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1589 — External Information System Services \| Risk Assessments / Organizational Approvals](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ec7f9b-9478-40ff-8cfd-6a0d510081a8) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1589.json) |
|[Microsoft Managed Control 1590 — External Information System Services \| Risk Assessments / Organizational Approvals](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf296b8c-f391-4ea4-9198-be3c9d39dd1f) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1590.json) |

### <a name="external-information-system-services--identification-of-functions--ports--protocols--services"></a>Usługi zewnętrzne systemu informacji | Identyfikacja funkcji / portów / protokołów / usług

**Identyfikator:** NIST SP 800-53 R4 SA-9 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1591 — External Information System Services \| Ident. Funkcje / Porty / Protokoły / Usługi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff751cdb7-fbee-406b-969b-815d367cb9b3) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1591.json) |

### <a name="external-information-system-services--consistent-interests-of-consumers-and-providers"></a>Usługi zewnętrznego systemu informacji | Spójne zainteresowania konsumentów i dostawców

**Identyfikator:** NIST SP 800-53 R4 SA-9 (4) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1592 — External Information System Services Consistent Interests of Consumers and Providers (Kontrola zarządzana przez firmę Microsoft 1592 — spójne zainteresowania konsumentów i dostawców usług systemu \| informacji zewnętrznych)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d01ba6c-289f-42fd-a408-494b355b6222) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1592.json) |

### <a name="external-information-system-services--processing-storage-and-service-location"></a>Usługi zewnętrznego systemu informacji | Przetwarzanie, magazyn i lokalizacja usługi

**Identyfikator:** NIST SP 800-53 R4 SA-9 (5) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1593 — przetwarzanie, magazynowanie i lokalizacja usług zewnętrznych usług \| systemowych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cd0a426-b5f5-4fe0-9539-a6043cdbc6fa) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1593.json) |

### <a name="developer-configuration-management"></a>Zarządzanie konfiguracją dla deweloperów

**Identyfikator:** NIST SP 800-53 R4 SA-10 **Ownership**: Shared (Własność NIST SP 800-53 R4 SA-10): Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1594 — Developer Configuration Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F042ba2a1-8bb8-45f4-b080-c78cf62b90e9) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1594.json) |
|[Microsoft Managed Control 1595 — Developer Configuration Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e0414e7-6ef5-4182-8076-aa82fbb53341) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1595.json) |
|[Microsoft Managed Control 1596 — Developer Configuration Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21e25e01-0ae0-41be-919e-04ce92b8e8b8) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1596.json) |
|[Microsoft Managed Control 1597 — Developer Configuration Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68b250ec-2e4f-4eee-898a-117a9fda7016) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1597.json) |
|[Microsoft Managed Control 1598 — Developer Configuration Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae7e1f5e-2d63-4b38-91ef-bce14151cce3) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1598.json) |

### <a name="developer-configuration-management--software--firmware-integrity-verification"></a>Zarządzanie konfiguracją dla deweloperów | Weryfikacja integralności oprogramowania/oprogramowania układowego

**Identyfikator:** NIST SP 800-53 R4 SA-10 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1599 — Developer Configuration Management \| Software / Firmware Integrity Verification](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0004bbf0-5099-4179-869e-e9ffe5fb0945) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1599.json) |

### <a name="developer-security-testing-and-evaluation"></a>Testowanie i ocena zabezpieczeń dla deweloperów

**Identyfikator:** Własność NIST SP 800-53 R4 SA-11: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1600 — testowanie i ocena zabezpieczeń dla deweloperów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc53f3123-d233-44a7-930b-f40d3bfeb7d6) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1600.json) |
|[Microsoft Managed Control 1601 — testowanie i ocena zabezpieczeń dla deweloperów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ee79a0c-addf-4ce9-9b3c-d9576ed5e20e) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1601.json) |
|[Microsoft Managed Control 1602 — testowanie i ocena zabezpieczeń dla deweloperów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fddae2e97-a449-499f-a1c8-aea4a7e52ec9) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1602.json) |
|[Microsoft Managed Control 1603 — testowanie i ocena zabezpieczeń dla deweloperów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b909c26-162f-47ce-8e15-0c1f55632eac) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1603.json) |
|[Microsoft Managed Control 1604 — testowanie i ocena zabezpieczeń dla deweloperów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44dbba23-0b61-478e-89c7-b3084667782f) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1604.json) |

### <a name="developer-security-testing-and-evaluation--static-code-analysis"></a>Testowanie i ocena zabezpieczeń dla deweloperów | Statyczne Code Analysis

**Identyfikator:** NIST SP 800-53 R4 SA-11 (1) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1605 — testowanie zabezpieczeń i ocena dla deweloperów — statyczne \| Code Analysis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0062eb8b-dc75-4718-8ea5-9bb4a9606655) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1605.json) |

### <a name="developer-security-testing-and-evaluation--threat-and-vulnerability-analyses"></a>Testowanie i ocena zabezpieczeń dla deweloperów | Analizy zagrożeń i luk w zabezpieczeniach

**Identyfikator:** NIST SP 800-53 R4 SA-11 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1606 — Developer Security Testing and Evaluation Threat and Vulnerability Analyses (Kontrola zarządzana przez firmę Microsoft 1606 — analiza zagrożeń i luk w zabezpieczeniach dla \| deweloperów)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaa8a9a4-5bbe-4c72-98f6-a3a47ae2b1ca) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1606.json) |

### <a name="developer-security-testing-and-evaluation--dynamic-code-analysis"></a>Testowanie i ocena zabezpieczeń dla deweloperów | Dynamiczne Code Analysis

**Identyfikator:** NIST SP 800-53 R4 SA-11 (8) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1607 — developer security testing and evaluation \| dynamic Code Analysis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F976a74cf-b192-4d35-8cab-2068f272addb) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1607.json) |

### <a name="supply-chain-protection"></a>Ochrona łańcucha dostaw

**Identyfikator:** Własność NIST SP 800-53 R4 SA-12: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1608 — Supply Chain Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb73b7b3b-677c-4a2a-b949-ad4dc4acd89f) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1608.json) |

### <a name="development-process-standards-and-tools"></a>Proces programistyki, standardy i narzędzia

**Identyfikator:** NIST SP 800-53 R4 SA-15 **Ownership**: Shared (Własność NIST SP 800-53 R4 SA-15): Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1609 — proces tworzenia, standardy i narzędzia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e93fa71-42ac-41a7-b177-efbfdc53c69f) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1609.json) |
|[Microsoft Managed Control 1610 — proces tworzenia, standardy i narzędzia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9f3fb54-4222-46a1-a308-4874061f8491) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1610.json) |

### <a name="developer-provided-training"></a>Developer-Provided szkoleniowe

**Identyfikator:** Własność NIST SP 800-53 R4 SA-16: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1611 — Developer-Provided szkoleniowe](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdda8a0c-ac32-43f6-b2f4-7dc1df03f43f) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1611.json) |

### <a name="developer-security-architecture-and-design"></a>Architektura i projekt zabezpieczeń dla deweloperów

**Identyfikator:** NIST SP 800-53 R4 SA-17 Ownership : Shared (Własność NIST SP 800-53 R4 SA-17): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1612 — architektura i projekt zabezpieczeń deweloperów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2037b3d-8b04-4171-8610-e6d4f1d08db5) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1612.json) |
|[Microsoft Managed Control 1613 — architektura i projekt zabezpieczeń deweloperów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe2ad78b-8748-4bff-a924-f74dfca93f30) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1613.json) |
|[Microsoft Managed Control 1614 — architektura i projekt zabezpieczeń dewelopera](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8154e3b3-cc52-40be-9407-7756581d71f6) |Firma Microsoft implementuje tę kontrolę pozyskiwania systemów i usług |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1614.json) |

## <a name="system-and-communications-protection"></a>Ochrona systemu i komunikacji

### <a name="system-and-communications-protection-policy-and-procedures"></a>Zasady i procedury ochrony systemu i komunikacji

**Identyfikator:** Własność NIST SP 800-53 R4 SC-1: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1615 — zasady i procedury ochrony systemu i komunikacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff35e02aa-0a55-49f8-8811-8abfa7e6f2c0) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1615.json) |
|[Microsoft Managed Control 1616 — zasady i procedury ochrony systemu i komunikacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2006457a-48b3-4f7b-8d2e-1532287f9929) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1616.json) |

### <a name="application-partitioning"></a>Partycjonowanie aplikacji

**Identyfikator:** Własność NIST SP 800-53 R4 SC-2: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1617 — partycjonowanie aplikacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa631d8f5-eb81-4f9d-9ee1-74431371e4a3) |Firma Microsoft implementuje tę kontrolę systemu i ochrony komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1617.json) |

### <a name="security-function-isolation"></a>Izolacja funkcji zabezpieczeń

**Identyfikator:** Własność NIST SP 800-53 R4 SC-3: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1618 — izolacja funkcji zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff52f89aa-4489-4ec4-950e-8c96a036baa9) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1618.json) |

### <a name="information-in-shared-resources"></a>Informacje w zasobach udostępnionych

**Identyfikator:** NIST SP 800-53 R4 SC-4 Ownership : Shared (Własność NIST SP 800-53 R4 SC-4): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1619 — informacje w zasobach udostępnionych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc722e569-cb52-45f3-a643-836547d016e1) |Firma Microsoft implementuje tę kontrolę systemu i ochrony komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1619.json) |

### <a name="denial-of-service-protection"></a>Denial of Service Protection

**Identyfikator:** NIST SP 800-53 R4 SC-5 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure DDoS Protection Standardowa powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |Standard ochrony przed DDoS powinien być włączony dla wszystkich sieci wirtualnych z podsiecią, która jest częścią bramy aplikacji z publicznym adresem IP. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
|[Microsoft Managed Control 1620 — odmowa ochrony usługi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd17c826b-1dec-43e1-a984-7b71c446649c) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1620.json) |

### <a name="resource-availability"></a>Dostępność zasobów

**Identyfikator:** NIST SP 800-53 R4 SC-6 **Ownership**: Shared (Własność NIST SP 800-53 R4 SC-6): Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1621 — dostępność zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cb9f731-744a-4691-a481-ca77b0411538) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1621.json) |

### <a name="boundary-protection"></a>Ochrona granic

**Identyfikator:** NIST SP 800-53 R4 SC-7 Ownership : Shared (Własność NIST SP 800-53 R4 SC-7): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zalecenia dotyczące adaptacyjnego wzmacniania zabezpieczeń sieci powinny być stosowane na maszynach wirtualnych skierowanych do Internetu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analizuje wzorce ruchu maszyn wirtualnych skierowanych do Internetu i udostępnia zalecenia dotyczące reguł sieciowej grupy zabezpieczeń, które zmniejszają ryzyko ataku |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Wszystkie porty sieciowe powinny być ograniczone do sieciowych grup zabezpieczeń skojarzonych z maszyną wirtualną](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center zidentyfikował, że niektóre reguły ruchu przychodzącego sieciowych grup zabezpieczeń są zbyt permisywne. Reguły ruchu przychodzącego nie powinny zezwalać na dostęp z zakresów "Any" lub "Internet". Może to potencjalnie umożliwić atakującym ukierunkowanie Twoich zasobów. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[Microsoft Managed Control 1622 — Boundary Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fecf56554-164d-499a-8d00-206b07c27bed) |Firma Microsoft implementuje tę kontrolę systemu i ochrony komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1622.json) |
|[Microsoft Managed Control 1623 — Boundary Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02ce1b22-412a-4528-8630-c42146f917ed) |Firma Microsoft implementuje tę kontrolę systemu i ochrony komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1623.json) |
|[Microsoft Managed Control 1624 — Boundary Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37d079e3-d6aa-4263-a069-dd7ac6dd9684) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1624.json) |
|[Konta magazynu powinny ograniczać dostęp sieciowy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Dostęp sieciowy do kont magazynu powinien być ograniczony. Skonfiguruj reguły sieci, aby tylko aplikacje z dozwolonych sieci mogły uzyskać dostęp do konta magazynu. Aby zezwolić na połączenia z określonego Internetu lub klientów lokalnych, można przyznać dostęp do ruchu z określonych sieci wirtualnych platformy Azure lub z zakresów publicznych internetowych adresów IP |Inspekcja, Odmowa, Wyłączone |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="boundary-protection--access-points"></a>Ochrona granic | Punkty dostępu

**Identyfikator:** NIST SP 800-53 R4 SC-7 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu do sieci just in time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp just in time (JIT) do sieci będzie monitorowany przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft Managed Control 1625 — punkty dostępu ochrony \| granic](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9b66a4d-70a1-4b47-8fa1-289cec68c605) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1625.json) |

### <a name="boundary-protection--external-telecommunications-services"></a>Ochrona granic | Zewnętrzne usługi telekomunikacyjne

**Identyfikator:** NIST SP 800-53 R4 SC-7 (4) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu do sieci just in time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Możliwy dostęp do sieci just in time (JIT) będzie monitorowany przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft Managed Control 1626 — Boundary Protection \| External Telecommunications Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8f6bddd-6d67-439a-88d4-c5fe39a79341) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1626.json) |
|[Microsoft Managed Control 1627 — Boundary Protection \| External Telecommunications Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd73310d-76fc-422d-bda4-3a077149f179) |Firma Microsoft implementuje tę kontrolę systemu i ochrony komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1627.json) |
|[Microsoft Managed Control 1628 — Boundary Protection \| External Telecommunications Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67de62b4-a737-4781-8861-3baed3c35069) |Firma Microsoft implementuje tę kontrolę systemu i ochrony komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1628.json) |
|[Microsoft Managed Control 1629 — Boundary Protection External Telecommunications Services (Kontrola zarządzana przez firmę Microsoft 1629 — zewnętrzne \| usługi telekomunikacyjne ochrony granic)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc171b095-7756-41de-8644-a062a96043f2) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1629.json) |
|[Microsoft Managed Control 1630 — zewnętrzne usługi telekomunikacyjne \| ochrony granic](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3643717a-3897-4bfd-8530-c7c96b26b2a0) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1630.json) |

### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Ochrona granic | Odmów domyślnie / Zezwalaj według wyjątku

**Identyfikator:** NIST SP 800-53 R4 SC-7 (5) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1631 - Boundary Protection \| Deny By Default / Allow By Exception](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74ae9b8e-e7bb-4c9c-992f-c535282f7a2c) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1631.json) |

### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Ochrona granic | Zapobieganie tunelowania podzielonemu dla urządzeń zdalnych

**Identyfikator:** NIST SP 800-53 R4 SC-7 (7) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1632 — Boundary Protection Prevent Split Tunneling for Remote Devices (Kontrola zarządzana przez firmę Microsoft 1632 — ochrona granic uniemożliwia tunelowanie podzielone \| dla urządzeń zdalnych)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ce9073a-77fa-48f0-96b1-87aa8e6091c2) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1632.json) |

### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Ochrona granic | Przekieruj ruch do uwierzytelnionych serwerów proxy

**Identyfikator:** NIST SP 800-53 R4 SC-7 (8) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1633 — Boundary Protection Route Traffic To Authenticated Proxy Servers (Zarządzana przez firmę Microsoft kontrola 1633 — ochrona granic przekierowywała \| ruch do uwierzytelnionych serwerów proxy)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F07557aa0-e02f-4460-9a81-8ecd2fed601a) |Firma Microsoft implementuje tę kontrolę systemu i ochrony komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1633.json) |

### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Ochrona granic | Zapobieganie nieautoryzowanemu eksfiltracji

**Identyfikator:** NIST SP 800-53 R4 SC-7 (10) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1634 — Boundary Protection Prevent Unauthorized Exfiltration (Kontrola zarządzana przez firmę Microsoft 1634 — ochrona granic przed \| nieautoryzowanym eksfiltracją)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F292a7c44-37fa-4c68-af7c-9d836955ded2) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1634.json) |

### <a name="boundary-protection--host-based-protection"></a>Ochrona granic | Host-Based Protection

**Identyfikator:** NIST SP 800-53 R4 SC-7 (12) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1635 — Boundary Protection \| Host-Based Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87551b5d-1deb-4d0f-86cc-9dc14cb4bf7e) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1635.json) |

### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Ochrona granic | Izolacja narzędzi zabezpieczeń / mechanizmów / składników pomocy technicznej

**Identyfikator:** NIST SP 800-53 R4 SC-7 (13) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1636 — izolacja ochrony granic narzędzi \| zabezpieczeń / mechanizmów / składników pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7b694eed-7081-43c6-867c-41c76c961043) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1636.json) |

### <a name="boundary-protection--fail-secure"></a>Ochrona granic | Zabezpieczanie przed niepowodzeniem

**Identyfikator:** NIST SP 800-53 R4 SC-7 (18) **Własność:** Microsoft

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1637 — Boundary Protection Fail \| Secure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4075bedc-c62a-4635-bede-a01be89807f3) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1637.json) |

### <a name="boundary-protection--dynamic-isolation--segregation"></a>Ochrona granic | Izolacja dynamiczna / podział

**Identyfikator:** NIST SP 800-53 R4 SC-7 (20) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1638 - Boundary Protection Dynamic Isolation /Segregation (Microsoft Managed Control 1638 — dynamic \| isolation/segregation (Izolacja dynamiczna/podział ochrony granic)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49b99653-32cd-405d-a135-e7d60a9aae1f) |Firma Microsoft implementuje tę kontrolę systemu i ochrony komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1638.json) |

### <a name="boundary-protection--isolation-of-information-system-components"></a>Ochrona granic | Izolacja składników systemu informacji

**Identyfikator:** NIST SP 800-53 R4 SC-7 (21) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1639 — Boundary Protection Isolation Of Information System Components (Microsoft Managed Control 1639 — izolacja ochrony granic \| składników systemu informacji)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78e8e649-50f6-4fe3-99ac-fedc2e63b03f) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1639.json) |

### <a name="transmission-confidentiality-and-integrity"></a>Poufność i integralność transmisji

**Identyfikator:** NIST SP 800-53 R4 SC-8 Ownership : Shared (Własność NIST SP 800-53 R4 SC-8): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1640 — poufność i integralność transmisji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a289ce-6a20-4b75-a0f3-dc8601b6acd0) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1640.json) |

### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Poufność transmisji i integralność | Kryptograficzna lub alternatywna ochrona fizyczna

**Identyfikator:** NIST SP 800-53 R4 SC-8 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Microsoft Managed Control 1641 — Transmission Confidentiality And Integrity Cryptographic or Alternate Physical Protection (Kontrola zarządzana przez firmę Microsoft 1641 — kryptograficzna lub alternatywna \| ochrona fizyczna)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd39d4f68-7346-4133-8841-15318a714a24) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1641.json) |
|[Należy włączyć tylko bezpieczne Azure Cache for Redis połączenia z siecią](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Inspekcja włączania tylko połączeń za pośrednictwem protokołu SSL do Azure Cache for Redis. Korzystanie z bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[Należy włączyć bezpieczny transfer na konta magazynu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Wymaganie inspekcji bezpiecznego transferu na koncie magazynu. Bezpieczny transfer to opcja, która wymusza na koncie magazynu akceptowanie żądań tylko z bezpiecznych połączeń (HTTPS). Użycie protokołu HTTPS zapewnia uwierzytelnianie między serwerem i usługą oraz chroni dane podczas przesyłania przed atakami warstwy sieciowej, takimi jak ataki typu man-in-the-middle, podsłuchiwanie i przejmowanie sesji |Inspekcja, Odmowa, Wyłączone |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie serwera/usługi i chroni dane podczas przesyłania przed atakami podsłuchiwu w warstwie sieciowej. |Inspekcja, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |
|[Serwery sieci Web z systemem Windows powinny być skonfigurowane do używania protokołów bezpiecznej komunikacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |Aby chronić prywatność informacji przekazywanej przez Internet, serwery internetowe powinny używać najnowszej wersji standardowego protokołu kryptograficznego, Transport Layer Security (TLS). Szyfrowanie TLS zabezpiecza komunikację za pośrednictwem sieci przy użyciu certyfikatów zabezpieczeń w celu szyfrowania połączenia między maszynami. Protokół TLS 1.3 jest szybszy i bezpieczniejszy niż wcześniejsze wersje: TLS 1.0-1.2 i SSL 2-3, które są uznawane za starsze protokoły. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

### <a name="network-disconnect"></a>Rozłączanie z siecią

**Identyfikator:** NIST SP 800-53 R4 SC-10 Ownership : Shared (Własność NIST SP 800-53 R4 SC-10): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1642 — rozłączanie sieci](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53397227-5ee3-4b23-9e5e-c8a767ce6928) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1642.json) |

### <a name="cryptographic-key-establishment-and-management"></a>Tworzenie kluczy kryptograficznych i zarządzanie nimi

**Identyfikator:** NIST SP 800-53 R4 SC-12 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1643 — tworzenie kluczy kryptograficznych i zarządzanie nimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8d492c-dd7a-46f7-a723-fa66a425b87c) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1643.json) |

### <a name="cryptographic-key-establishment-and-management--availability"></a>Zarządzanie i ustanawianie kluczy kryptograficznych | Dostępność

**Identyfikator:** NIST SP 800-53 R4 SC-12 (1) **Własność:** Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1644 — dostępność tworzenia kluczy kryptograficznych i zarządzania \| nimi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7211477-c970-446b-b4af-062f37461147) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1644.json) |

### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Zarządzanie i ustanawianie kluczy kryptograficznych | Klucze symetryczne

**Identyfikator:** NIST SP 800-53 R4 SC-12 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1645 — tworzenie kluczy kryptograficznych i zarządzanie \| kluczami symetrycznymi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafbd0baf-ff1a-4447-a86f-088a97347c0c) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1645.json) |

### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Tworzenie kluczy kryptograficznych i zarządzanie nimi | Klucze asymetryczne

**Identyfikator:** NIST SP 800-53 R4 SC-12 (3) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1646 — cryptographic key establishment and management asymmetric keys (Kontrola zarządzana przez firmę Microsoft 1646 — tworzenie kluczy kryptograficznych i zarządzanie kluczami \| asymetrycznymi)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F506814fa-b930-4b10-894e-a45b98c40e1a) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1646.json) |

### <a name="cryptographic-protection"></a>Ochrona kryptograficzna

**Identyfikator:** NIST SP 800-53 R4 SC-13 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1647 — Ochrona kryptograficzna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F791cfc15-6974-42a0-9f4c-2d4b82f4a78c) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1647.json) |

### <a name="collaborative-computing-devices"></a>Urządzenia do przetwarzania opartego na współpracy

**Identyfikator:** NIST SP 800-53 R4 SC-15 **Ownership**: Shared (Własność NIST SP 800-53 R4 SC-15): Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1648 — Collaborative Computing Devices](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a9eb14b-495a-4ebb-933c-ce4ef5264e32) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1648.json) |
|[Microsoft Managed Control 1649 — collaborative computing devices](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26d292cc-b0b8-4c29-9337-68abc758bf7b) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1649.json) |

### <a name="public-key-infrastructure-certificates"></a>Certyfikaty infrastruktury kluczy publicznych

**Identyfikator:** NIST SP 800-53 R4 SC-17 **Ownership**: Shared (Własność NIST SP 800-53 R4 SC-17): Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1650 — certyfikaty infrastruktury kluczy publicznych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F201d3740-bd16-4baf-b4b8-7cda352228b7) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1650.json) |

### <a name="mobile-code"></a>Mobile Code

**Identyfikator:** NIST SP 800-53 R4 SC-18 Ownership : Shared (Własność NIST SP 800-53 R4 SC-18): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1651 — Mobile Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6db63528-c9ba-491c-8a80-83e1e6977a50) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1651.json) |
|[Microsoft Managed Control 1652 — Mobile Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6998e84a-2d29-4e10-8962-76754d4f772d) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1652.json) |
|[Microsoft Managed Control 1653 — Mobile Code](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1c00a7-7fd0-42b0-8c5b-c45f6fa1f71b) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1653.json) |

### <a name="voice-over-internet-protocol"></a>Protokół głosowy przez Internet

**Identyfikator:** NIST SP 800-53 R4 SC-19 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1654 — voice over Internet Protocol](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a2ee16e-ab1f-414a-800b-d1608835862b) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1654.json) |
|[Microsoft Managed Control 1655 — voice over Internet Protocol](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F121eab72-390e-4629-a7e2-6d6184f57c6b) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1655.json) |

### <a name="secure-name--address-resolution-service-authoritative-source"></a>Usługa rozpoznawania bezpiecznych nazw/adresów (źródło autorytatywne)

**Identyfikator:** NIST SP 800-53 R4 SC-20 **Ownership**: Shared (Własność NIST SP 800-53 R4 SC-20): Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1656 — Usługa rozpoznawania bezpiecznych nazw/adresów (źródło autorytatywne)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cb067d5-c8b5-4113-a7ee-0a493633924b) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1656.json) |
|[Microsoft Managed Control 1657 — Secure Name/Address Resolution Service (Źródło autorytatywne)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90f01329-a100-43c2-af31-098996135d2b) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1657.json) |

### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Usługa rozpoznawania bezpiecznych nazw/adresów (cykliczny lub buforowania rozpoznawania nazw)

**Identyfikator:** Własność NIST SP 800-53 R4 SC-21: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1658 — Secure Name/Address Resolution Service (Recursive or Caching Resolver)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063b540e-4bdc-4e7a-a569-3a42ddf22098) |Firma Microsoft implementuje tę kontrolę systemu i ochrony komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1658.json) |

### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Architektura i aprowizowanie dla usługi rozpoznawania nazw/adresów

**Identyfikator:** NIST SP 800-53 R4 SC-22 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1659 — architektura i aprowizowanie dla usługi rozpoznawania nazw/adresów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35a4102f-a778-4a2e-98c2-971056288df8) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1659.json) |

### <a name="session-authenticity"></a>Autentyczność sesji

**Identyfikator:** NIST SP 800-53 R4 SC-23 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1660 — autentyczność sesji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63096613-ce83-43e5-96f4-e588e8813554) |Firma Microsoft implementuje tę kontrolę systemu i ochrony komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1660.json) |

### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Autentyczność sesji | Unieważnij identyfikatory sesji podczas wylogowywu

**Identyfikator:** NIST SP 800-53 R4 SC-23 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1661 — autentyczność sesji unieważnia identyfikatory \| sesji podczas wylogowywu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c643c9a-1be7-4016-a5e7-e4bada052920) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1661.json) |

### <a name="fail-in-known-state"></a>Niepowodzenie w znanym stanie

**Identyfikator:** Własność NIST SP 800-53 R4 SC-24: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1662 — niepowodzenie w znanym stanie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F165cb91f-7ea8-4ab7-beaf-8636b98c9d15) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1662.json) |

### <a name="protection-of-information-at-rest"></a>Ochrona informacji w spoczynku

**Identyfikator:** NIST SP 800-53 R4 SC-28 **Ownership**: Shared (Własność NIST SP 800-53 R4 SC-28): Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1663 — ochrona informacji w spoczynku](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60171210-6dde-40af-a144-bf2670518bfa) |Firma Microsoft implementuje tę kontrolę systemu i ochrony komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1663.json) |

### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Ochrona danych w spoczynku | Ochrona kryptograficzna

**Identyfikator:** NIST SP 800-53 R4 SC-28 (1) **Własność:** Klient

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zaawansowane zabezpieczenia danych powinny być włączone na SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Inspekcja poszczególnych SQL Managed Instance bez zaawansowanych zabezpieczeń danych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na serwerach SQL należy włączyć zaawansowane zabezpieczenia danych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Inspekcja serwerów SQL bez Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Maszyny wirtualne bez włączonego szyfrowania dysków będą monitorowane przez Azure Security Center zgodnie z zaleceniami. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |
|[Microsoft Managed Control 1664 — Ochrona informacji w spoczynku \| — ochrona kryptograficzna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2cdf6b8-9505-4619-b579-309ba72037ac) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1664.json) |
|[Transparent Data Encryption w bazach danych SQL powinna być włączona](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Należy włączyć funkcję Transparent Data Encryption, aby chronić dane w spoczynku i spełniać wymagania dotyczące zgodności |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="process-isolation"></a>Izolacja procesów

**Identyfikator:** NIST SP 800-53 R4 SC-39 **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1665 — izolacja procesów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5df3a55c-8456-44d4-941e-175f79332512) |Firma Microsoft implementuje tę kontrolę ochrony systemu i komunikacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1665.json) |

## <a name="system-and-information-integrity"></a>Integralność systemu i informacji

### <a name="system-and-information-integrity-policy-and-procedures"></a>Zasady i procedury dotyczące integralności informacji i systemu

**Identyfikator:** Własność NIST SP 800-53 R4 SI-1: Współdzielona

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1666 — zasady i procedury dotyczące integralności systemu i informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12e30ee3-61e6-4509-8302-a871e8ebb91e) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1666.json) |
|[Microsoft Managed Control 1667 — zasady i procedury integralności systemu i informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd61880dc-6e38-4f2a-a30c-3406a98f8220) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1667.json) |

### <a name="flaw-remediation"></a>Korygowanie błędów

**Identyfikator:** Własność NIST SP 800-53 R4 SI-2: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Na maszynach wirtualnych należy włączyć rozwiązanie do oceny luk w zabezpieczeniach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Przeprowadza inspekcję maszyn wirtualnych w celu wykrycia, czy są uruchomione obsługiwane rozwiązanie do oceny luk w zabezpieczeniach. Podstawowym składnikiem każdego programu związanego z ryzykiem cybernetycznym i bezpieczeństwem jest identyfikacja i analiza luk w zabezpieczeniach. Azure Security Center warstwa cenowa usługi Azure Security Center obejmuje skanowanie luk w zabezpieczeniach dla maszyn wirtualnych bez dodatkowych kosztów. Ponadto Security Center automatycznie wdrożyć to narzędzie. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Microsoft Managed Control 1668 — korygowanie błędów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fb0966e-be1d-42c3-baca-60df5c0bcc61) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1668.json) |
|[Microsoft Managed Control 1669 — korygowanie błędów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48f2f62b-5743-4415-a143-288adc0e078d) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1669.json) |
|[Microsoft Managed Control 1670 — korygowanie błędów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6108469-57ee-4666-af7e-79ba61c7ae0c) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1670.json) |
|[Microsoft Managed Control 1671 — korygowanie błędów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5bbef7-a316-415b-9b38-29753ce8e698) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1671.json) |
|[Należy zainstalować aktualizacje systemu w zestawach skalowania maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Sprawdź, czy brakuje aktualizacji zabezpieczeń systemu i aktualizacji krytycznych, które należy zainstalować, aby upewnić się, że zestawy skalowania maszyn wirtualnych z systemami Windows i Linux są bezpieczne. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |
|[Należy zainstalować aktualizacje systemu na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Brakujące aktualizacje systemu zabezpieczeń na serwerach będą monitorowane przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Serwery, które nie spełniają skonfigurowanego planu bazowego, będą monitorowane przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Należy skorygować luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Przeszukaj luki w zabezpieczeniach systemu operacyjnego w zestawach skalowania maszyn wirtualnych, aby chronić je przed atakami. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[Należy skorygować luki w zabezpieczeniach baz danych SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Monitoruj wyniki skanowania w ramach oceny luk w zabezpieczeniach i zalecenia dotyczące sposobu korygowania luk w zabezpieczeniach bazy danych. |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="flaw-remediation--central-management"></a>Korygowanie błędów | Centralne zarządzanie

**Identyfikator:** NIST SP 800-53 R4 SI-2 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1672 — centralne zarządzanie korygowania \| błędów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb45fe972-904e-45a4-ac20-673ba027a301) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1672.json) |

### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Korygowanie błędów | Zautomatyzowany stan korygowania błędów

**Identyfikator:** NIST SP 800-53 R4 SI-2 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1673 - Flaw Remediation Automated Flaw Remediation Status (Kontrola zarządzana przez firmę Microsoft 1673 — stan zautomatyzowanego korygowania \| wad)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdff0b90d-5a6f-491c-b2f8-b90aa402d844) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1673.json) |

### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Błąd korygowania | Czas na korygowanie błędów/testów porównawczych dla akcji naprawczych

**Identyfikator:** NIST SP 800-53 R4 SI-2 (3) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1674 — czas korygowania błędów w celu skorygowania \| błędów/testów porównawczych dla akcji naprawczych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e9e233-dd0a-4bde-aea5-1371bce0e002) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1674.json) |
|[Microsoft Managed Control 1675 — czas korygowania błędów w celu skorygowania \| błędów/testów porównawczych dla akcji naprawczych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffacb66e0-1c48-478a-bed5-747a312323e1) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1675.json) |

### <a name="malicious-code-protection"></a>Ochrona przed złośliwym kodem

**Identyfikator:** NIST SP 800-53 R4 SI-3 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rozwiązanie ochrony punktu końcowego powinno być zainstalowane w zestawach skalowania maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Inspekcja istnienia i kondycji rozwiązania ochrony punktu końcowego w zestawach skalowania maszyn wirtualnych w celu ochrony przed zagrożeniami i lukami w zabezpieczeniach. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft Managed Control 1676 — Malicious Code Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10fb58b-56a8-489e-9ce3-7ffe24e78e4b) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1676.json) |
|[Microsoft Managed Control 1677 — Malicious Code Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a248e1e-040f-43e5-bff2-afc3a57a3923) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1677.json) |
|[Microsoft Managed Control 1678 — Malicious Code Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd533cb0-b416-4be7-8e86-4d154824dfd7) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1678.json) |
|[Microsoft Managed Control 1679 — Malicious Code Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cf42a28-193e-41c5-98df-7688e7ef0a88) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1679.json) |
|[Monitorowanie braku programu Endpoint Protection w programie Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Serwery bez zainstalowanego agenta programu Endpoint Protection będą monitorowane przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="malicious-code-protection--central-management"></a>Ochrona przed złośliwym kodem | Centralne zarządzanie

**Identyfikator:** NIST SP 800-53 R4 SI-3 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rozwiązanie ochrony punktu końcowego powinno być zainstalowane w zestawach skalowania maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Inspekcja istnienia i kondycji rozwiązania ochrony punktu końcowego w zestawach skalowania maszyn wirtualnych w celu ochrony przed zagrożeniami i lukami w zabezpieczeniach. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft Managed Control 1680 — centralne zarządzanie ochroną przed złośliwym \| kodem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399cd6ee-0e18-41db-9dea-cde3bd712f38) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1680.json) |
|[Monitorowanie brakującej ochrony punktu końcowego w Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Serwery bez zainstalowanego agenta programu Endpoint Protection będą monitorowane przez Azure Security Center zgodnie z zaleceniami |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="malicious-code-protection--automatic-updates"></a>Ochrona przed złośliwym kodem | Aktualizacje automatyczne

**Identyfikator:** NIST SP 800-53 R4 SI-3 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1681 — aktualizacje automatyczne ochrony przed złośliwym \| kodem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12623e7e-4736-4b2e-b776-c1600f35f93a) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1681.json) |

### <a name="malicious-code-protection--nonsignature-based-detection"></a>Ochrona przed złośliwym kodem | Nonsignature-Based wykrywania

**Identyfikator:** NIST SP 800-53 R4 SI-3 (7) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1682 — wykrywanie złośliwego kodu \| Nonsignature-Based kodu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F62b638c5-29d7-404b-8d93-f21e4b1ce198) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1682.json) |

### <a name="information-system-monitoring"></a>Monitorowanie systemu informacji

**Identyfikator:** NIST SP 800-53 R4 SI-4 Ownership : Shared (Własność NIST SP 800-53 R4 SI-4): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Zaawansowane zabezpieczenia danych powinny być włączone na SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Inspekcja poszczególnych SQL Managed Instance danych bez zaawansowanego zabezpieczeń danych. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Na serwerach SQL należy włączyć zaawansowane zabezpieczenia danych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Inspekcja serwerów SQL bez Advanced Data Security |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[Inspekcja obszaru roboczego usługi Log Analytics dla maszyny wirtualnej — niezgodność raportów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |Raportuje maszyny wirtualne jako niezgodne, jeśli nie logują się do obszaru roboczego usługi Log Analytics określonego w przypisaniu zasad/inicjatywy. |Inspekcji |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |
|[Agent usługi Log Analytics powinien być włączony dla obrazów maszyn wirtualnych na liście](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |Raportuje maszyny wirtualne jako niezgodne, jeśli obraz maszyny wirtualnej nie znajduje się na liście zdefiniowanych i agent nie jest zainstalowany. |AuditIfNotExists, Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[Agenta usługi Log Analytics należy włączyć w zestawach skalowania maszyn wirtualnych dla obrazów maszyn wirtualnych na liście](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |Raportuje zestawy skalowania maszyn wirtualnych jako niezgodne, jeśli obraz maszyny wirtualnej nie znajduje się na zdefiniowanej liście i agent nie jest zainstalowany. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[Microsoft Managed Control 1683 — information system monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c79fee4-88dd-44ce-bbd4-4de88948c4f8) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1683.json) |
|[Microsoft Managed Control 1684 — information system monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16bfdb59-db38-47a5-88a9-2e9371a638cf) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1684.json) |
|[Microsoft Managed Control 1685 — Information System Monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36b0ef30-366f-4b1b-8652-a3511df11f53) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1685.json) |
|[Microsoft Managed Control 1686 — Information System Monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe17085c5-0be8-4423-b39b-a52d3d1402e5) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1686.json) |
|[Microsoft Managed Control 1687 — Information System Monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a87fc7f-301e-49f3-ba2a-4d74f424fa97) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1687.json) |
|[Microsoft Managed Control 1688 — Information System Monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063c3f09-e0f0-4587-8fd5-f4276fae675f) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1688.json) |
|[Microsoft Managed Control 1689 — Information System Monitoring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fde901f2f-a01a-4456-97f0-33cda7966172) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1689.json) |

### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Monitorowanie systemu informacji | System-Wide system wykrywania włamań

**Identyfikator:** NIST SP 800-53 R4 SI-4 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1690 — information system monitoring \| System-Wide Intrusion Detection System](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2567a23-d1c3-4783-99f3-d471302a4d6b) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1690.json) |

### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Monitorowanie systemu informacji | Zautomatyzowane narzędzia do analizy Real-Time danych

**Identyfikator:** NIST SP 800-53 R4 SI-4 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1691 — information system monitoring \| Automated Tools for Real-Time Analysis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71475fb4-49bd-450b-a1a5-f63894c24725) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1691.json) |

### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Monitorowanie systemu informacji | Przychodzący i wychodzący ruch komunikacyjny

**Identyfikator:** NIST SP 800-53 R4 SI-4 (4) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1692 — information system monitoring inbound and outbound communications traffic (Kontrola zarządzana przez firmę Microsoft 1692 — monitorowanie przychodzącego i wychodzącego ruchu \| komunikacyjnego systemu informacji)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ecda928-9df4-4dd7-8f44-641a91e470e8) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1692.json) |

### <a name="information-system-monitoring--system-generated-alerts"></a>Monitorowanie systemu informacji | System-Generated alerty

**Identyfikator:** NIST SP 800-53 R4 SI-4 (5) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1693 — alerty monitorowania systemu \| System-Generated informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa450eba6-2efc-4a00-846a-5804a93c6b77) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1693.json) |

### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Monitorowanie systemu informacji | Analizowanie anomalii ruchu komunikacyjnego

**Identyfikator:** NIST SP 800-53 R4 SI-4 (11) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1694 — Information System Monitoring Analyze Communications Traffic Anomalies (Kontrola zarządzana przez firmę Microsoft 1694 — monitorowanie systemu informacji \| — analizowanie anomalii ruchu komunikacyjnego)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F426c4ac9-ff17-49d0-acd7-a13c157081c0) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1694.json) |

### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Monitorowanie systemu informacji | Wykrywanie włamań bezprzewodowych

**Identyfikator:** NIST SP 800-53 R4 SI-4 (14) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1695 — information system monitoring wireless intrusion detection (Kontrola zarządzana przez firmę Microsoft 1695 — monitorowanie systemu informacji \| w celu wykrywania włamań bezprzewodowych)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13fcf812-ec82-4eda-9b89-498de9efd620) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1695.json) |

### <a name="information-system-monitoring--correlate-monitoring-information"></a>Monitorowanie systemu informacji | Korelowanie informacji monitorowania

**Identyfikator:** NIST SP 800-53 R4 SI-4 (16) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1696 — Information System Monitoring \| Correlate Monitoring Information](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69d2a238-20ab-4206-a6dc-f302bf88b1b8) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1696.json) |

### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Monitorowanie systemu informacji | Analizowanie ruchu/potajemna eksfiltracja

**Identyfikator:** NIST SP 800-53 R4 SI-4 (18) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1697 — Information System Monitoring \| Analyze Traffic / Covert Exfiltration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9873db2-18ad-46b3-a11a-1a1f8cbf0335) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1697.json) |

### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Monitorowanie systemu informacji | Osoby narażone na większe ryzyko

**Identyfikator:** NIST SP 800-53 R4 SI-4 (19) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1698 — information system monitoring \| individuals posing greater risk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F31b752c1-05a9-432a-8fce-c39b56550119) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1698.json) |

### <a name="information-system-monitoring--privileged-users"></a>Monitorowanie systemu informacji | Użytkownicy uprzywilejowali

**Identyfikator:** NIST SP 800-53 R4 SI-4 (20) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1699 — information system monitoring \| Privileged Users](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69c7bee8-bc19-4129-a51e-65a7b39d3e7c) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1699.json) |

### <a name="information-system-monitoring--unauthorized-network-services"></a>Monitorowanie systemu informacji | Nieautoryzowane Network Services

**Identyfikator:** NIST SP 800-53 R4 SI-4 (22) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1700 — information system monitoring \| Unauthorized Network Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7831b4ba-c3f4-4cb1-8c11-ef8d59438cd5) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1700.json) |

### <a name="information-system-monitoring--host-based-devices"></a>Monitorowanie systemu informacji | Host-Based urządzeń

**Identyfikator:** NIST SP 800-53 R4 SI-4 (23) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1701 — information system monitoring \| Host-Based Devices](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff25bc08f-27cb-43b6-9a23-014d00700426) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1701.json) |

### <a name="information-system-monitoring--indicators-of-compromise"></a>Monitorowanie systemu informacji | Wskaźniki naruszenia zabezpieczeń

**Identyfikator:** NIST SP 800-53 R4 SI-4 (24) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1702 — information system monitoring \| indicators of compromise](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4dfc0855-92c4-4641-b155-a55ddd962362) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1702.json) |

### <a name="security-alerts-advisories-and-directives"></a>Alerty zabezpieczeń, ostrzeżenia i dyrektywy

**Identyfikator:** NIST SP 800-53 R4 SI-5 **Ownership**: Shared

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1703 — alerty zabezpieczeń, ostrzeżenia i dyrektywy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F804faf7d-b687-40f7-9f74-79e28adf4205) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1703.json) |
|[Microsoft Managed Control 1704 — alerty zabezpieczeń, biuletyny i dyrektywy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d44b6fa-1134-4ea6-ad4e-9edb68f65429) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1704.json) |
|[Microsoft Managed Control 1705 — alerty zabezpieczeń, biuletyny i dyrektywy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff82e3639-fa2b-4e06-a786-932d8379b972) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1705.json) |
|[Microsoft Managed Control 1706 — alerty zabezpieczeń, biuletyny i dyrektywy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff475ee0e-f560-4c9b-876b-04a77460a404) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1706.json) |

### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Alerty zabezpieczeń, ostrzeżenia i dyrektywy | Zautomatyzowane alerty i ostrzeżenia

**Identyfikator:** NIST SP 800-53 R4 SI-5 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1707 — alerty zabezpieczeń, ostrzeżenia i dyrektywy Automatyczne \| alerty i ostrzeżenia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4a2ac8-868a-4702-a345-6c896c3361ce) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1707.json) |

### <a name="security-function-verification"></a>Weryfikacja funkcji zabezpieczeń

**Identyfikator:** NIST SP 800-53 R4 SI-6 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1708 — weryfikacja funkcji zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a1e2c88-13de-4959-8ee7-47e3d74f1f48) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1708.json) |
|[Microsoft Managed Control 1709 — weryfikacja funkcji zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F025992d6-7fee-4137-9bbf-2ffc39c0686c) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1709.json) |
|[Microsoft Managed Control 1710 — weryfikacja funkcji zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf2a93c8-e6dd-4c94-acdd-4a2eedfc478e) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1710.json) |
|[Microsoft Managed Control 1711 — weryfikacja funkcji zabezpieczeń](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb083a535-a66a-41ec-ba7f-f9498bf67cde) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1711.json) |

### <a name="software-firmware-and-information-integrity"></a>Oprogramowanie, oprogramowanie układowe i integralność informacji

**Identyfikator:** Własność NIST SP 800-53 R4 SI-7: **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1712 — Software, Firmware, And Information Integrity (Kontrola zarządzana przez firmę Microsoft 1712 — oprogramowanie, oprogramowanie układowe i integralność informacji)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44e543aa-41db-42aa-98eb-8a5eb1db53f0) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1712.json) |

### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Oprogramowanie, oprogramowanie układowe i integralność informacji | Sprawdzanie integralności

**Identyfikator:** NIST SP 800-53 R4 SI-7 (1) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1713 — Software, Firmware, And Information Integrity Integrity Checks (Kontrola zarządzana przez firmę Microsoft 1713 — sprawdzanie integralności oprogramowania, oprogramowania układowego \| i integralności informacji)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d87c70b-5012-48e9-994b-e70dd4b8def0) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1713.json) |

### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Oprogramowanie, oprogramowanie układowe i integralność informacji | Automatyczne powiadomienia o naruszeniach integralności

**Identyfikator:** NIST SP 800-53 R4 SI-7 (2) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1714 — Software, Firmware, And Information Integrity Automated Notifications of Integrity Violations (Automatyczne powiadomienia o naruszeniach integralności oprogramowania, oprogramowania układowego i \| integralności informacji)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe12494fa-b81e-4080-af71-7dbacc2da0ec) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1714.json) |

### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Oprogramowanie, oprogramowanie układowe i integralność informacji | Automatyczna odpowiedź na naruszenia integralności

**Identyfikator:** NIST SP 800-53 R4 SI-7 (5) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1715 — automatyczne reagowanie na naruszenia integralności oprogramowania, oprogramowania układowego i integralności \| informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd469ae0-71a8-4adc-aafc-de6949ca3339) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1715.json) |

### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Oprogramowanie, oprogramowanie układowe i integralność informacji | Integracja wykrywania i reagowania

**Identyfikator:** NIST SP 800-53 R4 SI-7 (7) **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1716 — integracja wykrywania i reagowania oprogramowania, oprogramowania układowego i integralności \| informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe54c325e-42a0-4dcf-b105-046e0f6f590f) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1716.json) |

### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Oprogramowanie, oprogramowanie układowe i integralność informacji | Kod binarny lub plik wykonywalny komputera

**Identyfikator:** NIST SP 800-53 R4 SI-7 (14) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1717 — Software, Firmware, And Information Integrity Binary Or Machine Executable Code (Kontrola zarządzana przez firmę Microsoft 1717 — oprogramowanie, oprogramowanie układowe i kod wykonywalny integralności \| informacji)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967773fc-d9ab-4a4e-8ff6-f5e9e3f5dbef) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1717.json) |
|[Microsoft Managed Control 1718 — Software, Firmware, And Information Integrity \| Binary Or Machine Executable Code (Kontrola zarządzana przez firmę Microsoft 1718 — oprogramowanie, oprogramowanie układowe i kod wykonywalny integralności informacji)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0dced7ab-9ce5-4137-93aa-14c13e06ab17) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1718.json) |

### <a name="spam-protection"></a>Ochrona przed spamem

**Identyfikator:** NIST SP 800-53 R4 SI-8 Ownership : Shared (Własność NIST SP 800-53 R4 SI-8): **Współdzielona**

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1719 — Spam Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc13da9b4-fe14-4fe2-853a-5997c9d4215a) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1719.json) |
|[Microsoft Managed Control 1720 — Spam Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44b9a7cd-f36a-491a-a48b-6d04ae7c4221) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1720.json) |

### <a name="spam-protection--central-management"></a>Ochrona przed spamem | Centralne zarządzanie

**Identyfikator:** NIST SP 800-53 R4 SI-8 (1) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1721 — Spam Protection \| Central Management](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd207aaef-7c4d-4f8c-9dce-4d62dfa3d29a) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1721.json) |

### <a name="spam-protection--automatic-updates"></a>Ochrona przed spamem | Aktualizacje automatyczne

**Identyfikator:** NIST SP 800-53 R4 SI-8 (2) **Własność:** Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1722 — aktualizacje automatyczne ochrony przed \| spamem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1da06bd-25b6-4127-a301-c313d6873fff) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1722.json) |

### <a name="information-input-validation"></a>Weryfikacja danych wejściowych informacji

**Identyfikator:** NIST SP 800-53 R4 SI-10 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1723 — weryfikacja danych wejściowych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe91927a0-ac1d-44a0-95f8-5185f9dfce9f) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1723.json) |

### <a name="error-handling"></a>Obsługa błędów

**Identyfikator:** NIST SP 800-53 R4 SI-11 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1724 — obsługa błędów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd07594d1-0307-4c08-94db-5d71ff31f0f6) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1724.json) |
|[Microsoft Managed Control 1725 — obsługa błędów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafc234b5-456b-4aa5-b3e2-ce89108124cc) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1725.json) |

### <a name="information-handling-and-retention"></a>Obsługa i przechowywanie informacji

**Identyfikator:** NIST SP 800-53 R4 SI-12 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt(y) |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1726 — obsługa i przechowywanie informacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaff1279-05e0-4463-9a70-8ba5de4c7aa4) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1726.json) |

### <a name="memory-protection"></a>Ochrona pamięci

**Identyfikator:** NIST SP 800-53 R4 SI-16 **Własność**: Udostępnione

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekty |Wersja<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft Managed Control 1727 — Memory Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F697175a7-9715-4e89-b98b-c6f605888fa3) |Firma Microsoft implementuje tę kontrolę integralności systemu i informacji |Inspekcji |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1727.json) |

> [!NOTE]
> Dostępność określonych definicji Azure Policy może się różnić w Azure Government i innych chmurach krajowych.

## <a name="next-steps"></a>Następne kroki

Dodatkowe artykuły dotyczące Azure Policy:

- [Omówienie zgodności z przepisami.](../concepts/regulatory-compliance.md)
- Zobacz strukturę [definicji inicjatywy](../concepts/initiative-definition-structure.md).
- Przejrzyj inne przykłady na [Azure Policy przykładów.](./index.md)
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [korygować niezgodne zasoby.](../how-to/remediate-resources.md)
