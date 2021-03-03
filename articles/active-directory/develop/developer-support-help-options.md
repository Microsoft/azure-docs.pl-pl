---
title: Pomoc techniczna i opcje pomocy dla deweloperów aplikacji usługi Azure AD
description: Dowiedz się, jak uzyskać pomoc i pomoc techniczną dotyczącą pytań i problemów związanych z programowaniem podczas tworzenia aplikacji, która integruje się z tożsamościami firmy Microsoft (Azure Active Directory i konto Microsoft)
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: c72a305a7000052be4f11c9c24a76db94de47ace
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650111"
---
# <a name="support-and-help-options-for-developers"></a>Pomoc techniczna i opcje pomocy dla deweloperów

Jeśli dopiero zaczynasz integrację z usługą Azure Active Directory (Azure AD), tożsamościami firmy Microsoft lub interfejsem API Microsoft Graph lub po wdrożeniu nowej funkcji w aplikacji, istnieją sytuacje, w których trzeba uzyskać pomoc od społeczności lub poznać opcje pomocy technicznej dostępne dla deweloperów. Ten artykuł ułatwia zapoznanie się z tymi opcjami, w tym:

> [!div class="checklist"]
> * Jak wyszukać, czy Twoje pytanie nie zostało odebrane przez społeczność, czy też istniejąca dokumentacja funkcji, którą próbujesz zaimplementować, już istnieje
> * W niektórych przypadkach wystarczy użyć naszych narzędzi do obsługi, aby debugować konkretny problem
> * Jeśli nie możesz znaleźć potrzebnej odpowiedzi, możesz zadać pytanie w *firmie Microsoft Q&a*
> * Jeśli znajdziesz problem z jedną z naszych bibliotek uwierzytelniania, zgłoś problem w usłudze *GitHub*
> * Na koniec Jeśli musisz skontaktować się z osobą, możesz chcieć otworzyć żądanie pomocy technicznej

## <a name="search"></a>Wyszukaj

Jeśli masz pytanie związane z programowaniem, możesz znaleźć odpowiedź w dokumentacji, na przykładach usługi [GitHub](https://github.com/azure-samples)lub odpowiedzi na pytania [firmy&Microsoft](/answers/products/) pytań.

### <a name="scoped-search"></a>Wyszukiwanie w zakresie

W celu uzyskania szybszych wyników zakres wyszukiwania do [firmy Microsoft Q&](/answers/products/)dokumentację i przykłady kodu, używając następującego zapytania w ulubionym aparacie wyszukiwania:

```
{Your Search Terms} (site:http://www.docs.microsoft.com/answers/products/ OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Gdzie *{Twoje terminy wyszukiwania}* odpowiadają Twoim słów kluczowych wyszukiwania.

## <a name="use-the-development-support-tools"></a>Korzystanie z narzędzi obsługi deweloperskiej

| Narzędzie  | Opis  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Wklej identyfikator lub token dostępu, aby zdekodować nazwy i wartości oświadczeń. |
| [Eksplorator programu Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)| Narzędzie, które pozwala na wykonywanie żądań i wyświetlanie odpowiedzi dotyczących interfejsu API Microsoft Graph. |

## <a name="post-a-question-to-microsoft-qa"></a>Opublikuj pytanie w firmie Microsoft Q&A

[Microsoft Q&A](/answers/products/) jest preferowanym kanałem dla pytań związanych z programowaniem. W tym miejscu członkowie społeczności deweloperów i członków zespołu firmy Microsoft są bezpośrednio włączeni do rozwiązywania problemów.

Jeśli nie możesz znaleźć odpowiedzi na swoje pytanie, korzystając z wyszukiwania, Prześlij nowe pytanie do [firmy Microsoft Q&a](/answers/products/) . Skorzystaj z jednego z następujących tagów, aby zadawać pytania ułatwiające społeczności identyfikację i szybkie oddawanie odpowiedzi:

|Składnik/obszar  | Tagi |
|---------|---------|
| Biblioteka ADAL | [ADAL](/answers/topics/azure-ad-adal-deprecation.html) |
| Biblioteka MSAL     | [msal](/answers/topics/azure-ad-msal.html) |
| Oprogramowanie pośredniczące OWIN  | [[Azure-Active-Directory]](/answers/topics/azure-active-directory.html) |
| [Funkcje B2B platformy Azure](../external-identities/what-is-b2b.md)  | [[Azure-AD-B2B]](/answers/topics/azure-ad-b2b.html) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[Azure-AD-B2C]](/answers/topics/azure-ad-b2c.html) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[Azure-AD-Graph]](/answers/topics/azure-ad-graph.html) |
| Każdy inny obszar związany z uwierzytelnianiem lub tematami autoryzacji | [[Azure-Active-Directory]](/answers/topics/azure-active-directory.html) |

Następujące wpisy [firmy Microsoft Q&a](/answers/products/) zawierają wskazówki dotyczące sposobu zadawania pytań i dodawania kodu źródłowego. Postępuj zgodnie z poniższymi wskazówkami, aby zwiększyć szanse, aby członkowie społeczności mogli szybko ocenić swoje pytania i odpowiedzieć na nie:

* [Jak mogę zadać dobre pytanie](/answers/articles/24951/how-to-write-a-quality-question.html)
* [Jak utworzyć przykład minimalny, pełny i możliwy do zweryfikowania](/answers/articles/24907/how-to-write-a-quality-answer.html)

## <a name="create-a-github-issue"></a>Utwórz problem w usłudze GitHub

Jeśli znajdziesz błąd lub problem związany z naszymi bibliotekami, zgłoś problem w naszych repozytoriach usługi GitHub. Ponieważ nasze biblioteki są typu open source, można również przesłać żądanie ściągnięcia.

Aby zapoznać się z listą bibliotek i repozytoriów usługi GitHub, zobacz następujące tematy:

* Biblioteki [Azure Active Directory Authentication Library (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) i repozytoria GitHub
* Biblioteki [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) i repozytoria GitHub

## <a name="open-a-support-request"></a>Otwórz żądanie obsługi

Jeśli musisz skontaktować się z osobą, możesz otworzyć żądanie pomocy technicznej. Jeśli jesteś klientem platformy Azure, dostępne są różne opcje pomocy technicznej. Aby porównać plany, zobacz [Tę stronę](https://azure.microsoft.com/support/plans/). Pomoc techniczna dla deweloperów jest również dostępna dla klientów platformy Azure. Informacje o sposobach zakupu planów pomocy technicznej dla deweloperów można znaleźć na [tej stronie](https://azure.microsoft.com/support/plans/developer/).

* Jeśli masz już plan pomocy technicznej platformy Azure, [Otwórz tutaj żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Jeśli nie jesteś klientem platformy Azure, możesz również otworzyć żądanie pomocy technicznej w firmie Microsoft za pośrednictwem [pomocy technicznej komercyjnej](https://support.serviceshub.microsoft.com/supportforbusiness).

Możesz również wypróbować [agenta wirtualnego](https://support.microsoft.com/contactus/?ws=support) , aby uzyskać pomoc techniczną lub zadawać pytania.