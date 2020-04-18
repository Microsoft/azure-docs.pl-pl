---
title: Dostęp warunkowy — wymagaj usługi MFA dla wszystkich użytkowników — usługa Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego, aby wymagać od wszystkich użytkowników wykonywania uwierzytelniania wieloskładnikowego
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60d0ad0a1c0a1b4d13ce4d386df22406a8ab8e51
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617635"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Dostęp warunkowy: Wymagaj usługi MFA dla wszystkich użytkowników

Jak Alex Weinert, Katalog Bezpieczeństwa Tożsamości w Firmie Microsoft, wspomina w swoim blogu [Pa $ $word nie ma znaczenia:](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

> Twoje hasło nie ma znaczenia, ale MFA nie! Na podstawie naszych badań, Twoje konto jest o ponad 99,9% mniej prawdopodobne, aby zostać naruszone, jeśli używasz mfa.

Wskazówki w tym artykule pomoże organizacji utworzyć zrównoważone zasady usługi MFA dla środowiska.

## <a name="user-exclusions"></a>Wykluczenia użytkowników

Zasady dostępu warunkowego są zaawansowanymi narzędziami, zalecamy wyłączenie następujących kont z zasad:

* **Dostęp awaryjny** lub **break-glass** kont, aby zapobiec blokady konta całej dzierżawcy. W mało prawdopodobnym scenariuszu wszyscy administratorzy są zablokowane z dzierżawy, konto administracyjne dostępu awaryjnego może służyć do logowania się do dzierżawy podjąć kroki w celu odzyskania dostępu.
   * Więcej informacji można znaleźć w artykule [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Konta usług** i **podmioty usługi,** takie jak konto usługi Azure AD Connect Sync. Konta usług są kontami nieinterakcyjnymi, które nie są powiązane z żadnym konkretnym użytkownikiem. Są one zwykle używane przez usługi zaplecza umożliwiające programowy dostęp do aplikacji, ale są również używane do logowania się do systemów do celów administracyjnych. Konta usług, takie jak te, powinny być wykluczone, ponieważ nie można ukończyć programowo. Wywołania przez podmioty usługi nie są blokowane przez dostęp warunkowy.
   * Jeśli organizacja ma te konta w użyciu w skryptach lub kod, należy rozważyć zastąpienie ich [tożsamościami zarządzanymi](../managed-identities-azure-resources/overview.md). Jako tymczasowe obejście można wykluczyć te określone konta z zasad linii bazowej.

## <a name="application-exclusions"></a>Wykluczenia aplikacji

Organizacje mogą mieć wiele aplikacji w chmurze w użyciu. Nie wszystkie z tych aplikacji mogą wymagać równego bezpieczeństwa. Na przykład listy płac i wniosków o frekwencję może wymagać mfa, ale kafeterii prawdopodobnie nie. Administratorzy mogą wykluczyć określone aplikacje z zasad.

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Poniższe kroki pomogą utworzyć zasady dostępu warunkowego, aby wymagać od wszystkich użytkowników wykonywania uwierzytelniania wieloskładnikowego.

1. Zaloguj się do **witryny Azure portal** jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz **pozycję Nowa zasada**.
1. Nadaj polityce nazwę. Zaleca się, aby organizacje tworzyły znaczący standard nazw swoich zasad.
1. W obszarze **Przydziały**wybierz **pozycję Użytkownicy i grupy**
   1. W obszarze **Uwzględnij**wybierz **pozycję Wszyscy użytkownicy**
   1. W obszarze **Wyklucz**wybierz **pozycję Użytkownicy i grupy** i wybierz dostęp awaryjny w organizacji lub konta typu break-glass. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **Aplikacje lub akcje** > w chmurze**Uwzględnij**wybierz pozycję **Wszystkie aplikacje w chmurze**.
   1. W obszarze **Wyklucz**wybierz wszystkie aplikacje, które nie wymagają uwierzytelniania wieloskładnikowego.
1. W **obszarze Warunki** > **aplikacje klienckie (Wersja zapoznawcza)** ustaw **pozycję Konfiguruj** na **Tak**i wybierz opcję **Gotowe**.
1. W obszarze **Kontrola** > dostępu**Przyznanie**wybierz pozycję **Udzielić dostępu**, **Wymagaj uwierzytelniania wieloskładnikowego**i wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw **włącz zasadę** **na Włącz**.
1. Wybierz **pozycję Utwórz,** aby utworzyć, aby włączyć zasady.

### <a name="named-locations"></a>Nazwane lokalizacje

Organizacje mogą zdecydować się na włączenie znanych lokalizacji sieciowych znanych jako **nazwane lokalizacje** do swoich zasad dostępu warunkowego. Te nazwane lokalizacje mogą obejmować zaufane sieci IPv4, takie jak te dla głównej lokalizacji biura. Aby uzyskać więcej informacji na temat konfigurowania nazwanych lokalizacji, zobacz artykuł [Jaki jest warunek lokalizacji w usłudze Azure Active Directory Dostęp warunkowy?](location-condition.md)

W powyższej zasadach przykładowych organizacja może nie wymagać uwierzytelniania wieloskładnikowego, jeśli uzyskujesz dostęp do aplikacji w chmurze z sieci firmowej. W takim przypadku mogą dodać następującą konfigurację do zasad:

1. W obszarze **Przydziały**wybierz **pozycję Warunki** > **lokalizacje**.
   1. Skonfiguruj **Tak**.
   1. Uwzględnij **dowolną lokalizację**.
   1. Wyklucz **wszystkie zaufane lokalizacje**.
   1. Wybierz pozycję **Done** (Gotowe).
1. Wybierz pozycję **Done** (Gotowe).
1. **Zapisz** zmiany zasad.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko dla dostępu warunkowego](howto-conditional-access-report-only.md)

[Symulowanie zachowania logowania za pomocą narzędzia Co jeśli dostęp warunkowy](troubleshoot-conditional-access-what-if.md)
