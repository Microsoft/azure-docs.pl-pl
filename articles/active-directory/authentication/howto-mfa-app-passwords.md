---
title: Konfigurowanie haseł aplikacji dla usługi Azure AD Multi-Factor Authentication — Azure Active Directory
description: Dowiedz się, jak konfigurować i używać haseł aplikacji dla starszych aplikacji w usłudze Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfb38f9fcdba6898b690d0af68b715fea07e80bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743109"
---
# <a name="enable-and-use-azure-ad-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>Włączanie usługi Azure AD Multi-Factor Authentication i używanie jej w starszych aplikacjach za pomocą haseł aplikacji

Niektóre starsze aplikacje nie korzystające z przeglądarki, takie jak Office 2010 lub starsze i Apple mail przed systemem iOS 11, nie rozumieją przerw lub przerw w procesie uwierzytelniania. Jeśli użytkownik jest włączony w usłudze Azure AD Multi-Factor Authentication i spróbuje użyć jednej z tych starszych aplikacji niekorzystających z przeglądarki, nie może się uwierzytelnić. Aby korzystać z tych aplikacji w bezpieczny sposób za pomocą usługi Azure AD Multi-Factor Authentication włączonej dla kont użytkowników, można użyć haseł aplikacji. Te hasła aplikacji zastąpiły tradycyjne hasło, aby umożliwić aplikacji ominięcie uwierzytelniania wieloskładnikowego i poprawne działanie.

Nowoczesne uwierzytelnianie jest obsługiwane przez klientów Microsoft Office 2013 i nowszych. Klienci korzystający z pakietu Office 2013, w tym Outlook, obsługują nowoczesne protokoły uwierzytelniania i mogą być włączeni do pracy z weryfikacją dwuetapową. Po włączeniu klienta hasła aplikacji nie są wymagane dla klienta.

W tym artykule opisano sposób włączania i używania haseł aplikacji w przypadku starszych aplikacji, które nie obsługują obsługi zapytań z uwierzytelnianiem wieloskładnikowym.

>[!NOTE]
> Hasła aplikacji nie działają z zasadami uwierzytelniania wieloskładnikowego opartymi na dostępie warunkowym i nowoczesnego uwierzytelniania.

## <a name="overview-and-considerations"></a>Omówienie i zagadnienia

Gdy konto użytkownika jest włączone dla Multi-Factor Authentication usługi Azure AD, zwykły monit logowania zostanie przerwany przez żądanie przeprowadzenia dodatkowej weryfikacji. Niektóre starsze aplikacje nie rozumieją tego przerwania w procesie logowania, więc uwierzytelnianie nie powiedzie się. Aby zachować bezpieczeństwo kont użytkowników i pozostawić Multi-Factor Authentication usługi Azure AD, można użyć haseł aplikacji zamiast zwykłej nazwy użytkownika i hasła. Gdy hasło aplikacji jest używane podczas logowania, nie ma dodatkowych monitów weryfikacyjnych, więc uwierzytelnianie zakończyło się pomyślnie.

Hasła aplikacji są generowane automatycznie, a nie określone przez użytkownika. To automatycznie generowane hasło utrudnia atakującemu odgadnięcie, więc jest bezpieczniejsze. Użytkownicy nie muszą śledzić haseł ani wprowadzać ich za każdym razem, gdy hasła aplikacji są wprowadzane tylko raz dla aplikacji.

Korzystając z haseł aplikacji, należy zastosować następujące zagadnienia:

