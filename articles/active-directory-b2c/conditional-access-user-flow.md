---
title: Dodawanie dostępu warunkowego do przepływu użytkownika w Azure AD B2C
description: Dowiedz się, jak dodać dostęp warunkowy do przepływów użytkownika Azure AD B2C. Skonfiguruj ustawienia uwierzytelniania wieloskładnikowego (MFA) i zasady dostępu warunkowego w przepływie użytkownika, aby wymusić zasady i skorygować ryzykowne logowania.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c120f343ec539783f04fe35e96891c5372c5d39
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109083"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Dodawanie dostępu warunkowego do przepływów użytkowników w Azure Active Directory B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Dostęp warunkowy można dodać do Azure Active Directory B2C przepływów użytkownika w celu zarządzania ryzykownymi logowaniami do aplikacji. Integracja usługi Identity Protection i dostępu warunkowego w programie Azure AD B2C umożliwia skonfigurowanie zasad, które identyfikują ryzykowne zachowanie logowania i wymuszają zasady, które wymagają dalszych działań od użytkownika lub administratora. Są to składniki, które umożliwiają dostęp warunkowy w Azure AD B2C przepływów użytkownika:

- **Przepływ użytkownika**. Utwórz przepływ użytkownika, który prowadzi użytkownika przez proces logowania i tworzenia konta. W obszarze Ustawienia przepływu użytkownika wskaż, czy aktywować zasady dostępu warunkowego, gdy użytkownik postępuje zgodnie z przepływem użytkownika.
- **Aplikacja, która kieruje użytkowników do przepływu użytkownika**. Skonfiguruj swoją aplikację, aby kierować użytkowników do odpowiednich przepływów logowania i logowania użytkowników, określając punkt końcowy przepływu użytkownika w aplikacji.
- **Zasady dostępu warunkowego**. [Utwórz zasady dostępu warunkowego](conditional-access-identity-protection-setup.md) i określ aplikacje, do których mają być stosowane zasady. Gdy użytkownik postępuje zgodnie z przepływem użytkownika logowania lub rejestracji dla aplikacji, zasady dostępu warunkowego korzystają z sygnałów usługi Identity Protection, aby identyfikować ryzykowne logowania i w razie potrzeby przedstawiają odpowiednie działania naprawcze.

Dostęp warunkowy jest obsługiwany w najnowszych wersjach przepływów użytkowników. Zasady dostępu warunkowego można dodać do nowych przepływów użytkownika podczas ich tworzenia lub można je dodać do istniejących przepływów użytkownika, o ile wersja obsługuje dostęp warunkowy. Podczas dodawania dostępu warunkowego do istniejącego przepływu użytkownika istnieją dwa ustawienia, które należy przejrzeć:

- **Uwierzytelnianie wieloskładnikowe (MFA)**: użytkownicy mogą teraz używać kodu jednorazowego za pośrednictwem programu SMS lub głosu lub hasła jednorazowego za pośrednictwem poczty e-mail na potrzeby uwierzytelniania wieloskładnikowego. Ustawienia usługi MFA są niezależne od ustawień dostępu warunkowego. Można ustawić usługę MFA na **zawsze włączone** , aby uwierzytelnianie wieloskładnikowe było zawsze wymagane niezależnie od konfiguracji dostępu warunkowego. Można też ustawić uwierzytelnianie wieloskładnikowe na **warunkowe** , aby usługa MFA była wymagana tylko wtedy, gdy wymaga tego zasady.

- **Dostęp warunkowy**: to ustawienie powinno być zawsze **włączone**. Zwykle **tego ustawienia można wyłączyć tylko** podczas rozwiązywania problemów lub migracji lub starszych implementacji.

Dowiedz się więcej na temat [ochrony tożsamości i dostępu warunkowego](conditional-access-identity-protection-overview.md) w programie Azure AD B2C lub zapoznaj się z tematem [jak go skonfigurować](conditional-access-identity-protection-setup.md).

## <a name="add-conditional-access-to-a-new-user-flow"></a>Dodawanie dostępu warunkowego do nowego przepływu użytkownika

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **przepływy użytkowników**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.
1. Na stronie **Tworzenie przepływu użytkownika** wybierz typ przepływu użytkownika.
1. W obszarze **Wybierz wersję** wybierz pozycję **zalecane**, a następnie wybierz pozycję **Utwórz**. ([Dowiedz się więcej](user-flow-versions.md) o wersjach przepływu użytkowników).

    ![Utwórz stronę przepływu użytkownika w Azure Portal z wyróżnionymi właściwościami](./media/tutorial-create-user-flows/select-version.png)

1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *signupsignin1*.
1. W sekcji **dostawcy tożsamości** wybierz dostawców tożsamości, których chcesz zezwolić na ten przepływ użytkownika.
2. W sekcji **uwierzytelnianie** wieloskładnikowe wybierz żądaną **metodę MFA**, a następnie w obszarze **wymuszanie MFA** wybierz pozycję **warunkowe (zalecane)**.
 
   ![Konfigurowanie uwierzytelniania wieloskładnikowego](media/conditional-access-user-flow/configure-mfa.png)

