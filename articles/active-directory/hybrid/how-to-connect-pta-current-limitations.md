---
title: 'Azure AD Connect: uwierzytelnianie przekazywane — bieżące ograniczenia | Microsoft Docs'
description: W tym artykule opisano bieżące ograniczenia uwierzytelniania przekazywanego w usłudze Azure Active Directory (Azure AD)
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
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e9c4489f59f72e4d0b5c7a0b911da188eb0828c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280200"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory uwierzytelnianie przekazywane: bieżące ograniczenia

>[!IMPORTANT]
>Uwierzytelnianie przekazujące Azure Active Directory (Azure AD) jest bezpłatną funkcją i nie są potrzebne żadne płatne wersje usługi Azure AD do użycia. Uwierzytelnianie przekazywane jest dostępne tylko w całym świecie wystąpienia usługi Azure AD, a nie w [chmurze Microsoft Azure (Niemcy)](https://www.microsoft.de/cloud-deutschland) lub w [chmurze Microsoft Azure Government](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Obsługiwane są następujące scenariusze:

- Logowania użytkowników do aplikacji opartych na przeglądarce sieci Web.
- Logowania użytkowników do klientów programu Outlook przy użyciu starszych protokołów, takich jak Exchange ActiveSync, EAS, SMTP, POP i IMAP.
- Logowania użytkowników do starszych aplikacji klienckich pakietu Office i aplikacji pakietu Office, które obsługują [nowoczesne uwierzytelnianie](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview): wersje pakietu Office 2013 i 2016.
- Logowania użytkowników do starszych aplikacji protokołów, takich jak program PowerShell w wersji 1,0 i innych.
- Sprzężenia usługi Azure AD dla urządzeń z systemem Windows 10.
- Hasła aplikacji dla Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Następujące scenariusze _nie_ są obsługiwane:

- Wykrywanie użytkowników z [nieujawnionymi poświadczeniami](../identity-protection/overview-identity-protection.md).
- Azure AD Domain Services wymaga włączenia synchronizacji skrótów haseł w dzierżawie. W związku z tym dzierżawców korzystających z _uwierzytelniania_ przekazywanego nie działają w scenariuszach wymagających Azure AD Domain Services.
- Uwierzytelnianie przekazywane nie jest zintegrowane z [Azure AD Connect Health](./whatis-azure-ad-connect.md).

> [!IMPORTANT]
> Jako obejście _tylko_ dla nieobsługiwanych scenariuszy (z wyjątkiem integracji Azure AD Connect Health) Włącz synchronizację skrótów haseł na stronie [funkcje opcjonalne](how-to-connect-install-custom.md#optional-features) w Kreatorze Azure AD Connect.
> 
> [!NOTE]
> Włączenie synchronizacji skrótów haseł umożliwia przełączenie w tryb failover w przypadku niezakłóconej infrastruktury lokalnej. Ten tryb failover z uwierzytelniania przekazywanego do synchronizacji skrótów haseł nie jest automatyczny. Należy ręcznie przełączyć metodę logowania przy użyciu Azure AD Connect. Jeśli serwer, na którym działa Azure AD Connect, przestanie działać, będziesz potrzebować pomocy z pomoc techniczna firmy Microsoft, aby wyłączyć uwierzytelnianie przekazywane.

## <a name="next-steps"></a>Następne kroki
- [Szybki Start](how-to-connect-pta-quick-start.md): Rozpoczynanie pracy z uwierzytelnianiem przekazującym usługi Azure AD.
- [Migrowanie z AD FS do uwierzytelniania przekazywanego](https://aka.ms/ADFSTOPTADPDownload) — szczegółowy przewodnik migracji z AD FS (lub innych technologii federacyjnych) do uwierzytelniania przekazywanego.
- [Inteligentna blokada](../authentication/howto-password-smart-lockout.md): informacje na temat konfigurowania możliwości inteligentnego blokowania w dzierżawie w celu ochrony kont użytkowników.
- [Głębokie szczegółowe](how-to-connect-pta-how-it-works.md): zrozumienie, jak działa funkcja uwierzytelniania przekazywanego.
- [Często zadawane pytania](how-to-connect-pta-faq.md): Znajdź odpowiedzi na często zadawane pytania dotyczące funkcji uwierzytelniania przekazywanego.
- [Rozwiązywanie problemów](tshoot-connect-pass-through-authentication.md): informacje na temat rozwiązywania typowych problemów z funkcją uwierzytelniania przekazywanego.
- [Głębokie szczegółowe zabezpieczeń](how-to-connect-pta-security-deep-dive.md): Uzyskaj szczegółowe informacje techniczne dotyczące funkcji uwierzytelniania przekazywanego.
- [Bezproblemowe logowanie jednokrotne w usłudze Azure AD](how-to-connect-sso.md): Dowiedz się więcej o tej funkcji uzupełniającej.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): użyj forum Azure Active Directory, aby obsłużyć nowe żądania funkcji.
