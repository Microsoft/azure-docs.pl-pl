---
title: Wprowadzenie do rejestracji połączonej — usługa Azure Active Directory
description: Włączanie połączonego uwierzytelniania wieloskładnikowego usługi Azure AD i samoobsługowej rejestracji resetowania haseł
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
ms.openlocfilehash: 466b063253ee49ab58c2685f359b4bb8a4079532
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639698"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Włączanie połączonej rejestracji informacji o zabezpieczeniach w usłudze Azure Active Directory

Przed rejestracją połączoną użytkownicy zarejestrowali metody uwierzytelniania azure wieloskładnikowego i samoobsługowego resetowania haseł (SSPR) oddzielnie. Ludzie byli zdezorientowani, że podobne metody były używane do uwierzytelniania wieloskładnikowego i samooczyżania SSPR, ale musieli zarejestrować się dla obu funkcji. Teraz, dzięki połączonej rejestracji, użytkownicy mogą zarejestrować się raz i uzyskać korzyści zarówno z uwierzytelniania wieloskładnikowego, jak i samookreślenia.

Przed włączeniem nowego środowiska zapoznaj się z artykułem [Połączono rejestrację informacji o zabezpieczeniach,](concept-registration-mfa-sspr-combined.md) aby upewnić się, że rozumiesz funkcjonalność i efekty tej funkcji.

![Połączone doświadczenie rejestracji informacji o zabezpieczeniach](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Włączanie rejestracji łączonej

Wykonaj następujące kroki, aby włączyć rejestrację połączoną:

1. Zaloguj się do witryny Azure portal jako administrator użytkownika lub administrator globalny.
2. Przejdź do ustawień**użytkownika** >  **usługi Azure Active Directory** > Zarządzanie**ustawieniami podglądu funkcji użytkownika**.
3. W obszarze **Użytkownicy można używać funkcji w wersji zapoznawczej do rejestrowania informacji zabezpieczających i zarządzania**nimi wybierz opcję włączenia dla **wybranej** grupy użytkowników lub dla **wszystkich** użytkowników.

   ![Włącz połączone środowisko podglądu informacji zabezpieczających dla wszystkich użytkowników](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> Po włączeniu rejestracji połączonej użytkownicy, którzy zarejestrują lub potwierdzą swój numer telefonu lub aplikację mobilną za pośrednictwem nowego środowiska, mogą używać ich do uwierzytelniania wieloskładnikowego i samookreślenia, jeśli te metody są włączone w zasadach uwierzytelniania wieloskładnikowego i samookapła. Jeśli następnie wyłączysz to środowisko, użytkownicy, którzy `https://aka.ms/ssprsetup` przejdą do poprzedniej strony rejestracji samookapła, będą musieli wykonać uwierzytelnianie wieloskładnikowe, zanim będą mogli uzyskać dostęp do strony.

Jeśli w programie Internet Explorer skonfigurowano listę przypisania witryny do stref, następujące witryny muszą znajdować się w tej samej strefie:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Zasady dostępu warunkowego dla połączonej rejestracji

Zabezpieczanie, kiedy i jak użytkownicy rejestrują się w celu autoryzacji wieloskładnikowej platformy Azure i samoobsługowego resetowania hasła jest teraz możliwe dzięki działaniom użytkownika w zasadach dostępu warunkowego. Ta funkcja jest dostępna dla organizacji, które włączyły [funkcję połączonej rejestracji.](../authentication/concept-registration-mfa-sspr-combined.md) Ta funkcja może być włączona w organizacjach, w których użytkownicy mają rejestrować się w usłudze Azure Multi-Factor Authentication i SSPR z centralnej lokalizacji, takiej jak zaufana lokalizacja sieciowa podczas dołączania do działu kadr.

Aby uzyskać więcej informacji na temat tworzenia zaufanych lokalizacji w programie Dostęp warunkowy, zobacz artykuł [Jaki jest warunek lokalizacji w usłudze Azure Active Directory Dostęp warunkowy?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Tworzenie zasad wymagających rejestracji z zaufanej lokalizacji

Poniższe zasady mają zastosowanie do wszystkich wybranych użytkowników, którzy próbują zarejestrować się przy użyciu połączonego środowiska rejestracji i blokuje dostęp, chyba że łączą się z lokalizacji oznaczonej jako zaufana sieć.

1. W **witrynie Azure portal**przejdź do usługi **Azure Active Directory** > **Security** > **Conditional Access**
1. Wybierz **+ Nowe zasady**
1. Wprowadź nazwę tej zasady, na przykład *Rejestracja połączonych informacji zabezpieczających w zaufanych sieciach*.
1. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**. Wybierz użytkowników i grupy, do których ma być stosowana ta zasada, a następnie wybierz pozycję **Gotowe**.

   > [!WARNING]
   > Użytkownicy muszą być włączeni do rejestracji połączonej.

1. W obszarze **Aplikacje lub akcje w chmurze**wybierz pozycję **Akcje użytkownika**. Zaznacz **pozycję Zarejestruj informacje zabezpieczające**, a następnie wybierz pozycję **Gotowe**.

    ![Tworzenie zasad dostępu warunkowego w celu kontrolowania rejestracji informacji zabezpieczających](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. W **obszarze Lokalizacje** > **warunków**należy skonfigurować następujące opcje:
   1. Konfigurowanie **tak**
   1. Uwzględnij **dowolną lokalizację**
   1. Wyklucz **wszystkie zaufane lokalizacje**
1. Wybierz **pozycję Gotowe** w oknie *Lokalizacje,* a następnie wybierz pozycję **Gotowe** w oknie *Warunki.*
1. W obszarze **Udziel kontrolki** > dostępu**wybierz**pozycję **Blokuj dostęp**, a następnie wybierz pozycję **Wybierz**
1. Ustaw **włącz zasadę** **na Włączone**
1. Aby sfinalizować zasadę, wybierz pozycję **Utwórz**

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz pomocy, zobacz, jak [rozwiązać problem połączonej rejestracji informacji o zabezpieczeniach](howto-registration-mfa-sspr-combined-troubleshoot.md) lub dowiedz [się, jaki jest warunek lokalizacji w usłudze Azure Active Directory Dostęp warunkowy?](../conditional-access/location-condition.md)

Aby włączyć funkcje dzierżawy usługi Azure AD, zobacz samouczki, aby [włączyć samoobsługowe resetowanie haseł](tutorial-enable-sspr.md) i [włączyć uwierzytelnianie wieloskładnikowe platformy Azure](tutorial-enable-azure-mfa.md).

Dowiedz się, jak [włączyć rejestrację połączoną w dzierżawie](howto-registration-mfa-sspr-combined.md) lub [zmusić użytkowników do ponownego zarejestrowania metod uwierzytelniania.](howto-mfa-userdevicesettings.md#manage-user-authentication-options)

Można również przejrzeć [dostępne metody uwierzytelniania wieloskładnikowego platformy Azure i samookadowania.](concept-authentication-methods.md)
