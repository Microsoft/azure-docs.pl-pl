---
title: Azure Security test — wersja 2 — uprzywilejowany dostęp
description: Azure Security test — wersja 2 uprzywilejowany dostęp
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059345"
---
# <a name="security-control-privileged-access"></a>Kontrola zabezpieczeń: uprzywilejowany dostęp

Dostęp uprzywilejowany obejmuje kontrolki umożliwiające ochronę uprzywilejowanego dostępu do dzierżawy i zasobów platformy Azure. Obejmuje to szeroki zakres formantów do ochrony modelu administracyjnego, kont administracyjnych i stacji roboczych dostępu uprzywilejowanego przed rozmyślnymi i nieumyślnie ryzykiem.

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1: Ochrona i ograniczenie administratorów globalnych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC-2 |

Użytkownicy przypisani do roli administratora globalnego mogą odczytywać i modyfikować każde ustawienie administracyjne w organizacji usługi Azure AD. Ogranicz liczbę kont administratora globalnego platformy Azure do nie więcej niż dwóch dla każdej subskrypcji. Najbardziej krytyczne role wbudowane w usłudze Azure AD to Administrator globalny i administrator ról uprzywilejowanych, ponieważ użytkownicy przypisani do tych dwóch ról mogą delegować role administratorów:
- Administrator globalny/administrator firmy: Użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w usłudze Azure AD, a także usług korzystających z tożsamości usługi Azure AD.

- Administrator ról uprzywilejowanych: Użytkownicy z tą rolą mogą zarządzać przypisaniami ról w usłudze Azure AD, a także w Azure AD Privileged Identity Management (PIM). Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami jednostek PIM i administracyjnych.

Uwaga: mogą istnieć inne role krytyczne, które należy zastosować w przypadku używania ról niestandardowych z określonymi przypisanymi uprawnieniami. Można również zastosować podobne kontrolki do konta administratora krytycznych zasobów firmy.  

Można włączyć uprzywilejowany dostęp just-in-Time (JIT) do zasobów platformy Azure i usługi Azure AD przy użyciu Azure AD Privileged Identity Management (PIM). Kompilator JIT przyznaje uprawnienia tymczasowe do wykonywania zadań uprzywilejowanych tylko wtedy, gdy użytkownicy ich potrzebują. Usługa PIM może również generować alerty zabezpieczeń w przypadku podejrzanych lub niebezpiecznych działań w organizacji usługi Azure AD.

Uwaga: Niektóre usługi platformy Azure, takie jak Azure SQL, obsługują uwierzytelnianie użytkowników lokalnych oprócz uwierzytelniania usługi Azure AD. Ten typ uwierzytelniania użytkownika lokalnego nie jest zarządzany przez usługę Azure AD. Trzeba będzie osobno zarządzać tymi użytkownikami.

- [Uprawnienia roli administratora w usłudze Azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md)

