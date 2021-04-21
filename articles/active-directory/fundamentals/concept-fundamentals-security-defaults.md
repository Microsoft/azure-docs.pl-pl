---
title: Azure Active Directory domyślne zabezpieczeń
description: Domyślne zasady zabezpieczeń, które pomagają chronić organizacje przed powszechnymi atakami w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: efa88e1be5c5df5dd09cb5a97c8ece352496ccdb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769701"
---
# <a name="what-are-security-defaults"></a>Co to są wartości domyślne zabezpieczeń?

Zarządzanie zabezpieczeniami może być trudne, gdy popularne mogą być typowe ataki związane z tożsamościami, takie jak rozsyłanie haseł, powtarzanie i wyłudzanie informacji. Ustawienia domyślne zabezpieczeń ułatwiają ochronę organizacji przed tymi atakami przy użyciu wstępnie skonfigurowanych ustawień zabezpieczeń:

- Wymaganie od wszystkich użytkowników zarejestrowania się w usłudze Azure AD Multi-Factor Authentication.
- Wymaganie od administratorów wykonania uwierzytelniania wieloskładnikowego.
- Blokowanie starszych protokołów uwierzytelniania.
- Wymaganie od użytkowników wykonania uwierzytelniania wieloskładnikowego w razie potrzeby.
- Ochrona uprzywilejowanych działań, takich jak dostęp do Azure Portal.

