---
title: 'Azure AD Connect: uwierzytelnianie przekazywane — jak działa | Microsoft Docs'
description: W tym artykule opisano sposób działania uwierzytelniania przekazywanego Azure Active Directory
services: active-directory
keywords: Azure AD Connect uwierzytelniania przekazywanego, instalacji Active Directory, wymaganych składników usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe92f761ac0b16da7c3cc3c69c1fa4b00f4e7579
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996597"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory uwierzytelniania przekazywanego: szczegółowe głębokiej wiedzy technicznej
Ten artykuł zawiera omówienie sposobu działania uwierzytelniania przekazywanego za pomocą usługi Azure Active Directory (Azure AD). Aby uzyskać szczegółowe informacje techniczne i o zabezpieczeniach, zobacz artykuł [głębokie szczegółowe zabezpieczeń](how-to-connect-pta-security-deep-dive.md) .

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Jak działa Azure Active Directory uwierzytelnianie przekazywane?

>[!NOTE]
>Zgodnie z wymaganiami wstępnymi dotyczącymi uwierzytelniania przekazywanego użytkownicy muszą być obsługiwani do usługi Azure AD z poziomu lokalnego Active Directory przy użyciu Azure AD Connect. Uwierzytelnianie przekazywane nie dotyczy użytkowników korzystających tylko z chmury.

Gdy użytkownik próbuje zalogować się do aplikacji zabezpieczonej przez usługę Azure AD, a w przypadku włączenia uwierzytelniania przekazywanego w dzierżawie zostaną wykonane następujące kroki:

1. Użytkownik próbuje uzyskać dostęp do aplikacji, na przykład [programu Outlook Web App](https://outlook.office365.com/owa/).
2. Jeśli użytkownik nie jest jeszcze zalogowany, użytkownik zostanie przekierowany do strony **logowania użytkownika** usługi Azure AD.
3. Użytkownik wprowadza swoją nazwę użytkownika na stronie logowania do usługi Azure AD, a następnie wybiera przycisk **dalej** .
4. Użytkownik wprowadza hasło do strony logowania do usługi Azure AD, a następnie wybiera przycisk **Zaloguj** .
5. Usługa Azure AD po odebraniu żądania logowania umieszcza nazwę użytkownika i hasło (zaszyfrowane przy użyciu klucza publicznego agentów uwierzytelniania) w kolejce.
6. Lokalny Agent uwierzytelniania Pobiera nazwę użytkownika i zaszyfrowane hasło z kolejki. Należy zauważyć, że Agent nie jest często sondowany pod kątem żądań z kolejki, ale pobiera żądania za pośrednictwem wstępnie ustalonego połączenia stałego.
7. Agent odszyfrowuje hasło przy użyciu jego klucza prywatnego.
8. Agent sprawdza poprawność nazwy użytkownika i hasła do Active Directory przy użyciu standardowych interfejsów API systemu Windows, która jest podobnym mechanizmem do działania Active Directory Federation Services (AD FS). Nazwa użytkownika może być lokalną domyślną nazwą użytkownika, zazwyczaj `userPrincipalName` lub innym atrybutem skonfigurowanym w Azure AD Connect (znanym jako `Alternate ID` ).
9. Lokalny Active Directory kontroler domeny (DC) oblicza żądanie i zwraca odpowiednią odpowiedź (powodzenie, Niepowodzenie, wygasłe hasło lub użytkownik jest zablokowany) do agenta.
10. Z kolei Agent uwierzytelniania zwraca tę odpowiedź z powrotem do usługi Azure AD.
11. Usługa Azure AD oblicza odpowiedź i reaguje na użytkownika zgodnie z potrzebami. Na przykład usługa Azure AD bezpośrednio podpisuje użytkownika lub żąda usługi Azure AD Multi-Factor Authentication.
12. Jeśli logowanie użytkownika zakończyło się pomyślnie, użytkownik może uzyskać dostęp do aplikacji.

Na poniższym diagramie przedstawiono wszystkie składniki i czynności, które należy wykonać:

![Uwierzytelnianie przekazywane](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Następne kroki
- [Bieżące ograniczenia](how-to-connect-pta-current-limitations.md): informacje o scenariuszach, które są obsługiwane i które nie są.
- [Szybki Start](how-to-connect-pta-quick-start.md): Zapoznaj się z uwierzytelnianiem przekazującym usługi Azure AD.
- [Migrowanie z AD FS do uwierzytelniania przekazywanego](https://aka.ms/adfstoPTADP) — szczegółowy przewodnik migracji z AD FS (lub innych technologii federacyjnych) do uwierzytelniania przekazywanego.
- [Inteligentna blokada](../authentication/howto-password-smart-lockout.md): Skonfiguruj funkcję inteligentnego blokowania w dzierżawie, aby chronić konta użytkowników.
- [Często zadawane pytania](how-to-connect-pta-faq.md): Znajdź odpowiedzi na często zadawane pytania.
- [Rozwiązywanie problemów](tshoot-connect-pass-through-authentication.md): informacje na temat rozwiązywania typowych problemów z funkcją uwierzytelniania przekazywanego.
- [Głębokie szczegółowe zabezpieczeń](how-to-connect-pta-security-deep-dive.md): Uzyskaj szczegółowe informacje techniczne dotyczące funkcji uwierzytelniania przekazywanego.
- [Bezproblemowe logowanie jednokrotne w usłudze Azure AD](how-to-connect-sso.md): Dowiedz się więcej o tej funkcji uzupełniającej.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): użyj forum Azure Active Directory, aby obsłużyć nowe żądania funkcji.

