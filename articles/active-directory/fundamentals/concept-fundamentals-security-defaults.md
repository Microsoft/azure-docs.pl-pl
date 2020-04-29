---
title: Ustawienia domyślne zabezpieczeń Azure Active Directory
description: Domyślne zasady zabezpieczeń, które ułatwiają ochronę organizacji przed typowymi atakami
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f307553a97973d03b0699248373e53e4845aa39a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869915"
---
# <a name="what-are-security-defaults"></a>Co to są wartości domyślne zabezpieczeń?

Zarządzanie zabezpieczeniami może być trudne, gdy typowe ataki związane z tożsamościami stają się coraz bardziej popularne. Ataki te obejmują rozpylanie, odtwarzanie i wyłudzanie informacji.

Domyślne ustawienia zabezpieczeń w programie Azure Active Directory (Azure AD) ułatwiają ochronę organizacji. Wartości domyślne zabezpieczeń zawierają wstępnie skonfigurowane ustawienia zabezpieczeń dla typowych ataków. 

Firma Microsoft udostępnia wartości domyślne zabezpieczeń dla wszystkich użytkowników. Celem jest zapewnienie, że wszystkie organizacje mają podstawowy poziom zabezpieczeń włączony bez dodatkowych kosztów. Ustawienia domyślne zabezpieczeń są włączane w Azure Portal.

