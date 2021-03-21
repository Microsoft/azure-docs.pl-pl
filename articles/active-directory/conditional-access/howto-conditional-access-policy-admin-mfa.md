---
title: Dostęp warunkowy — Wymagaj uwierzytelniania wieloskładnikowego dla administratorów Azure Active Directory
description: Utwórz niestandardowe zasady dostępu warunkowego, aby wymagać od administratorów uwierzytelniania wieloskładnikowego
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35178ecc9bc736bbaca3adc932022b15cc2fc956
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632088"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Dostęp warunkowy: Wymagaj uwierzytelniania wieloskładnikowego dla administratorów

Kontami przypisanymi prawami administracyjnymi są osoby atakujące. Wymaganie uwierzytelniania wieloskładnikowego (MFA) na tych kontach jest łatwym sposobem ograniczenia ryzyka naruszenia bezpieczeństwa kont.

Firma Microsoft zaleca wymaganie uwierzytelniania wieloskładnikowego na następujących rolach:

* Administrator uwierzytelniania
* Administrator rozliczeń
* Administrator dostępu warunkowego
* Administrator programu Exchange
* Administrator globalny
* Administrator pomocy technicznej
* Administrator haseł
* Administrator ról uprzywilejowanych
* Administrator zabezpieczeń
* Administrator programu SharePoint
* Administrator użytkowników

Organizacje mogą zdecydować się na dołączenie lub wykluczenie ról w miarę ich dopasowania.

## <a name="user-exclusions"></a>Wykluczenia użytkowników

Zasady dostępu warunkowego to zaawansowane narzędzia, dlatego zalecamy wykluczenie następujących kont z zasad:

* **Dostęp awaryjny** lub konta z **przerwaniem** do blokowania kont w całej dzierżawie. W mało prawdopodobnym scenariuszu wszyscy administratorzy są Zablokowani z Twojej dzierżawy, w celu zalogowania się do dzierżawy można użyć konta administratora z dostępem awaryjnym.
   * Więcej informacji można znaleźć w artykule [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../roles/security-emergency-access.md).
* **Konta usług** i jednostki **usługi**, takie jak konto synchronizacji Azure AD Connect. Konta usług są kontami nieinteraktywnymi, które nie są powiązane z żadnym konkretnym użytkownikiem. Są one zwykle używane przez usługi zaplecza umożliwiające programistyczny dostęp do aplikacji, ale są również używane do logowania się do systemów w celach administracyjnych. Konta usług, takie jak te, powinny być wykluczone, ponieważ nie można programowo zakończyć usługi MFA. Wywołania wykonywane przez jednostki usługi nie są blokowane przez dostęp warunkowy.
   * Jeśli w organizacji są używane te konta w skryptach lub kodzie, należy rozważyć zastępowanie ich [tożsamościami zarządzanymi](../managed-identities-azure-resources/overview.md). W ramach tymczasowego obejścia można wykluczyć te określone konta z zasad linii bazowej.

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Poniższe kroki pomogą utworzyć zasady dostępu warunkowego, aby wymagać przypisanych ról administracyjnych do uwierzytelniania wieloskładnikowego.

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory**  >    >  **dostępu warunkowego** zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie** wybierz pozycję **role katalogu** i wybierz role wbudowane, takie jak:
      * Administrator uwierzytelniania
      * Administrator rozliczeń
      * Administrator dostępu warunkowego
      * Administrator programu Exchange
      * Administrator globalny
      * Administrator pomocy technicznej
      * Administrator haseł
      * Administrator zabezpieczeń
      * Administrator programu SharePoint
      * Administrator użytkowników
   
      > [!WARNING]
      > Zasady dostępu warunkowego obsługują role wbudowane. Zasady dostępu warunkowego nie są wymuszane dla innych typów ról, w tym ról [administracyjnych należących do zakresu jednostki](../roles/admin-units-assign-roles.md) lub [niestandardowych](../roles/custom-create.md).

   1. W obszarze **Wyklucz** wybierz pozycję **Użytkownicy i grupy** , a następnie wybierz opcję dostęp awaryjny lub konta w ramach swojej organizacji. 
   1. Kliknij **Gotowe**.
1. W obszarze **aplikacje lub akcje w chmurze**  >  wybierz pozycję **wszystkie aplikacje w chmurze**, a następnie wybierz pozycję **gotowe**.
1. W obszarze **Kontrola dostępu**  >  **przyznawanie** wybierz pozycję **Udziel dostępu**, **Wymagaj uwierzytelniania wieloskładnikowego**, a następnie wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć zasady.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko Raport z dostępem warunkowym](howto-conditional-access-insights-reporting.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)
