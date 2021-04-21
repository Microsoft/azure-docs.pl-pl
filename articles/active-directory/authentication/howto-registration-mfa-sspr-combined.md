---
title: Włączanie rejestracji połączonych informacji zabezpieczających — Azure Active Directory
description: Dowiedz się, jak uprościć środowisko użytkownika końcowego dzięki połączeniu usługi Azure AD Multi-Factor Authentication i rejestracji samoobsługowego resetowania hasła.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6eba5ac4ed61847596e12f56544e6d07dca8075
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829579"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Włączanie rejestracji połączonych informacji zabezpieczających w Azure Active Directory

Przed połączoną rejestracją użytkownicy zarejestrował oddzielnie metody uwierzytelniania dla usługi Azure AD Multi-Factor Authentication i samoobsługowego resetowania hasła. Ludzie byli zdezorientowani, że podobne metody były używane w usługach Azure AD Multi-Factor Authentication i SSPR, ale musieli zarejestrować się w obu funkcjach. Teraz dzięki połączonej rejestracji użytkownicy mogą zarejestrować się raz i korzystać z zalet zarówno usługi Azure AD Multi-Factor Authentication, jak i usługi SSPR.

> [!NOTE]
> Od 15 sierpnia 2020 r. wszystkie nowe dzierżawy usługi Azure AD będą automatycznie włączane do rejestracji połączonej. Dzierżawy utworzone po tej dacie nie będą mogły korzystać ze starszych przepływów pracy rejestracji.

Aby upewnić się, że rozumiesz funkcje i efekty przed włączeniem nowego rozwiązania, zobacz pojęcia połączonej rejestracji [informacji zabezpieczających](concept-registration-mfa-sspr-combined.md).

![Ulepszone środowisko rejestracji połączonych informacji zabezpieczających](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Włączanie rejestracji łączonej

Aby włączyć rejestrację połączoną, wykonaj następujące kroki:

1. Zaloguj się do Azure Portal jako administrator użytkowników lub administrator globalny.
2. Przejdź do **Azure Active Directory**  >  **Ustawienia użytkownika Zarządzaj**  >  **ustawieniami wersji zapoznawczej funkcji użytkownika.**
3. W **obszarze Użytkownicy mogą używać połączonego środowisko** rejestracji  informacji zabezpieczających wybierz opcję włączenia dla wybranej grupy użytkowników lub dla **opcji Wszyscy** użytkownicy.

   ![Włączanie połączonego środowisko informacji zabezpieczających dla użytkowników](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Po włączeniu rejestracji połączonej użytkownicy, którzy rejestrują lub potwierdzają swój numer telefonu lub aplikację mobilną za pomocą nowego środowiska, mogą używać ich w usługach Azure AD Multi-Factor Authentication i SSPR, jeśli te metody są włączone w zasadach usługi Azure AD Multi-Factor Authentication i funkcji SSPR.
>
> Jeśli następnie wyłączysz to środowisko, użytkownicy, którzy przejdą do poprzedniej strony rejestracji funkcji SSPR na stronie , będą zobowiązani do przeprowadzenia uwierzytelniania wieloskładnikowego, zanim będą mieli `https://aka.ms/ssprsetup` dostęp do strony.

Jeśli lista przypisań lokacji do strefy jest skonfigurowana w programie Internet Explorer, następujące lokacje muszą być w tej samej strefie: 

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Zasady dostępu warunkowego do rejestracji połączonej

Aby zabezpieczyć czas i sposób rejestrowania użytkowników w usłudze Azure AD Multi-Factor Authentication i samoobsługowego resetowania hasła, można użyć akcji użytkownika w zasadach dostępu warunkowego. Ta funkcja może być włączona w organizacjach, które chcą, aby użytkownicy rejestrowały się w usłudze Azure AD Multi-Factor Authentication i funkcji SSPR z centralnej lokalizacji, takiej jak zaufana lokalizacja sieciowa podczas dołączania do działu kadr.

> [!NOTE]
> Te zasady mają zastosowanie tylko wtedy, gdy użytkownik uzyskuje dostęp do połączonej strony rejestracji. Te zasady nie wymuszają rejestracji mfa, gdy użytkownik uzyskuje dostęp do innych aplikacji.
>
> Zasady rejestracji usługi MFA można utworzyć przy użyciu usługi [Azure Identity Protection — konfigurowanie zasad usługi MFA.](../identity-protection/howto-identity-protection-configure-mfa-policy.md)

Aby uzyskać więcej informacji na temat tworzenia zaufanych lokalizacji w dostępie warunkowym, zobacz Jaki jest warunek lokalizacji w Azure Active Directory [warunkowego?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Tworzenie zasad wymagających rejestracji z zaufanej lokalizacji

Wykonaj następujące kroki, aby utworzyć zasady, które będą stosowane do wszystkich wybranych użytkowników próbujących zarejestrować się przy użyciu połączonego środowiska rejestracji, i blokują dostęp, chyba że będą nawiązywać połączenia z lokalizacji oznaczonej jako zaufana sieć:

1. Na stronie **Azure Portal** przejdź **do** Azure Active Directory  >  **dostępu**  >  **warunkowego zabezpieczeń**.
1. Wybierz **pozycję + Nowe zasady.**
1. Wprowadź nazwę tych zasad, taką jak *Połączona rejestracja informacji zabezpieczających w zaufanych sieciach.*
1. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**. Wybierz użytkowników i grupy, których mają dotyczyć te zasady, a następnie wybierz pozycję **Gotowe.**

   > [!WARNING]
   > Użytkownicy muszą mieć włączoną rejestrację połączoną.

1. W **obszarze Aplikacje lub akcje w chmurze** wybierz pozycję Akcje **użytkownika.** Zaznacz **pole wyboru Register security information (Zarejestruj informacje o** zabezpieczeniach), a następnie wybierz pozycję Done **(Gotowe).**

    ![Tworzenie zasad dostępu warunkowego w celu kontrolowania rejestracji informacji zabezpieczających](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. W **obszarze**  >  **Lokalizacje warunków** skonfiguruj następujące opcje:
   1. Skonfiguruj **pozycję Tak.**
   1. Uwzględnij **dowolną lokalizację**.
   1. **Wyklucz wszystkie zaufane lokalizacje.**
1. Wybierz **pozycję Gotowe** w *oknie Lokalizacje,* a następnie wybierz pozycję **Gotowe** w *oknie* Warunki.
1. W **obszarze Kontrole dostępu** U  >  **przyznaj** wybierz pozycję **Blokuj dostęp,** a następnie **wybierz pozycję**.
1. Ustaw przełącznik **Włącz zasady** na wartość **Włączone**.
1. Aby zakończyć tworzenie zasad, wybierz pozycję **Utwórz.**

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz pomocy, zobacz Rozwiązywanie problemów z rejestracją [połączonych informacji zabezpieczających](howto-registration-mfa-sspr-combined-troubleshoot.md) lub dowiedz się, jaki jest [warunek lokalizacji w dostępie warunkowym usługi Azure AD?](../conditional-access/location-condition.md)

Po włączeniu rejestracji połączonej przez [](tutorial-enable-sspr.md) użytkowników można włączyć samoobsługowe resetowanie hasła i włączyć [usługę Azure AD Multi-Factor Authentication.](tutorial-enable-azure-mfa.md)

W razie potrzeby dowiedz się, jak [wymusić ponowne zarejestrowanie metod uwierzytelniania przez użytkowników.](howto-mfa-userdevicesettings.md#manage-user-authentication-options)
