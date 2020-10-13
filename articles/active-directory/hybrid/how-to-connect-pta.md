---
title: 'Azure AD Connect: uwierzytelnianie przekazywane | Microsoft Docs'
description: W tym artykule opisano uwierzytelnianie przekazujące usługi Azure Active Directory (Azure AD) i sposób, w jaki umożliwiają logowanie za pomocą usługi Azure AD, sprawdzając poprawność haseł użytkowników do Active Directory lokalnych.
services: active-directory
keywords: Co to jest Azure AD Connect uwierzytelnianie przekazywane, instalowanie Active Directory, wymagane składniki usługi Azure AD, logowanie jednokrotne, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b8f613cb7c75d9dd6af1fcf62f9d484398072c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279469"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Logowanie użytkownika przy użyciu uwierzytelniania przekazywanego usługi Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Co to jest Azure Active Directory uwierzytelnianie przekazywane?

Uwierzytelnianie przekazujące Azure Active Directory (Azure AD) pozwala użytkownikom na logowanie się do aplikacji lokalnych i opartych na chmurze przy użyciu tych samych haseł. Ta funkcja zapewnia lepszą obsługę użytkowników (jedno hasło mniej do zapamiętania) i obniża koszty pomocy technicznej IT, zmniejszając prawdopodobieństwo tego, że użytkownik zapomni sposób logowania się. Gdy użytkownicy logują się przy użyciu usługi Azure AD, ta funkcja sprawdza hasła użytkowników bezpośrednio do Active Directory lokalnego.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Ta funkcja jest alternatywą dla [synchronizacji skrótów haseł usługi Azure AD](how-to-connect-password-hash-synchronization.md), która zapewnia takie same korzyści wynikające z uwierzytelniania w chmurze w organizacjach. Jednak niektóre organizacje, które chcą wymusić lokalne Active Directory zasady zabezpieczeń i haseł, mogą korzystać z uwierzytelniania przekazywanego. Zapoznaj się z [tym przewodnikiem](./choose-ad-authn.md) , aby porównać różne metody logowania do usługi Azure AD i jak wybrać właściwą metodę logowania dla organizacji.

![Uwierzytelnianie przekazywane przez usługę Azure AD](./media/how-to-connect-pta/pta1.png)

Uwierzytelnianie przekazywane można połączyć za pomocą funkcji [jednokrotnego logowania](how-to-connect-sso.md) jednokrotnego. W ten sposób, gdy użytkownicy uzyskują dostęp do aplikacji na swoich maszynach firmowych w sieci firmowej, nie muszą wpisywać ich haseł, aby się zalogować.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Najważniejsze zalety korzystania z uwierzytelniania przekazywanego za pomocą usługi Azure AD

- *Fantastyczne środowisko pracy użytkownika*
  - Użytkownicy używają tych samych haseł do logowania się do aplikacji lokalnych i opartych na chmurze.
  - Użytkownicy poświęcają mniej czasu na rozmowy z pomocą techniczną działu IT w rozwiązywaniu problemów związanych z hasłami.
  - Użytkownicy mogą wykonywać zadania samoobsługowego [zarządzania hasłami](../authentication/concept-sspr-howitworks.md) w chmurze.
- *Łatwe wdrażanie & administrowanie*
  - Nie ma potrzeby złożonych wdrożeń lokalnych ani konfiguracji sieci.
  - Potrzebuje tylko uproszczonego agenta, który ma zostać zainstalowany lokalnie.
  - Brak obciążeń związanych z zarządzaniem. Agent automatycznie otrzymuje ulepszenia i poprawki błędów.
- *Bezpieczny*
  - Hasła lokalne nigdy nie są przechowywane w chmurze w żadnej postaci.
  - Chroni Twoje konta użytkowników, bezproblemowo korzystając z [zasad dostępu warunkowego usługi Azure AD](../conditional-access/overview.md), w tym Multi-Factor Authentication (MFA), [blokując starsze uwierzytelnianie](../conditional-access/concept-conditional-access-conditions.md) i przez [odfiltrowanie ataków z hasłami](../authentication/howto-password-smart-lockout.md)bezprawnego.
  - Agent wykonuje tylko połączenia wychodzące z sieci. W związku z tym nie jest wymagane, aby zainstalować agenta w sieci obwodowej, znanej także jako Strefa DMZ.
  - Komunikacja między agentem a usługą Azure AD jest zabezpieczona przy użyciu uwierzytelniania opartego na certyfikatach. Te certyfikaty są automatycznie odnawiane co kilka miesięcy przez usługę Azure AD.
