---
title: Włącz łączenie informacji o zabezpieczeniach — Azure Active Directory
description: Dowiedz się, jak uprościć środowisko użytkownika końcowego za pomocą połączonej usługi Azure AD Multi-Factor Authentication i samoobsługowego rejestrowania funkcji resetowania haseł.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29a577b50a561cb5b829e453c523e0bd18a70e1a
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741681"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Włącz rejestrację połączonych informacji o zabezpieczeniach w Azure Active Directory

Przed połączoną rejestracją użytkownicy zarejestrowali metody uwierzytelniania dla usługi Azure AD Multi-Factor Authentication i samoobsługowego resetowania hasła (SSPR). Ktoś został mylić, że podobne metody były używane dla usługi Azure AD Multi-Factor Authentication i SSPR, ale musiały zarejestrować się w przypadku obu funkcji. Teraz dzięki łącznej rejestracji użytkownicy mogą rejestrować się raz i korzystać z zalet usługi Azure AD Multi-Factor Authentication i SSPR.

> [!NOTE]
> Od 15 sierpnia 2020 wszystkie nowe dzierżawy usługi Azure AD będą automatycznie włączane na potrzeby rejestracji złożonej. Od 14 grudnia 2020 wszyscy dzierżawy usługi Azure AD z jednym użytkownikiem zostaną automatycznie włączeni do rejestracji złożonej.

Aby poznać funkcje i efekty przed włączeniem nowego środowiska, zobacz [powiązane koncepcje rejestracji informacji o zabezpieczeniach](concept-registration-mfa-sspr-combined.md).

![Ulepszone środowisko rejestracji informacji o zabezpieczeniach](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Włączanie rejestracji łączonej

Aby włączyć rejestrację łączną, wykonaj następujące kroki:

1. Zaloguj się do Azure Portal jako administrator użytkownika lub Administrator globalny.
2. Przejdź do pozycji **Azure Active Directory**  >  **Ustawienia użytkownika**  >  **Zarządzaj ustawieniami wersji zapoznawczej funkcji użytkownika**.
3. W obszarze **Użytkownicy mogą używać połączenia rejestracji informacji o zabezpieczeniach**, wybierz opcję Włącz dla **wybranej** grupy użytkowników lub dla **wszystkich** użytkowników.

   ![Włącz obsługę połączonych informacji zabezpieczających dla użytkowników](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Po włączeniu łączenia się użytkowników, którzy rejestrują lub potwierdzają swój numer telefonu lub aplikację mobilną za pomocą nowego środowiska, mogą używać ich w usłudze Azure AD Multi-Factor Authentication i SSPR, jeśli te metody są włączone w zasadach usługi Azure AD Multi-Factor Authentication i SSPR.
>
> Jeśli wyłączysz to środowisko, użytkownicy, którzy przejdą do poprzedniej strony rejestracji SSPR w programie, `https://aka.ms/ssprsetup` są zobowiązani do przeprowadzenia uwierzytelniania wieloskładnikowego, zanim będą mogli uzyskać dostęp do strony.

Jeśli skonfigurowano *listę przypisywania lokacji do strefy* w programie Internet Explorer, następujące Lokacje muszą znajdować się w tej samej strefie:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Zasady dostępu warunkowego dla połączonej rejestracji

Aby zabezpieczyć czas i sposób rejestrowania użytkowników w usłudze Azure AD Multi-Factor Authentication i funkcji samoobsługowego resetowania hasła, można użyć akcji użytkownika w zasadach dostępu warunkowego. Ta funkcjonalność może być włączona w organizacjach, które użytkownicy mogą rejestrować w usłudze Azure AD Multi-Factor Authentication i SSPR z centralnej lokalizacji, na przykład w zaufanej lokalizacji sieciowej podczas dołączania do usługi kadr.

> [!NOTE]
> Te zasady mają zastosowanie tylko wtedy, gdy użytkownik uzyskuje dostęp do połączonej strony rejestracji. Te zasady nie wymuszają rejestracji MFA, gdy użytkownik uzyskuje dostęp do innych aplikacji.
>
> Zasady rejestracji MFA można utworzyć za pomocą [usługi Azure Identity Protection — Skonfiguruj zasady MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md).

Aby uzyskać więcej informacji na temat tworzenia zaufanych lokalizacji w dostępie warunkowym, zobacz [co to jest warunek lokalizacji w Azure Active Directory dostęp warunkowy?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Utwórz zasady, aby wymagać rejestracji z zaufanej lokalizacji

Wykonaj następujące kroki, aby utworzyć zasady, które mają zastosowanie do wszystkich wybranych użytkowników próbujących zarejestrować się przy użyciu połączonego środowiska rejestracji, i Zablokuj dostęp, chyba że łączysz się z lokalizacji oznaczonej jako zaufane sieci:

1. W **Azure Portal** przejdź do **Azure Active Directory**  >  **zabezpieczenia**  >  **dostęp warunkowy**.
1. Wybierz pozycję **+ nowe zasady**.
1. Wprowadź nazwę tych zasad, *na przykład rejestrację informacji o zabezpieczeniach w zaufanych sieciach*.
1. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**. Wybierz użytkowników i grupy, których mają dotyczyć te zasady, a następnie wybierz pozycję **gotowe**.

   > [!WARNING]
   > Użytkownicy muszą być włączeni do rejestracji połączonej.

1. W obszarze **aplikacje lub akcje w chmurze** wybierz pozycję **akcje użytkownika**. Zaznacz pole **Rejestruj informacje o zabezpieczeniach**, a następnie wybierz pozycję **gotowe**.

    ![Tworzenie zasad dostępu warunkowego w celu kontrolowania rejestracji informacji zabezpieczających](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. W obszarze **warunki**  >  **lokalizacji** skonfiguruj następujące opcje:
   1. Skonfiguruj **tak**.
   1. Uwzględnij **dowolną lokalizację**.
   1. Wyklucz **wszystkie Zaufane lokalizacje**.
1. Wybierz pozycję **gotowe** w oknie *lokalizacje* , a następnie wybierz pozycję **gotowe** w oknie *warunki* .
1. W obszarze **Kontrola dostępu**  >  **Przydziel**, wybierz opcję **Blokuj dostęp**, a następnie **Wybierz**.
1. Ustaw przełącznik **Włącz zasady** na wartość **Włączone**.
1. Aby zakończyć zasady, wybierz pozycję **Utwórz**.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz pomocy, zobacz [Rozwiązywanie problemów z rejestracją połączonych informacji zabezpieczających](howto-registration-mfa-sspr-combined-troubleshoot.md) lub Dowiedz się, [Czym jest warunek lokalizacji w dostępie warunkowym usługi Azure AD?](../conditional-access/location-condition.md)

Gdy użytkownicy będą mogli korzystać z rejestracji połączonej, można [włączyć funkcję samoobsługowego resetowania hasła](tutorial-enable-sspr.md) i [włączyć Multi-Factor Authentication usługi Azure AD](tutorial-enable-azure-mfa.md).

W razie potrzeby Dowiedz się, jak [zmusić użytkowników do ponownego rejestrowania metod uwierzytelniania](howto-mfa-userdevicesettings.md#manage-user-authentication-options).
