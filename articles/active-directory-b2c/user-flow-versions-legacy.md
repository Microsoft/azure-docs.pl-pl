---
title: Starsze wersje przepływu użytkowników w Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się więcej o starszych wersjach przepływów użytkowników dostępnych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1fbe93c93b5ede2c6b031dab53a1450da473f802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517807"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>Starsze wersje przepływu użytkowników w Azure Active Directory B2C

> [!IMPORTANT]
> W tym artykule opisano starszą metodę umieszczania wersji dla przepływów użytkowników, która oferuje wersje V1 (gotowe do produkcji) i wersje V 1.1 i v2 (wersja zapoznawcza) przepływów użytkowników. Środowiska inne niż chmura publiczna platformy Azure będą nadal używać tej starszej metody przechowywania wersji. W chmurze publicznej platformy Azure ta metoda jest zastępowana przez [nowe **zalecane** i w wersji **zapoznawczej**](user-flow-versions.md).
> 
Przepływy użytkowników w Azure Active Directory B2C (Azure AD B2C) ułatwiają konfigurowanie typowych [zasad](user-flow-overview.md) , które w pełni opisują środowiska tożsamości klientów. Te doświadczenia obejmują rejestrowanie, logowanie, Resetowanie hasła lub edytowanie profilów.

W poniższej tabeli, chyba że przepływ użytkownika zostanie zidentyfikowany jako **zalecany**, jest on traktowany jako *wersja zapoznawcza*. Należy używać tylko zalecanych przepływów użytkowników dla aplikacji produkcyjnych.

## <a name="v1"></a>Wersja 1

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła | Tak | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu poczty e-mail. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>Ustawienia zgodności tokenu</li><li>[Wymagania dotyczące złożoności hasła](password-complexity.md)</li></ul> |
| Edytowanie profilu | Tak | Umożliwia użytkownikowi skonfigurowanie atrybutów użytkownika. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li></ul> |
| Zaloguj się przy użyciu ROPC | Nie | Umożliwia użytkownikowi z kontem lokalnym logowanie się bezpośrednio w aplikacjach natywnych (nie jest wymagana żadna przeglądarka). Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li></ul> |
| Zaloguj | Nie | Umożliwia użytkownikowi zalogowanie się do swojego konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>Zablokuj logowanie</li><li>Wymuś Resetowanie hasła</li><li>Nie wylogowuj mnie (KMSI)</ul><br>Nie można dostosować interfejsu użytkownika przy użyciu tego przepływu użytkownika. |
| Rejestrowanie | Nie | Umożliwia użytkownikowi utworzenie konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Wymagania dotyczące złożoności hasła](password-complexity.md)</li></ul> |
| Rejestrowanie i logowanie | Tak | Umożliwia użytkownikowi utworzenie konta lub zalogowanie się na jego koncie. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Wymagania dotyczące złożoności hasła](password-complexity.md)</li></ul>|

## <a name="v11"></a>Wersja 1.1

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła v 1.1 | Nie | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu ich poczty e-mail (dostępny jest nowy układ strony). Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>Ustawienia zgodności tokenu</li><li>[Wymagania dotyczące złożoności hasła](password-complexity.md)</li></ul> |

## <a name="v2"></a>Wersja 2

| Przepływ użytkownika | Zalecane | Opis |
| --------- | ----------- | ----------- |
| Resetowanie hasła v2 | Nie | Umożliwia użytkownikowi wybranie nowego hasła po zweryfikowaniu poczty e-mail. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>Ustawienia zgodności tokenu</li><li>[Kontrola wieku](age-gating.md)</li><li>[wymagania dotyczące złożoności hasła](password-complexity.md)</li></ul> |
| Edytowanie profilu v2 | Tak | Umożliwia użytkownikowi skonfigurowanie atrybutów użytkownika. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li></ul> |
| Zaloguj się w wersji 2 | Nie | Umożliwia użytkownikowi zalogowanie się do swojego konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Kontrola wieku](age-gating.md)</li><li>Dostosowywanie strony logowania</li></ul> |
| Zarejestruj się w wersji 2 | Nie | Umożliwia użytkownikowi utworzenie konta. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>[Okres istnienia tokenu](tokens-overview.md)</li><li>Ustawienia zgodności tokenu</li><li>Zachowanie sesji</li><li>[Kontrola wieku](age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](password-complexity.md)</li></ul> |
| Rejestracja i logowanie w wersji 2 | Nie | Umożliwia użytkownikowi utworzenie konta lub zalogowanie się do niego. Korzystając z tego przepływu użytkownika, można skonfigurować: <ul><li>[Uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md)</li><li>[Kontrola wieku](age-gating.md)</li><li>[Wymagania dotyczące złożoności hasła](password-complexity.md)</li></ul> |