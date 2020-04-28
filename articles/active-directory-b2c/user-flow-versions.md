---
title: Wersje przepływu użytkownika w Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się więcej o wersjach przepływów użytkowników dostępnych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40d21d3390396e0cb7e44d4e19598f9b0b691087
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78185625"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Wersje przepływu użytkownika w Azure Active Directory B2C

Przepływy użytkowników w Azure Active Directory B2C (Azure AD B2C) ułatwiają konfigurowanie typowych [zasad](user-flow-overview.md) , które w pełni opisują środowiska tożsamości klientów. Te doświadczenia obejmują rejestrowanie, logowanie, Resetowanie hasła lub edytowanie profilów. W Azure AD B2C można wybrać jedną z kolekcji zalecanych przepływów użytkowników i Podgląd przepływów użytkowników.

Nowe przepływy użytkowników są dodawane jako nowe wersje. Gdy przepływy użytkowników staną się stabilne, zaleca się ich użycie. Przepływy użytkownika są oznaczane jako **zalecane** , jeśli zostały dokładnie przetestowane. Przepływy użytkowników będą uwzględniane w wersji zapoznawczej, dopóki nie zostanie oznaczone jako zalecane. Użyj zalecanego przepływu użytkownika dla dowolnej aplikacji produkcyjnej, ale wybierz z innych wersji, aby przetestować nowe funkcje, gdy staną się dostępne. Nie należy używać starszych wersji zalecanych przepływów użytkowników.

>[!IMPORTANT]
> Chyba że przepływ użytkownika zostanie zidentyfikowany jako **zalecany**, jest on traktowany jako *wersja zapoznawcza*. Należy używać tylko zalecanych przepływów użytkowników dla aplikacji produkcyjnych.

## <a name="v1"></a>Wersja 1

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła | Tak | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu poczty e-mail. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>Ustawienia zgodności tokenu</li><li>[Wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul> |
| Edytowanie profilu | Tak | Umożliwia użytkownikowi skonfigurowanie atrybutów użytkownika. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li></ul> |
| Zaloguj się przy użyciu ROPC | Nie | Umożliwia użytkownikowi z kontem lokalnym logowanie się bezpośrednio w aplikacjach natywnych (nie jest wymagana żadna przeglądarka). Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li></ul> |
| Zaloguj się | Nie | Umożliwia użytkownikowi zalogowanie się do swojego konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>Zablokuj logowanie</li><li>Wymuś Resetowanie hasła</li><li>Nie wylogowuj mnie (KMSI)</ul><br>Nie można dostosować interfejsu użytkownika przy użyciu tego przepływu użytkownika. |
| Rejestrowanie | Nie | Umożliwia użytkownikowi utworzenie konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul> |
| Rejestrowanie i logowanie | Tak | Umożliwia użytkownikowi utworzenie konta lub zalogowanie się na jego koncie. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>Wersja 1.1

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła v 1.1 | Nie | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu ich poczty e-mail (dostępny jest nowy układ strony). Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>Ustawienia zgodności tokenu</li><li>[Wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>Wersja 2

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła v2 | Nie | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu poczty e-mail. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>Ustawienia zgodności tokenu</li><li>[Kontrola wieku](basic-age-gating.md)</li><li>[wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul> |
| Edytowanie profilu v2 | Tak | Umożliwia użytkownikowi skonfigurowanie atrybutów użytkownika. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li></ul> |
| Zaloguj się w wersji 2 | Nie | Umożliwia użytkownikowi zalogowanie się do swojego konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Kontrola wieku](basic-age-gating.md)</li><li>Dostosowywanie strony logowania</li></ul> |
| Zarejestruj się w wersji 2 | Nie | Umożliwia użytkownikowi utworzenie konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Kontrola wieku](basic-age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul> |
| Rejestracja i logowanie w wersji 2 | Nie | Umożliwia użytkownikowi utworzenie konta lub zalogowanie się do niego. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](custom-policy-multi-factor-authentication.md)</li><li>[Kontrola wieku](basic-age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](user-flow-password-complexity.md)</li></ul> |
