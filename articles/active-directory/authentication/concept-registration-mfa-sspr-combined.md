---
title: Rejestracja łączona na potrzeby samoobsługowego resetowania hasła i usługi MFA — Azure Active Directory
description: Rejestracja w usłudze Azure AD Multi-Factor Authentication i samoobsługowego resetowania hasła
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
ms.openlocfilehash: 4d4caaf8704f2ee49f8f094ad22065ae462154be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143920"
---
# <a name="combined-security-information-registration-overview"></a>Przegląd rejestracji informacji o zabezpieczeniach

Przed połączoną rejestracją użytkownicy zarejestrowali metody uwierzytelniania dla usługi Azure Multi-Factor Authentication i samoobsługowego resetowania hasła (SSPR). Ktoś został mylić, że podobne metody zostały użyte do Multi-Factor Authentication i SSPR, ale musiały zarejestrować się w przypadku obu funkcji. Teraz dzięki łącznej rejestracji użytkownicy mogą rejestrować się raz i korzystać z zalet zarówno Multi-Factor Authentication, jak i SSPR.

W tym artykule przedstawiono łączną rejestrację zabezpieczeń. Aby rozpocząć pracę z łączną rejestracją zabezpieczeń, zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Włącz łączną rejestrację zabezpieczeń](howto-registration-mfa-sspr-combined.md)

