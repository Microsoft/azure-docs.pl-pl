---
title: Połączona Rejestracja dla SSPR i Multi-Factor Authentication platformy Azure — Azure Active Directory
description: Dowiedz się więcej na temat połączonego środowiska rejestracji dla Azure Active Directory, aby umożliwić użytkownikom rejestrowanie w usłudze Azure Multi-Factor Authentication i Samoobsługowe resetowanie hasła
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79a5d306643fa9597b30f6941c420c403695c22e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424575"
---
# <a name="combined-security-information-registration-for-azure-active-directory-overview"></a>Rejestracja informacji o zabezpieczeniach dla Azure Active Directory przegląd

Przed połączoną rejestracją użytkownicy zarejestrowali metody uwierzytelniania dla usługi Azure Multi-Factor Authentication i samoobsługowego resetowania hasła (SSPR). Ktoś został mylić, że podobne metody zostały użyte do Multi-Factor Authentication i SSPR, ale musiały zarejestrować się w przypadku obu funkcji. Teraz dzięki łącznej rejestracji użytkownicy mogą rejestrować się raz i korzystać z zalet zarówno Multi-Factor Authentication, jak i SSPR.

> [!NOTE]
> Od 15 sierpnia 2020 wszystkie nowe dzierżawy usługi Azure AD będą automatycznie włączane na potrzeby rejestracji złożonej.

W tym artykule przedstawiono łączną rejestrację zabezpieczeń. Aby rozpocząć pracę z łączną rejestracją zabezpieczeń, zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Włącz łączną rejestrację zabezpieczeń](howto-registration-mfa-sspr-combined.md)