1. W sekcji **dostęp warunkowy** zaznacz pole wyboru **Wymuszaj zasady dostępu warunkowego** .

   ![Konfigurowanie ustawień dostępu warunkowego](media/conditional-access-user-flow/configure-conditional-access.png)

1. W sekcji **atrybuty użytkownika i oświadczenia** wybierz oświadczenia i atrybuty, które mają być zbierane i wysyłane przez użytkownika podczas tworzenia konta. Na przykład wybierz pozycję **Pokaż więcej**, a następnie wybierz atrybuty i oświadczenia dla **kraju/regionu** i **nazwę wyświetlaną**. Wybierz przycisk **OK**.

    ![Strona wyboru atrybutów i oświadczeń z trzema wybranymi oświadczeniami](./media/conditional-access-user-flow/configure-user-attributes-claims.png)

1. Kliknij przycisk **Utwórz** , aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

## <a name="add-conditional-access-to-an-existing-user-flow"></a>Dodawanie dostępu warunkowego do istniejącego przepływu użytkownika

> [!NOTE]
> Istniejący przepływ użytkownika musi być wersją, która obsługuje dostęp warunkowy. Te wersje przepływu użytkownika mają etykietę **zalecane**.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.

1. W obszarze **zasady** wybierz pozycję **przepływy użytkownika**. Następnie wybierz przepływ użytkownika.

1. Wybierz **Właściwości** i upewnij się, że przepływ użytkownika obsługuje dostęp warunkowy przez wybranie **Właściwości** i wyszukiwanie ustawienia etykieta **dostęp warunkowy**.
 
   ![Konfigurowanie uwierzytelniania wieloskładnikowego i dostępu warunkowego we właściwościach](media/conditional-access-user-flow/add-conditional-access.png)

1. W sekcji **uwierzytelnianie** wieloskładnikowe wybierz żądaną **metodę MFA**, a następnie w obszarze **wymuszanie MFA** wybierz pozycję **warunkowe (zalecane)**.
 
1. W sekcji **dostęp warunkowy** zaznacz pole wyboru **Wymuszaj zasady dostępu warunkowego** .

1. Wybierz pozycję **Zapisz**.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

Aby przetestować dostęp warunkowy w przepływie użytkownika, należy [utworzyć zasady dostępu warunkowego](conditional-access-identity-protection-setup.md) i włączyć dostęp warunkowy w przepływie użytkownika zgodnie z powyższym opisem. 

### <a name="prerequisites"></a>Wymagania wstępne

- Do utworzenia ryzykownych zasad logowania jest wymagana Azure AD B2C Premium 2. Dzierżawy w warstwie Premium P1 mogą tworzyć zasady dotyczące lokalizacji, aplikacji lub grupy.
- W celach testowych można [zarejestrować testową aplikację sieci Web](tutorial-register-applications.md) `https://jwt.ms` , która jest aplikacją sieci Web będącą własnością firmy Microsoft, która wyświetla zdekodowaną zawartość tokenu (zawartość tokenu nigdy nie opuszcza przeglądarki). 
- Aby symulować ryzykowne logowanie, Pobierz przeglądarkę TOR i spróbuj zalogować się do punktu końcowego przepływu użytkownika.
- Aby [utworzyć zasady dostępu warunkowego](conditional-access-identity-protection-setup.md), należy użyć następujących ustawień:
   
   - W przypadku **użytkowników i grup** wybierz użytkownika testowego (nie zaznaczaj opcji **Wszyscy użytkownicy** lub Zablokuj możliwość zalogowania się).
   - W przypadku **aplikacji lub akcji w chmurze** wybierz **pozycję Wybierz aplikacje**, a następnie wybierz aplikację jednostki uzależnionej.
   - W obszarze warunki wybierz pozycję **ryzyko związane z logowaniem** i **wysoki**, **Średni** i **niski** poziom ryzyka.
   - W obszarze **Udziel** wybierz opcję **Blokuj dostęp**.

      ![Wykrycia ryzyka](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

### <a name="run-the-user-flow"></a>Uruchamianie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**. W obszarze **aplikacja** wybierz pozycję *webapp1*. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .

   ![Uruchom stronę przepływu użytkownika w portalu z wyróżnionym przyciskiem Uruchom przepływ użytkownika](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Skopiuj adres URL w obszarze **Uruchom punkt końcowy przepływu użytkownika**.

1. Aby zasymulować ryzykowne logowanie, Otwórz [przeglądarkę sieci Tor](https://www.torproject.org/download/) i użyj adresu URL skopiowanego w kroku wersji zapoznawczej, aby zalogować się do zarejestrowanej aplikacji.

1. Wprowadź żądane informacje na stronie logowania, a następnie spróbuj się zalogować. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony. W tokenie dekodowane jwt.ms należy zobaczyć, że logowanie zostało zablokowane:

   ![Testowanie zablokowanego logowania](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie interfejsu użytkownika w przepływie użytkownika Azure AD B2C](customize-ui-with-html.md)
