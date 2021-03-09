---
title: Wersje przepływu użytkownika w Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się więcej o wersjach przepływów użytkowników dostępnych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1c05ff1bf1956943230bf523584025787495d57f
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517858"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Wersje przepływu użytkownika w Azure Active Directory B2C

Przepływy użytkowników w Azure Active Directory B2C (Azure AD B2C) ułatwiają konfigurowanie typowych [zasad](user-flow-overview.md) , które w pełni opisują środowiska tożsamości klientów. Te doświadczenia obejmują rejestrowanie, logowanie, Resetowanie hasła lub edytowanie profilów. W poniższych tabelach opisano przepływy użytkowników, które są dostępne w Azure AD B2C.

> [!IMPORTANT]
> Zmieniliśmy sposób odwoływania się do wersji przepływów użytkownika. Wcześniej oferowaliśmy wersje V1 (gotowe do zastosowań produkcyjnych) oraz wersje V1.1 i V2 (wersje zapoznawcze). Teraz zostały skonsolidowane przepływy użytkowników w dwie wersje:
>
>- **Zalecane** przepływy użytkowników to nowe wersje zapoznawcze przepływów użytkowników. Są one dokładnie przetestowane i łączą wszystkie funkcje starszych wersji **v2** i **1.1** . W przód nowe zalecane przepływy użytkowników zostaną utrzymane i zaktualizowane. Po przejściu do tych nowych zalecanych przepływów użytkowników będziesz mieć dostęp do nowych funkcji po ich udostępnieniu.
>- **Standardowe** przepływy użytkowników, znane wcześniej jako **V1**, są ogólnie dostępne, gotowe do użycia w środowisku produkcyjnym. Jeśli przepływ użytkownika jest krytyczny i zależy od wysoce stabilnych wersji, można nadal używać standardowych przepływów użytkowników, w przypadku których te wersje nie będą przechowywane i aktualizowane.
>
>Wszystkie starsze przepływy użytkowników w wersji zapoznawczej (wersja 1.1 i v2) znajdują się na ścieżce do wycofania od **1 sierpnia 2021**. Jeśli to możliwe, zdecydowanie zalecamy [przełączenie do nowych **zalecanych** wersji](#how-to-switch-to-a-new-recommended-user-flow) najszybciej, jak to możliwe, aby zawsze korzystać z najnowszych funkcji i aktualizacji. *Te zmiany dotyczą tylko chmury publicznej platformy Azure. Inne środowiska będą nadal używać [starszej wersji przepływu użytkownika](user-flow-versions-legacy.md).*

## <a name="recommended-user-flows"></a>Zalecane przepływy użytkowników

Zalecane przepływy użytkowników to wersje w wersji zapoznawczej, które łączą nowe funkcje ze starszymi możliwościami wersji 2 i 1.1. Dzięki temu będzie można utrzymywać i aktualizować zalecane przepływy użytkowników.

| Przepływ użytkownika | Opis |
| --------- | ----------- |
| Resetowanie hasła (wersja zapoznawcza) | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu poczty e-mail. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>Ustawienia zgodności tokenu</li><li>[Kontrola wieku](age-gating.md)</li><li>[wymagania dotyczące złożoności hasła](password-complexity.md)</li></ul> |
| Edytowanie profilu (wersja zapoznawcza) | Umożliwia użytkownikowi skonfigurowanie atrybutów użytkownika. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li></ul> |
| Logowanie (wersja zapoznawcza) | Umożliwia użytkownikowi zalogowanie się do swojego konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Kontrola wieku](age-gating.md)</li><li>Dostosowywanie strony logowania</li></ul> |
| Rejestracja (wersja zapoznawcza) | Umożliwia użytkownikowi utworzenie konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Kontrola wieku](age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](password-complexity.md)</li></ul> |
| Rejestracja i logowanie (wersja zapoznawcza) | Umożliwia użytkownikowi utworzenie konta lub zalogowanie się do niego. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>[Kontrola wieku](age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>Standardowe przepływy użytkownika

Standardowe przepływy użytkowników (wcześniej nazywane w wersji 1) są ogólnie dostępne w przepływach użytkowników gotowych do produkcji. Standardowe przepływy użytkowników nie zostaną zaktualizowane do przodu.

| Przepływ użytkownika | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu poczty e-mail. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>Ustawienia zgodności tokenu</li><li>[Wymagania dotyczące złożoności hasła](password-complexity.md)</li></ul> |
| Edytowanie profilu | Umożliwia użytkownikowi skonfigurowanie atrybutów użytkownika. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li></ul> |
| Zaloguj | Umożliwia użytkownikowi zalogowanie się do swojego konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>Zablokuj logowanie</li><li>Wymuś Resetowanie hasła</li><li>Nie wylogowuj mnie (KMSI)</ul><br>Nie można dostosować interfejsu użytkownika przy użyciu tego przepływu użytkownika. |
| Rejestrowanie | Umożliwia użytkownikowi utworzenie konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Wymagania dotyczące złożoności hasła](password-complexity.md)</li></ul> |
| Rejestrowanie i logowanie | Umożliwia użytkownikowi utworzenie konta lub zalogowanie się na jego koncie. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Wymagania dotyczące złożoności hasła](password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>Jak przełączyć się do nowego zalecanego przepływu użytkownika

Aby przełączyć ze starszej wersji przepływu użytkownika do nowej **zalecanej** wersji zapoznawczej, wykonaj następujące kroki:

1. Utwórz nowe zasady przepływu użytkownika, wykonując czynności opisane w [samouczku: Tworzenie przepływów użytkowników w Azure Active Directory](tutorial-create-user-flows.md). Podczas tworzenia przepływu użytkownika wybierz **zalecaną** wersję.

3. Skonfiguruj nowy przepływ użytkownika przy użyciu tych samych ustawień, które zostały skonfigurowane w starszych zasadach.

4. Zaktualizuj adres URL logowania aplikacji do nowo utworzonych zasad.

5. Po przetestowaniu przepływu użytkownika i potwierdzeniu jego działania usuń starszy przepływ użytkownika, wykonując następujące czynności:
   1. W menu przegląd Azure AD B2C dzierżawy wybierz pozycję **przepływy użytkownika**.
   2. Znajdź przepływ użytkownika, który chcesz usunąć.
   3. W ostatniej kolumnie Wybierz menu kontekstowe (**...**), a następnie wybierz pozycję **Usuń**.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>Czy nadal mogę utworzyć starsze przepływy użytkownika w wersji 2 i 1.1?

Nie będzie można tworzyć nowych przepływów użytkowników w oparciu o starsze wersje w wersji 2 i 1.1, ale możesz nadal odczytywać, aktualizować i usuwać wszystkie starsze, obecnie używane przepływy użytkownika w wersji 2 i 1.1.

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>Czy istnieje jakieś powód, aby nadal korzystać ze starszych przepływów użytkowników w wersji 2 i 1.1?

Nie całkiem. Nowe **zalecane** wersje zapoznawcze zawierają te same funkcje, co w przypadku starszych wersji wersji 2 i 1.1. Nic nie zostało usunięte i w rzeczywistości zawierają teraz dodatkowe funkcje.

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>Jeśli nie przełączysz się ze starszych zasad v2 i 1.1, jak wpłynie to na moją aplikację?

Jeśli używasz starszego przepływu użytkownika w wersji 2 lub 1.1, ta zmiana wersji nie będzie miała wpływu na aplikację. Aby uzyskać dostęp do nowych funkcji lub zmian zasad w przyszłości, musisz przełączyć się do nowych **zalecanych** wersji.

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Czy firma Microsoft będzie nadal obsługiwać starsze zasady przepływu użytkownika w wersji 2 lub 1.1?

Starsze wersje wersji 2 i 1.1 przepływów użytkownika będą nadal w pełni obsługiwane.
