---
title: Zapobiegaj autoprzyspieszaniu logowania w usłudze Azure AD przy użyciu zasad odnajdywania obszaru głównego
description: Dowiedz się, jak zapobiegać domain_hint autoprzyspieszania do federacyjnego dostawców tożsamości.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: hirsin
ms.openlocfilehash: 53dfdfaf37695059d6d52428c2ba109970d9f7f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589382"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Wyłącz funkcję autoprzyspieszania w dostawcy tożsamości federacyjnym podczas logowania użytkownika przy użyciu zasad odnajdywania obszaru macierzystego

[Zasady odnajdywania obszaru macierzystego](/graph/api/resources/homeRealmDiscoveryPolicy) (HRD) oferują administratorom wiele sposobów na kontrolowanie sposobu, w jaki użytkownicy są uwierzytelniani. `domainHintPolicy`Sekcja zasad HRD służy do przeprowadzenia migracji użytkowników federacyjnych do poświadczeń zarządzanych przez chmurę, takich jak [Fido](../authentication/howto-authentication-passwordless-security-key.md), przez zapewnienie, że zawsze odwiedzają stronę logowania usługi Azure AD i nie są autoprzyspieszone do federacyjnego dostawcy tożsamości ze względu na wskazówki dotyczące domeny.

Te zasady są niezbędne w sytuacjach, w których administratorzy nie mogą kontrolować ani aktualizować dodawania podpowiedzi do domeny podczas logowania.  Na przykład program `outlook.com/contoso.com` wysyła użytkownika do strony logowania z `&domain_hint=contoso.com` parametrem dołączonym, aby przyspieszyć użytkownika bezpośrednio do dostawcy tożsamości federacyjnego dla `contoso.com` domeny. Użytkownicy z poświadczeniami zarządzanymi wysyłanymi do dostawcy tożsamości federacyjnego nie mogą logować się przy użyciu ich poświadczeń zarządzanych, co zmniejsza bezpieczeństwo i frustrujące użytkowników przy użyciu losowych środowisk logowania. Administratorzy wprowadzający zarządzane poświadczenia [powinny również skonfigurować te zasady](#suggested-use-within-a-tenant) , aby upewnić się, że użytkownicy będą zawsze mogli korzystać z ich zarządzanych poświadczeń.

## <a name="domainhintpolicy-details"></a>Szczegóły DomainHintPolicy

Sekcja DomainHintPolicy zasad HRD jest obiektem JSON, który umożliwia administratorowi rezygnację z określonych domen i aplikacji z poziomu użycia wskazówki domeny.  Funkcje te mówią, że strona logowania usługi Azure AD będzie zachowywać się tak, jakby `domain_hint` parametr w żądaniu logowania był nieobecny.

### <a name="the-respect-and-ignore-policy-sections"></a>Sekcje zasad przestrzegania i ignorowania

|Sekcja | Znaczenie | Wartości |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Jeśli ta Wskazówka domeny jest wysyłana w żądaniu, zignoruj ją. |Tablica adresów domen (na przykład `contoso.com` ). Obsługuje również `all_domains`|
|`RespectDomainHintForDomains`| Jeśli ta Wskazówka domeny jest wysyłana w żądaniu, należy to przestrzegać nawet wtedy, gdy `IgnoreDomainHintForApps` wskazuje, że aplikacja w żądaniu nie powinna autoprzyspieszać. Jest to używane do spowalniania wdrażania przestarzałych wskazówek dotyczących domen w sieci — można wskazać, że niektóre domeny powinny być nadal przyspieszone. | Tablica adresów domen (na przykład `contoso.com` ). Obsługuje również `all_domains`|
|`IgnoreDomainHintForApps`| Jeśli żądanie od tej aplikacji jest dostarczane z wskazówką domeny, zignoruj ją. | Tablica identyfikatorów aplikacji (GUIDs). Obsługuje również `all_apps`|
|`RespectDomainHintForApps` |Jeśli żądanie od tej aplikacji jest dostarczane z wskazówką domeny, należy to przestrzegać nawet wtedy `IgnoreDomainHintForDomains` , gdy obejmuje tę domenę. Służy do zapewnienia, że niektóre aplikacje będą działać, jeśli zostaną wykryte przerwania bez wskazówek dotyczących domeny. | Tablica identyfikatorów aplikacji (GUIDs). Obsługuje również `all_apps`|

### <a name="policy-evaluation"></a>Ocena zasad

Logika DomainHintPolicy jest uruchamiana na każdym żądaniu przychodzącym zawierającym wskazówkę dotyczącą domeny i przyspiesza na podstawie dwóch fragmentów danych w żądaniu — domeny w podpowiedzi domeny i identyfikatora klienta (aplikacji). W skrócie — "przestrzeganie" dla domeny lub aplikacji ma pierwszeństwo przed instrukcją ignorowania wskazówki domeny dla danej domeny lub aplikacji.

1. W przypadku braku jakichkolwiek zasad dotyczących podpowiedzi domen lub jeśli żadna z 4 sekcji odwołuje się do wymienionej wskazówki aplikacji lub domeny, [pozostałe zasady HRD zostaną ocenione](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies).
1. Jeśli jeden (lub oba) `RespectDomainHintForApps` lub sekcja lub `RespectDomainHintForDomains` zawiera wskazówkę dotyczącą aplikacji lub domeny w żądaniu, wówczas użytkownik będzie przełączany do dostawcy tożsamości federacyjnego zgodnie z żądaniem.
1. Jeśli jedna (lub obie) `IgnoreDomainHintsForApps` lub `IgnoreDomainHintsForDomains` odwołuje się do aplikacji lub wskazówki domeny w żądaniu, a nie odwołuje się do nich, żądanie nie zostanie przyspieszone ponownie, a użytkownik pozostanie na stronie logowania usługi Azure AD, aby podać nazwę użytkownika.

Gdy użytkownik wprowadzi nazwę użytkownika na stronie logowania, może użyć swoich poświadczeń zarządzanych, jeśli są one zarejestrowane.  Jeśli nie użyjesz poświadczeń zarządzanych lub nie zarejestrowano żadnego z nich, zostaną one przeniesione do dostawcy tożsamości federacyjnego dla wpisu poświadczenia w zwykły sposób.

## <a name="suggested-use-within-a-tenant"></a>Sugerowane użycie w ramach dzierżawy

Administratorzy domen federacyjnych powinni skonfigurować tę sekcję zasad HRDymi w planie obejmującym cztery fazy. Celem tego planu jest ostatecznie, że wszyscy użytkownicy w dzierżawie mogą korzystać z zarządzanych poświadczeń bez względu na domenę lub aplikację, a także zapisywać te aplikacje, które mają twarde zależności dotyczące `domain_hint` użycia.  Ten plan ułatwia administratorom znalezienie tych aplikacji, wyłączenie ich z nowych zasad i kontynuowanie wdrażania zmian w pozostałej części dzierżawy.

1. Wybierz domenę, aby wstępnie wycofać tę zmianę do.  Będzie to Twoja domena testowa, więc wybierz jedną, która może być bardziej przypadać na zmiany w środowisku użytkownika (tj. na innej stronie logowania).  Spowoduje to zignorowanie wszystkich wskazówek dotyczących domeny ze wszystkich aplikacji, które używają tej nazwy domeny. [Ustaw](#configuring-policy-through-graph-explorer) te zasady w ramach dzierżawy — domyślne zasady HRD:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Zbierz informacje zwrotne od użytkowników domeny testowej. Zbierz szczegóły dotyczące aplikacji, które uległy w wyniku tej zmiany — mają zależność od użycia wskazówki domeny i powinny zostać zaktualizowane. Na razie Dodaj je do `RespectDomainHintForApps` sekcji:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Kontynuuj rozszerzanie wdrożenia zasad do nowych domen, zbierając więcej informacji zwrotnych.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Ukończ wdrażanie — docelowa wszystkie domeny, z wyłączeniem tych, które powinny być nadal przyspieszone:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

Po wykonaniu kroku 4 wszyscy użytkownicy `guestHandlingDomain.com` mogą zalogować się na stronie logowania usługi Azure AD, nawet jeśli w przeciwnym razie wskazówki dotyczące domeny byłyby powodowały autoprzyspieszanie do FEDERACYJNEGO dostawcy tożsamości.  Wyjątkiem jest to, że jeśli aplikacja żądająca logowania jest jednym z wykluczonych — dla tych aplikacji, wszystkie wskazówki dotyczące domeny będą nadal akceptowane.

## <a name="configuring-policy-through-graph-explorer"></a>Konfigurowanie zasad przy użyciu Eksploratora grafów

Ustaw [zasady HRD](/graph/api/resources/homeRealmDiscoveryPolicy) jako standardowe przy użyciu Microsoft Graph.  

1. Przyznaj uprawnienie Policy. ReadWrite. ApplicationConfiguration w [Eksploratorze grafu](https://developer.microsoft.com/graph/graph-explorer).  
1. Użyj adresu URL `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. Opublikuj nowe zasady na tym adresie URL lub zastępowanie istniejących zasad, jeśli zastąpisz `/policies/homerealmdiscoveryPolicies/{policyID}` istniejący.

Zawartość OPUBLIKOWANa lub Poprawka:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

Pamiętaj, aby użyć ukośników, aby wypróbować `Definition` sekcję JSON podczas korzystania z programu Graph.  

`isOrganizationDefault` musi mieć wartość true, ale nazwa wyświetlana i definicja mogą ulec zmianie.

## <a name="next-steps"></a>Następne kroki

* [Włącz logowanie przy użyciu klucza zabezpieczeń bezhasło](../authentication/howto-authentication-passwordless-security-key.md)
* [Włączanie logowania bez hasła przy użyciu aplikacji Microsoft Authenticator](../authentication/howto-authentication-passwordless-phone.md)