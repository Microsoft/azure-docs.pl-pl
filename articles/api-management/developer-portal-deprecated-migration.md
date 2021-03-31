---
title: Migrowanie do nowego portalu dla deweloperów ze starszego portalu dla deweloperów
titleSuffix: Azure API Management
description: Dowiedz się, jak przeprowadzić migrację ze starszego portalu dla deweloperów do nowego portalu dla deweloperów w API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: f5105c685de4b3ccdffe69eec8ee8eeb32976c1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92326025"
---
# <a name="migrate-to-the-new-developer-portal"></a>Migrowanie do nowego portalu dla deweloperów

W tym artykule opisano kroki, które należy wykonać w celu przeprowadzenia migracji z przestarzałego starszego portalu do nowego portalu dla deweloperów w API Management.

> [!IMPORTANT]
> Starsza wersja portalu dla deweloperów jest teraz przestarzała i będzie otrzymywać tylko aktualizacje zabezpieczeń. Można nadal z nich korzystać, tak jak na zwykłych okresach, aż do wycofania z października 2023, gdy zostanie on usunięty z wszystkich usług API Management.

![Portal dla deweloperów API Management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Ulepszenia w nowym portalu dla deweloperów

Nowy portal dla deweloperów dotyczy wielu ograniczeń dla przestarzałego portalu. Jest to [Edytor przeciągania i upuszczania wizualizacji umożliwiający Edytowanie zawartości](api-management-howto-developer-portal-customize.md) oraz dedykowany panel przeznaczony dla projektantów do stylu witryny sieci Web. Strony, dostosowania i konfiguracja są zapisywane jako zasoby Azure Resource Manager w usłudze API Management, co pozwala [zautomatyzować wdrożenia portalu](api-management-howto-developer-portal.md#automate). Na koniec baza kodu portalu jest w stanie Open Source, więc [można ją rozłożyć z funkcjonalnością niestandardową](api-management-howto-developer-portal.md#managed-vs-self-hosted).

## <a name="how-to-migrate-to-new-developer-portal"></a>Jak przeprowadzić migrację do nowego portalu dla deweloperów

Nowy portal dla deweloperów jest niezgodny z przestarzałym portalem, a zautomatyzowana migracja nie jest możliwa. Musisz ręcznie odtworzyć zawartość (strony, tekst, pliki multimedialne) i dostosować wygląd nowego portalu. Dokładne kroki będą się różnić w zależności od dostosowań i złożoności portalu. Aby uzyskać wskazówki, zapoznaj się z [samouczkiem](api-management-howto-developer-portal-customize.md) dotyczącym portalu dla deweloperów. Pozostała konfiguracja, taka jak lista interfejsów API, produktów, użytkowników i dostawców tożsamości, jest automatycznie udostępniana w obu portalach.

> [!IMPORTANT]
> Jeśli nowy portal dla deweloperów został uruchomiony przed, ale nie wprowadzono żadnych zmian, [Zresetuj domyślną zawartość](api-management-howto-developer-portal.md#preview-to-ga) , aby zaktualizować ją do najnowszej wersji.

Podczas migracji z przestarzałego portalu należy pamiętać o następujących zmianach:

- W przypadku uwidocznienia portalu dla deweloperów za pośrednictwem domeny niestandardowej [Przypisz domenę](configure-custom-domain.md) do nowego portalu dla deweloperów. Użyj opcji **Portal dla deweloperów** z listy rozwijanej w Azure Portal.
- [Zastosuj zasady CORS](api-management-howto-developer-portal.md#cors) w interfejsach API, aby włączyć interaktywną konsolę testową.
- W przypadku dodania niestandardowego CSS do stylu portalu należy [replikować style przy użyciu wbudowanego panelu projektowania](api-management-howto-developer-portal-customize.md). Iniekcja CSS nie jest dozwolona w nowym portalu.
- Możesz wstrzyknąć niestandardowy kod JavaScript tylko w [autonomicznej wersji nowego portalu](api-management-howto-developer-portal.md#managed-vs-self-hosted).
- Jeśli API Management znajduje się w sieci wirtualnej i jest dostępna w Internecie za pośrednictwem Application Gateway, [zapoznaj się z tym artykułem dotyczącym tej dokumentacji](api-management-howto-integrate-internal-vnet-appgateway.md) , aby zapoznać się z szczegółowymi krokami konfiguracji. Należy:

    - Włącz łączność z punktem końcowym zarządzania API Management.
    - Włącz łączność z nowym punktem końcowym portalu.
    - Wyłącz wybrane reguły zapory aplikacji sieci Web.

- Jeśli domyślne szablony powiadomień e-mail zostały zmienione w taki sposób, aby zawierały jawnie zdefiniowany przestarzały adres URL portalu, należy zmienić je na adres URL portalu lub wskazać nowy adres URL portalu. Jeśli zamiast tego szablony używają wbudowanego parametru adresu URL portalu, nie są wymagane żadne zmiany.
- *Problemy* i *aplikacje* nie są obsługiwane w nowym portalu dla deweloperów.
- Bezpośrednia integracja z dostawcami tożsamości w serwisach Facebook, Microsoft, Twitter i Google AS nie jest obsługiwana w nowym portalu dla deweloperów. Można zintegrować z tymi dostawcami za pośrednictwem Azure AD B2C.
- Jeśli używasz delegowania, Zmień zwrotny adres URL w aplikacjach i Użyj [punktu końcowego interfejsu API *uzyskiwania tokenu dostępu współdzielonego*](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) zamiast punktu końcowego *Generuj adres URL logowania jednokrotnego* .
- Jeśli używasz usługi Azure AD jako dostawcy tożsamości:

    - Zmień zwrotny adres URL w aplikacji, aby wskazywał nową domenę portalu deweloperów.
    - Zmodyfikuj sufiks adresu URL zwrotnego w aplikacji z `/signin-aad` do `/signin` .

- Jeśli używasz Azure AD B2C jako dostawcy tożsamości:

    - Zmień zwrotny adres URL w aplikacji, aby wskazywał nową domenę portalu deweloperów.
    - Zmodyfikuj sufiks adresu URL zwrotnego w aplikacji z `/signin-aad` do `/signin` .
    - Podaj *imię* i nazwisko, *nazwisko* i *Identyfikator obiektu użytkownika* w oświadczeniach aplikacji.

- Jeśli używasz protokołu OAuth 2,0 w interaktywnej konsoli testowej, Zmień adres URL powrotu w aplikacji, aby wskazywał nową domenę portalu dla deweloperów i zmodyfikować sufiks:

    - Od `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` do `/signin-oauth/code/callback/[serverName]` dla przepływu przydzielenia kodu autoryzacji.
    - Od `/docs/services/[serverName]/console/oauth2/implicit/callback` do `/signin-oauth/implicit/callback` dla niejawnego przepływu dotacji.
- Jeśli używasz OpenID Connect Connect w interaktywnej konsoli testowej, Zmień adres URL powrotu w aplikacji, aby wskazywał nową domenę portalu dla deweloperów i zmodyfikować sufiks:

    - Od `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` do `/signin-oauth/code/callback/[serverName]` dla przepływu przydzielenia kodu autoryzacji.
    - Od `/docs/services/[serverName]/console/openidconnect/implicit/callback` do `/signin-oauth/implicit/callback` dla niejawnego przepływu dotacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o portalu dla deweloperów:

- [Portal deweloperów usługi Azure API Management — omówienie](api-management-howto-developer-portal.md)
- [Dostęp do portalu dla deweloperów i dostosowywanie go](api-management-howto-developer-portal-customize.md)