* Dla każdego użytkownika istnieje limit 40 haseł aplikacji.
* Aplikacje, które buforują hasła i używają ich w scenariuszach lokalnych, mogą się nie powieść, ponieważ hasło aplikacji nie jest znane poza kontem służbowym. Przykładem tego scenariusza są wiadomości e-mail programu Exchange, które są lokalne, ale zarchiwizowana poczta znajduje się w chmurze. W tym scenariuszu to samo hasło nie działa.
* Po włączeniu usługi Azure AD Multi-Factor Authentication na koncie użytkownika hasła aplikacji mogą być używane z większością klientów nie korzystających z przeglądarki, takich jak Outlook i Microsoft Skype dla firm. Jednak akcje administracyjne nie mogą być wykonywane przy użyciu haseł aplikacji przez aplikacje nie korzystające z przeglądarki, takie jak Windows PowerShell. Akcje nie mogą być wykonywane nawet wtedy, gdy użytkownik ma konto administracyjne.
    * Aby uruchamiać skrypty programu PowerShell, należy utworzyć konto usługi o silnym haśle i nie włączać konta na potrzeby weryfikacji dwuetapowej.
* Jeśli podejrzewasz, że zabezpieczenia konta użytkownika zostały naruszone i odwoływanie/Resetowanie hasła konta, należy również zaktualizować hasła aplikacji. Hasła aplikacji nie są automatycznie odwoływane, gdy hasło do konta użytkownika zostanie odwołane/zresetowane. Użytkownik powinien usunąć istniejące hasła aplikacji i utworzyć nowe.
   * Aby uzyskać więcej informacji, zobacz [Tworzenie i usuwanie haseł aplikacji ze strony dodatkowej weryfikacji zabezpieczeń](../user-help/multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page).

>[!WARNING]
> Hasła aplikacji nie działają w środowiskach hybrydowych, w których klienci komunikują się z punktami końcowymi automatycznego odnajdywania lokalnego i w chmurze. Hasła domeny są wymagane do uwierzytelniania lokalnego. Do uwierzytelniania w chmurze wymagane są hasła aplikacji.

### <a name="app-password-names"></a>Nazwy haseł aplikacji

Nazwy haseł aplikacji powinny odzwierciedlać urządzenie, na którym są używane. Jeśli masz komputer przenośny, który ma aplikacje niekorzystające z przeglądarki, takie jak Outlook, Word i Excel, Utwórz jedno hasło aplikacji o nazwie **laptop** dla tych aplikacji. Utwórz inne hasło aplikacji o nazwie **Desktop** dla tych samych aplikacji, które są uruchamiane na komputerze stacjonarnym.

Zaleca się utworzenie jednego hasła aplikacji dla każdego urządzenia, a nie jednego hasła aplikacji.

## <a name="federated-or-single-sign-on-app-passwords"></a>Hasła aplikacji federacyjnych lub logowania jednokrotnego

Usługa Azure AD obsługuje Federacji lub Logowanie jednokrotne (SSO) z lokalnym Active Directory Domain Services (AD DS). Jeśli Twoja organizacja jest federacyjnym usługą Azure AD i używasz usługi Azure AD Multi-Factor Authentication, mają zastosowanie następujące zagadnienia dotyczące haseł aplikacji:

>[!NOTE]
> Poniższe punkty dotyczą tylko klientów federacyjnych (SSO).

* Hasła aplikacji są weryfikowane przez usługę Azure AD i w związku z tym pomijają Federacji. Federacja jest aktywnie używana tylko podczas konfigurowania haseł aplikacji.
* Nie nawiązano kontaktu z dostawcą tożsamości (dostawcy tożsamości) dla użytkowników federacyjnych (SSO), w przeciwieństwie do przepływu pasywnego. Hasła aplikacji są przechowywane na koncie służbowym. Jeśli użytkownik opuści firmę, informacje o użytkowniku są przepływane do konta służbowego przy użyciu narzędzia **DirSync** w czasie rzeczywistym. Synchronizacja z wyłączeniem lub usuwaniem konta może potrwać do 3 godzin, co może opóźnić wyłączenie/usunięcie hasła aplikacji w usłudze Azure AD.
* Lokalne ustawienia Access Control klienta nie są uznawane przez funkcję haseł aplikacji.
* Funkcja haseł aplikacji nie udostępnia możliwości rejestrowania lub inspekcji lokalnego uwierzytelniania.

