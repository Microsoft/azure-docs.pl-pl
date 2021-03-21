---
title: Azure Security test — wersja 2 — uprzywilejowany dostęp
description: Azure Security test — wersja 2 uprzywilejowany dostęp
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 29d4acc50ed872c37268a0b21c3e34837249a026
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035419"
---
# <a name="security-control-v2-privileged-access"></a>Kontrola zabezpieczeń v2: uprzywilejowany dostęp

Dostęp uprzywilejowany obejmuje kontrolki umożliwiające ochronę uprzywilejowanego dostępu do dzierżawy i zasobów platformy Azure. Obejmuje to szeroki zakres formantów do ochrony modelu administracyjnego, kont administracyjnych i stacji roboczych dostępu uprzywilejowanego przed rozmyślnymi i nieumyślnie ryzykiem.

Aby wyświetlić odpowiednie wbudowane Azure Policy, zobacz [szczegóły dotyczące zgodności z przepisami opartymi na systemie Azure Security Tests: Privileged Access](../../governance/policy/samples/azure-security-benchmark.md#privileged-access)

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Ochrona i ograniczanie użytkowników z wysokim poziomem uprawnień

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC-2 |

Ogranicz liczbę kont użytkowników z wysokim poziomem uprawnień i Chroń te konta na poziomie podwyższonego poziomu. Najbardziej krytyczne role wbudowane w usłudze Azure AD to Administrator globalny i administrator ról uprzywilejowanych, ponieważ użytkownicy przypisani do tych dwóch ról mogą delegować role administratorów. Dzięki tym przywilejom użytkownicy mogą bezpośrednio lub pośrednio odczytywać i modyfikować każdy zasób w środowisku platformy Azure:

- Administrator globalny: Użytkownicy z tą rolą mają dostęp do wszystkich funkcji administracyjnych w usłudze Azure AD, a także usług korzystających z tożsamości usługi Azure AD.

- Administrator ról uprzywilejowanych: Użytkownicy z tą rolą mogą zarządzać przypisaniami ról w usłudze Azure AD, a także w Azure AD Privileged Identity Management (PIM). Ponadto ta rola umożliwia zarządzanie wszystkimi aspektami jednostek PIM i administracyjnych.

Uwaga: mogą istnieć inne role krytyczne, które należy zastosować w przypadku używania ról niestandardowych z określonymi przypisanymi uprawnieniami. Można również zastosować podobne kontrolki do konta administratora krytycznych zasobów firmy.

Można włączyć uprzywilejowany dostęp just-in-time (JIT) do zasobów platformy Azure i usługi Azure AD przy użyciu usługi Azure AD Privileged Identity Management (PIM). Dostęp JIT polega na przyznawaniu uprawnień tymczasowych do wykonywania zadań uprzywilejowanych tylko wtedy, gdy użytkownicy ich potrzebują. Usługa PIM może również generować alerty zabezpieczeń w przypadku podejrzanych lub niebezpiecznych działań w ramach organizacji usługi Azure AD.

- [Uprawnienia ról administratora w usłudze Azure AD](../../active-directory/roles/permissions-reference.md)

- [Używanie alertów zabezpieczeń usługi Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](../../active-directory/roles/security-planning.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zarządzanie tożsamościami i kluczami](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Operacje zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Ogranicz dostęp administracyjny do systemów o krytycznym znaczeniu dla firmy

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| PA-2 | 13,2, 2,10 | AC-2, SC-3, SC-7 |

Izoluj dostęp do systemów o krytycznym znaczeniu dla firmy, ograniczając konta, które mają przyznane uprzywilejowany dostęp do subskrypcji i grup zarządzania, do których się znajdują. Upewnij się, że ograniczono również dostęp do systemów zarządzania, tożsamości i zabezpieczeń, które mają dostęp administracyjny do najważniejszych zasobów firmy, takich jak kontrolery domena usługi Active Directory (DC), narzędzia zabezpieczeń i narzędzia do zarządzania systemem z agentami zainstalowanymi w systemach krytycznych dla firmy. Osoby atakujące, którzy naruszają te systemy zarządzania i zabezpieczeń, mogą natychmiast weaponize je w celu naruszenia krytycznych zasobów firmy. 

Wszystkie typy kontroli dostępu powinny być wyrównane do strategii segmentacji przedsiębiorstwa w celu zapewnienia spójnej kontroli dostępu. 

Należy przypisać oddzielne konta uprzywilejowane, które różnią się od kont użytkowników standardowych używanych do obsługi poczty e-mail, przeglądania i produktywności.

- [Składniki platformy Azure i Model referencyjny](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Dostęp do grupy zarządzania](../../governance/management-groups/overview.md#management-group-access)

- [Administratorzy subskrypcji platformy Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zarządzanie tożsamościami i kluczami](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Architektura zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Regularne przeglądanie i uzgadnianie dostępu użytkowników

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| PA-3 | 4,1, 16,9, 16,10 | AC-2 |

Regularnie Przeglądaj konta użytkowników i przypisywanie dostępu, aby upewnić się, że konta i ich poziom dostępu są prawidłowe. Za pomocą przeglądów dostępu w usłudze Azure AD można przeglądać członkostwa w grupach, uzyskiwać dostęp do aplikacji dla przedsiębiorstw i przypisań ról. Raportowanie usługi Azure AD może zapewnić dzienniki, aby ułatwić odnajdywanie starych kont. Możesz również użyć Azure AD Privileged Identity Management, aby utworzyć przepływ pracy raportu przeglądu dostępu, który ułatwia proces recenzowania.
Ponadto usługa Azure Privileged Identity Management może być skonfigurowana do zgłaszania alertów w przypadku tworzenia zbyt dużej liczby kont administratorów oraz do identyfikowania kont administratorów, które są przestarzałe lub nieprawidłowo skonfigurowane. 

Uwaga: Niektóre usługi platformy Azure obsługują lokalnych użytkowników i role, które nie są zarządzane za pomocą usługi Azure AD. Należy zarządzać tymi użytkownikami osobno.

- [Tworzenie przeglądu dostępu do ról zasobów platformy Azure w Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zarządzanie tożsamościami i kluczami](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurowanie dostępu awaryjnego w usłudze Azure AD

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| PA-4 | 16 | AC-2, CP-2 |

Aby zapobiec przypadkowemu zablokowaniu organizacji usługi Azure AD, skonfiguruj konto dostępu awaryjnego w celu uzyskania dostępu, gdy nie można używać normalnych kont administracyjnych. Konta dostępu awaryjnego są zwykle wysoce uprzywilejowane i nie powinny być przypisane do konkretnych osób. Konta dostępu awaryjnego są ograniczone do sytuacji awaryjnych lub nagłych, w których normalne konta administracyjne nie mogą być używane.
Należy upewnić się, że poświadczenia (takie jak hasło, certyfikat lub karta inteligentna) dla kont dostępu awaryjnego są bezpieczne i znane tylko osobom, które są upoważnione do ich używania tylko w sytuacji awaryjnej.

- [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../../active-directory/roles/security-emergency-access.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zarządzanie tożsamościami i kluczami](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Operacje zabezpieczeń (SecOP)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatyzowanie zarządzania uprawnieniami

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| PA-5 | 16 | AC-2, AC-5, PM-10 |

Użyj funkcji zarządzania prawami usługi Azure AD, aby zautomatyzować przepływy pracy żądania dostępu, w tym przydziały dostępu, przeglądy i wygaśnięcie. Obsługiwane jest również podwójne lub wieloetapowe zatwierdzenie.
- [Co to są przeglądy dostępu do usługi Azure AD](../../active-directory/governance/access-reviews-overview.md) 

- [Co to jest zarządzanie prawami w usłudze Azure AD](../../active-directory/governance/entitlement-management-overview.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zarządzanie tożsamościami i kluczami](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Korzystanie ze stacji roboczych z dostępem uprzywilejowanym

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| PA-6 | 4,6, 11,6, 12,12 | AC-2, SC-3, SC-7 |

Zabezpieczone, izolowane stacje robocze mają kluczowe znaczenie dla bezpieczeństwa ról poufnych, takich jak administrator, deweloper i operator usługi krytycznej. Używaj wysoce bezpiecznych stacji roboczych użytkowników i/lub Azure bastionu na potrzeby zadań administracyjnych. Użyj usługi Azure Active Directory, zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender i/lub usługi Microsoft Intune do wdrożenia bezpiecznej i zarządzanej stacji roboczej użytkownika na potrzeby zadań administracyjnych. Zabezpieczone stacje robocze mogą być zarządzane centralnie w celu wymuszenia bezpiecznej konfiguracji, w tym silnego uwierzytelniania, oprogramowania i sprzętu, a także ograniczonego dostępu logicznego i sieci. 

- [Informacje na temat stacji roboczych uprzywilejowanego dostępu](/security/compass/privileged-access-deployment)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Operacje zabezpieczeń (SecOP)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Zarządzanie tożsamościami i kluczami](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>DU-7: Przestrzeganie podejścia wystarczającego zakresu administracji (zasada stosowania najniższych uprawnień)

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| PA-7 | 14,6 | AC-2, AC-3, SC-3 |

Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure za pomocą przypisań ról. Te role można przypisać do użytkowników, podmiotów usługi grupy i tożsamości zarządzanych. Istnieją wstępnie zdefiniowane wbudowane role dla niektórych zasobów i te role można spisować lub odpytać za pomocą narzędzi, takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell i Azure Portal. Uprawnienia, które można przypisać do zasobów za pomocą usługi Azure RBAC, powinny być zawsze ograniczone do tego, co jest wymagane przez role. Ograniczone uprawnienia uzupełniają podejście just in Time (JIT) Azure AD Privileged Identity Management (PIM), a te uprawnienia powinny być okresowo weryfikowane.

Przy użyciu wbudowanych ról można przydzielić uprawnienia i w razie potrzeby tworzyć tylko role niestandardowe.

- [Co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md)

- [Jak skonfigurować usługę Azure RBAC](../../role-based-access-control/role-assignments-portal.md)

- [Jak korzystać z przeglądów tożsamości i dostępu w usłudze Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Zarządzanie tożsamościami i kluczami](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: wybór procesu zatwierdzania dla pomocy technicznej firmy Microsoft 

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| PA-8 | 16 | AC-2, AC-3, AC-4 |

W przypadku scenariuszy, w których firma Microsoft potrzebuje dostępu do danych klienta, Skrytka klienta oferuje możliwość jawnego przeglądania i zatwierdzania lub odrzucania każdego żądania dostępu do danych klienta.

- [Opis Skrytka klienta](../fundamentals/customer-lockbox-overview.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia aplikacji i metodyka DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Zarządzanie tożsamościami i kluczami](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)