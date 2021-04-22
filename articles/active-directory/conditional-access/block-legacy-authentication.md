---
title: Blokuj starsze uwierzytelnianie — Azure Active Directory
description: Dowiedz się, jak poprawić poziom zabezpieczeń, blokując starsze uwierzytelnianie przy użyciu dostępu warunkowego usługi Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84c8b82219f2b2aea39bbcd23f030243d9ea8635
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861809"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Instrukcje: Blokowanie starszego uwierzytelniania w usłudze Azure AD przy użyciu dostępu warunkowego   

Aby zapewnić użytkownikom łatwy dostęp do aplikacji w chmurze, usługa Azure Active Directory (Azure AD) obsługuje szeroką gamę protokołów uwierzytelniania, w tym starsze uwierzytelnianie. Starsze protokoły nie obsługują jednak uwierzytelniania wieloskładnikowego (MFA). Uwierzytelniania wieloskładnikowego jest w wielu środowiskach typowe wymaganie dotyczące kradzieży tożsamości. 

Alex Weinert, dyrektor ds. zabezpieczeń tożsamości w firmie Microsoft, w swoim [](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) wpisie w blogu z 12 marca 2020 r. Nowe narzędzia do blokowania starszego uwierzytelniania w organizacji kładzie nacisk na to, dlaczego organizacje powinny blokować starsze uwierzytelnianie i jakie inne narzędzia oferuje firma Microsoft, aby wykonać to zadanie:

> Aby uwierzytelnianie wieloskładnikowe było skuteczne, należy również zablokować starsze uwierzytelnianie. Wynika to z tego, że starsze protokoły uwierzytelniania, takie jak POP, SMTP, IMAP i MAPI, nie mogą wymuszać uwierzytelniania wieloskładnikowego, dzięki czemu są preferowanymi punktami wejścia dla osób atakujących w Twojej organizacji...
> 
>... Numery dotyczące starszego uwierzytelniania na podstawie analizy ruchu Azure Active Directory (Azure AD) są bardzo liczne:
> 
> - Ponad 99% ataków systemowych na hasła korzysta ze starszych protokołów uwierzytelniania
> - Ponad 97% ataków na wypychanie poświadczeń korzysta ze starszego uwierzytelniania
> - Konta usługi Azure AD w organizacjach, które wyłączyły starsze uwierzytelnianie, mają o 67% mniej naruszyć bezpieczeństwa niż te, w których włączono starsze uwierzytelnianie
>

Jeśli środowisko jest gotowe do zablokowania starszego uwierzytelniania w celu poprawy ochrony dzierżawy, możesz osiągnąć ten cel przy użyciu dostępu warunkowego. W tym artykule wyjaśniono, jak można skonfigurować zasady dostępu warunkowego, które blokują starsze uwierzytelnianie dla dzierżawy. Klienci bez licencji, które obejmują dostęp warunkowy, mogą korzystać z ustawień [domyślnych zabezpieczeń](../fundamentals/concept-fundamentals-security-defaults.md)) w celu blokowania starszego uwierzytelniania.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz podstawowe [pojęcia](overview.md) dotyczące dostępu warunkowego usługi Azure AD.

## <a name="scenario-description"></a>Opis scenariusza

Usługa Azure AD obsługuje kilka najczęściej używanych protokołów uwierzytelniania i autoryzacji, w tym starsze uwierzytelnianie. Starsze uwierzytelnianie odnosi się do protokołów, które korzystają z uwierzytelniania podstawowego. Zazwyczaj te protokoły nie mogą wymuszać żadnego typu uwierzytelniania dwuskładnikowego. Przykłady aplikacji opartych na starszym uwierzytelnianiu to:

- Starsze Microsoft Office aplikacji
- Aplikacje korzystające z protokołów poczty, takich jak POP, IMAP i SMTP

Uwierzytelnianie jednoskładnikowe (na przykład nazwa użytkownika i hasło) nie jest w tych dniach wystarczające. Hasła są złe, ponieważ są łatwe do odgadnięcia, a my (ludzie) źle wybieramy dobre hasła. Hasła są również narażone na różne ataki, takie jak wyłudzanie informacji i ataki na hasła. Jedną z najprostszych czynności, które można wykonać w celu ochrony przed zagrożeniami hasłami, jest zaimplementowanie uwierzytelniania wieloskładnikowego (MFA). W przypadku uwierzytelniania wieloskładnikowego, nawet jeśli osoba atakująca uzyskuje dostęp do hasła użytkownika, samo hasło nie wystarcza do pomyślnego uwierzytelnienia i uzyskania dostępu do danych.

Jak uniemożliwić aplikacjom korzystającym ze starszego uwierzytelniania uzyskiwanie dostępu do zasobów dzierżawy? Zaleca się po prostu zablokowanie ich przy użyciu zasad dostępu warunkowego. W razie potrzeby zezwala się na używanie aplikacji opartych na starszym uwierzytelnianiu tylko określonym użytkownikom i określonym lokalizacjom sieciowym.

