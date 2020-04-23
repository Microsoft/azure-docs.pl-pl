---
title: Domyślne ustawienia zabezpieczeń usługi Azure Active Directory
description: Domyślne zasady zabezpieczeń, które pomagają chronić organizacje przed typowymi atakami
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
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869915"
---
# <a name="what-are-security-defaults"></a>Co to są ustawienia domyślne zabezpieczeń?

Zarządzanie zabezpieczeniami może być trudne, gdy typowe ataki związane z tożsamością stają się coraz bardziej popularne. Ataki te obejmują spray hasłem, powtórkę i phishing.

Domyślne zabezpieczenia w usłudze Azure Active Directory (Azure AD) ułatwiają bezpieczeństwo i pomagają chronić organizację. Domyślne zabezpieczenia zawierają wstępnie skonfigurowane ustawienia zabezpieczeń dla typowych ataków. 

Firma Microsoft udostępnia wszystkim domyślne ustawienia zabezpieczeń. Celem jest zapewnienie, że wszystkie organizacje mają podstawowy poziom zabezpieczeń włączony bez dodatkowych kosztów. Ustawienia domyślne zabezpieczeń można włączyć w witrynie Azure portal.

![Zrzut ekranu przedstawiający witrynę Azure portal z przełącznikiem umożliwiającym włączenie ustawień domyślnych zabezpieczeń](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> Jeśli dzierżawa została utworzona w dniu lub po 22 października 2019 r., możliwe, że występują nowe zachowanie domyślne zabezpieczeń i już mają włączone ustawienia domyślne zabezpieczeń w dzierżawie. Aby chronić wszystkich naszych użytkowników, domyślne zabezpieczenia są wdrażane dla wszystkich nowych utworzonych dzierżaw.

Więcej szczegółów na temat tego, dlaczego zabezpieczenia są udostępniane, można znaleźć w blogu Alex Weinert, [Wprowadzenie ustawień domyślnych zabezpieczeń](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="unified-multi-factor-authentication-registration"></a>Ujednolicona rejestracja uwierzytelniania wieloskładnikowego

Wszyscy użytkownicy w dzierżawie muszą zarejestrować się w celu uwierzytelniania wieloskładnikowego (MFA) w formie usługi Azure Multi-Factor Authentication service. Użytkownicy mają 14 dni na zarejestrowanie się w celu uwierzytelniania wieloskładnikowego przy użyciu aplikacji Microsoft Authenticator. Po upływie 14 dni użytkownik nie będzie mógł się zalogować, dopóki rejestracja uwierzytelniania wieloskładnikowego nie zostanie zakończona.

Rozumiemy, że niektórzy użytkownicy mogą być poza biurem lub nie zalogują się w ciągu 14 dni natychmiast po włączeniu ustawień domyślnych zabezpieczeń. Aby upewnić się, że każdy użytkownik ma wystarczająco dużo czasu na zarejestrowanie się w celu uwierzytelniania wieloskładnikowego, okres 14-dniowy jest unikatowy dla każdego użytkownika. 14-dniowy okres użytkownika rozpoczyna się po pierwszym pomyślnym interaktywnym logowaniach po włączeniu domyślnych ustawień zabezpieczeń.

## <a name="multi-factor-authentication-enforcement"></a>Wymuszanie uwierzytelniania wieloskładnikowego

### <a name="protecting-administrators"></a>Ochrona administratorów

Użytkownicy z dostępem do kont uprzywilejowanych mają zwiększony dostęp do środowiska. Ze względu na moc tych kont, należy traktować je ze szczególną ostrożnością. Jedną z typowych metod poprawy ochrony kont uprzywilejowanych jest wymaganie silniejszej formy weryfikacji konta w celu zalogowania się. W usłudze Azure AD można uzyskać silniejszą weryfikację konta, wymagając uwierzytelniania wieloskładnikowego.

Po zakończeniu rejestracji za pomocą uwierzytelniania wieloskładnikowego do wykonania będzie wymagane dziewięć ról administratora usługi Azure AD, aby wykonać dodatkowe uwierzytelnianie za każdym razem, gdy się zalogują:

- Administrator globalny
- Administrator programu SharePoint
- Administrator programu Exchange
- Administrator dostępu warunkowego
- Administrator zabezpieczeń
- Administrator pomocy technicznej lub administrator haseł
- Administrator rozliczeń
- Administrator użytkownika
- Administrator uwierzytelniania

### <a name="protecting-all-users"></a>Ochrona wszystkich użytkowników

Mamy tendencję do myślenia, że konta administratora są jedynymi kontami, które wymagają dodatkowych warstw uwierzytelniania. Administratorzy mają szeroki dostęp do poufnych informacji i mogą wprowadzać zmiany w ustawieniach w całej subskrypcji. Ale atakujący mają tendencję do kierowania użytkowników końcowych. 

Po uzyskaniu dostępu przez osoby atakujące mogą zażądać dostępu do uprzywilejowanych informacji w imieniu pierwotnego właściciela konta. Mogą nawet pobrać cały katalog, aby przeprowadzić atak phishingowy na całą organizację. 

Jedną z typowych metod poprawy ochrony dla wszystkich użytkowników jest wymaganie silniejszej formy weryfikacji konta, takiej jak uwierzytelnianie wieloskładnikowe, dla wszystkich. Po zakończeniu rejestracji uwierzytelniania wieloskładnikowego zostaną poproszeni o dodatkowe uwierzytelnienie w razie potrzeby.

### <a name="blocking-legacy-authentication"></a>Blokowanie uwierzytelniania starszego

Aby zapewnić użytkownikom łatwy dostęp do aplikacji w chmurze, usługa Azure AD obsługuje wiele protokołów uwierzytelniania, w tym uwierzytelnianie starsze. *Uwierzytelnianie starsze* to termin, który odnosi się do żądania uwierzytelnienia złożonego przez:

- Klienci, którzy nie korzystają z nowoczesnego uwierzytelniania (na przykład klienta pakietu Office 2010).
- Każdy klient korzystający ze starszych protokołów poczty, takich jak IMAP, SMTP lub POP3.

Obecnie większość prób logowania kompromitujących pochodzi ze starszego uwierzytelniania. Uwierzytelnianie starsze nie obsługuje uwierzytelniania wieloskładnikowego. Nawet jeśli w katalogu włączono zasady uwierzytelniania wieloskładnikowego, osoba atakująca może uwierzytelnić się przy użyciu starszego protokołu i pominąć uwierzytelnianie wieloskładnikowe. 

Po włączeniu ustawień domyślnych zabezpieczeń w dzierżawie wszystkie żądania uwierzytelniania wykonane przez starszy protokół zostaną zablokowane. Domyślne zabezpieczenia blokują podstawowe uwierzytelnianie programu Exchange Active Sync.

> [!WARNING]
> Przed włączeniem ustawień domyślnych zabezpieczeń upewnij się, że administratorzy nie używają starszych protokołów uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Jak odejść od starszego uwierzytelniania](concept-fundamentals-block-legacy-authentication.md).

### <a name="protecting-privileged-actions"></a>Ochrona akcji uprzywilejowanych

Organizacje korzystają z różnych usług platformy Azure zarządzanych za pośrednictwem interfejsu API usługi Azure Resource Manager, w tym:

- Azure Portal 
- Azure PowerShell 
- Interfejs wiersza polecenia platformy Azure

Używanie usługi Azure Resource Manager do zarządzania usługami jest bardzo uprzywilejowaną akcją. Usługa Azure Resource Manager może zmieniać konfiguracje obejmujące całą dzierżawę, takie jak ustawienia usługi i rozliczenia subskrypcji. Uwierzytelnianie jednoskładnikowe jest narażone na różne ataki, takie jak phishing i spray do haseł. 

Ważne jest, aby zweryfikować tożsamość użytkowników, którzy chcą uzyskać dostęp do usługi Azure Resource Manager i zaktualizować konfiguracje. Weryfikujesz ich tożsamość, wymagając dodatkowego uwierzytelniania przed zezwoleniem na dostęp.

Po włączeniu domyślnych zabezpieczeń w dzierżawie każdy użytkownik, który uzyskuje dostęp do witryny Azure portal, azure powershell lub interfejsu wiersza polecenia platformy Azure, będzie musiał wykonać dodatkowe uwierzytelnianie. Ta zasada ma zastosowanie do wszystkich użytkowników, którzy uzyskują dostęp do usługi Azure Resource Manager, niezależnie od tego, czy są administratorem, czy użytkownikiem. 

> [!NOTE]
> Przed 2017 r. dzierżawcy usługi Exchange Online domyślnie mają nowoczesne uwierzytelnianie wyłączone. Aby uniknąć możliwości pętli logowania podczas uwierzytelniania za pośrednictwem tych dzierżaw, należy [włączyć nowoczesne uwierzytelnianie.](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)

> [!NOTE]
> Konto synchronizacji usługi Azure AD Connect jest wykluczone z domyślnych zabezpieczeń i nie zostanie wyświetlona monit o zarejestrowanie się lub wykonanie uwierzytelniania wieloskładnikowego. Organizacje nie powinny używać tego konta do innych celów.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Następujące dodatkowe zagadnienia są związane z wdrażaniem domyślnych zabezpieczeń dla dzierżawy.

### <a name="authentication-methods"></a>Metody uwierzytelniania

Domyślne zabezpieczenia zezwalają na rejestrację i korzystanie z uwierzytelniania wieloskładnikowego platformy Azure **przy użyciu tylko aplikacji Microsoft Authenticator przy użyciu powiadomień**. Dostęp warunkowy umożliwia użycie dowolnej metody uwierzytelniania, która jest łączona przez administratora.

|   | Domyślne ustawienia zabezpieczeń | Dostęp warunkowy |
| --- | --- | --- |
| Powiadomienie za pośrednictwem aplikacji mobilnej | X | X |
| Kod weryfikacyjny z aplikacji mobilnej lub tokenu sprzętowego |   | X |
| Wiadomość SMS na telefon |   | X |
| Zadzwoń na telefon |   | X |
| Hasła aplikacji |   | X** |

** Hasła aplikacji są dostępne tylko w u administratorów usług mfa dla użytkowników ze starszymi scenariuszami uwierzytelniania tylko wtedy, gdy są włączone przez administratorów.

### <a name="conditional-access"></a>Dostęp warunkowy

Za pomocą dostępu warunkowego można skonfigurować zasady podobne do domyślnych zabezpieczeń, ale z większą szczegółowością, w tym wykluczenia użytkowników, które nie są dostępne w ustawieniach domyślnych zabezpieczeń. Jeśli używasz dostępu warunkowego i masz włączone zasady dostępu warunkowego w swoim środowisku, domyślne ustawienia zabezpieczeń nie będą dostępne. Jeśli masz licencję, która zapewnia dostęp warunkowy, ale nie masz włączonych żadnych zasad dostępu warunkowego w danym środowisku, możesz używać domyślnych zabezpieczeń, dopóki nie włączysz zasad dostępu warunkowego. Więcej informacji na temat licencjonowania usługi Azure AD można znaleźć na [stronie cennika usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

![Komunikat ostrzegawczy, że mogą mieć ustawienia domyślne zabezpieczeń lub dostęp warunkowy nie oba](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Poniżej przedstawiono przewodniki krok po kroku dotyczące konfigurowania równoważnych zasad za pomocą dostępu warunkowego:

- [Wymaganie uwierzytelniania wieloskładnikowego dla administratorów](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Wymaganie uwierzytelniania wieloskładnikowego do zarządzania platformą Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Blokowanie starszego uwierzytelniania](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Wymaganie uwierzytelniania wieloskładnikowego dla wszystkich użytkowników](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Wymagaj rejestracji usługi Azure MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) — wymaga ochrony tożsamości usługi Azure AD

## <a name="enabling-security-defaults"></a>Włączanie ustawień domyślnych zabezpieczeń

Aby włączyć domyślne zabezpieczenia w katalogu:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator zabezpieczeń, administrator dostępu warunkowego lub administrator globalny.
1. Przejdź do **właściwości usługi Azure Active Directory** > **Properties**.
1. Wybierz pozycję **Zarządzaj ustawieniami domyślnymi zabezpieczeń**.
1. Ustaw **przełącznik Włącz domyślne zabezpieczenia** na **Tak**.
1. Wybierz pozycję **Zapisz**.

## <a name="disabling-security-defaults"></a>Wyłączanie ustawień domyślnych zabezpieczeń

Organizacje, które zdecydują się zaimplementować zasady dostępu warunkowego, które zastępują domyślne zabezpieczenia, muszą wyłączyć domyślne zabezpieczenia. 

![Komunikat ostrzegawczy wyłącz ustawienia domyślne zabezpieczeń, aby włączyć dostęp warunkowy](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Aby wyłączyć ustawienia domyślne zabezpieczeń w katalogu:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator zabezpieczeń, administrator dostępu warunkowego lub administrator globalny.
1. Przejdź do **właściwości usługi Azure Active Directory** > **Properties**.
1. Wybierz pozycję **Zarządzaj ustawieniami domyślnymi zabezpieczeń**.
1. Ustaw **przełącznik Włącz domyślne zabezpieczenia** na **Nie**.
1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

[Typowe zasady dostępu warunkowego](../conditional-access/concept-conditional-access-policy-common.md)
