---
title: Dostęp warunkowy — Wymagaj zgodnych urządzeń — Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego w celu wymagania zgodnych urządzeń
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c98269f9851272e8caa9b26ae0c57ed13e9a99f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89049132"
---
# <a name="conditional-access-require-compliant-devices"></a>Dostęp warunkowy: Wymagaj zgodnych urządzeń

Organizacje, które wdrożyły Microsoft Intune mogą korzystać z informacji zwróconych z urządzeń w celu identyfikowania urządzeń spełniających wymagania dotyczące zgodności, takich jak:

* Wymaganie kodu PIN do odblokowania
* Wymaganie szyfrowania urządzenia
* Wymaganie minimalnej lub maksymalnej wersji systemu operacyjnego
* Wymaganie, aby urządzenie nie zostało złamane lub odblokowane

Te informacje o zgodności z zasadami są przekazywane do usługi Azure AD, gdzie dostęp warunkowy może podejmować decyzje o udzieleniu lub zablokowaniu dostępu do zasobów. Więcej informacji o zasadach zgodności urządzeń znajduje się w artykule, [Ustawianie reguł na urządzeniach w celu zezwalania na dostęp do zasobów w organizacji przy użyciu usługi Intune](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Poniższe kroki pomogą utworzyć zasady dostępu warunkowego, aby wymagać, aby urządzenia uzyskujące dostęp do zasobów były oznaczone jako zgodne z zasadami zgodności usługi Intune w organizacji.

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory**  >    >  **dostępu warunkowego** zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania** wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie** wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz** wybierz pozycję **Użytkownicy i grupy** , a następnie wybierz opcję dostęp awaryjny lub konta w ramach swojej organizacji. 
   1. Kliknij **Gotowe**.
1. W obszarze **aplikacje w chmurze lub akcje**  >  **Dołącz** wybierz pozycję **wszystkie aplikacje w chmurze**.
   1. Jeśli musisz wykluczyć określone aplikacje z zasad, możesz je wybrać z karty **Wyklucz** w obszarze **Wybierz wykluczone aplikacje w chmurze** i wybierz **pozycję Wybierz**.
   1. Kliknij **Gotowe**.
1. W obszarze **warunki**  >  **aplikacje klienckie (wersja zapoznawcza)**  >  **Wybierz aplikacje klienckie, do których te zasady będą stosowane**, pozostaw wszystkie wybrane wartości domyślne i wybierz pozycję **gotowe**.
1. W obszarze **Kontrola dostępu**  >  wybierz pozycję **Wymagaj, aby urządzenie było oznaczone jako zgodne**.
   1. Wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć zasady.

> [!NOTE]
> Możesz zarejestrować nowe urządzenia w usłudze Intune, nawet jeśli wybrano opcję **Wymagaj, aby urządzenie było oznaczone jako zgodne** dla **wszystkich użytkowników** i **aplikacji w chmurze** , korzystając z powyższych kroków. **Wymagaj, aby urządzenie było oznaczone jako zgodne** z kontrolką nie blokuje rejestracji w usłudze Intune. 

### <a name="known-behavior"></a>Znane zachowanie

W systemach Windows 7, iOS, Android, macOS i niektórych przeglądarkach sieci Web innych firm usługa Azure AD identyfikuje urządzenie przy użyciu certyfikatu klienta, który jest inicjowany, gdy urządzenie jest zarejestrowane w usłudze Azure AD. Gdy użytkownik po raz pierwszy zaloguje się za pomocą przeglądarki, użytkownik jest monitowany o wybranie certyfikatu. Użytkownik końcowy musi wybrać ten certyfikat, zanim będzie mógł kontynuować korzystanie z przeglądarki.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko Raport z dostępem warunkowym](howto-conditional-access-insights-reporting.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)

[Współdziałanie zasad zgodności urządzeń z usługą Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