Zasady dostępu warunkowego są wymuszane po zakończeniu uwierzytelniania pierwszego czynnika. W związku z tym dostęp warunkowy nie jest przeznaczony jako pierwsza linia obrony dla scenariuszy takich jak ataki typu "odmowa usługi" (DoS), ale może wykorzystywać sygnały z tych zdarzeń (na przykład poziom ryzyka logowania, lokalizację żądania itp.) w celu określenia dostępu.

## <a name="implementation"></a>Implementacja

W tej sekcji wyjaśniono, jak skonfigurować zasady dostępu warunkowego w celu blokowania starszego uwierzytelniania. 

### <a name="legacy-authentication-protocols"></a>Starsze protokoły uwierzytelniania

Następujące opcje są uznawane za starsze protokoły uwierzytelniania

- Uwierzytelniony protokół SMTP — używany przez klientów POP i IMAP do wysyłania wiadomości e-mail.
- Wykrywania automatycznego — używane przez klientów programu Outlook i programu EAS do znajdowanie skrzynek pocztowych w usłudze Exchange Online i nawiązywanie z nie połączenia.
- Exchange ActiveSync (EAS) — służy do nawiązywania połączeń ze skrzynkami pocztowymi w u usługi Exchange Online.
- Exchange Online PowerShell — służy do nawiązywania połączenia z usługą Exchange Online za pomocą zdalnego programu PowerShell. Jeśli zablokujesz uwierzytelnianie podstawowe dla programu PowerShell usługi Exchange Online, do nawiązania połączenia musisz użyć modułu programu PowerShell usługi Exchange Online. Aby uzyskać instrukcje, zobacz [Connect to Exchange Online PowerShell using multi-factor authentication (Nawiązywanie połączenia z programem PowerShell usługi Exchange Online przy użyciu uwierzytelniania wieloskładnikowego).](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)
- Exchange Web Services (EWS) — interfejs programowania używany przez programy Outlook, Outlook dla komputerów Mac i aplikacje innych firm.
- IMAP4 — używane przez klientów poczty e-mail IMAP.
- MAPI za pośrednictwem protokołu HTTP (MAPI/HTTP) — używany przez program Outlook 2010 i nowsze.
- Offline Książka adresowa (OAB) — kopia kolekcji listy adresów, które są pobierane i używane przez program Outlook.
- Outlook Anywhere (RPC przez HTTP) — używany przez program Outlook 2016 i starsze.
- Usługa Outlook — używana przez aplikację Poczta i kalendarz na Windows 10.
- POP3 — używany przez klientów poczty e-mail POP.
- Usługi sieci Web raportowania — używane do pobierania danych raportu w u usługi Exchange Online.
- Inni klienci — inne protokoły zidentyfikowane jako korzystające ze starszego uwierzytelniania.

Aby uzyskać więcej informacji na temat tych protokołów uwierzytelniania i usług, zobacz Raporty dotyczące działań logowania [w Azure Active Directory portal.](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)

### <a name="identify-legacy-authentication-use"></a>Identyfikowanie użycia starszego uwierzytelniania

Przed zablokowaniem starszego uwierzytelniania w katalogu należy najpierw zrozumieć, czy użytkownicy mają aplikacje, które korzystają ze starszego uwierzytelniania, i jak wpływa ono na cały katalog. Dzienniki logowania usługi Azure AD mogą służyć do zrozumienia, czy używasz starszego uwierzytelniania.

1. Przejdź do **Azure Portal**  >  **Azure Active Directory**  >  **logowania.**
1. Dodaj kolumnę Aplikacja kliency, jeśli nie jest wyświetlana, klikając pozycję **Columns**  >  **Client App (Aplikacje klienckie kolumn).**
1. **Dodawanie filtrów**  >  **Aplikacja klien** > wybierz wszystkie starsze protokoły uwierzytelniania. Wybierz poza oknem dialogowym filtrowania, aby zastosować wybrane opcje i zamknąć okno dialogowe.
1. Jeśli aktywowano podgląd nowych raportów dotyczących działań [logowania,](../reports-monitoring/concept-all-sign-ins.md)powtórz powyższe kroki również na karcie Logowania użytkownika (nieinterakcyjne). 

Filtrowanie będzie pokazywać tylko próby logowania, które zostały wykonane przez starsze protokoły uwierzytelniania. Kliknięcie poszczególnych prób logowania spowoduje wyświetlanie większej liczby szczegółów. Pole **Aplikacja kliency** na karcie **Informacje podstawowe** będzie wskazywać, który starszy protokół uwierzytelniania został użyty.

Te dzienniki będą wskazywać, którzy użytkownicy nadal są zależni od starszego uwierzytelniania i które aplikacje będą używać starszych protokołów do żądania uwierzytelnienia. W przypadku użytkowników, którzy nie są wyświetlani w tych dziennikach i zostaną potwierdzeni, że nie korzystali ze starszego uwierzytelniania, należy zaimplementować zasady dostępu warunkowego tylko dla tych użytkowników.

