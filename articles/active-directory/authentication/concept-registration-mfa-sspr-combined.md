---
title: Rejestracja łączona na potrzeby samoobsługowego resetowania hasła i usługi MFA — Azure Active Directory
description: Uwierzytelnianie wieloskładnikowe usługi Azure AD i samoobsługowa rejestracja resetowania haseł
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48350bf8f0ffb8681d95f6f42f9aa93256395f9a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534690"
---
# <a name="combined-security-information-registration-overview"></a>Przegląd rejestracji informacji o zabezpieczeniach połączonej

Przed rejestracją połączoną użytkownicy zarejestrowali metody uwierzytelniania azure wieloskładnikowego i samoobsługowego resetowania haseł (SSPR) oddzielnie. Ludzie byli zdezorientowani, że podobne metody były używane do uwierzytelniania wieloskładnikowego i samooczyżania SSPR, ale musieli zarejestrować się dla obu funkcji. Teraz, dzięki połączonej rejestracji, użytkownicy mogą zarejestrować się raz i uzyskać korzyści zarówno z uwierzytelniania wieloskładnikowego, jak i samookreślenia.

W tym artykule opisano, czym jest połączona rejestracja zabezpieczeń. Aby rozpocząć korzystanie z połączonej rejestracji zabezpieczeń, zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Włączanie połączonej rejestracji zabezpieczeń](howto-registration-mfa-sspr-combined.md)

