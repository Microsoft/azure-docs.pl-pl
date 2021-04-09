---
title: Tworzenie odporności w uwierzytelnianiu użytkowników zewnętrznych przy użyciu Azure Active Directory
description: Przewodnik dla administratorów IT i architektów do tworzenia odpornego uwierzytelniania dla użytkowników zewnętrznych
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
ms.openlocfilehash: 487efce1fe57413dda740c42a7fd3d5ea91cfa49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724963"
---
# <a name="build-resilience-in-external-user-authentication"></a>Tworzenie odporności w uwierzytelnianiu użytkowników zewnętrznych

[Azure Active Directory współpracy B2B](../external-identities/what-is-b2b.md) (Azure AD B2B) to funkcja [tożsamości zewnętrznych](../external-identities/delegate-invitations.md) , która umożliwia współpracę z innymi organizacjami i osobami. Umożliwia ona bezpieczne dołączanie użytkowników-Gości do dzierżawy usługi Azure AD bez konieczności zarządzania poświadczeniami. Użytkownicy zewnętrzni przenoszą swoją tożsamość i poświadczenia z nich od zewnętrznego dostawcy tożsamości (dostawcy tożsamości), więc nie muszą pamiętać nowych poświadczeń. 

## <a name="ways-to-authenticate-external-users"></a>Sposoby uwierzytelniania użytkowników zewnętrznych

Możesz wybrać metody uwierzytelniania użytkownika zewnętrznego w katalogu. Możesz użyć programu Microsoft dostawców tożsamości lub innego dostawców tożsamości.

W każdym dostawcy tożsamości zewnętrznym jest zależna od dostępności tego dostawcy tożsamości. Niektóre metody łączenia się z usługą dostawców tożsamości umożliwiają podwyższenie poziomu odporności.

> [!NOTE] 
> Usługa Azure AD B2B ma wbudowaną możliwość uwierzytelniania dowolnego użytkownika z dowolnego [Azure Active Directory](../index.yml) dzierżawy lub osobistego [konta Microsoft](https://account.microsoft.com/account). Nie trzeba wykonywać żadnych czynności konfiguracyjnych, korzystając z tych wbudowanych opcji.

### <a name="considerations-for-resilience-with-other-idps"></a>Zagadnienia dotyczące odporności z innymi dostawców tożsamości

W przypadku korzystania z zewnętrznego dostawców tożsamości do uwierzytelniania użytkowników-Gości istnieją pewne konfiguracje, które należy zapewnić, aby zapobiec przerwom w działaniu.

| Metoda uwierzytelniania| Zagadnienia dotyczące odporności |
| - | - |
| Federacja z dostawców tożsamościami społecznościowymi, takimi jak [Facebook](../external-identities/facebook-federation.md) czy [Google](../external-identities/google-federation.md).| Musisz zachować konto za pomocą dostawcy tożsamości oraz skonfigurować identyfikator klienta i klucz tajny klienta. |
| [Bezpośrednia Federacja z dostawcami tożsamości SAML i WS-Federation](../external-identities/direct-federation.md)| Aby uzyskać dostęp do punktów końcowych, na których jesteś zależny, musisz współpracować z właścicielem dostawcy tożsamości. <br>Należy zachować metadane zawierające certyfikaty i punkty końcowe. |
| [Wyślij wiadomość e-mail do jednorazowego kodu dostępu](../external-identities/one-time-passcode.md)| Ta metoda jest zależna od systemu poczty e-mail firmy Microsoft, systemu poczty e-mail użytkownika i klienta poczty e-mail użytkownika. |


 

## <a name="self-service-sign-up-preview"></a>Rejestracja samoobsługowa (wersja zapoznawcza)

Alternatywnie, aby wysyłać zaproszenia lub linki, możesz włączyć samoobsługowe [Rejestrowanie się](../external-identities/self-service-sign-up-overview.md).  Umożliwia to zewnętrznym użytkownikom żądanie dostępu do aplikacji. Należy utworzyć [Łącznik interfejsu API](../external-identities/self-service-sign-up-add-api-connector.md) i skojarzyć go z przepływem użytkownika. Można kojarzyć przepływy użytkowników, które definiują środowisko użytkownika w co najmniej jednej aplikacji. 

[Łączniki interfejsu API](../external-identities/api-connectors-overview.md) można zintegrować z samoobsługowym przepływem pracy przy użyciu interfejsów API systemu zewnętrznego. Ta integracja z interfejsem API może służyć do [niestandardowych przepływów pracy zatwierdzania](../external-identities/self-service-sign-up-add-approvals.md), [wykonywania weryfikacji tożsamości](../external-identities/code-samples-self-service-sign-up.md)i innych zadań, takich jak zastąpienie atrybutów użytkownika. Korzystanie z interfejsów API wymaga zarządzania następującymi zależnościami.

* **Uwierzytelnianie łącznika interfejsu API**: skonfigurowanie łącznika wymaga adresu URL punktu końcowego, nazwy użytkownika i hasła. Skonfiguruj proces, za pomocą którego są przechowywane te poświadczenia, i pracuj z właścicielem interfejsu API, aby upewnić się, że wiadomo, co ma harmonogram wygasania.

* **Odpowiedź łącznika interfejsu API**: Projektuj łączniki interfejsu API w przepływie tworzenia konta, aby bezpiecznie się nie powieść, jeśli interfejs API nie jest dostępny. Należy zapoznać się z [przykładowymi odpowiedziami interfejsu](../external-identities/self-service-sign-up-add-api-connector.md) API i udostępnić je [deweloperom.](../external-identities/self-service-sign-up-add-api-connector.md) Współpracuj z zespołem programistycznym interfejsu API, aby przetestować wszystkie możliwe scenariusze odpowiedzi, w tym kontynuację, sprawdzanie poprawności i blokowanie odpowiedzi. 

## <a name="next-steps"></a>Następne kroki
Odporność zasobów dla administratorów i architektów
 
* [Tworzenie odporności przy użyciu zarządzania poświadczeniami](resilience-in-credentials.md)

* [Tworzenie odporności przy użyciu Stanów urządzeń](resilience-with-device-states.md)

* [Tworzenie odporności przy użyciu ciągłej oceny dostępu (CAE)](resilience-with-continuous-access-evaluation.md)

* [Tworzenie odporności w ramach uwierzytelniania hybrydowego](resilience-in-hybrid.md)

* [Tworzenie odporności w dostępie do aplikacji za pomocą serwera proxy aplikacji](resilience-on-premises-access.md)

Zasoby dotyczące odporności dla deweloperów

* [Tworzenie odporności IAM w aplikacjach](resilience-app-development-overview.md)

* [Odporność na kompilacje w systemach CIAM](resilience-b2c.md)
