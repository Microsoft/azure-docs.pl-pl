---
title: Dostęp warunkowy — Blokuj starsze uwierzytelnianie — Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego w celu blokowania starszych protokołów uwierzytelniania
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
ms.openlocfilehash: 2d6539a233fbb8038d82a8ea41da2c9e79745324
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83995194"
---
# <a name="conditional-access-block-legacy-authentication"></a>Dostęp warunkowy: Blokuj starsze uwierzytelnianie

Ze względu na zwiększone ryzyko związane ze starszymi protokołami uwierzytelniania firma Microsoft zaleca, aby organizacje blokowały żądania uwierzytelniania przy użyciu tych protokołów i wymagały nowoczesnego uwierzytelniania.

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Poniższe kroki pomogą w tworzeniu zasad dostępu warunkowego w celu blokowania starszych żądań uwierzytelniania. Te zasady są umieszczane w [trybie tylko do raportowania](howto-conditional-access-report-only.md) , aby rozpocząć, aby administratorzy mogli ustalić wpływ, jaki będzie miał na istniejących użytkowników. Gdy Administratorzy są woli, że zasady mają zastosowanie, mogą przełączyć **się na wdrożenie lub wdrożyć** je przez dodanie określonych grup i wykluczenie innych.

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory**  >  **Security**  >  **dostępu warunkowego**zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania**wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie**wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz**wybierz pozycję **Użytkownicy i grupy** , a następnie wybierz wszystkie konta, które muszą mieć możliwość korzystania z starszego uwierzytelniania. Wyklucz co najmniej jedno konto, aby zapobiec blokowaniu. Jeśli nie wykluczasz żadnego konta, nie będziesz w stanie utworzyć tych zasad.
   1. Wybierz pozycję **Gotowe**.
1. W obszarze **aplikacje lub akcje w chmurze**wybierz pozycję **wszystkie aplikacje w chmurze**.
   1. Wybierz pozycję **Gotowe**.
1. W obszarze **warunki**  >  **aplikacje klienckie (wersja zapoznawcza)** ustaw wartość **Konfiguruj** na **tak**.
   1. Sprawdź tylko pola **aplikacje mobilne i klienci stacjonarni**  >  **inni klienci**.
   1. Wybierz pozycję **Gotowe**.
1. W obszarze **kontrole dostępu**  >  **Udziel**wybierz opcję **Blokuj dostęp**.
   1. Wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** **tylko na raport**.
1. Wybierz pozycję **Utwórz** , aby utworzyć zasady.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko Raport z dostępem warunkowym](howto-conditional-access-report-only.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)

[Jak skonfigurować urządzenie lub aplikację wielofunkcyjną do wysyłania wiadomości e-mail przy użyciu pakietu Office 365 i Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
