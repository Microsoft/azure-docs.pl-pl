---
title: Zapobieganie automatycznemu przyspieszaniu logowania w usłudze Azure AD przy użyciu zasad odnajdywania home realm
description: Dowiedz się, jak domain_hint automatyczne przyspieszanie do federowanych tożsamości.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: iangithinji
ms.reviewer: hirsin
ms.openlocfilehash: b89e0e1c8bd8109fac8b4b7c05a845a3e234b617
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375554"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Wyłączanie automatycznego przyspieszania federcznego dostawcy tożsamości podczas logowania użytkownika przy użyciu zasad odnajdywania obszarów domowych

Zasady odnajdywania domeny głównej (HRD, [Home Realm Discovery Policy)](/graph/api/resources/homeRealmDiscoveryPolicy) oferują administratorom wiele sposobów kontrolowania sposobu i miejsca uwierzytelniania użytkowników. Sekcja zasad HRD ma na celu pomoc w migracji użytkowników federowanych do poświadczeń zarządzanych przez chmurę, takich jak FIDO, dzięki upewnieniu się, że zawsze odwiedzają stronę logowania usługi Azure AD i nie są automatycznie przyspieszani do federowego dostawcy tożsamości ze względu na wskazówki dotyczące `domainHintPolicy` domeny. [](../authentication/howto-authentication-passwordless-security-key.md)

