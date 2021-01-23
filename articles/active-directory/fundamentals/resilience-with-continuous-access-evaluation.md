---
title: Tworzenie odporności przy użyciu ciągłej oceny dostępu w Azure Active Directory
description: Przewodnik dla architektów i administratorów IT przy użyciu CAE
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a12cc3299fbb5916afba48d2608024f28999634
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724631"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>Tworzenie odporności przy użyciu ciągłej oceny dostępu

[Wersja ewaluacyjna ciągłego dostępu](../conditional-access/concept-continuous-access-evaluation.md) (CAE) umożliwia aplikacjom usługi Azure AD subskrybowanie zdarzeń krytycznych, które następnie można ocenić i wymusić. Obejmuje to ocenę następujących zdarzeń:

* Konto użytkownika jest usuwane lub wyłączone.

* Hasło użytkownika zostało zmienione

* Uwierzytelnianie wieloskładnikowe jest włączone dla użytkownika.

* Administrator jawnie odwoła token.

* Wykryto podwyższone ryzyko użytkownika.

W związku z tym aplikacje mogą odrzucać niewygasłe tokeny na podstawie zdarzeń zasygnalizowane przez usługę Azure AD, jak przedstawiono na poniższym diagramie.

![conceptualiagram CAE](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>Jak pomoc CAE?

Ten mechanizm umożliwia usłudze Azure AD wystawianie tokenów o dłuższym czasie, a jednocześnie umożliwia aplikacjom odwoływanie dostępu i wymuszanie ponownego uwierzytelniania tylko wtedy, gdy jest to konieczne. Wynikiem tego wzorca jest mniejsza liczba wywołań do uzyskania tokenów, co oznacza, że przepływ end-to-end jest bardziej odporny. 

Aby można było korzystać z CAE, zarówno usługa, jak i klient muszą mieć możliwość CAEnia. Usługi Microsoft 365, takie jak Exchange Online, Teams i SharePoint Online Support CAE. Po stronie klienta środowiska oparte na przeglądarce, które korzystają z tych usług Office 365 (np. aplikacja sieci Web programu Outlook) i określonych wersji natywnych klientów pakietu Office 365, to CAE. Więcej usług w chmurze firmy Microsoft stanie się CAE.

Firma Microsoft współpracuje z branżą, aby kompilować [standardy](https://openid.net/wg/sse/) , które umożliwią aplikacjom innych firm korzystanie z tej funkcji. Możesz również opracowywać aplikacje, które są CAE. Aby uzyskać więcej informacji, zobacz jak utworzyć odporność w aplikacji.

## <a name="how-do-i-implement-cae"></a>Jak mogę wdrożyć CAE?

* [Włącz CAE](../conditional-access/concept-continuous-access-evaluation.md) w konfiguracji zabezpieczeń usługi Azure AD.

* Upewnij się, że Twoja organizacja używa [zgodnych wersji](../conditional-access/concept-continuous-access-evaluation.md) Microsoft Office aplikacji natywnych.

* [Zoptymalizuj polecenia ponownego uwierzytelniania](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

 
## <a name="next-steps"></a>Następne kroki
Odporność zasobów dla administratorów i architektów
 
* [Tworzenie odporności przy użyciu zarządzania poświadczeniami](resilience-in-credentials.md)

* [Tworzenie odporności przy użyciu Stanów urządzeń](resilience-with-device-states.md)

* [Tworzenie odporności w uwierzytelnianiu użytkowników zewnętrznych](resilience-b2b-authentication.md)

* [Tworzenie odporności w ramach uwierzytelniania hybrydowego](resilience-in-hybrid.md)

* [Tworzenie odporności w dostępie do aplikacji za pomocą serwera proxy aplikacji](resilience-on-premises-access.md)

Zasoby dotyczące odporności dla deweloperów

* [Tworzenie odporności IAM w aplikacjach](resilience-app-development-overview.md)

* [Odporność na kompilacje w systemach CIAM](resilience-b2c.md)