- *Wysoka dostępność*
  - Dodatkowych agentów można zainstalować na wielu serwerach lokalnych w celu zapewnienia wysokiej dostępności żądań logowania.

## <a name="feature-highlights"></a>Najważniejsze funkcje

- Program obsługuje logowanie użytkownika do wszystkich aplikacji opartych na przeglądarce sieci Web oraz Microsoft Office aplikacji klienckich korzystających z [nowoczesnego uwierzytelniania](https://aka.ms/modernauthga).
- Nazwy użytkowników logowania mogą być lokalną domyślną nazwą użytkownika ( `userPrincipalName` ) lub innym atrybutem skonfigurowanym w Azure AD Connect (znanym jako `Alternate ID` ).
- Funkcja działa bezproblemowo z funkcjami [dostępu warunkowego](../conditional-access/overview.md) , takimi jak Multi-Factor Authentication (MFA), aby pomóc w zabezpieczeniu użytkowników.
- Zintegrowana z funkcją samoobsługowego [zarządzania hasłami](../authentication/concept-sspr-howitworks.md)w chmurze, w tym funkcja zapisywania zwrotnego haseł do lokalnego Active Directory i ochrony hasłem przez zakazywanie często używanych haseł.
- Środowiska z obsługą wielu lasów są obsługiwane, jeśli istnieją relacje zaufania lasów między lasami usługi AD i jeśli Routing sufiksów nazw jest prawidłowo skonfigurowany.
- Jest to bezpłatna funkcja i nie są potrzebne żadne płatne wersje usługi Azure AD do użycia.
- Można ją włączyć za pośrednictwem [Azure AD Connect](whatis-hybrid-identity.md).
- Używa on uproszczonego agenta lokalnego, który nasłuchuje i reaguje na żądania weryfikacji hasła.
- Zainstalowanie wielu agentów zapewnia wysoką dostępność żądań logowania.
- Zapewnia [ochronę](../authentication/howto-password-smart-lockout.md) kont lokalnych przed atakami z wykorzystaniem hasła w chmurze.

## <a name="next-steps"></a>Następne kroki

- [Szybki Start](how-to-connect-pta-quick-start.md) — uzyskiwanie i uruchamianie uwierzytelniania przekazywanego za pomocą usługi Azure AD.
- [Migrowanie z AD FS do uwierzytelniania przekazywanego](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) — szczegółowy przewodnik migracji z AD FS (lub innych technologii federacyjnych) do uwierzytelniania przekazywanego.
- [Inteligentna blokada](../authentication/howto-password-smart-lockout.md) — umożliwia skonfigurowanie funkcji blokady inteligentnej w dzierżawie, aby chronić konta użytkowników.
- [Bieżące ograniczenia](how-to-connect-pta-current-limitations.md) — Dowiedz się, które scenariusze są obsługiwane i które nie są.
- [Szczegółowee techniczne głębokie](how-to-connect-pta-how-it-works.md) — zrozumienie, jak działa ta funkcja.
- [Często zadawane pytania](how-to-connect-pta-faq.md) — odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów](tshoot-connect-pass-through-authentication.md) — Dowiedz się, jak rozwiązywać typowe problemy z funkcją.
- [Głębokie szczegółowe zabezpieczeń](how-to-connect-pta-security-deep-dive.md) — dodatkowe szczegółowe informacje techniczne dotyczące funkcji.
- [Usługa Azure AD bezproblemowe logowanie jednokrotne](how-to-connect-sso.md) — Dowiedz się więcej na temat tej funkcji uzupełniającej.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłaszania nowych żądań funkcji.