![Mój profil pokazujący zarejestrowane informacje zabezpieczające dla użytkownika](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Przed włączeniem nowego środowiska zapoznaj się z dokumentacją zorientowaną na administratora i dokumentacją zorientowaną na użytkownika, aby upewnić się, że rozumiesz funkcjonalność i wpływ tej funkcji. Zapoznaj się z [dokumentacją użytkownika](../user-help/user-help-security-info-overview.md) w celu przygotowania użytkowników do nowego środowiska i zapewnienia pomyślnego wdrożenia.

Rejestracja informacji o zabezpieczeniach połączonej usługi Azure AD nie jest obecnie dostępna dla chmur narodowych, takich jak Azure USA, Azure (Niemcy) i Azure (Chiny).

> [!IMPORTANT]
> Nowe zachowanie będzie widoczne dla użytkowników, którzy są włączeni do wersji zapoznawczej i udoskonalonego środowiska rejestracji połączonej. Użytkownicy, którzy są włączeni do obu środowisk, będą widzieć tylko nowe środowisko my profilu. Nowy mój profil jest wyrównany do wyglądu i sposobu działania złożonej rejestracji oraz zapewnia bezproblemowe środowisko dla użytkowników. Użytkownicy mogą zobaczyć mój profil, przechodząc [https://myprofile.microsoft.com](https://myprofile.microsoft.com)do.
>
> Podczas próby uzyskania dostępu do opcji informacje zabezpieczające może wystąpić komunikat o błędzie. Na przykład "Niestety, nie możemy cię zalogować". W takim przypadku upewnij się, że nie masz żadnej konfiguracji ani obiektu zasad grupy, który blokuje pliki cookie innych firm w przeglądarce sieci Web.

Moje strony profilów są zlokalizowane na podstawie ustawień języka komputera uzyskujących dostęp do strony. Firma Microsoft przechowuje najnowszy język używany w pamięci podręcznej przeglądarki, dlatego kolejne próby uzyskania dostępu do stron będą nadal renderowane w ostatnim używanym języku. W przypadku wyczyszczenia pamięci podręcznej strony zostaną ponownie renderowane. Jeśli chcesz wymusić określony język, możesz dodać `?lng=<language>` na końcu adresu URL, gdzie `<language>` jest kodem języka, który ma być renderowany.

![Konfigurowanie SSPR lub innych metod weryfikacji zabezpieczeń](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metody dostępne w połączeniu z rejestracją

Rejestracja łączona obsługuje następujące metody uwierzytelniania i akcje:

|   | Zarejestruj | Change | Usuwanie |
| --- | --- | --- | --- |
| Microsoft Authenticator | Tak (maksymalnie 5) | Nie | Tak |
| Inna aplikacja uwierzytelniania | Tak (maksymalnie 5) | Nie | Tak |
| Token sprzętu | Nie | Nie | Tak |
| Telefon | Tak | Tak | Tak |
| Alternatywny numer telefonu | Tak | Tak | Tak |
| Telefon służbowy | Nie | Nie | Nie |
| Poczta e-mail | Tak | Tak | Tak |
| Pytania zabezpieczające | Tak | Nie | Tak |
| Hasła aplikacji | Tak | Nie | Tak |
| FIDO2 klucze zabezpieczeń<br />*Tryb zarządzany tylko ze strony z [informacjami o zabezpieczeniach](https://mysignins.microsoft.com/security-info)*| Tak | Tak | Tak |

> [!NOTE]
> Hasła aplikacji są dostępne tylko dla użytkowników, którzy zostali wyegzekwowani dla Multi-Factor Authentication. Hasła aplikacji nie są dostępne dla użytkowników, którzy są włączeni do Multi-Factor Authentication za pomocą zasad dostępu warunkowego.

Użytkownicy mogą ustawić jedną z następujących opcji jako domyślną metodę Multi-Factor Authentication:

- Microsoft Authenticator — powiadomienie.
- Aplikacja lub token sprzętowy uwierzytelniania — kod.
- Połączenie telefoniczne.
- Wiadomość SMS.

W miarę jak będziemy nadal dodawać kolejne metody uwierzytelniania do usługi Azure AD, te metody będą dostępne w ramach rejestracji złożonej.

## <a name="combined-registration-modes"></a>Połączone tryby rejestracji

Istnieją dwa tryby rejestracji połączonej: przerwanie i zarządzanie.

- **Tryb przerwania** to środowisko podobne do kreatora prezentowane użytkownikom podczas rejestrowania lub odświeżania informacji zabezpieczających podczas logowania.
- **Tryb zarządzania** jest częścią profilu użytkownika i umożliwia użytkownikom zarządzanie swoimi informacjami o zabezpieczeniach.

Dla obu trybów użytkownicy, którzy wcześniej zarejestrowali metodę, która może zostać użyta do Multi-Factor Authentication będzie musiał wykonać Multi-Factor Authentication, zanim uzyskają dostęp do swoich informacji zabezpieczających. Użytkownicy muszą potwierdzić swoje informacje przed kontynuowaniem używania poprzednio zarejestrowanych metod. 

### <a name="interrupt-mode"></a>Tryb przerwania

Rejestracja łączona uwzględnia zarówno zasady Multi-Factor Authentication, jak i SSPR, jeśli oba są włączone dla dzierżawy. Te zasady kontrolują, czy użytkownik jest przerywany do rejestracji podczas logowania i które metody są dostępne do rejestracji.

Poniżej przedstawiono kilka scenariuszy, w których użytkownicy mogą zostać poproszeni o zarejestrowanie lub odświeżenie swoich informacji zabezpieczających:

- Rejestracja Multi-Factor Authentication wymuszana przez ochronę tożsamości: użytkownicy są proszeni o zarejestrowanie się podczas logowania. Rejestrują metody Multi-Factor Authentication i metody SSPR (Jeśli użytkownik jest włączony dla SSPR).
- Multi-Factor Authentication rejestracji wymuszane przez Multi-Factor Authentication poszczególnych użytkowników: użytkownicy są proszeni o zarejestrowanie się podczas logowania. Rejestrują metody Multi-Factor Authentication i metody SSPR (Jeśli użytkownik jest włączony dla SSPR).
- Rejestracja Multi-Factor Authentication wymuszana przy użyciu dostępu warunkowego lub innych zasad: użytkownicy są proszeni o rejestrację, gdy używają zasobu wymagającego Multi-Factor Authentication. Rejestrują metody Multi-Factor Authentication i metody SSPR (Jeśli użytkownik jest włączony dla SSPR).
- SSPR Rejestracja: użytkownicy są proszeni o zarejestrowanie się podczas logowania. Rejestrują tylko metody SSPR.
- Wymuszone odświeżanie SSPR: użytkownicy muszą przeglądać informacje zabezpieczające w interwale ustawionym przez administratora. Użytkownicy są wyświetlani informacjami i mogą potwierdzić bieżące informacje lub wprowadzić zmiany w razie konieczności.

W przypadku wymuszania rejestracji użytkownicy są pokazani minimalną liczbę metod, które muszą być zgodne z zasadami Multi-Factor Authentication i SSPR, od najbezpieczniejszego do najmniej zabezpieczonego.

Przykład:

- Użytkownik jest włączony do SSPR. Zasady SSPR muszą mieć dwie metody resetowania i włączenia kodu aplikacji mobilnej, poczty e-mail i telefonu.
   - Ten użytkownik jest wymagany do zarejestrowania dwóch metod.
      - Użytkownik jest domyślnie pokazywany jako aplikacja uwierzytelniania i telefon.
      - Użytkownik może zdecydować się na zarejestrowanie poczty e-mail zamiast aplikacji lub telefonu uwierzytelniania.

Ten schemat blokowy opisuje, które metody są wyświetlane użytkownikowi po przerwaniu rejestracji podczas logowania:

![Schemat blokowy połączonych informacji zabezpieczających](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Jeśli masz Multi-Factor Authentication i SSPR włączone, zalecamy wymuszenie rejestracji Multi-Factor Authentication.

Jeśli zasady SSPR wymagają od użytkowników przeglądania ich informacji zabezpieczających w regularnych odstępach czasu, użytkownicy są przerywani podczas logowania i pokazują wszystkie zarejestrowane metody. Mogą oni potwierdzić bieżące informacje, jeśli są aktualne lub mogą wprowadzać zmiany, jeśli ich potrzebują. Użytkownicy muszą korzystać z uwierzytelniania wieloskładnikowego podczas uzyskiwania dostępu do tej strony.

### <a name="manage-mode"></a>Tryb zarządzania

Użytkownicy mogą uzyskać dostęp do trybu zarządzania, [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) przechodząc do lub wybierając pozycję **informacje zabezpieczające** z mojego profilu. Z tego miejsca użytkownicy mogą dodawać metody, usuwać lub zmieniać istniejące metody, zmieniać metodę domyślną i nie tylko.

## <a name="key-usage-scenarios"></a>Scenariusze użycia klucza

### <a name="set-up-security-info-during-sign-in"></a>Konfigurowanie informacji zabezpieczających podczas logowania

Administrator wymusił rejestrację.

Użytkownik nie skonfigurował wszystkich wymaganych informacji zabezpieczających i przechodzi do Azure Portal. Po wprowadzeniu nazwy użytkownika i hasła użytkownik otrzymuje monit o skonfigurowanie informacji zabezpieczających. Następnie użytkownik postępuje zgodnie z instrukcjami wyświetlanymi w kreatorze, aby skonfigurować wymagane informacje zabezpieczające. Jeśli Twoje ustawienia pozwalają na to, użytkownik może zdecydować się na skonfigurowanie metod innych niż te, które są domyślnie wyświetlane. Po zakończeniu pracy Kreatora użytkownicy przeglądają skonfigurowane metody i ich domyślną metodę dla Multi-Factor Authentication. Aby ukończyć proces instalacji, użytkownik potwierdza informacje i kontynuuje Azure Portal.

### <a name="set-up-security-info-from-my-profile"></a>Skonfiguruj informacje zabezpieczające z mojego profilu

Administrator nie wymusił rejestracji.

Użytkownik, który jeszcze nie skonfigurował wszystkich wymaganych informacji zabezpieczających, [https://myprofile.microsoft.com](https://myprofile.microsoft.com)przejdzie do programu. Użytkownik wybiera **informacje zabezpieczające** w okienku po lewej stronie. W tym miejscu użytkownik zdecyduje się dodać metodę, wybiera dowolną z dostępnych metod i postępuje zgodnie z instrukcjami, aby skonfigurować tę metodę. Po zakończeniu użytkownik zobaczy metodę, która została właśnie skonfigurowana na stronie informacje zabezpieczające.

### <a name="delete-security-info-from-my-profile"></a>Usuń informacje zabezpieczające z mojego profilu

Użytkownik, który wcześniej skonfigurował co najmniej jedną metodę [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Użytkownik zdecyduje się na usunięcie jednej z wcześniej zarejestrowanych metod. Po zakończeniu użytkownik nie widzi już tej metody na stronie informacje zabezpieczające.

### <a name="change-the-default-method-from-my-profile"></a>Zmień domyślną metodę z mojego profilu

Użytkownik, który wcześniej skonfigurował co najmniej jedną metodę, która może być używana do Multi-Factor Authentication nawigowania do [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Użytkownik zmienia bieżącą metodę domyślną na inną metodę domyślną. Po zakończeniu użytkownik zobaczy nową domyślną metodę na stronie informacje zabezpieczające.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zapoznaj się z samouczkami, aby włączyć funkcję samoobsługowego [resetowania hasła](tutorial-enable-sspr.md) i [włączyć usługę Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Dowiedz się, jak [włączyć rejestrację łączną w dzierżawie](howto-registration-mfa-sspr-combined.md) lub [zmusić użytkowników do ponownego rejestrowania metod uwierzytelniania](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Możesz również przejrzeć [dostępne metody dla usług Azure Multi-Factor Authentication i SSPR](concept-authentication-methods.md).