![Mój profil z zarejestrowanymi informacjami zabezpieczającymi dla użytkownika](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Przed włączeniem nowego środowiska zapoznaj się z dokumentacją skoncentrowaną na administratorze i dokumentacją skoncentrowaną na użytkowniku, aby upewnić się, że rozumiesz funkcjonalność i efekt tej funkcji. Oprzeć swoje szkolenie na [dokumentacji użytkownika,](../user-help/user-help-security-info-overview.md) aby przygotować użytkowników do nowego środowiska i pomóc zapewnić pomyślne wdrożenie.

Rejestracja połączonych informacji o zabezpieczeniach usługi Azure AD nie jest obecnie dostępna dla chmur krajowych, takich jak Azure US GOVERNMENT, Azure Germany lub Azure China 21Vianet.

> [!IMPORTANT]
> Użytkownicy, którzy są włączeni zarówno dla oryginalnego podglądu, jak i ulepszonego środowiska połączonej rejestracji, zobaczą nowe zachowanie. Użytkownicy, którzy są włączeni dla obu środowisk zobaczą tylko nowe środowisko Mój profil. Nowy mój profil jest zgodny z wyglądem połączonej rejestracji i zapewnia użytkownikom bezproblemowe działanie. Użytkownicy mogą zobaczyć mój [https://myprofile.microsoft.com](https://myprofile.microsoft.com)profil, przechodząc do .
>
> Podczas próby uzyskania dostępu do opcji Informacje zabezpieczające może wystąpić komunikat o błędzie. Na przykład "Przepraszamy, nie możemy Cię zalogować". W takim przypadku upewnij się, że nie masz żadnego obiektu zasad konfiguracji lub grupy, który blokuje pliki cookie innych firm w przeglądarce internetowej.

Strony mojego profilu są zlokalizowane na podstawie ustawień językowych komputera uzyskującego dostęp do strony. Firma Microsoft przechowuje najnowszy język używany w pamięci podręcznej przeglądarki, więc kolejne próby uzyskania dostępu do stron będą nadal renderowane w ostatnim używanym języku. Jeśli wyczyścisz pamięć podręczną, strony zostaną ponownie renderowane. Jeśli chcesz wymusić określony język, `?lng=<language>` możesz dodać na końcu `<language>` adresu URL, gdzie znajduje się kod języka, który chcesz renderować.

![Konfigurowanie sspr lub innych metod weryfikacji zabezpieczeń](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metody dostępne w rejestracji połączonej

Rejestracja łączona obsługuje następujące metody i akcje uwierzytelniania:

|   | Zarejestruj | Change | Usuń |
| --- | --- | --- | --- |
| Microsoft Authenticator | Tak (maksymalnie 5) | Nie | Tak |
| Inna aplikacja uwierzytelniającego | Tak (maksymalnie 5) | Nie | Tak |
| Token sprzętowy | Nie | Nie | Tak |
| Telefon | Tak | Tak | Tak |
| Alternatywny telefon | Tak | Tak | Tak |
| Telefon służbowy | Nie | Nie | Nie |
| Adres e-mail | Tak | Tak | Tak |
| Pytania zabezpieczające | Tak | Nie | Tak |
| Hasła aplikacji | Tak | Nie | Tak |
| Klucze zabezpieczeń FIDO2<br />*Tryb zarządzany tylko ze strony [Informacje o zabezpieczeniach](https://mysignins.microsoft.com/security-info)*| Tak | Tak | Tak |

> [!NOTE]
> Hasła aplikacji są dostępne tylko dla użytkowników, którzy zostali wymuszani do uwierzytelniania wieloskładnikowego. Hasła aplikacji nie są dostępne dla użytkowników, którzy są włączeni w przypadku uwierzytelniania wieloskładnikowego za pośrednictwem zasad dostępu warunkowego.

Użytkownicy mogą ustawić jedną z następujących opcji jako domyślną metodę uwierzytelniania wieloskładnikowego:

- Microsoft Authenticator – powiadomienie.
- Aplikacja uwierzytelniacza lub token sprzętowy — kod.
- Rozmowa telefoniczna.
- Wiadomość SMS.

W miarę jak będziemy nadal dodawać więcej metod uwierzytelniania do usługi Azure AD, te metody będą dostępne w połączonej rejestracji.

## <a name="combined-registration-modes"></a>Połączone tryby rejestracji

Istnieją dwa tryby połączonej rejestracji: przerwanie i zarządzanie.

- **Tryb przerwania** to środowisko podobne do kreatora, prezentowane użytkownikom podczas rejestrowania lub odświeżania informacji zabezpieczających podczas logowania.
- **Tryb zarządzania** jest częścią profilu użytkownika i umożliwia użytkownikom zarządzanie informacjami zabezpieczającymi.

W przypadku obu trybów użytkownicy, którzy wcześniej zarejestrowali metodę, która może być używana do uwierzytelniania wieloskładnikowego, będą musieli wykonać uwierzytelnianie wieloskładnikowe, zanim będą mogli uzyskać dostęp do swoich informacji zabezpieczających.

### <a name="interrupt-mode"></a>Tryb przerwania

Połączona rejestracja jest zgodna zarówno z uwierzytelnianiem wieloskładnikowym, jak i zasadami samookapowania, jeśli obie są włączone dla dzierżawy. Te zasady określają, czy użytkownik został przerwany do rejestracji podczas logowania i jakie metody są dostępne do rejestracji.

Oto kilka scenariuszy, w których użytkownicy mogą zostać poproszeni o zarejestrowanie lub odświeżenie swoich informacji zabezpieczających:

- Rejestracja uwierzytelniania wieloskładnikowego wymuszana za pomocą ochrony tożsamości: użytkownicy są proszeni o zarejestrowanie się podczas logowania. Rejestrują metody uwierzytelniania wieloskładnikowego i metody samoobserwowania (jeśli użytkownik jest włączony dla samoobserwowania.
- Rejestracja uwierzytelniania wieloskładnikowego wymuszana za pomocą uwierzytelniania wieloskładnikowego dla użytkownika: użytkownicy są proszeni o zarejestrowanie się podczas logowania. Rejestrują metody uwierzytelniania wieloskładnikowego i metody samoobserwowania (jeśli użytkownik jest włączony dla samoobserwowania.
- Rejestracja uwierzytelniania wieloskładnikowego wymuszana za pomocą dostępu warunkowego lub innych zasad: użytkownicy są proszeni o zarejestrowanie się podczas korzystania z zasobu wymagającego uwierzytelniania wieloskładnikowego. Rejestrują metody uwierzytelniania wieloskładnikowego i metody samoobserwowania (jeśli użytkownik jest włączony dla samoobserwowania.
- Wymuszanie rejestracji SSPR: użytkownicy są proszeni o rejestrację podczas logowania. Rejestrują tylko metody SSPR.
- Wymuszone odświeżanie sspr: użytkownicy muszą przeglądać swoje informacje zabezpieczające w interwale ustawionym przez administratora. Użytkownicy mają wyświetlane informacje i mogą potwierdzić aktualne informacje lub w razie potrzeby wprowadzić zmiany.

Gdy rejestracja jest wymuszana, użytkownicy są wyświetlane minimalną liczbę metod potrzebnych do zapewnienia zgodności z zasadami uwierzytelniania wieloskładnikowego i SSPR, od większości do najmniej bezpieczne.

Przykład:

- Użytkownik jest włączony dla sspr. Zasady samoostawiania się numeru SSPR wymagały dwóch metod resetowania i włączyły kod aplikacji mobilnej, adres e-mail i telefon.
   - Ten użytkownik jest wymagany do zarejestrowania dwóch metod.
      - Domyślnie użytkownik jest wyświetlany w aplikacji uwierzytelniającego i telefonie.
      - Użytkownik może zarejestrować e-mail zamiast aplikacji uwierzytelniającego lub telefonu.

W tym schematie blokowym opisano, które metody są wyświetlane użytkownikowi po przerwaniu rejestracji podczas logowania:

![Połączony schemat blokowy informacji o zabezpieczeniach](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Jeśli masz włączone zarówno uwierzytelnianie wieloskładnikowe, jak i wiele innych, zalecamy wymuszanie rejestracji uwierzytelniania wieloskładnikowego.

Jeśli zasady samoobsługowe wymagają od użytkowników przeglądania ich informacji zabezpieczających w regularnych odstępach czasu, użytkownicy są przerywane podczas logowania i wyświetlane wszystkie ich zarejestrowanych metod. Mogą potwierdzić aktualne informacje, jeśli są aktualne, lub mogą wprowadzać zmiany, jeśli zajdzie taka potrzeba. Użytkownicy muszą wykonać uwierzytelnianie wieloskładnikowe podczas uzyskiwania dostępu do tej strony.

### <a name="manage-mode"></a>Zarządzanie trybem

Użytkownicy mogą uzyskać dostęp [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) do trybu zarządzania, przechodząc do lub wybierając **informacje zabezpieczające** z mojego profilu. W tym miejscu użytkownicy mogą dodawać metody, usuwać lub zmieniać istniejące metody, zmieniać metodę domyślną i nie tylko.

## <a name="key-usage-scenarios"></a>Kluczowe scenariusze użycia

### <a name="set-up-security-info-during-sign-in"></a>Konfigurowanie informacji zabezpieczających podczas logowania

Administrator wymusił rejestrację.

Użytkownik nie skonfigurował wszystkich wymaganych informacji zabezpieczających i przechodzi do witryny Azure portal. Po wprowadzeniu nazwy użytkownika i hasła użytkownik jest monitowany o skonfigurowanie informacji zabezpieczających. Następnie użytkownik wykonuje kroki pokazane w kreatorze, aby skonfigurować wymagane informacje zabezpieczające. Jeśli ustawienia na to zezwalają, użytkownik może skonfigurować metody inne niż wyświetlane domyślnie. Po zakończeniu pracy kreatora użytkownicy przejrzyją skonfigurowane metody i domyślną metodę uwierzytelniania wieloskładnikowego. Aby zakończyć proces instalacji, użytkownik potwierdza informacje i kontynuuje witrynę Azure portal.

### <a name="set-up-security-info-from-my-profile"></a>Konfigurowanie informacji zabezpieczających z mojego profilu

Administrator nie wymusił rejestracji.

Użytkownik, który nie skonfigurował jeszcze wszystkich wymaganych [https://myprofile.microsoft.com](https://myprofile.microsoft.com)informacji zabezpieczających, przechodzi do pliku . Użytkownik wybiera **informacje o zabezpieczeniach** w lewym okienku. W tym miejscu użytkownik zdecyduje się dodać metodę, wybiera dowolną z dostępnych metod i wykonuje kroki, aby skonfigurować tę metodę. Po zakończeniu użytkownik zobaczy metodę, która została właśnie skonfigurowana na stronie Informacje o zabezpieczeniach.

### <a name="delete-security-info-from-my-profile"></a>Usuwanie informacji zabezpieczających z mojego profilu

Użytkownik, który wcześniej skonfigurował co najmniej jedną [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)metodę, przechodzi do pliku . Użytkownik zdecyduje się usunąć jedną z wcześniej zarejestrowanych metod. Po zakończeniu użytkownik nie widzi już tej metody na stronie Informacje o zabezpieczeniach.

### <a name="change-the-default-method-from-my-profile"></a>Zmienianie metody domyślnej z mojego profilu

Użytkownik, który wcześniej skonfigurował co najmniej jedną metodę, która może być [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)używana do uwierzytelniania wieloskładnikowego, przechodzi do . Użytkownik zmienia bieżącą metodę domyślną na inną metodę domyślną. Po zakończeniu użytkownik zobaczy nową metodę domyślną na stronie Informacje o zabezpieczeniach.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zapoznaj się z samouczkami, aby [włączyć samoobsługowe resetowanie hasła](tutorial-enable-sspr.md) i [włączyć uwierzytelnianie wieloskładnikowe platformy Azure](tutorial-enable-azure-mfa.md).

Dowiedz się, jak [włączyć rejestrację połączoną w dzierżawie](howto-registration-mfa-sspr-combined.md) lub [zmusić użytkowników do ponownego zarejestrowania metod uwierzytelniania.](howto-mfa-userdevicesettings.md#manage-user-authentication-options)

Można również przejrzeć [dostępne metody uwierzytelniania wieloskładnikowego platformy Azure i samookadowania.](concept-authentication-methods.md)