Niektóre zaawansowane architektury wymagają kombinacji poświadczeń do uwierzytelniania wieloskładnikowego z klientami programu. Te poświadczenia mogą zawierać nazwę użytkownika i hasła konta służbowego oraz hasła aplikacji. Wymagania są zależne od tego, jak uwierzytelnianie jest wykonywane. W przypadku klientów, którzy uwierzytelniają się w infrastrukturze lokalnej, wymagana jest nazwa użytkownika i hasło konta służbowego. W przypadku klientów, którzy uwierzytelniają się w usłudze Azure AD, wymagane jest hasło aplikacji.

Załóżmy na przykład, że masz następującą architekturę:

* Lokalne wystąpienie Active Directory jest federacyjne z usługą Azure AD.
* Używasz usługi Exchange Online.
* Używasz programu Skype dla firm lokalnie.
* Używasz usługi Azure AD Multi-Factor Authentication.

W tym scenariuszu używane są następujące poświadczenia:

* Aby zalogować się do usługi Skype dla firm, użyj nazwy użytkownika i hasła konta służbowego.
* Aby uzyskać dostęp do książki adresowej z klienta programu Outlook, który nawiązuje połączenie z usługą Exchange Online, Użyj hasła aplikacji.

## <a name="allow-users-to-create-app-passwords"></a>Zezwól użytkownikom na tworzenie haseł aplikacji

Domyślnie użytkownicy nie mogą tworzyć haseł aplikacji. Funkcja haseł aplikacji musi być włączona, aby użytkownicy mogli z nich korzystać. Aby umożliwić użytkownikom tworzenie haseł aplikacji, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wyszukaj i wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Użytkownicy**.
3. Na pasku nawigacyjnym w górnej części okna *Użytkownicy* wybierz pozycję **Multi-Factor Authentication** .
4. W obszarze Multi-Factor Authentication wybierz pozycję **Ustawienia usługi**.
5. Na stronie **Ustawienia usługi** wybierz opcję **zezwól użytkownikom na tworzenie haseł aplikacji do logowania się do aplikacji niekorzystających z przeglądarki** .

    ![Zrzut ekranu przedstawiający Azure Portal, w którym są wyświetlane ustawienia usługi uwierzytelniania wieloskładnikowego, aby zezwolić użytkownikowi na hasła aplikacji](media/concept-authentication-methods/app-password-authentication-method.png)
    
> [!NOTE]
>
> Po wyłączeniu możliwości tworzenia haseł aplikacji przez użytkowników istniejące hasła aplikacji nadal będą działały. Jednak użytkownicy nie mogą zarządzać istniejącymi hasłami aplikacji ani ich usuwać po wyłączeniu tej możliwości.
>
> Wyłączenie możliwości tworzenia haseł aplikacji zaleca również [utworzenie zasad dostępu warunkowego w celu wyłączenia korzystania ze starszego uwierzytelniania](../conditional-access/block-legacy-authentication.md). Takie podejście uniemożliwia pracę istniejących haseł aplikacji i wymusza korzystanie z nowoczesnych metod uwierzytelniania.

## <a name="create-an-app-password"></a>Utwórz hasło aplikacji

Gdy użytkownicy zakończą swoją wstępną rejestrację w usłudze Azure AD Multi-Factor Authentication, istnieje możliwość utworzenia haseł aplikacji na końcu procesu rejestracji.

Użytkownicy mogą również tworzyć hasła aplikacji po rejestracji. Aby uzyskać więcej informacji i szczegółowe instrukcje dla użytkowników, zobacz [co to są hasła aplikacji w usłudze Azure AD Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o tym, jak umożliwić użytkownikom szybkie rejestrowanie w usłudze Azure AD Multi-Factor Authentication, zobacz [Omówienie rejestracji informacji o zabezpieczeniach](concept-registration-mfa-sspr-combined.md).