![Zrzut ekranu przedstawiający Azure Portal z przełącznikiem umożliwiającym włączenie ustawień domyślnych zabezpieczeń](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> Jeśli dzierżawa została utworzona w dniu lub po 22 października 2019, istnieje możliwość, że masz nowe zachowanie bezpieczne przez domyślne i masz już włączone wartości domyślne zabezpieczeń w dzierżawie. W celu ochrony wszystkich naszych użytkowników domyślnie są wdrażane wszystkie nowo utworzone dzierżawy.

Więcej informacji o tym, dlaczego są udostępniane wartości domyślne zabezpieczeń, można znaleźć w wpisie w blogu Alex Weinert, [wprowadzając wartości domyślne zabezpieczeń](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="unified-multi-factor-authentication-registration"></a>Ujednolicona Rejestracja Multi-Factor Authentication

Wszyscy użytkownicy w dzierżawie muszą zarejestrować się w celu korzystania z uwierzytelniania wieloskładnikowego (MFA) w formie usługi Azure Multi-Factor Authentication. Użytkownicy mają 14 dni na zarejestrowanie Multi-Factor Authentication przy użyciu aplikacji Microsoft Authenticator. Po upływie 14 dni użytkownik nie będzie mógł się zalogować do momentu zakończenia rejestracji Multi-Factor Authentication.

Rozumiemy, że niektórzy użytkownicy mogą znajdować się poza biurem lub nie będą logować się w ciągu 14 dni bezpośrednio po włączeniu ustawień domyślnych zabezpieczeń. Aby zapewnić, że każdy użytkownik ma dużo czasu na zarejestrowanie się w celu Multi-Factor Authentication, okres 14 dni jest unikatowy dla każdego użytkownika. 14-dniowy okres użytkownika rozpoczyna się po pierwszym pomyślnym zalogowaniu interakcyjnym po włączeniu domyślnych ustawień zabezpieczeń.

## <a name="multi-factor-authentication-enforcement"></a>Wymuszanie Multi-Factor Authentication

### <a name="protecting-administrators"></a>Ochrona administratorów

Użytkownicy mający dostęp do kont uprzywilejowanych mają zwiększony dostęp do Twojego środowiska. Ze względu na moc tych kont należy traktować je z uwzględnieniem specjalnych zaopieki. Jedną z typowych metod ulepszania ochrony uprzywilejowanych kont jest wymaganie, aby w celu zalogowania się była silniejsza weryfikacja konta. W usłudze Azure AD możesz uzyskać silniejszą weryfikację konta, wymagając Multi-Factor Authentication.

Po zakończeniu rejestracji w Multi-Factor Authentication zostanie zaakceptowana następująca dziewiąta rola administratora usługi Azure AD do przeprowadzenia dodatkowego uwierzytelniania przy każdym logowaniu:

- Administrator globalny
- Administrator programu SharePoint
- Administrator programu Exchange
- Administrator dostępu warunkowego
- Administrator zabezpieczeń
- Administrator pomocy technicznej lub administrator haseł
- Administrator rozliczeń
- Administrator użytkowników
- Administrator uwierzytelniania

### <a name="protecting-all-users"></a>Ochrona wszystkich użytkowników

Chcemy myśleć, że konta administratorów są jedynymi kontami, które wymagają dodatkowych warstw uwierzytelniania. Administratorzy mają szeroki dostęp do poufnych informacji i mogą wprowadzać zmiany w ustawieniach całej subskrypcji. Osoby atakujące mają do nich możliwość ukierunkowania na użytkowników końcowych. 

Gdy Ci osoby atakujące uzyskali dostęp, mogą zażądać dostępu do informacji uprzywilejowanych w imieniu właściciela oryginalnego konta. Mogą nawet pobrać cały katalog, aby przeprowadzić atak wyłudzania informacji w całej organizacji. 

Jedną z częstych metod ulepszania ochrony dla wszystkich użytkowników jest wymaganie silniejszej formy weryfikacji konta, takiej jak Multi-Factor Authentication, dla wszystkich. Po zakończeniu Multi-Factor Authentication rejestracji użytkownicy będą monitowani o dodatkowe uwierzytelnianie w razie potrzeby.

### <a name="blocking-legacy-authentication"></a>Blokowanie starszego uwierzytelniania

Aby zapewnić użytkownikom łatwy dostęp do aplikacji w chmurze, usługa Azure AD obsługuje różne protokoły uwierzytelniania, w tym starsze uwierzytelnianie. *Starsza wersja uwierzytelniania* to termin, który odwołuje się do żądania uwierzytelnienia wykonanego przez:

- Klienci, którzy nie korzystają z nowoczesnego uwierzytelniania (na przykład klienta pakietu Office 2010).
- Każdy klient korzystający ze starszych protokołów poczty, takich jak IMAP, SMTP lub POP3.

Dzisiaj większość nieżądanych prób logowania pochodzi ze starszego uwierzytelniania. Starsza wersja uwierzytelniania nie obsługuje Multi-Factor Authentication. Nawet jeśli masz zasady Multi-Factor Authentication włączone w katalogu, osoba atakująca może się uwierzytelnić przy użyciu starszego protokołu i Multi-Factor Authentication obejścia. 

Po włączeniu wartości domyślnych zabezpieczeń w dzierżawie zostaną zablokowane wszystkie żądania uwierzytelnienia podejmowane przez starszy protokół. Domyślne ustawienia zabezpieczeń są blokowane Exchange Active Sync uwierzytelniania podstawowego.

> [!WARNING]
> Przed włączeniem ustawień domyślnych zabezpieczeń upewnij się, że administratorzy nie używają starszych protokołów uwierzytelniania. Aby uzyskać więcej informacji, zobacz [jak przenieść się z starszego uwierzytelniania](concept-fundamentals-block-legacy-authentication.md).

### <a name="protecting-privileged-actions"></a>Ochrona uprzywilejowanych akcji

Organizacje korzystają z różnych usług platformy Azure zarządzanych za pomocą interfejsu API Azure Resource Manager, w tym:

- Azure Portal 
- Azure PowerShell 
- Interfejs wiersza polecenia platformy Azure

Używanie Azure Resource Manager do zarządzania usługami to wysoce uprzywilejowana akcja. Azure Resource Manager mogą zmieniać konfiguracje dla całej dzierżawy, takie jak ustawienia usługi i rozliczenia subskrypcji. Uwierzytelnianie wieloskładnikowe jest podatne na różne ataki, takie jak phishing i rozpylanie haseł. 

Ważne jest, aby zweryfikować tożsamość użytkowników, którzy chcą uzyskać dostęp do Azure Resource Manager i konfiguracji aktualizacji. Tożsamość należy zweryfikować, wymagając dodatkowego uwierzytelniania przed zezwoleniem na dostęp.

Po włączeniu wartości domyślnych zabezpieczeń w dzierżawie każdy użytkownik, który uzyskuje dostęp do Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, będzie musiał wykonać dodatkowe uwierzytelnianie. Te zasady mają zastosowanie do wszystkich użytkowników, którzy uzyskują dostęp do Azure Resource Manager, niezależnie od tego, czy są oni administratorem czy użytkownikiem. 

> [!NOTE]
> Dzierżawy usługi Exchange Online w wersji pre-2017 są domyślnie wyłączone. Aby uniknąć możliwości pętli logowania podczas uwierzytelniania za pomocą tych dzierżawców, należy [włączyć nowoczesne uwierzytelnianie](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> Konto synchronizacji Azure AD Connect jest wykluczone z domyślnych ustawień zabezpieczeń i nie zostanie wyświetlony monit o zarejestrowanie się w usłudze lub przeprowadzenie uwierzytelniania wieloskładnikowego. Organizacje nie powinny używać tego konta do innych celów.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Poniższe zagadnienia dodatkowe są związane z wdrażaniem ustawień domyślnych zabezpieczeń dla dzierżawy.

### <a name="authentication-methods"></a>Metody uwierzytelniania

Domyślne ustawienia zabezpieczeń umożliwiają rejestrację i korzystanie z usługi Azure Multi-Factor Authentication **przy użyciu tylko aplikacji Microsoft Authenticator przy użyciu powiadomień**. Dostęp warunkowy umożliwia korzystanie z dowolnej metody uwierzytelniania, która ma zostać włączona przez administratora.

|   | Domyślne ustawienia zabezpieczeń | Dostęp warunkowy |
| --- | --- | --- |
| Powiadomienie za poorednictwem aplikacji mobilnej | X | X |
| Kod weryfikacyjny z aplikacji mobilnej lub tokenu sprzętowego |   | X |
| Wiadomość SMS na telefon |   | X |
| Wywołanie telefonu |   | X |
| Hasła aplikacji |   | X * * |

* * Hasła aplikacji są dostępne tylko w ramach usługi MFA dla poszczególnych użytkowników ze starszymi scenariuszami uwierzytelniania tylko wtedy, gdy są włączone przez administratorów.

### <a name="conditional-access"></a>Dostęp warunkowy

Za pomocą dostępu warunkowego można skonfigurować zasady podobne do domyślnych ustawień zabezpieczeń, ale z większą szczegółowością, w tym wykluczeniami użytkowników, które nie są dostępne w domyślnych ustawieniach zabezpieczeń. Jeśli używasz dostępu warunkowego i w środowisku włączono zasady dostępu warunkowego, nie będą dostępne żadne ustawienia domyślne zabezpieczeń. Jeśli masz licencję, która zapewnia dostęp warunkowy, ale nie masz włączonych zasad dostępu warunkowego w danym środowisku, możesz użyć domyślnych ustawień zabezpieczeń do momentu włączenia zasad dostępu warunkowego. Więcej informacji na temat licencjonowania usługi Azure AD można znaleźć na [stronie cennika usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

![Komunikat ostrzegawczy, który może mieć wartości domyślne zabezpieczeń lub dostęp warunkowy nie zarówno](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Poniżej przedstawiono wskazówki krok po kroku dotyczące konfigurowania równoważnych zasad przy użyciu dostępu warunkowego:

- [Wymaganie uwierzytelniania wieloskładnikowego dla administratorów](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Wymaganie uwierzytelniania wieloskładnikowego do zarządzania platformą Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Blokowanie starszego uwierzytelniania](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Wymaganie uwierzytelniania wieloskładnikowego dla wszystkich użytkowników](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Wymagaj rejestracji w usłudze Azure MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) — wymaga Azure AD Identity Protection

## <a name="enabling-security-defaults"></a>Włączanie ustawień domyślnych zabezpieczeń

Aby włączyć domyślne ustawienia zabezpieczeń w katalogu:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator zabezpieczeń, administrator dostępu warunkowego lub Administrator globalny.
1. Przejdź do **Azure Active Directory** > **Właściwości**.
1. Wybierz pozycję **Zarządzaj ustawieniami domyślnymi zabezpieczeń**.
1. Ustaw opcję **Włącz ustawienia domyślne zabezpieczeń** na **wartość tak**.
1. Wybierz pozycję **Zapisz**.

## <a name="disabling-security-defaults"></a>Wyłączanie ustawień domyślnych zabezpieczeń

Organizacje, które zdecydują się zaimplementować zasady dostępu warunkowego, które zastępują domyślne ustawienia zabezpieczeń, muszą wyłączyć ustawienia domyślne zabezpieczeń. 

![Komunikat ostrzegawczy Wyłącz ustawienia domyślne zabezpieczeń, aby włączyć dostęp warunkowy](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Aby wyłączyć domyślne ustawienia zabezpieczeń w katalogu:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator zabezpieczeń, administrator dostępu warunkowego lub Administrator globalny.
1. Przejdź do **Azure Active Directory** > **Właściwości**.
1. Wybierz pozycję **Zarządzaj ustawieniami domyślnymi zabezpieczeń**.
1. Ustaw opcję **Włącz ustawienia domyślne zabezpieczeń** na wartość **nie**.
1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

[Typowe zasady dostępu warunkowego](../conditional-access/concept-conditional-access-policy-common.md)
