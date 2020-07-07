---
title: Zalecane najlepsze rozwiązania dotyczące zabezpieczeń
description: W przypadku korzystania z funkcji zarządzania zasobami delegowanymi przez platformę Azure ważne jest, aby uwzględnić zabezpieczenia i kontrolę dostępu.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: d9b806aaf988fedfde6ce468f3eff948aa8ce344
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80246912"
---
# <a name="recommended-security-practices"></a>Zalecane najlepsze rozwiązania dotyczące zabezpieczeń

W przypadku korzystania z funkcji [zarządzania zasobami delegowanymi przez platformę Azure](azure-delegated-resource-management.md)ważne jest, aby uwzględnić zabezpieczenia i kontrolę dostępu. Użytkownicy w dzierżawie będą mieć bezpośredni dostęp do subskrypcji klientów i grup zasobów, dlatego należy podjąć kroki w celu utrzymania bezpieczeństwa dzierżawy. Należy również upewnić się, że zezwolisz na dostęp, który jest potrzebny do efektywnego zarządzania zasobami klientów. Ten temat zawiera zalecenia ułatwiające wykonanie tej czynności.

## <a name="require-azure-multi-factor-authentication"></a>Wymaganie usługi Azure Multi-Factor Authentication

[Usługa Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (znana także jako weryfikacja dwuetapowa) zapobiega uzyskaniu dostępu do konta przez osoby atakujące, wymagając wielu kroków uwierzytelniania. Należy wymagać Multi-Factor Authentication dla wszystkich użytkowników w dzierżawie dostawcy usług, w tym wszystkich użytkowników, którzy będą mieli dostęp do zasobów klienta.

Zalecamy poproszenie klientów o wdrożenie Multi-Factor Authentication platformy Azure w swoich dzierżawach.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Przypisywanie uprawnień do grup przy użyciu zasad najniższych uprawnień

Aby ułatwić zarządzanie, zalecamy korzystanie z grup użytkowników usługi Azure AD dla każdej roli wymaganej do zarządzania zasobami klientów. Pozwala to na dodanie lub usunięcie poszczególnych użytkowników do grupy w zależności od potrzeb, zamiast przypisywania uprawnień bezpośrednio do tego użytkownika.

> [!IMPORTANT]
> Aby można było dodać uprawnienia dla grupy usługi Azure AD, **typem grupy** musi być **zabezpieczenia** , a nie **Office 365**. Ta opcja jest wybierana podczas tworzenia grupy. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowej grupy i dodawanie członków w usłudze Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Podczas tworzenia struktury uprawnień należy przestrzegać zasad najniższych uprawnień, aby użytkownicy mieli tylko uprawnienia potrzebne do ukończenia swoich zadań, co zmniejsza prawdopodobieństwo wystąpienia nieumyślnego błędu.

Na przykład możesz chcieć użyć struktury podobnej do następujących:

|Nazwa grupy  |Typ  |principalId  |Definicja roli  |Identyfikator definicji roli  |
|---------|---------|---------|---------|---------|
|Architektury     |Grupa użytkowników         |\<principalId\>         |Współautor         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|Ocena     |Grupa użytkowników         |\<principalId\>         |Czytelnik         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|Specjaliści dla maszyn wirtualnych     |Grupa użytkowników         |\<principalId\>         |Współautor maszyny wirtualnej         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |Główna nazwa usługi (SPN)         |\<principalId\>         |Współautor         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

Po utworzeniu tych grup można przypisywać użytkowników zgodnie z wymaganiami. Dodawać tylko użytkowników, którzy naprawdę muszą mieć dostęp. Pamiętaj o regularnym przeglądaniu członkostwa w grupach i usunięciu wszystkich użytkowników, którzy nie są już zarejestrowani.

Należy pamiętać, że po dołączeniu [klientów za pomocą publicznej oferty usług zarządzanej](../how-to/publish-managed-services-offers.md)każda grupa (lub użytkownik lub nazwa główna usługi) będzie mieć takie same uprawnienia dla każdego klienta, który kupuje plan. Aby przypisać różne grupy do pracy z poszczególnymi klientami, należy opublikować oddzielny plan prywatny, który jest wyłączny dla każdego klienta, lub dołączyć klientów osobno przy użyciu szablonów Azure Resource Manager. Można na przykład opublikować plan publiczny o bardzo ograniczonym dostępie, a następnie skontaktować się z klientem bezpośrednio w celu dołączenia swoich zasobów w celu uzyskania dodatkowego dostępu przy użyciu dostosowanego szablonu zasobów platformy Azure w razie potrzeby.


## <a name="next-steps"></a>Następne kroki

- [Wdróż Multi-Factor Authentication platformy Azure](../../active-directory/authentication/howto-mfa-getstarted.md).
- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](cross-tenant-management-experience.md).