Te zasady są potrzebne w sytuacjach, gdy administrator aplikacji nie może kontrolować ani aktualizować wskazówek dotyczących domeny podczas logowania.  Na przykład program wysyła użytkownika do strony logowania z dołączonym parametrem , aby automatycznie przyspieszyć użytkownika bezpośrednio do `outlook.com/contoso.com` `&domain_hint=contoso.com` federowego dostawcy tożsamości dla `contoso.com` domeny. Użytkownicy z poświadczeniami zarządzanymi wysyłanymi do federowego dostawcy tożsamości nie mogą zalogować się przy użyciu poświadczeń zarządzanych, co zmniejsza bezpieczeństwo i frustruje użytkowników losowymi logowaniami. Administratorzy, którzy wprowadzają poświadczenia zarządzane, powinni również skonfigurować [te](#suggested-use-within-a-tenant) zasady, aby zapewnić, że użytkownicy będą zawsze korzystać ze swoich poświadczeń zarządzanych.

## <a name="domainhintpolicy-details"></a>Szczegóły domainHintPolicy

Sekcja DomainHintPolicy zasad HRD jest obiektem JSON, który umożliwia administratorowi rezygnację z używania wskazówek dotyczących niektórych domen i aplikacji.  Funkcjonalnie informuje to stronę logowania usługi Azure AD, aby zachowywała się tak, jakby parametr w żądaniu logowania `domain_hint` nie był obecny.

### <a name="the-respect-and-ignore-policy-sections"></a>Sekcje zasad Respekt i Ignoruj

|Sekcja | Znaczenie | Wartości |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Jeśli ta wskazówka domeny jest wysyłana w żądaniu, zignoruj ją. |Tablica adresów domeny (na przykład `contoso.com` ). Obsługuje również `all_domains`|
|`RespectDomainHintForDomains`| Jeśli ta wskazówka domeny jest wysyłana w żądaniu, należy ją przestrzegać, nawet jeśli wskazuje, że aplikacja w żądaniu nie powinna `IgnoreDomainHintForApps` automatycznie przyspieszać. Jest to używane do spowolnienia wycofywania wskazówek dotyczących domeny w ramach sieci — można wskazać, że niektóre domeny powinny być nadal przyspieszone. | Tablica adresów domeny (na przykład `contoso.com` ). Obsługuje również `all_domains`|
|`IgnoreDomainHintForApps`| Jeśli żądanie z tej aplikacji zawiera wskazówkę domeny, zignoruj ją. | Tablica identyfikatorów aplikacji (GUID). Obsługuje również `all_apps`|
|`RespectDomainHintForApps` |Jeśli żądanie z tej aplikacji zawiera wskazówkę domeny, należy ją przestrzegać, nawet jeśli `IgnoreDomainHintForDomains` obejmuje tę domenę. Służy do zapewnienia, że niektóre aplikacje będą nadal działać, jeśli odkryjesz, że działają one bez wskazówek dotyczących domeny. | Tablica identyfikatorów aplikacji (GUID). Obsługuje również `all_apps`|

### <a name="policy-evaluation"></a>Ocena zasad

Logika DomainHintPolicy jest uruchamiana dla każdego żądania przychodzącego, które zawiera wskazówkę domeny i przyspiesza na podstawie dwóch fragmentów danych w żądaniu — domeny w wskazówce domeny i identyfikatora klienta (aplikacji). Krótko mówiąc — "Respekt" dla domeny lub aplikacji ma pierwszeństwo przed instrukcją "Ignoruj" wskazówkę domeny dla danej domeny lub aplikacji.

1. W przypadku braku jakichkolwiek zasad wskazówki dotyczącej domeny lub jeśli żadna z 4 sekcji nie odwołuje się do aplikacji lub wskazówki dotyczącej domeny, pozostałe zasady [HRD zostaną ocenione.](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies)
1. Jeśli jedna (lub obie) sekcja lub zawiera wskazówkę aplikacji lub domeny w żądaniu, wówczas użytkownik zostanie automatycznie przyspieszony do federowego dostawcy tożsamości zgodnie z `RespectDomainHintForApps` `RespectDomainHintForDomains` żądaniem.
1. Jeśli jedna (lub obie) z sekcji lub odwołuje się do aplikacji lub wskazówki dotyczącej domeny w żądaniu i nie odwołuje się do nich sekcja "Respektuj", żądanie nie zostanie automatycznie przyspieszone, a użytkownik pozostanie na stronie logowania usługi Azure AD, aby podać nazwę `IgnoreDomainHintsForApps` `IgnoreDomainHintsForDomains` użytkownika.

Po wprowadzeniu nazwy użytkownika na stronie logowania użytkownik może użyć swoich poświadczeń zarządzanych, jeśli został zarejestrowany.  Jeśli użytkownik zdecyduje się nie używać poświadczeń zarządzanych lub nie ma zarejestrowanego konta, zostanie on w zwykły sposób przenoszony do federowego dostawcy tożsamości w celu wprowadzenia poświadczeń.

## <a name="suggested-use-within-a-tenant"></a>Sugerowane użycie w ramach dzierżawy

Administratorzy domen federowanych powinni skonfigurować tę sekcję zasad HRD w planie czterofazowym. Celem tego planu jest to, aby wszyscy użytkownicy w dzierżawie mieli możliwość korzystania z poświadczeń zarządzanych niezależnie od domeny lub aplikacji, zapisuj te aplikacje, które mają twarde zależności od `domain_hint` użycia.  Ten plan ułatwia administratorom znajdowanie tych aplikacji, zwalnianie ich z nowych zasad i kontynuowanie wycofywania zmian w pozostałej części dzierżawy.

1. Wybierz domenę, w przypadku których ta zmiana ma być początkowo wycofywała się.  Będzie to domena testowa, więc wybierz domenę, która może być bardziej otwarta na zmiany w interfejsie użytkownika (tj. na innej stronie logowania).  Spowoduje to zignorowanie wszystkich wskazówek dotyczących domeny ze wszystkich aplikacji, które używają tej nazwy domeny. [Ustaw](#configuring-policy-through-graph-explorer) te zasady w domyślnych zasadach HRD dzierżawy:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Zbierz opinie od użytkowników domeny testowej. Zbieraj szczegółowe informacje dotyczące aplikacji, które zostały złamane w wyniku tej zmiany — są one zależne od użycia wskazówki dotyczącej domeny i powinny zostać zaktualizowane. Na razie dodaj je do `RespectDomainHintForApps` sekcji :

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Kontynuuj rozszerzanie zasad na nowe domeny, zbierając więcej opinii.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Ukończ swoje rozwiązanie — przekieruj wszystkie domeny do wszystkich, wykluczając te, które powinny być nadal przyspieszane:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

Po ukończeniu kroku 4 wszyscy użytkownicy, z wyjątkiem użytkowników programu , mogą logować się na stronie logowania usługi Azure AD, nawet jeśli wskazówki dotyczące domeny w przeciwnym razie powodują automatyczne przyspieszenie `guestHandlingDomain.com` federowania dostawcy tożsamości.  Wyjątek stanowi sytuacja, w przypadku gdy aplikacja żądająca logowania jest jedną z wykluczeń — w przypadku tych aplikacji wszystkie wskazówki dotyczące domeny będą nadal akceptowane.

## <a name="configuring-policy-through-graph-explorer"></a>Konfigurowanie zasad za pomocą Eksploratora programu Graph

Ustaw zasady [HRD w](/graph/api/resources/homeRealmDiscoveryPolicy) zwykły sposób, używając Microsoft Graph.  

1. Przyznaj uprawnienie Policy.ReadWrite.ApplicationConfiguration w [Eksploratorze programu Graph.](https://developer.microsoft.com/graph/graph-explorer)  
1. Korzystanie z adresu URL `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. Opublikuj nowe zasady pod tym adresem URL lub popraw je, jeśli nadpiszesz `/policies/homerealmdiscoveryPolicies/{policyID}` istniejącą.

Zawartość post lub patch:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

Pamiętaj, aby użyć ukośników, aby poprzeć `Definition` sekcję JSON podczas korzystania z programu Graph.  

`isOrganizationDefault` parametr musi mieć wartość true, ale wartości displayName i definition mogą ulec zmianie.

## <a name="next-steps"></a>Następne kroki

* [Włączanie logowania za pomocą klucza zabezpieczeń bez hasła](../authentication/howto-authentication-passwordless-security-key.md)
* [Włączanie logowania bez hasła przy użyciu Microsoft Authenticator aplikacji](../authentication/howto-authentication-passwordless-phone.md)