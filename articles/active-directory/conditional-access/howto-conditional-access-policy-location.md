---
title: Dostęp warunkowy — Blokuj dostęp według lokalizacji — Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego w celu blokowania dostępu do zasobów według lokalizacji IP
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10b0f86447436b8bf08874daf472b1223bff7d0a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83737660"
---
# <a name="conditional-access-block-access-by-location"></a>Dostęp warunkowy: Blokuj dostęp według lokalizacji

Z warunkiem lokalizacji w polu dostęp warunkowy można kontrolować dostęp do aplikacji w chmurze na podstawie lokalizacji sieciowej użytkownika. Warunek lokalizacji jest często używany do blokowania dostępu z krajów/regionów, w których organizacja wie, że ruch nie powinien pochodzić z.

## <a name="define-locations"></a>Definiuj lokalizacje

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory**  >  **zabezpieczenia**  >  **dostępu warunkowego**  >  **o nazwie lokalizacje**.
1. Wybierz pozycję **Nowa lokalizacja**.
1. Nadaj lokalizacji nazwę.
1. Wybierz pozycję **zakresy adresów IP** , jeśli znasz określone zewnętrznie zakresy adresów IPv4, które składają się na tę lokalizację lub **kraje/regiony**.
   1. Podaj **zakresy adresów IP** lub wybierz **kraje/regiony** dla określonej lokalizacji.
      * W przypadku wybrania opcji kraje/regiony można opcjonalnie wybrać opcję dołączenia nieznanych obszarów.
1. Wybierz pozycję **Zapisz**

Więcej informacji o warunku lokalizacji w dostępie warunkowym można znaleźć w artykule [co to jest warunek lokalizacji w Azure Active Directory dostęp warunkowy](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **Azure Active Directory**  >  **Security**  >  **dostępu warunkowego**zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Nadaj zasadom nazwę. Firma Microsoft zaleca, aby organizacje utworzyły znaczący Standard nazw swoich zasad.
1. W obszarze **przypisania**wybierz pozycję **Użytkownicy i grupy**
   1. W obszarze **dołączanie**wybierz pozycję **Wszyscy użytkownicy**.
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **aplikacje lub akcje w chmurze**  >  **Include**wybierz pozycję **wszystkie aplikacje w chmurze**, a następnie wybierz pozycję **gotowe**.
1. W **Conditions**obszarze  >  **Lokalizacja**warunków.
   1. Ustaw **wartość** **tak**
   1. **Dołącz** **wybrane lokalizacje**
   1. Wybierz zablokowaną lokalizację utworzoną dla swojej organizacji.
   1. Kliknij **pozycję**  >  **gotowe**  >  **gotowe**.
1. W obszarze **warunki**  >  **aplikacje klienckie (wersja zapoznawcza)** ustaw wartość opcji **Konfiguruj** na **tak**, a następnie wybierz pozycję **gotowe**.
1. W obszarze blok **kontroli dostępu**  >  **Block**, a następnie wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw opcję **Włącz zasady** na **włączone**.
1. Wybierz pozycję **Utwórz** , aby utworzyć zasady.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko Raport z dostępem warunkowym](howto-conditional-access-report-only.md)

[Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)