![Mój profil pokazujący zarejestrowane informacje zabezpieczające dla użytkownika](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Przed włączeniem nowego środowiska zapoznaj się z dokumentacją zorientowaną na administratora i dokumentacją zorientowaną na użytkownika, aby upewnić się, że rozumiesz funkcjonalność i wpływ tej funkcji. Zapoznaj się z [dokumentacją użytkownika](../user-help/security-info-setup-signin.md) w celu przygotowania użytkowników do nowego środowiska i zapewnienia pomyślnego wdrożenia.

Rejestracja informacji o zabezpieczeniach połączonej usługi Azure AD nie jest obecnie dostępna dla chmur narodowych, takich jak Azure (Niemcy) lub Azure Chiny. Jest ona dostępna dla instytucji rządowych USA platformy Azure.

> [!IMPORTANT]
> Użytkownicy, którzy są włączeni do zarówno oryginalnej wersji zapoznawczej, jak i udoskonalonego, połączonego środowiska rejestracji, zobaczą nowe zachowanie. Użytkownicy, którzy są włączeni do obu środowisk, zobaczą tylko nowe środowisko my profile. Nowy *mój profil* jest wyrównany do wyglądu i sposobu działania złożonej rejestracji oraz zapewnia bezproblemowe środowisko dla użytkowników. Użytkownicy mogą zobaczyć mój profil, przechodząc do [https://myprofile.microsoft.com](https://myprofile.microsoft.com) .
>
> Podczas próby uzyskania dostępu do opcji informacje zabezpieczające, na przykład "Niestety, nie możemy cię zalogować", może wystąpić komunikat o błędzie. Upewnij się, że nie masz żadnej konfiguracji ani obiektu zasad grupy, który blokuje pliki cookie innych firm w przeglądarce sieci Web.

*Moje strony profilów* są zlokalizowane na podstawie ustawień języka komputera uzyskujących dostęp do strony. Firma Microsoft przechowuje najnowszy język używany w pamięci podręcznej przeglądarki, a następnie próbuje uzyskać dostęp do stron w dalszym ciągu w ostatnio używanym języku. Jeśli wyczyścisz pamięć podręczną, strony są ponownie renderowane.

Jeśli chcesz wymusić określony język, możesz dodać `?lng=<language>` na końcu adresu URL, gdzie `<language>` jest kodem języka, który ma być renderowany.

![Konfigurowanie SSPR lub innych metod weryfikacji zabezpieczeń](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metody dostępne w połączeniu z rejestracją

Rejestracja łączona obsługuje następujące metody uwierzytelniania i akcje:

| Metoda | Zarejestruj | Zmiana | Usuń |
| --- | --- | --- | --- |
| Microsoft Authenticator | Tak (maksymalnie 5) | Nie | Tak |
| Inna aplikacja uwierzytelniania | Tak (maksymalnie 5) | Nie | Tak |
| Token sprzętu | Nie | Nie | Tak |
| Telefon | Tak | Tak | Tak |
| Alternatywny numer telefonu | Tak | Tak | Tak |
| Telefon służbowy | Nie | Nie | Nie |
| E-mail | Tak | Tak | Tak |
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

W miarę jak będziemy nadal dodawać kolejne metody uwierzytelniania do usługi Azure AD, te metody są dostępne w ramach rejestracji złożonej.

## <a name="combined-registration-modes"></a>Połączone tryby rejestracji

Istnieją dwa tryby rejestracji połączonej: przerwanie i zarządzanie.

- **Tryb przerwania** to środowisko podobne do kreatora prezentowane użytkownikom podczas rejestrowania lub odświeżania informacji zabezpieczających podczas logowania.
- **Tryb zarządzania** jest częścią profilu użytkownika i umożliwia użytkownikom zarządzanie swoimi informacjami o zabezpieczeniach.

Dla obu trybów użytkownicy, którzy wcześniej zarejestrowali metodę, która może być użyta do Multi-Factor Authentication musi wykonać Multi-Factor Authentication, zanim uzyskają dostęp do swoich informacji zabezpieczających. Użytkownicy muszą potwierdzić swoje informacje przed kontynuowaniem używania poprzednio zarejestrowanych metod. 

### <a name="interrupt-mode"></a>Tryb przerwania

Rejestracja łączona uwzględnia zarówno zasady Multi-Factor Authentication, jak i SSPR, jeśli oba są włączone dla dzierżawy. Te zasady kontrolują, czy użytkownik jest przerywany do rejestracji podczas logowania i które metody są dostępne do rejestracji.

Poniżej przedstawiono przykładowe scenariusze, w których użytkownicy mogą zostać poproszeni o zarejestrowanie lub odświeżenie swoich informacji zabezpieczających:

- *Rejestracja Multi-Factor Authentication wymuszana przez ochronę tożsamości:* Użytkownicy są monitowani o zarejestrowanie się podczas logowania. Rejestrują metody Multi-Factor Authentication i metody SSPR (Jeśli użytkownik jest włączony dla SSPR).
- *Rejestracja Multi-Factor Authentication wymuszana przez Multi-Factor Authentication poszczególnych użytkowników:* Użytkownicy są monitowani o zarejestrowanie się podczas logowania. Rejestrują metody Multi-Factor Authentication i metody SSPR (Jeśli użytkownik jest włączony dla SSPR).
- *Rejestracja Multi-Factor Authentication wymuszana przy użyciu dostępu warunkowego lub innych zasad:* Użytkownicy są proszeni o rejestrację, gdy używają zasobu wymagającego Multi-Factor Authentication. Rejestrują metody Multi-Factor Authentication i metody SSPR (Jeśli użytkownik jest włączony dla SSPR).
- *SSPR rejestracji:* Użytkownicy są monitowani o zarejestrowanie się podczas logowania. Rejestrują tylko metody SSPR.
- *Wymuszone odświeżanie SSPR:* Użytkownicy muszą przejrzeć informacje zabezpieczające w interwale ustawionym przez administratora. Użytkownicy są wyświetlani informacjami i mogą potwierdzić bieżące informacje lub wprowadzić zmiany w razie konieczności.

W przypadku wymuszania rejestracji użytkownicy są pokazani minimalną liczbę metod, które muszą być zgodne z zasadami Multi-Factor Authentication i SSPR, od najbezpieczniejszego do najmniej zabezpieczonego.

Rozważmy następujący przykładowy scenariusz:

- Użytkownik jest włączony do SSPR. Zasady SSPR muszą mieć dwie metody resetowania i włączenia kodu aplikacji mobilnej, poczty e-mail i telefonu.
- Ten użytkownik jest wymagany do zarejestrowania dwóch metod.
   - Użytkownik jest domyślnie pokazywany jako aplikacja uwierzytelniania i telefon.
   - Użytkownik może zdecydować się na zarejestrowanie poczty e-mail zamiast aplikacji lub telefonu uwierzytelniania.

Poniższy schemat blokowy opisuje, które metody są wyświetlane użytkownikowi po przerwaniu rejestracji podczas logowania:

![Schemat blokowy połączonych informacji zabezpieczających](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Jeśli masz Multi-Factor Authentication i SSPR włączone, zalecamy wymuszenie rejestracji Multi-Factor Authentication.

Jeśli zasady SSPR wymagają od użytkowników przeglądania ich informacji zabezpieczających w regularnych odstępach czasu, użytkownicy są przerywani podczas logowania i pokazują wszystkie zarejestrowane metody. Mogą oni potwierdzić bieżące informacje, jeśli są aktualne lub mogą wprowadzić zmiany, jeśli są one potrzebne. Użytkownicy muszą korzystać z uwierzytelniania wieloskładnikowego podczas uzyskiwania dostępu do tej strony.

### <a name="manage-mode"></a>Tryb zarządzania

Użytkownicy mogą uzyskać dostęp do trybu zarządzania, przechodząc do [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) lub wybierając pozycję **informacje zabezpieczające** z mojego profilu. Z tego miejsca użytkownicy mogą dodawać metody, usuwać lub zmieniać istniejące metody, zmieniać metodę domyślną i nie tylko.

## <a name="key-usage-scenarios"></a>Scenariusze użycia klucza

### <a name="set-up-security-info-during-sign-in"></a>Konfigurowanie informacji zabezpieczających podczas logowania

Administrator wymusił rejestrację.

Użytkownik nie skonfigurował wszystkich wymaganych informacji zabezpieczających i przechodzi do Azure Portal. Po wprowadzeniu nazwy użytkownika i hasła użytkownik otrzymuje monit o skonfigurowanie informacji zabezpieczających. Następnie użytkownik postępuje zgodnie z instrukcjami wyświetlanymi w kreatorze, aby skonfigurować wymagane informacje zabezpieczające. Jeśli Twoje ustawienia pozwalają na to, użytkownik może zdecydować się na skonfigurowanie metod innych niż te, które są domyślnie wyświetlane. Po zakończeniu pracy Kreatora użytkownicy przeglądają skonfigurowane metody i ich domyślną metodę dla Multi-Factor Authentication. Aby ukończyć proces instalacji, użytkownik potwierdza informacje i kontynuuje Azure Portal.

### <a name="set-up-security-info-from-my-profile"></a>Skonfiguruj informacje zabezpieczające z mojego profilu

Administrator nie wymusił rejestracji.

Użytkownik, który jeszcze nie skonfigurował wszystkich wymaganych informacji zabezpieczających, przejdzie do programu [https://myprofile.microsoft.com](https://myprofile.microsoft.com) . Użytkownik wybiera **informacje zabezpieczające** w okienku po lewej stronie. W tym miejscu użytkownik zdecyduje się dodać metodę, wybiera dowolną z dostępnych metod i postępuje zgodnie z instrukcjami, aby skonfigurować tę metodę. Po zakończeniu użytkownik zobaczy metodę, która została skonfigurowana na stronie informacje zabezpieczające.

### <a name="delete-security-info-from-my-profile"></a>Usuń informacje zabezpieczające z mojego profilu

Użytkownik, który wcześniej skonfigurował co najmniej jedną metodę [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Użytkownik zdecyduje się na usunięcie jednej z wcześniej zarejestrowanych metod. Po zakończeniu użytkownik nie widzi już tej metody na stronie informacje zabezpieczające.

### <a name="change-the-default-method-from-my-profile"></a>Zmień domyślną metodę z mojego profilu

Użytkownik, który wcześniej skonfigurował co najmniej jedną metodę, która może być używana do Multi-Factor Authentication nawigowania do [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Użytkownik zmienia bieżącą metodę domyślną na inną metodę domyślną. Po zakończeniu użytkownik zobaczy nową domyślną metodę na stronie informacje zabezpieczające.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zapoznaj się z samouczkami, aby włączyć funkcję samoobsługowego [resetowania hasła](tutorial-enable-sspr.md) i [włączyć usługę Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Dowiedz się, jak [włączyć rejestrację łączną w dzierżawie](howto-registration-mfa-sspr-combined.md) lub [zmusić użytkowników do ponownego rejestrowania metod uwierzytelniania](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Możesz również przejrzeć [dostępne metody dla usług Azure Multi-Factor Authentication i SSPR](concept-authentication-methods.md).