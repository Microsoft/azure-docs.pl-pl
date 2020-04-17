---
title: Wprowadzenie do rejestracji połączonej — usługa Azure Active Directory
description: Włączanie połączonego uwierzytelniania wieloskładnikowego usługi Azure AD i samoobsługowej rejestracji resetowania haseł
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d9544b1f4dd5ecbf66493f26c373c5502dce68a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451089"
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

Zabezpieczanie, kiedy i jak użytkownicy rejestrują się w celu autoryzacji wieloskładnikowej platformy Azure i samoobsługowego resetowania hasła jest teraz możliwe dzięki działaniom użytkownika w zasadach dostępu warunkowego. Ta funkcja jest dostępna dla organizacji, które włączyły [funkcję połączonej rejestracji.](../authentication/concept-registration-mfa-sspr-combined.md) Ta funkcja może być włączona w organizacjach, w których użytkownicy mają rejestrować się w usłudze Azure Multi-Factor Authentication i SSPR z centralnej lokalizacji, takiej jak zaufana lokalizacja sieciowa podczas dołączania do działu kadr. Aby uzyskać więcej informacji na temat tworzenia zaufanych lokalizacji w programie Dostęp warunkowy, zobacz artykuł [Jaki jest warunek lokalizacji w usłudze Azure Active Directory Dostęp warunkowy?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Tworzenie zasad wymagających rejestracji z zaufanej lokalizacji

Poniższe zasady mają zastosowanie do wszystkich wybranych użytkowników, którzy próbują zarejestrować się przy użyciu połączonego środowiska rejestracji i blokują dostęp, chyba że łączą się z lokalizacji oznaczonej jako zaufana sieć.

![Tworzenie zasad urzędu certyfikacji w celu kontrolowania rejestracji informacji zabezpieczających](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. W **witrynie Azure portal**przejdź do usługi **Azure Active Directory** > **Security** > **Conditional Access**
1. Wybierz pozycję **Nowe zasady**
1. W nazwie wprowadź nazwę dla tej zasady. Na przykład **rejestracja połączonych informacji zabezpieczających w zaufanych sieciach**
1. W obszarze **Przydziały**kliknij pozycję **Użytkownicy i grupy**i wybierz użytkowników i grupy, do których ma się odnosić ta zasada

   > [!WARNING]
   > Użytkownicy muszą być włączeni do [rejestracji łączonej.](../authentication/howto-registration-mfa-sspr-combined.md)

1. W obszarze **Aplikacje lub akcje w chmurze**wybierz pozycję **Akcje użytkownika**, zaznacz pozycję **Zarejestruj informacje o zabezpieczeniach (wersja zapoznawcza)**
1. W **warunkach** > **lokalizacje**
   1. Konfigurowanie **tak**
   1. Uwzględnij **dowolną lokalizację**
   1. Wyklucz **wszystkie zaufane lokalizacje**
   1. Kliknij **gotowe** na lemieszu Lokalizacje
   1. Kliknij **gotowe** na ostrze Warunki
1. W obszarze **Kontrola** > **Grant** dostępu
   1. Kliknij **pozycję Blokuj dostęp**
   1. Następnie kliknij przycisk **Zaznacz**
1. Ustaw **włącz zasadę** **na Włączone**
1. Następnie kliknij przycisk **Utwórz**

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz pomocy, zobacz, jak [rozwiązać problem połączonej rejestracji informacji o zabezpieczeniach](howto-registration-mfa-sspr-combined-troubleshoot.md) lub dowiedz [się, jaki jest warunek lokalizacji w usłudze Azure Active Directory Dostęp warunkowy?](../conditional-access/location-condition.md)

Aby włączyć funkcje dzierżawy usługi Azure AD, zobacz samouczki, aby [włączyć samoobsługowe resetowanie haseł](tutorial-enable-sspr.md) i [włączyć uwierzytelnianie wieloskładnikowe platformy Azure](tutorial-enable-azure-mfa.md).

Dowiedz się, jak [włączyć rejestrację połączoną w dzierżawie](howto-registration-mfa-sspr-combined.md) lub [zmusić użytkowników do ponownego zarejestrowania metod uwierzytelniania.](howto-mfa-userdevicesettings.md#manage-user-authentication-options)

Można również przejrzeć [dostępne metody uwierzytelniania wieloskładnikowego platformy Azure i samookadowania.](concept-authentication-methods.md)