## <a name="block-legacy-authentication"></a>Blokowanie starszego uwierzytelniania 

Istnieją dwa sposoby blokowania starszego uwierzytelniania za pomocą zasad dostępu warunkowego.

- [Bezpośrednie blokowanie starszego uwierzytelniania](#directly-blocking-legacy-authentication)
- [Pośrednie blokowanie starszego uwierzytelniania](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>Bezpośrednie blokowanie starszego uwierzytelniania

Najprostszym sposobem zablokowania starszego uwierzytelniania w całej organizacji jest skonfigurowanie zasad dostępu warunkowego, które dotyczą w szczególności starszych klientów uwierzytelniania i blokują dostęp. Podczas przypisywania użytkowników i aplikacji do zasad pamiętaj o wykluczeniu użytkowników i kont usług, które nadal muszą logować się przy użyciu starszego uwierzytelniania. Skonfiguruj warunek aplikacji klienckich, wybierając pozycję **Klienci programu Exchange ActiveSync** i **inni klienci.** Aby zablokować dostęp do tych aplikacji klienckich, skonfiguruj dla kontrolek dostępu opcję Blokuj dostęp.

![Warunek aplikacji klienckich skonfigurowany do blokowania starszego uwierzytelniania](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>Pośrednie blokowanie starszego uwierzytelniania

Nawet jeśli Twoja organizacja nie jest gotowa do zablokowania starszego uwierzytelniania w całej organizacji, upewnij się, że logowania przy użyciu starszego uwierzytelniania nie pomijają zasad wymagających kontroli, takich jak wymaganie uwierzytelniania wieloskładnikowego lub zgodnych/hybrydowych urządzeń przyłączanych do usługi Azure AD. Podczas uwierzytelniania starsi klienci uwierzytelniania nie obsługują wysyłania usługi MFA, zgodności urządzeń ani informacji o stanie dołączania do usługi Azure AD. W związku z tym zastosuj zasady z kontrolkami udzielania do wszystkich aplikacji klienckich, aby logowania oparte na starszym uwierzytelnianiu, które nie spełniają uprawnień kontroli udzielania, zostały zablokowane. Po ogólnej dostępności warunku aplikacji klienckich w sierpniu 2020 r. nowo utworzone zasady dostępu warunkowego są domyślnie stosowane do wszystkich aplikacji klienckich.

![Domyślna konfiguracja warunku aplikacji klienckich](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>Co należy wiedzieć

Blokowanie dostępu przy użyciu **innych klientów** blokuje również program Exchange Online PowerShell i usługę Dynamics 365 przy użyciu uwierzytelniania podstawowego.

Skonfigurowanie zasad dla innych **klientów** blokuje całą organizację z niektórych klientów, takich jak SPConnect. Ten blok występuje, ponieważ starsi klienci uwierzytelniają się w nieoczekiwany sposób. Problem nie dotyczy głównych aplikacji pakietu Office, takich jak starsi klienci pakietu Office.

Może upłynieć do 24 godzin, aż zasady zajdą w życie.

Możesz wybrać wszystkie dostępne kontrolki udzielania dla **warunku Inni klienci.** Jednak środowisko użytkownika końcowego jest zawsze takie samo — zablokowany dostęp.

### <a name="sharepoint-online-and-b2b-guest-users"></a>Użytkownicy-goście usług SharePoint Online i B2B

Aby zablokować dostęp użytkowników B2B za pośrednictwem starszego uwierzytelniania do usługi SharePoint Online, organizacje muszą wyłączyć starsze uwierzytelnianie w programie SharePoint przy użyciu polecenia programu PowerShell i `Set-SPOTenant` ustawiać `-LegacyAuthProtocolsEnabled` parametr na `$false` . Więcej informacji na temat ustawiania tego parametru można znaleźć w dokumencie referencyjnym programu PowerShell dotyczącym polecenia [Set-SPOTenant](/powershell/module/sharepoint-online/set-spotenant)

## <a name="next-steps"></a>Następne kroki

- [Określanie wpływu przy użyciu trybu tylko do raportu z dostępem warunkowym](howto-conditional-access-insights-reporting.md)
- Jeśli jeszcze nie znasz konfigurowania zasad dostępu warunkowego, zobacz przykład require [MFA for specific](../authentication/tutorial-enable-azure-mfa.md) apps with Azure Active Directory Conditional Access (Wymaganie uwierzytelniania wieloskładnikowego dla określonych aplikacji z dostępem warunkowym).
- Aby uzyskać więcej informacji na temat obsługi nowoczesnego uwierzytelniania, zobacz [How modern authentication works for Office 2013 and Office 2016 client apps](/office365/enterprise/modern-auth-for-office-2013-and-2016) (Jak działa nowoczesne uwierzytelnianie dla aplikacji klienckich pakietu Office 2013 i Office 2016) 
- [Jak skonfigurować urządzenie wielofunkcyjne lub aplikację do wysyłania wiadomości e-mail przy użyciu Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)