![Zrzut ekranu przedstawiający Azure Portal z przełącznikiem umożliwiającym włączenie ustawień domyślnych zabezpieczeń](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
Więcej szczegółów na temat tego, dlaczego są udostępniane ustawienia domyślne zabezpieczeń, można znaleźć we wpisie w blogu Alexa Weinerta [Introducing security defaults](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)(Wprowadzenie do domyślnych ustawień zabezpieczeń).

## <a name="availability"></a>Dostępność

Firma Microsoft domyślnie ujedna zabezpieczenia jest dostępna dla wszystkich użytkowników. Celem jest zapewnienie, że wszystkie organizacje mają włączony podstawowy poziom zabezpieczeń bez dodatkowych kosztów. Domyślne ustawienia zabezpieczeń są włączane w Azure Portal. Jeśli dzierżawa została utworzona 22 października 2019 r. lub później, możliwe, że domyślne ustawienia zabezpieczeń są już włączone w dzierżawie. Aby chronić wszystkich naszych użytkowników, wartości domyślne zabezpieczeń są wycofywowane dla wszystkich nowo utworzonych dzierżaw.

### <a name="whos-it-for"></a>Dla kogo to jest?

- Jeśli jesteś organizacją, która chce zwiększyć poziom zabezpieczeń, ale nie wiesz, jak i od czego zacząć, wartości domyślne zabezpieczeń są dla Ciebie.
- Jeśli jesteś organizacją korzystającą z warstwy bezpłatna usługi Azure Active Directory licencjonowania, domyślne ustawienia zabezpieczeń są dla Ciebie.

### <a name="who-should-use-conditional-access"></a>Kto powinien używać dostępu warunkowego?

- Jeśli obecnie używasz zasad dostępu warunkowego do zsyłania sygnałów w celu podejmowania decyzji i wymuszania zasad organizacji, ustawienia domyślne zabezpieczeń prawdopodobnie nie będą odpowiednie dla Ciebie. 
- Jeśli jesteś organizacją z licencjami Azure Active Directory — wersja Premium, ustawienia domyślne zabezpieczeń prawdopodobnie nie będą odpowiednie dla Ciebie.
- Jeśli Twoja organizacja ma złożone wymagania dotyczące zabezpieczeń, należy rozważyć dostęp warunkowy.

## <a name="policies-enforced"></a>Wymuszane zasady

### <a name="unified-multi-factor-authentication-registration"></a>Rejestracja ujednoliconego uwierzytelniania wieloskładnikowego

Wszyscy użytkownicy w dzierżawie muszą zarejestrować się w celu uwierzytelniania wieloskładnikowego (MFA) w formie usługi Azure AD Multi-Factor Authentication. Użytkownicy mają 14 dni na zarejestrowanie się w usłudze Azure AD Multi-Factor Authentication przy użyciu Microsoft Authenticator aplikacji. Po upływie tych 14 dni użytkownik nie będzie mógł się zalogować do momentu ukończenia rejestracji. 14-dniowy okres użytkownika rozpoczyna się po pierwszym pomyślnym zalogowaniu interakcyjnym po włączeniu ustawień domyślnych zabezpieczeń.

### <a name="protecting-administrators"></a>Ochrona administratorów

Użytkownicy z uprzywilejowanym dostępem mają zwiększony dostęp do Twojego środowiska. Ze względu na możliwości tych kont należy traktować je ze szczególną ostrożnością. Jedną z typowych metod poprawy ochrony kont uprzywilejowanych jest wymaganie silniejszej formy weryfikacji konta podczas logowania. W usłudze Azure AD można uzyskać silniejszą weryfikację konta, wymagając uwierzytelniania wieloskładnikowego.

Po zakończeniu rejestracji w usłudze Azure AD Multi-Factor Authentication do przeprowadzenia dodatkowego uwierzytelniania będzie wymagane dziewięć następujących ról administratora usługi Azure AD po każdym zalogowaniu:

- Administrator globalny
- Administrator programu SharePoint
- Administrator programu Exchange
- Administrator dostępu warunkowego
- Administrator zabezpieczeń
- administrator pomocy technicznej
- Administrator rozliczeń
- Administrator użytkowników
- administrator uwierzytelniania

> [!WARNING]
> Upewnij się, że katalog ma co najmniej dwa konta z przypisanymi do nich uprawnieniami administratora globalnego. Pomoże to w przypadku zablokowania jednego administratora globalnego. Aby uzyskać więcej informacji, zobacz artykuł [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD.](../roles/security-emergency-access.md)

### <a name="protecting-all-users"></a>Ochrona wszystkich użytkowników

Zwykle uważamy, że konta administratorów są jedynymi kontami, które wymagają dodatkowych warstw uwierzytelniania. Administratorzy mają szeroki dostęp do poufnych informacji i mogą wprowadzać zmiany w ustawieniach w całej subskrypcji. Jednak osoby atakujące często są celem użytkowników końcowych. 

Po tym, jak osoby atakujące uzyskają dostęp, mogą zażądać dostępu do uprzywilejowanych informacji w imieniu oryginalnego właściciela konta. Mogą nawet pobrać cały katalog, aby przeprowadzić atak wyłudzania informacji na całą organizację. 

Jedną z typowych metod poprawy ochrony dla wszystkich użytkowników jest wymaganie silniejszej formy weryfikacji konta, takiej jak uwierzytelnianie wieloskładnikowe, dla wszystkich użytkowników. Gdy użytkownicy ukończą rejestrację usługi Multi-Factor Authentication, w razie potrzeby zostanie wyświetlony monit o dodatkowe uwierzytelnianie. Monit będzie wyświetlany głównie podczas uwierzytelniania przy użyciu nowego urządzenia lub aplikacji albo podczas wykonywania krytycznych ról i zadań. Ta funkcja chroni wszystkie aplikacje zarejestrowane w usłudze Azure AD, w tym aplikacje SaaS.

### <a name="blocking-legacy-authentication"></a>Blokowanie starszego uwierzytelniania

Aby zapewnić użytkownikom łatwy dostęp do aplikacji w chmurze, usługa Azure AD obsługuje różne protokoły uwierzytelniania, w tym starsze uwierzytelnianie. *Starsze uwierzytelnianie* to termin, który odnosi się do żądania uwierzytelniania wykonanego przez:

- Klienci, którzy nie korzystają z nowoczesnego uwierzytelniania (na przykład klienta pakietu Office 2010).
- Każdy klient, który używa starszych protokołów poczty, takich jak IMAP, SMTP lub POP3.

Obecnie większość prób logowania jest naruszeniem starszego uwierzytelniania. Starsze uwierzytelnianie nie obsługuje uwierzytelniania wieloskładnikowego. Nawet jeśli w katalogu są włączone zasady usługi Multi-Factor Authentication, osoba atakująca może uwierzytelnić się przy użyciu starszego protokołu i pominąć usługę Multi-Factor Authentication. 

Po włączeniu ustawień domyślnych zabezpieczeń w dzierżawie wszystkie żądania uwierzytelniania wykonane przez starszy protokół zostaną zablokowane. Ustawienia domyślne zabezpieczeń blok Exchange Active Sync uwierzytelniania podstawowego.

> [!WARNING]
> Przed włączeniem ustawień domyślnych zabezpieczeń upewnij się, że administratorzy nie korzystali ze starszych protokołów uwierzytelniania. Aby uzyskać więcej informacji, [zobacz Jak odejść od starszego uwierzytelniania.](concept-fundamentals-block-legacy-authentication.md)

- [Jak skonfigurować urządzenie wielofunkcyjne lub aplikację do wysyłania wiadomości e-mail przy użyciu Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>Ochrona uprzywilejowanych akcji

Organizacje korzystają z różnych usług platformy Azure zarządzanych za pośrednictwem Azure Resource Manager API, w tym:

- Azure Portal 
- Azure PowerShell 
- Interfejs wiersza polecenia platformy Azure

Używanie Azure Resource Manager do zarządzania usługami jest akcją o wysokim poziomie uprawnień. Azure Resource Manager może zmieniać konfiguracje dla całej dzierżawy, takie jak ustawienia usługi i rozliczenia subskrypcji. Uwierzytelnianie jednoskładnikowe jest narażone na różne ataki, takie jak wyłudzanie informacji i ataki na hasła. 

Ważne jest, aby zweryfikować tożsamość użytkowników, którzy chcą uzyskać dostęp do Azure Resource Manager i aktualizowania konfiguracji. Aby zweryfikować ich tożsamość, należy wymagać dodatkowego uwierzytelniania przed umożliwieniem dostępu.

Po włączeniu wartości domyślnych zabezpieczeń w dzierżawie każdy użytkownik, który uzyskuje dostęp do usługi Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, będzie musiał wykonać dodatkowe uwierzytelnianie. Te zasady mają zastosowanie do wszystkich użytkowników, którzy Azure Resource Manager dostęp do aplikacji, bez względu na to, czy są administratorem, czy użytkownikiem. 

> [!NOTE]
> Dzierżawy usługi Exchange Online przed 2017 r. mają domyślnie wyłączone nowoczesne uwierzytelnianie. Aby uniknąć możliwości pętli logowania podczas uwierzytelniania za pośrednictwem tych dzierżaw, należy włączyć [nowoczesne uwierzytelnianie](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> Konto Azure AD Connect jest wykluczone z ustawień domyślnych zabezpieczeń i nie będzie wyświetlany monit o zarejestrowanie się w celu przeprowadzenia uwierzytelniania wieloskładnikowego. Organizacje nie powinny używać tego konta do innych celów.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Następujące dodatkowe zagadnienia są związane z wdrażaniem ustawień domyślnych zabezpieczeń.

### <a name="authentication-methods"></a>Metody uwierzytelniania

Te bezpłatne ustawienia domyślne zabezpieczeń umożliwiają rejestrację i korzystanie z usługi Azure AD Multi-Factor Authentication tylko **przy użyciu aplikacji Microsoft Authenticator przy użyciu powiadomień.** Dostęp warunkowy umożliwia korzystanie z dowolnej metody uwierzytelniania, która ma być włączana przez administratora.

| Metoda | Domyślne ustawienia zabezpieczeń | Dostęp warunkowy |
| --- | --- | --- |
| Powiadomienie za pośrednictwem aplikacji mobilnej | X | X |
| Kod weryfikacyjny z aplikacji mobilnej lub tokenu sprzętowego | X** | X |
| Wiadomość SMS na telefon |   | X |
| Połączenie telefoniczne |   | X |
| Hasła aplikacji |   | X× |

- ** Użytkownicy mogą używać kodów weryfikacyjnych z Microsoft Authenticator, ale mogą się rejestrować tylko przy użyciu opcji powiadomienia.
- Hasła aplikacji są dostępne tylko w przypadku uwierzytelniania wieloskładnikowego na użytkownika ze starszymi scenariuszami uwierzytelniania tylko wtedy, gdy są włączone przez administratorów.

### <a name="disabled-mfa-status"></a>Wyłączony stan uwierzytelniania wieloskładnikowego

Jeśli Twoja organizacja jest poprzednim użytkownikiem usługi Azure AD Multi-Factor Authentication opartej na użytkowniku,  nie należy się niepokoić, że użytkownicy nie będą widzieć użytkowników w stanie Włączone lub Wymuszone, jeśli spojrzysz na stronę stanu uwierzytelniania wieloskładnikowego.  **Wyłączone** to odpowiedni stan dla użytkowników korzystających z ustawień domyślnych zabezpieczeń lub usługi Azure AD Multi-Factor Authentication opartej na dostępie warunkowym.

### <a name="conditional-access"></a>Dostęp warunkowy

Za pomocą dostępu warunkowego można konfigurować zasady podobne do domyślnych ustawień zabezpieczeń, ale z większą szczegółowością, w tym wykluczeniami użytkowników, które nie są dostępne w ustawieniach domyślnych zabezpieczeń. Jeśli używasz dostępu warunkowego i w swoim środowisku masz włączone zasady dostępu warunkowego, wartości domyślne zabezpieczeń nie będą dla Ciebie dostępne. Jeśli masz licencję, która zapewnia dostęp warunkowy, ale nie ma żadnych zasad dostępu warunkowego włączonych w Twoim środowisku, możesz używać wartości domyślnych zabezpieczeń do momentu włączenia zasad dostępu warunkowego. Więcej informacji na temat licencjonowania usługi Azure AD można znaleźć na stronie [cennika usługi Azure AD.](https://azure.microsoft.com/pricing/details/active-directory/)

![Komunikat ostrzegawczy informujący o tym, że mogą być dostępne wartości domyślne zabezpieczeń lub dostęp warunkowy nie jest obu](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Poniżej znajdują się przewodniki krok po kroku dotyczące sposobu używania dostępu warunkowego do konfigurowania zasad równoważnych z zasadami włączonymi przy użyciu ustawień domyślnych zabezpieczeń:

- [Wymaganie uwierzytelniania wieloskładnikowego dla administratorów](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Wymaganie uwierzytelniania wieloskładnikowego do zarządzania platformą Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Blokuj starsze uwierzytelnianie](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Wymaganie uwierzytelniania wieloskładnikowego dla wszystkich użytkowników](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Wymagaj rejestracji usługi Azure AD MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) — Azure AD Identity Protection część Azure AD — wersja Premium P2.

## <a name="enabling-security-defaults"></a>Włączanie wartości domyślnych zabezpieczeń

Aby włączyć wartości domyślne zabezpieczeń w katalogu:

1. Zaloguj się do [Azure Portal](https://portal.azure.com)   jako administrator zabezpieczeń, administrator dostępu warunkowego lub administrator globalny.
1. Przejdź do **Azure Active Directory**   >  **właściwości**.
1. Wybierz **pozycję Zarządzaj ustawieniami domyślnymi zabezpieczeń.**
1. Ustaw przełącznik **Włącz domyślne ustawienia zabezpieczeń** na wartość **Tak.**
1. Wybierz pozycję **Zapisz**.

## <a name="disabling-security-defaults"></a>Wyłączanie ustawień domyślnych zabezpieczeń

Organizacje, które zdecydują się na wdrożenie zasad dostępu warunkowego, które zastępują wartości domyślne zabezpieczeń, muszą wyłączyć wartości domyślne zabezpieczeń. 

![Komunikat ostrzegawczy wyłącza ustawienia domyślne zabezpieczeń w celu włączenia dostępu warunkowego](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Aby wyłączyć ustawienia domyślne zabezpieczeń w katalogu:

1. Zaloguj się do [Azure Portal](https://portal.azure.com)   jako administrator zabezpieczeń, administrator dostępu warunkowego lub administrator globalny.
1. Przejdź do **Azure Active Directory**   >  **właściwości**.
1. Wybierz **pozycję Zarządzaj ustawieniami domyślnymi zabezpieczeń.**
1. Ustaw przełącznik **Włącz ustawienia domyślne zabezpieczeń** na wartość **Nie.**
1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

[Typowe zasady dostępu warunkowego](../conditional-access/concept-conditional-access-policy-common.md)
