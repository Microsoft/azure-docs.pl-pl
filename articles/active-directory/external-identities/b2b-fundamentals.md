---
title: Azure Active Directory najlepsze rozwiązania i zalecenia dotyczące B2B
description: Zapoznaj się z najlepszymi rozwiązaniami i zaleceniami dotyczącymi dostępu użytkowników typu B2B (Business-to-Business) w Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f9ea8b1c1346deee9fed591493607270f18ad5b
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547767"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Azure Active Directory najlepszych rozwiązań B2B
Ten artykuł zawiera zalecenia i najlepsze rozwiązania dotyczące współpracy między firmami (B2B, Business-to-Business) w Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **Począwszy od marca 2021**, firma Microsoft nie będzie już obsługiwać wykupu zaproszeń przez tworzenie niezarządzanych ("wirusowego" lub "just-in-Time") kont usługi Azure AD i dzierżawców na potrzeby scenariuszy współpracy B2B. W tym czasie funkcja jednorazowego kodu dostępu wiadomości e-mail zostanie włączona dla wszystkich istniejących dzierżawców i domyślnie włączona dla nowych dzierżawców. Włączamy funkcję jednorazowego kodu dostępu wiadomości e-mail, ponieważ zapewnia ona bezproblemową metodę uwierzytelniania dla użytkowników-Gości. Istnieje jednak możliwość wyłączenia tej funkcji, jeśli nie zostanie ona użyta. Aby uzyskać szczegółowe informacje, zobacz [jednorazowe uwierzytelnianie kodu dostępu za pośrednictwem poczty e-mail](one-time-passcode.md) .


## <a name="b2b-recommendations"></a>Zalecenia B2B
| Zalecenie | Komentarze |
| --- | --- |
| Aby zapewnić optymalne środowisko logowania, sfederować się z dostawcami tożsamości | Zawsze, gdy to możliwe, sfederować bezpośrednio z dostawcami tożsamości, aby umożliwić zaproszonym użytkownikom zalogowanie się do udostępnionych aplikacji i zasobów bez konieczności tworzenia kont Microsoft (kont MSA) lub kont usługi Azure AD. Za pomocą [funkcji Google Federation](google-federation.md) można zezwolić użytkownikom gościa B2B na logowanie się przy użyciu konta Google. Można też użyć [funkcji bezpośredniego Federacji (wersja zapoznawcza)](direct-federation.md) , aby skonfigurować bezpośrednie Federacji z każdą organizacją, której dostawca tożsamości (dostawcy tożsamości) obsługuje protokół SAML 2,0 lub WS-Fed. |
| Korzystanie z funkcji jednokrotnego kodu dostępu do wiadomości E-mail dla Gości B2B, którzy nie mogą uwierzytelniać się w inny sposób | Funkcja [jednorazowego kodu dostępu wiadomości e-mail](one-time-passcode.md) uwierzytelnia użytkowników gościa B2B, gdy nie mogą być uwierzytelniane za pośrednictwem innych takich metod jak Azure AD, konto Microsoft (MSA) lub Google Federation. Gdy użytkownik-Gość zrealizuje zaproszenie lub uzyskuje dostęp do zasobu udostępnionego, może zażądać kodu tymczasowego, który jest wysyłany na adres e-mail. Następnie wprowadzają ten kod, aby kontynuować logowanie. |
| Dodawanie znakowania firmowego do strony logowania | Możesz dostosować swoją stronę logowania, aby była bardziej intuicyjna dla użytkowników gościa B2B. Zobacz temat jak [dodać znakowanie firmowe, aby się zalogować i panelu dostępu](../fundamentals/customize-branding.md). |
| Dodawanie zasad zachowania poufności informacji do środowiska wykupu przez Gościa B2B | Adres URL zasad zachowania poufności informacji organizacji można dodać do procesu wykupu po raz pierwszy, aby zaproszony użytkownik musiał wyrazić zgodę na warunki zachowania poufności informacji, aby kontynuować. Zobacz [instrukcje: Dodawanie informacji o ochronie prywatności organizacji w Azure Active Directory](../fundamentals/active-directory-properties-area.md). |
| Korzystanie z funkcji zaproszeń zbiorczych (wersja zapoznawcza) do zapraszania wielu użytkowników-Gości w tym samym czasie | Zapraszaj wielu użytkowników-Gości w organizacji w tym samym czasie, używając funkcji Zaproś w wersji zapoznawczej w Azure Portal. Ta funkcja umożliwia przekazanie pliku CSV w celu utworzenia użytkowników gościa B2B i wysłania zaproszeń zbiorczo. Zapoznaj się z [samouczkiem dotyczącym zbiorczego zapraszania użytkowników B2B](tutorial-bulk-invite.md). |
| Wymuś zasady dostępu warunkowego dla Multi-Factor Authentication (MFA) | Zalecamy wymuszenie stosowania zasad MFA dla aplikacji, które mają być udostępniane partnerom z użytkownikami B2B. W ten sposób uwierzytelnianie wieloskładnikowe będzie stale wymuszane w aplikacjach w dzierżawie, niezależnie od tego, czy organizacja partnera korzysta z usługi MFA. Zobacz [dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md). |
| W przypadku wymuszania zasad dostępu warunkowego opartego na urządzeniach Użyj list wykluczeń, aby zezwolić na dostęp do użytkowników B2B | Jeśli zasady dostępu warunkowego opartego na urządzeniach są włączone w organizacji, urządzenia użytkownika-gościa B2B będą blokowane, ponieważ nie są zarządzane przez organizację. Można utworzyć listę wykluczeń zawierającą określonych użytkowników partnerskich, aby wykluczyć ich z zasad dostępu warunkowego opartego na urządzeniach. Zobacz [dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md). |
| Użyj adresu URL specyficznego dla dzierżawy podczas udostępniania bezpośrednich linków użytkownikom gościa B2B | Alternatywą dla wiadomości e-mail z zaproszeniem jest nadanie gościa bezpośredniego linku do aplikacji lub portalu. Ten bezpośredni link musi być specyficzny dla dzierżawców, co oznacza, że musi zawierać identyfikator dzierżawy lub zweryfikowaną domenę, aby można było uwierzytelnić gościa w dzierżawie, gdzie znajduje się aplikacja udostępniona. Zobacz [środowisko wykupu dla użytkownika-gościa](redemption-experience.md). |
| Podczas tworzenia aplikacji użyj elementu UserType, aby określić środowisko użytkownika gościa  | Jeśli tworzysz aplikację i chcesz udostępnić różne środowiska użytkownikom dzierżawy i użytkownikom-Gościom, użyj właściwości UserType. W tokenie nie znajduje się obecnie oświadczenia UserType. Aplikacje powinny używać interfejsu API Microsoft Graph, aby wysłać zapytanie do katalogu, aby użytkownik mógł uzyskać użytkownika. |
| Zmień właściwość UserType *tylko* wtedy, gdy zostanie zmieniona relacja użytkownika z organizacją | Mimo że można użyć programu PowerShell do przekonwertowania właściwości UserType dla użytkownika z elementu członkowskiego na gościa (i na odwrót), należy zmienić tę właściwość tylko wtedy, gdy relacja użytkownika jest zmieniana w organizacji. Zobacz [właściwości użytkownika-gościa B2B](user-properties.md).|

## <a name="next-steps"></a>Następne kroki

[Zarządzanie udostępnianiem B2B](delegate-invitations.md)