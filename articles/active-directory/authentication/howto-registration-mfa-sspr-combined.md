---
title: Wprowadzenie do połączonej rejestracji — Azure Active Directory
description: Włączanie połączonej usługi Azure AD Multi-Factor Authentication i samoobsługowego rejestrowania funkcji resetowania haseł
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7feb69b2ea53794b780a983ed8ab4ba5874ac022
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85260852"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Włącz rejestrację połączonych informacji o zabezpieczeniach w Azure Active Directory

Przed połączoną rejestracją użytkownicy zarejestrowali metody uwierzytelniania dla usługi Azure Multi-Factor Authentication i samoobsługowego resetowania hasła (SSPR). Ktoś został mylić, że podobne metody były używane dla platformy Azure Multi-Factor Authentication i SSPR, ale musiały zarejestrować się w przypadku obu funkcji. Teraz dzięki łącznej rejestracji użytkownicy mogą rejestrować się raz i korzystać z zalet platformy Azure Multi-Factor Authentication i SSPR.

Przed włączeniem nowego środowiska zapoznaj się z artykułem [rejestracja informacji o zabezpieczeniach](concept-registration-mfa-sspr-combined.md) w celu zapoznania się z funkcjami i efektami tej funkcji.

![Ulepszone środowisko rejestracji informacji o zabezpieczeniach](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Włączanie rejestracji łączonej

Wykonaj następujące kroki, aby włączyć rejestrację połączoną:

1. Zaloguj się do Azure Portal jako administrator użytkownika lub Administrator globalny.
2. Przejdź do pozycji **Azure Active Directory**  >  **Ustawienia użytkownika**  >  **Zarządzaj ustawieniami wersji zapoznawczej funkcji użytkownika**.
3. W obszarze **Użytkownicy mogą używać połączenia rejestracji informacji o zabezpieczeniach**, wybierz opcję Włącz dla **wybranej** grupy użytkowników lub dla **wszystkich** użytkowników.

   ![Włącz obsługę połączonych informacji zabezpieczających dla użytkowników](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Po włączeniu łączenia się użytkowników, którzy rejestrują lub potwierdzają swój numer telefonu lub aplikację mobilną za pomocą nowego środowiska, mogą korzystać z nich na platformie Azure Multi-Factor Authentication i SSPR, jeśli te metody są włączone w ramach zasad usługi Azure Multi-Factor Authentication i SSPR. Jeśli wyłączysz to środowisko, użytkownicy, którzy przejdą do poprzedniej strony rejestracji SSPR w programie, `https://aka.ms/ssprsetup` będą musieli przeprowadzić uwierzytelnianie wieloskładnikowe, aby uzyskać dostęp do strony.

Jeśli skonfigurowano listę przypisywania lokacji do strefy w programie Internet Explorer, następujące Lokacje muszą znajdować się w tej samej strefie:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Zasady dostępu warunkowego dla połączonej rejestracji

Zabezpieczanie, kiedy i jak użytkownicy rejestrują się w usłudze Azure Multi-Factor Authentication i samoobsługowego resetowania hasła jest teraz możliwe z działaniami użytkowników w zasadach dostępu warunkowego. Ta funkcja jest dostępna dla organizacji, dla których włączono [funkcję rejestracji połączonej](../authentication/concept-registration-mfa-sspr-combined.md). Ta funkcjonalność może być włączona w organizacjach, w których użytkownicy mogą rejestrować się w usłudze Azure Multi-Factor Authentication i SSPR z centralnej lokalizacji, takiej jak Zaufane lokalizacje sieciowe podczas dołączania do usługi kadr.

> [!NOTE]
> Te zasady mają zastosowanie tylko wtedy, gdy użytkownik uzyskuje dostęp do połączonej strony rejestracji. Te zasady nie wymuszają rejestracji MFA, gdy użytkownik uzyskuje dostęp do innych aplikacji. Zasady rejestracji MFA można utworzyć za pomocą [usługi Azure Identity Protection — Skonfiguruj zasady MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md).

Aby uzyskać więcej informacji na temat tworzenia zaufanych lokalizacji w dostępie warunkowym, zobacz artykuł [jaki jest warunek lokalizacji w Azure Active Directory dostęp warunkowy?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Utwórz zasady, aby wymagać rejestracji z zaufanej lokalizacji

Poniższe zasady mają zastosowanie do wszystkich wybranych użytkowników próbujących zarejestrować się przy użyciu połączonego środowiska rejestracji i blokują dostęp, chyba że nawiązują połączenie z lokalizacji oznaczonej jako zaufane sieci.

1. W **Azure Portal**przejdź do **Azure Active Directory**  >  **zabezpieczenia**  >  **dostęp warunkowy**.
1. Wybierz pozycję **+ nowe zasady**.
1. Wprowadź nazwę tych zasad, *na przykład rejestrację informacji o zabezpieczeniach w zaufanych sieciach*.
1. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**. Wybierz użytkowników i grupy, których mają dotyczyć te zasady, a następnie wybierz pozycję **gotowe**.

   > [!WARNING]
   > Użytkownicy muszą być włączeni do rejestracji połączonej.

1. W obszarze **aplikacje lub akcje w chmurze**wybierz pozycję **akcje użytkownika**. Zaznacz pole **Rejestruj informacje o zabezpieczeniach**, a następnie wybierz pozycję **gotowe**.

    ![Tworzenie zasad dostępu warunkowego w celu kontrolowania rejestracji informacji zabezpieczających](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. W obszarze **warunki**  >  **lokalizacji**skonfiguruj następujące opcje:
   1. Skonfiguruj **tak**.
   1. Uwzględnij **dowolną lokalizację**.
   1. Wyklucz **wszystkie Zaufane lokalizacje**.
1. Wybierz pozycję **gotowe** w oknie *lokalizacje* , a następnie wybierz pozycję **gotowe** w oknie *warunki* .
1. W obszarze **Kontrola dostępu**  >  **Przydziel**, wybierz opcję **Blokuj dostęp**, a następnie **Wybierz**.
1. Ustaw pozycję **Włącz zasady** na wartość **Włączone**.
1. Aby zakończyć zasady, wybierz pozycję **Utwórz**.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz pomocy, zapoznaj się z tematem jak [rozwiązywać problemy z rejestracją połączonych informacji zabezpieczających](howto-registration-mfa-sspr-combined-troubleshoot.md) lub dowiedzieć się [, czym jest warunek lokalizacji w Azure Active Directory dostęp warunkowy?](../conditional-access/location-condition.md)

Aby włączyć funkcje w dzierżawie usługi Azure AD, zapoznaj się z samouczkami, aby włączyć funkcję samoobsługowego [resetowania hasła](tutorial-enable-sspr.md) i [włączyć usługę Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Dowiedz się, jak [zmusić użytkowników do ponownego rejestrowania metod uwierzytelniania](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Możesz również przejrzeć [dostępne metody dla usług Azure Multi-Factor Authentication i SSPR](concept-authentication-methods.md).
