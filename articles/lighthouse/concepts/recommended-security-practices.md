---
title: Zalecane najlepsze rozwiązania dotyczące zabezpieczeń
description: W przypadku korzystania z usługi Azure Lighthouse ważne jest uwzględnienie zabezpieczeń i kontroli dostępu.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 097611837e3bba2d1a44ec521862e03cc467d707
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399384"
---
# <a name="recommended-security-practices"></a>Zalecane najlepsze rozwiązania dotyczące zabezpieczeń

W przypadku korzystania z [usługi Azure Lighthouse](../overview.md)ważne jest uwzględnienie zabezpieczeń i kontroli dostępu. Użytkownicy w dzierżawie będą mieć bezpośredni dostęp do subskrypcji klientów i grup zasobów, dlatego należy podjąć kroki w celu utrzymania bezpieczeństwa dzierżawy. Należy również upewnić się, że zezwolisz na dostęp, który jest potrzebny do efektywnego zarządzania zasobami klientów. Ten temat zawiera zalecenia ułatwiające wykonanie tej czynności.

> [!TIP]
> Te zalecenia dotyczą również [przedsiębiorstw zarządzających wieloma dzierżawcami](enterprise.md) za pomocą usługi Azure Lighthouse.

## <a name="require-azure-multi-factor-authentication"></a>Wymaganie usługi Azure Multi-Factor Authentication

[Usługa Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (znana także jako weryfikacja dwuetapowa) zapobiega uzyskaniu dostępu do konta przez osoby atakujące, wymagając wielu kroków uwierzytelniania. Należy wymagać Multi-Factor Authentication dla wszystkich użytkowników w dzierżawie zarządzającej, w tym użytkowników, którzy będą mieli dostęp do delegowanych zasobów klientów.

Zalecamy poproszenie klientów o wdrożenie Multi-Factor Authentication platformy Azure w swoich dzierżawach.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>Przypisywanie uprawnień do grup przy użyciu zasad najniższych uprawnień

Aby ułatwić zarządzanie, należy użyć grup Azure Active Directory (Azure AD) dla każdej roli wymaganej do zarządzania zasobami klientów. Pozwala to na dodanie lub usunięcie poszczególnych użytkowników do grupy w miarę potrzeb zamiast przypisywania uprawnień bezpośrednio do poszczególnych użytkowników.

> [!IMPORTANT]
> Aby można było dodać uprawnienia dla grupy usługi Azure AD, **Typ grupy** musi być ustawiony na **zabezpieczenia**. Ta opcja jest wybierana podczas tworzenia grupy. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowej grupy i dodawanie członków w usłudze Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

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
