---
title: Ustawienia domyślne zabezpieczeń Azure Active Directory
description: Domyślne zasady zabezpieczeń, które ułatwiają ochronę organizacji przed typowymi atakami w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: c26cbf55c1e3883605d4c65659511af20cf02c7f
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836771"
---
# <a name="what-are-security-defaults"></a>Co to są wartości domyślne zabezpieczeń?

Zarządzanie zabezpieczeniami może być trudne z typowymi atakami związanymi z tożsamościami, takimi jak rozpylanie, odtwarzanie i wyłudzanie informacji. Ustawienia domyślne zabezpieczeń ułatwiają ochronę organizacji przed atakami ze wstępnie skonfigurowanymi ustawieniami zabezpieczeń:

- Wymaganie, aby wszyscy użytkownicy rejestrowali się w usłudze Azure AD Multi-Factor Authentication.
- Wymaganie od administratorów wykonywania uwierzytelniania wieloskładnikowego.
- Blokowanie starszych protokołów uwierzytelniania.
- Wymaganie od użytkowników uwierzytelniania wieloskładnikowego w razie potrzeby.
- Ochrona uprzywilejowanych działań, takich jak dostęp do Azure Portal.

![Zrzut ekranu przedstawiający Azure Portal z przełącznikiem umożliwiającym włączenie ustawień domyślnych zabezpieczeń](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
Więcej informacji o tym, dlaczego są udostępniane wartości domyślne zabezpieczeń, można znaleźć w wpisie w blogu Alex Weinert, [wprowadzając wartości domyślne zabezpieczeń](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="availability"></a>Dostępność

Firma Microsoft udostępnia wartości domyślne zabezpieczeń dla wszystkich użytkowników. Celem jest zapewnienie, że wszystkie organizacje mają podstawowy poziom zabezpieczeń włączony bez dodatkowych kosztów. Ustawienia domyślne zabezpieczeń są włączane w Azure Portal. Jeśli dzierżawa została utworzona w dniu 22 października 2019 lub później, możliwe, że wartości domyślne zabezpieczeń są już włączone w dzierżawie. W celu ochrony wszystkich naszych użytkowników domyślnie są wdrażane wszystkie nowo utworzone dzierżawy.

### <a name="whos-it-for"></a>Dla kogo jest?

- Jeśli jesteś organizacją, która chce zwiększyć stan bezpieczeństwa, ale nie wiesz, jak i gdzie zacząć, domyślnie są używane zabezpieczenia.
- Jeśli jesteś organizacją korzystającą z bezpłatnej usługi Azure Active Directory Licencjonowanie, domyślne ustawienia zabezpieczeń są dla Ciebie odpowiednie.

### <a name="who-should-use-conditional-access"></a>Kto powinien korzystać z dostępu warunkowego?

- Jeśli jesteś organizacją, która korzysta obecnie z zasad dostępu warunkowego, aby zapewnić sygnały ze sobą, aby podejmować decyzje i wymuszać zasady organizacyjne, domyślne ustawienia zabezpieczeń prawdopodobnie nie są odpowiednie. 
- Jeśli jesteś organizacją z licencjami na Azure Active Directory — wersja Premium, domyślne ustawienia zabezpieczeń prawdopodobnie nie są odpowiednie.
- Jeśli organizacja ma złożone wymagania dotyczące zabezpieczeń, należy wziąć pod uwagę dostęp warunkowy.

## <a name="policies-enforced"></a>Wymuszone zasady

### <a name="unified-multi-factor-authentication-registration"></a>Ujednolicona Rejestracja Multi-Factor Authentication

Wszyscy użytkownicy w dzierżawie muszą zarejestrować się w celu korzystania z uwierzytelniania wieloskładnikowego (MFA) w formie Multi-Factor Authentication usługi Azure AD. Użytkownicy mają 14 dni na rejestrację w usłudze Azure AD Multi-Factor Authentication przy użyciu aplikacji Microsoft Authenticator. Po upływie 14 dni użytkownik nie będzie mógł się zalogować, dopóki Rejestracja nie zostanie zakończona. 14-dniowy okres użytkownika rozpoczyna się po pierwszym pomyślnym zalogowaniu interakcyjnym po włączeniu ustawień domyślnych zabezpieczeń.

### <a name="protecting-administrators"></a>Ochrona administratorów

Użytkownicy z uprzywilejowanym dostępem mają zwiększony dostęp do Twojego środowiska. Ze względu na moc tych kont należy traktować je z uwzględnieniem specjalnych zaopieki. Jedną z typowych metod ulepszania ochrony uprzywilejowanych kont jest wymaganie, aby w celu zalogowania się była silniejsza weryfikacja konta. W usłudze Azure AD możesz uzyskać silniejszą weryfikację konta, wymagając uwierzytelniania wieloskładnikowego.

Po zakończeniu rejestracji w usłudze Azure AD Multi-Factor Authentication do przeprowadzenia dodatkowego uwierzytelniania przy każdym logowaniu będzie wymagane następujące dziewięć ról administratora usługi Azure AD:

- Administrator globalny
- Administrator programu SharePoint
- Administrator programu Exchange
- Administrator dostępu warunkowego
- Administrator zabezpieczeń
- Administrator pomocy technicznej
- Administrator rozliczeń
- Administrator użytkowników
- Administrator uwierzytelniania

### <a name="protecting-all-users"></a>Ochrona wszystkich użytkowników

Chcemy myśleć, że konta administratorów są jedynymi kontami, które wymagają dodatkowych warstw uwierzytelniania. Administratorzy mają szeroki dostęp do poufnych informacji i mogą wprowadzać zmiany w ustawieniach całej subskrypcji. Jednak osoby atakujące często kierują użytkowników końcowych. 

Gdy Ci osoby atakujące uzyskali dostęp, mogą zażądać dostępu do informacji uprzywilejowanych w imieniu właściciela oryginalnego konta. Mogą nawet pobrać cały katalog, aby przeprowadzić atak wyłudzania informacji w całej organizacji. 

Jedną z częstych metod ulepszania ochrony dla wszystkich użytkowników jest wymaganie silniejszej formy weryfikacji konta, takiej jak Multi-Factor Authentication, dla wszystkich. Po zakończeniu Multi-Factor Authentication rejestracji użytkownicy będą monitowani o dodatkowe uwierzytelnianie w razie potrzeby. Ta funkcja chroni wszystkie aplikacje zarejestrowane w usłudze Azure AD, w tym aplikacje SaaS.

### <a name="blocking-legacy-authentication"></a>Blokowanie starszego uwierzytelniania

Aby zapewnić użytkownikom łatwy dostęp do aplikacji w chmurze, usługa Azure AD obsługuje różne protokoły uwierzytelniania, w tym starsze uwierzytelnianie. *Starsza wersja uwierzytelniania* to termin, który odwołuje się do żądania uwierzytelnienia wykonanego przez:

- Klienci, którzy nie korzystają z nowoczesnego uwierzytelniania (na przykład klienta pakietu Office 2010).
- Każdy klient korzystający ze starszych protokołów poczty, takich jak IMAP, SMTP lub POP3.

Dzisiaj większość nieżądanych prób logowania pochodzi ze starszego uwierzytelniania. Starsza wersja uwierzytelniania nie obsługuje Multi-Factor Authentication. Nawet jeśli masz zasady Multi-Factor Authentication włączone w katalogu, osoba atakująca może się uwierzytelnić przy użyciu starszego protokołu i Multi-Factor Authentication obejścia. 

Po włączeniu wartości domyślnych zabezpieczeń w dzierżawie zostaną zablokowane wszystkie żądania uwierzytelnienia podejmowane przez starszy protokół. Domyślne ustawienia zabezpieczeń są blokowane Exchange Active Sync uwierzytelniania podstawowego.

> [!WARNING]
> Przed włączeniem ustawień domyślnych zabezpieczeń upewnij się, że administratorzy nie używają starszych protokołów uwierzytelniania. Aby uzyskać więcej informacji, zobacz [jak przenieść się z starszego uwierzytelniania](concept-fundamentals-block-legacy-authentication.md).

- [Jak skonfigurować urządzenie wielofunkcyjne lub aplikację do wysyłania wiadomości e-mail przy użyciu Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>Ochrona uprzywilejowanych akcji

Organizacje korzystają z różnych usług platformy Azure zarządzanych za pomocą interfejsu API Azure Resource Manager, w tym:

- Azure Portal 
- Azure PowerShell 
- Interfejs wiersza polecenia platformy Azure

Używanie Azure Resource Manager do zarządzania usługami to wysoce uprzywilejowana akcja. Azure Resource Manager mogą zmieniać konfiguracje dla całej dzierżawy, takie jak ustawienia usługi i rozliczenia subskrypcji. Uwierzytelnianie wieloskładnikowe jest podatne na różne ataki, takie jak phishing i rozpylanie haseł. 

Ważne jest, aby zweryfikować tożsamość użytkowników, którzy chcą uzyskać dostęp do Azure Resource Manager i konfiguracji aktualizacji. Tożsamość należy zweryfikować, wymagając dodatkowego uwierzytelniania przed zezwoleniem na dostęp.

Po włączeniu wartości domyślnych zabezpieczeń w dzierżawie każdy użytkownik, który uzyskuje dostęp do Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, będzie musiał wykonać dodatkowe uwierzytelnianie. Te zasady mają zastosowanie do wszystkich użytkowników, którzy uzyskują dostęp do Azure Resource Manager, niezależnie od tego, czy są oni administratorem czy użytkownikiem. 

> [!NOTE]
> Dzierżawy usługi Exchange Online w wersji pre-2017 są domyślnie wyłączone. Aby uniknąć możliwości pętli logowania podczas uwierzytelniania za pomocą tych dzierżawców, należy [włączyć nowoczesne uwierzytelnianie](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> Konto synchronizacji Azure AD Connect jest wykluczone z domyślnych ustawień zabezpieczeń i nie zostanie wyświetlony monit o zarejestrowanie się w usłudze lub przeprowadzenie uwierzytelniania wieloskładnikowego. Organizacje nie powinny używać tego konta do innych celów.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Poniższe zagadnienia dodatkowe są związane z wdrażaniem ustawień domyślnych zabezpieczeń.

### <a name="authentication-methods"></a>Metody uwierzytelniania

Te bezpłatne wartości domyślne zabezpieczeń umożliwiają rejestrację i korzystanie z usługi Azure AD Multi-Factor Authentication **przy użyciu tylko aplikacji Microsoft Authenticator przy użyciu powiadomień**. Dostęp warunkowy umożliwia korzystanie z dowolnej metody uwierzytelniania, która ma zostać włączona przez administratora.

| Metoda | Domyślne ustawienia zabezpieczeń | Dostęp warunkowy |
| --- | --- | --- |
| Powiadomienie za poorednictwem aplikacji mobilnej | X | X |
| Kod weryfikacyjny z aplikacji mobilnej lub tokenu sprzętowego | X * * | X |
| Wiadomość SMS na telefon |   | X |
| Wywołanie telefonu |   | X |
| Hasła aplikacji |   | X * * _ |

- _ * Użytkownicy mogą używać kodów weryfikacyjnych z aplikacji Microsoft Authenticator, ale mogą się rejestrować tylko przy użyciu opcji powiadomień.
- * * Hasła aplikacji są dostępne tylko w ramach usługi MFA dla poszczególnych użytkowników ze starszymi scenariuszami uwierzytelniania tylko wtedy, gdy są włączone przez administratorów.

### <a name="disabled-mfa-status"></a>Stan wyłączonej usługi MFA

Jeśli Twoja organizacja jest poprzednim użytkownikiem opartym na użytkownikach usługi Azure AD Multi-Factor Authentication, nie musisz otrzymywać alertów, aby nie wyświetlać użytkowników w stanie "_ *Enabled**" lub **wymuszać** się na stronie stanu usługi uwierzytelniania wieloskładnikowego. **Wyłączone** to odpowiedni stan dla użytkowników korzystających z domyślnych ustawień zabezpieczeń lub Multi-Factor Authentication usługi Azure AD opartych na dostępie warunkowym.

### <a name="conditional-access"></a>Dostęp warunkowy

Za pomocą dostępu warunkowego można skonfigurować zasady podobne do domyślnych ustawień zabezpieczeń, ale z większą szczegółowością, w tym wykluczeniami użytkowników, które nie są dostępne w domyślnych ustawieniach zabezpieczeń. Jeśli używasz dostępu warunkowego i w środowisku włączono zasady dostępu warunkowego, nie będą dostępne żadne ustawienia domyślne zabezpieczeń. Jeśli masz licencję, która zapewnia dostęp warunkowy, ale nie masz włączonych zasad dostępu warunkowego w danym środowisku, możesz użyć domyślnych ustawień zabezpieczeń do momentu włączenia zasad dostępu warunkowego. Więcej informacji na temat licencjonowania usługi Azure AD można znaleźć na [stronie cennika usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

![Komunikat ostrzegawczy, który może mieć wartości domyślne zabezpieczeń lub dostęp warunkowy nie zarówno](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Poniżej przedstawiono przewodnik krok po kroku dotyczący sposobu konfigurowania równoważnych zasad przy użyciu dostępu warunkowego dla tych zasad włączonych przez domyślne ustawienia zabezpieczeń:

- [Wymaganie uwierzytelniania wieloskładnikowego dla administratorów](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Wymaganie uwierzytelniania wieloskładnikowego do zarządzania platformą Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Blokowanie starszego uwierzytelniania](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Wymaganie uwierzytelniania wieloskładnikowego dla wszystkich użytkowników](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Wymagaj rejestracji w usłudze Azure AD MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) — wymaga Azure AD Identity Protection części Azure AD — wersja Premium P2.

## <a name="enabling-security-defaults"></a>Włączanie ustawień domyślnych zabezpieczeń

Aby włączyć domyślne ustawienia zabezpieczeń w katalogu:

1. Zaloguj się do [Azure Portal](https://portal.azure.com)   jako administrator zabezpieczeń, administrator dostępu warunkowego lub Administrator globalny.
1. Przejdź do **Azure Active Directory**   >  **Właściwości**.
1. Wybierz pozycję **Zarządzaj ustawieniami domyślnymi zabezpieczeń**.
1. Ustaw opcję **Włącz ustawienia domyślne zabezpieczeń** na **wartość tak**.
1. Wybierz pozycję **Zapisz**.

## <a name="disabling-security-defaults"></a>Wyłączanie ustawień domyślnych zabezpieczeń

Organizacje, które zdecydują się zaimplementować zasady dostępu warunkowego, które zastępują domyślne ustawienia zabezpieczeń, muszą wyłączyć ustawienia domyślne zabezpieczeń. 

![Komunikat ostrzegawczy Wyłącz ustawienia domyślne zabezpieczeń, aby włączyć dostęp warunkowy](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Aby wyłączyć domyślne ustawienia zabezpieczeń w katalogu:

1. Zaloguj się do [Azure Portal](https://portal.azure.com)   jako administrator zabezpieczeń, administrator dostępu warunkowego lub Administrator globalny.
1. Przejdź do **Azure Active Directory**   >  **Właściwości**.
1. Wybierz pozycję **Zarządzaj ustawieniami domyślnymi zabezpieczeń**.
1. Ustaw opcję **Włącz ustawienia domyślne zabezpieczeń** na wartość **nie**.
1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

[Typowe zasady dostępu warunkowego](../conditional-access/concept-conditional-access-policy-common.md)