- [Korzystanie z alertów zabezpieczeń usługi Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Używanie Azure Security Center do monitorowania tożsamości i dostępu](../../security-center/security-center-identity-access.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Tożsamość i klucze](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2: przegląd i regularne uzgadnianie dostępu użytkowników

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| PA-2 | 4,1, 16,9, 16,10 | AC-2 |

Regularnie Przeglądaj konta użytkowników i uprawnienia dostępu, aby upewnić się, że konta użytkowników i ich dostęp są prawidłowe. 

Za pomocą przeglądów tożsamości i dostępu w usłudze Azure AD można zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Raportowanie usługi Azure AD może zapewnić dzienniki, aby ułatwić odnajdywanie starych kont. Możesz również użyć Azure AD Privileged Identity Management, aby utworzyć przepływ pracy raportu przeglądu dostępu, aby ułatwić proces recenzowania.

W przypadku użytkowników administracyjnych na poziomie usługi i obciążenia platformy Azure należy przeprowadzić bardziej częste przeglądy użytkowników i dostępu. Za pomocą alertów zabezpieczeń usługi Azure Privileged Identity Management można również wykrywać, kiedy konta administratorów są tworzone, oraz znaleźć konta administratorów, które są stare lub nieprawidłowo skonfigurowane. 

Uwaga: Niektóre usługi platformy Azure, takie jak Azure SQL, obsługują lokalnych użytkowników, którzy nie są zarządzani za pomocą usługi Azure AD. Trzeba będzie osobno zarządzać tymi użytkownikami.

- [Jak uzyskać rolę katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak uzyskać członków roli katalogu w usłudze Azure AD przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Używanie Azure Security Center do monitorowania tożsamości i dostępu](../../security-center/security-center-identity-access.md)

- [Korzystanie z alertów zabezpieczeń usługi Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Wyrównaj obowiązki administracyjne między zespołami](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [Informacje o raportowaniu usługi Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../../active-directory/governance/access-reviews-overview.md)

- [Privileged Identity Management — przeglądanie dostępu do ról usługi Azure AD](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Azure Security Center — monitorowanie tożsamości i dostępu](../../security-center/security-center-identity-access.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Tożsamość i klucze](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3: Konfigurowanie konta dostępu awaryjnego w usłudze Azure AD

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| PA-3 | 12,3 | AC-2 |

Aby zapobiec przypadkowemu zablokowaniu organizacji usługi Azure AD, skonfiguruj konto dostępu awaryjnego w celu uzyskania dostępu, gdy nie można używać normalnych kont administracyjnych. Konta dostępu awaryjnego są zwykle wysoce uprzywilejowane i nie powinny być przypisane do konkretnych osób. Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnych lub "szkła Break", w których normalne konta administracyjne nie mogą być używane.

Należy upewnić się, że poświadczenia (takie jak hasło, certyfikat lub karta inteligentna) dla kont dostępu awaryjnego są bezpieczne i znane tylko osobom, które są upoważnione do ich używania tylko w sytuacji awaryjnej.

- [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Tożsamość i klucze](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Centrum operacji zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4: Automatyzacja przepływu pracy żądania tożsamości i dostępu do platformy Azure

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| PA-4 | Nie dotyczy | AC-2, AC-5, PM-10 |

Korzystaj z funkcji zarządzania prawami do usługi Azure AD, aby zautomatyzować przepływy pracy żądań dostępu do platformy Azure, w tym przydziały dostępu, Recenzje i wygaśnięcia. Obsługiwane jest również podwójne lub wieloetapowe zatwierdzenie.  

- [Co to jest zarządzanie prawami w usłudze Azure AD](../../active-directory/governance/entitlement-management-overview.md)

- [Jak skonfigurować proces żądania dostępu i zatwierdzania](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Tożsamość i klucze](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA-5: korzystanie z wysoce zabezpieczonych maszyn na potrzeby zadań administracyjnych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| PA-5 | 4,6, 11,6, 12,12 | AC-2, SC-7 |

Zabezpieczone, izolowane stacje robocze mają kluczowe znaczenie dla bezpieczeństwa poufnych ról, takich jak Administratorzy, deweloperzy i krytyczne operatory usług. Używaj wysoce bezpiecznych stacji roboczych użytkowników i/lub Azure bastionu na potrzeby zadań administracyjnych:
- Użyj Azure Active Directory, usługi Microsoft Defender Advanced Threat Protection (ATP) i/lub Microsoft Intune do wdrożenia bezpiecznej i zarządzanej stacji roboczej użytkownika na potrzeby zadań administracyjnych. Zabezpieczone stacje robocze mogą być zarządzane centralnie, aby wymusić bezpieczną konfigurację, w tym silne uwierzytelnianie, oprogramowanie i sprzętowe linie bazowe, ograniczony dostęp logiczny i sieciowy. 

- Użyj funkcji Azure bastionu, aby uzyskać bezpieczną ścieżkę dostępu do maszyn wirtualnych za pośrednictwem protokołu RDP lub SSH. Azure bastionu to w pełni zarządzana usługa PaaS, która może być obsługiwana dla każdej sieci wirtualnej bez tworzenia nowej sieci wirtualnej. 

- [Informacje na temat bezpiecznych stacji roboczych zarządzanych przez platformę Azure](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Wdrażanie bezpiecznej, zarządzanej na platformie Azure stacji roboczej](../../active-directory/devices/howto-azure-managed-workstation.md)

- [Korzystanie z hosta usługi Azure bastionu](../../bastion/bastion-create-host-portal.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Centrum operacji zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Tożsamość i klucze](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6: przypisywanie uprawnień do zasobów przy użyciu usługi Azure RBAC

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| PA-6 | 14,6 | AC-2, AC-3 |

Kontrola dostępu oparta na rolach (RBAC) na platformie Azure umożliwia zarządzanie uprawnieniami dostępu do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, grup jednostek usługi i zarządzanych tożsamości. Istnieją wstępnie zdefiniowane wbudowane role dla niektórych zasobów i te role można spisować lub odpytać za pomocą narzędzi takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell lub Azure Portal. 

- [Co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md)

- [Jak skonfigurować RBAC na platformie Azure](../../role-based-access-control/role-assignments-portal.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Tożsamość i klucze](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7: wybór procesu zatwierdzania dla pomocy technicznej firmy Microsoft

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| PA-7 | 16 | AC-2, AC-3, AC-4 |

W przypadku scenariuszy, w których firma Microsoft potrzebuje dostępu do danych klienta, Skrytka klienta oferuje możliwość jawnego przeglądania i zatwierdzania lub odrzucania poszczególnych żądań dostępu do danych klienta.

- [Opis Skrytka klienta](../fundamentals/customer-lockbox-overview.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Tożsamość i klucze](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

