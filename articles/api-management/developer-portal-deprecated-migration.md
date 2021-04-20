---
title: Migrowanie do nowego portalu dla deweloperów ze starszego portalu dla deweloperów
titleSuffix: Azure API Management
description: Dowiedz się, jak przeprowadzić migrację ze starszego portalu dla deweloperów do nowego portalu dla deweloperów w API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: e4f9f3822b58886f7d453d52402b078d8401133f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738886"
---
# <a name="migrate-to-the-new-developer-portal"></a>Migrowanie do nowego portalu dla deweloperów

W tym artykule opisano kroki, które należy wykonać, aby przeprowadzić migrację ze starszego portalu przestarzałego do nowego portalu deweloperów w API Management.

> [!IMPORTANT]
> Portal dla deweloperów w starszej wersji jest teraz przestarzały i będzie otrzymywać tylko aktualizacje zabezpieczeń. Możesz nadal z niego korzystać, jak zwykle, do momentu wycofania jej w październiku 2023 r., gdy zostanie ona usunięta ze wszystkich API Management usług.

![API Management portal dla deweloperów](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Ulepszenia w nowym portalu dla deweloperów

Nowy portal dla deweloperów rozwiązuje wiele ograniczeń przestarzałego portalu. Zawiera ona edytor [wizualizacji typu "przeciągnij i upuść"](api-management-howto-developer-portal-customize.md) do edytowania zawartości oraz dedykowany panel dla projektantów, który umożliwia styl witryny internetowej. Strony, dostosowania i konfiguracja są zapisywane jako Azure Resource Manager zasobów w usłudze API Management, która umożliwia [automatyzację wdrożeń portalu.](automate-portal-deployments.md) Na koniec baza kodu portalu to baza kodu typu open source, więc można ją rozszerzyć [za pomocą funkcji niestandardowych.](api-management-howto-developer-portal.md#managed-vs-self-hosted)

## <a name="how-to-migrate-to-new-developer-portal"></a>Jak przeprowadzić migrację do nowego portalu dla deweloperów

Nowy portal dla deweloperów jest niezgodny z przestarzałym portalem i automatyczna migracja nie jest możliwa. Musisz ręcznie ponownie utworzyć zawartość (strony, tekst, pliki multimedialne) i dostosować wygląd nowego portalu. Dokładne kroki różnią się w zależności od dostosowań i złożoności portalu. Aby uzyskać wskazówki, zapoznaj się [z samouczkiem](api-management-howto-developer-portal-customize.md) portalu dla deweloperów. Pozostała konfiguracja, na przykład lista interfejsów API, produktów, użytkowników i dostawców tożsamości, jest automatycznie udostępniana w obu portalach.

> [!IMPORTANT]
> Jeśli nowy portal dla deweloperów był już wcześniej uruchomiony, ale nie zostały wprowadzone żadne zmiany, zresetuj zawartość domyślną, aby zaktualizować go do najnowszej wersji.

Podczas migracji z przestarzałego portalu należy pamiętać o następujących zmianach:

- Jeśli uwidocznisz portal deweloperów za pośrednictwem domeny niestandardowej, [przypisz domenę](configure-custom-domain.md) do nowego portalu dla deweloperów. Użyj opcji **Portal deweloperów** z listy rozwijanej w Azure Portal.
- [Zastosuj zasady CORS do interfejsów](developer-portal-faq.md#cors) API, aby włączyć interaktywną konsolę testową.
- W przypadku wstrzykowania niestandardowego kodu CSS w celu zmiany stylu portalu należy zreplikować style przy użyciu [wbudowanego panelu projektowego](api-management-howto-developer-portal-customize.md). Wstrzykiwanie kodu CSS nie jest dozwolone w nowym portalu.
- Niestandardowy kod JavaScript można wstrzyknąć tylko w własnej [wersji nowego portalu.](api-management-howto-developer-portal.md#managed-vs-self-hosted)
- Jeśli urządzenie API Management w sieci wirtualnej i jest dostępne w Internecie za pośrednictwem usługi [Application Gateway,](api-management-howto-integrate-internal-vnet-appgateway.md) zapoznaj się z tym artykułem dokumentacji, aby uzyskać szczegółowe instrukcje konfiguracji. Musisz:

    - Włącz łączność z API Management punktu końcowego zarządzania usługi.
    - Włącz łączność z nowym punktem końcowym portalu.
    - Wyłącz wybrane Web Application Firewall reguł.

- Jeśli domyślne szablony powiadomień e-mail zostały zmienione tak, aby zawierały jawnie zdefiniowany przestarzały adres URL portalu, zmień je tak, aby używać parametru adresu URL portalu lub wskazać nowy adres URL portalu. Jeśli szablony zamiast tego używają wbudowanego parametru adresu URL portalu, nie są wymagane żadne zmiany.
- *Problemy* i *aplikacje* nie są obsługiwane w nowym portalu dla deweloperów.
- Bezpośrednia integracja z firmami Facebook, Microsoft, Twitter i Google jako dostawcami tożsamości nie jest obsługiwana w nowym portalu dla deweloperów. Integrację z tymi dostawcami można zintegrować za pośrednictwem Azure AD B2C.
- Jeśli używasz delegowania, zmień zwracany adres URL w aplikacjach i użyj punktu końcowego interfejsu [  API](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) uzyskiwania dostępu współdzielonych zamiast punktu końcowego *Generuj adres URL logowania jednokrotnego.*
- Jeśli używasz usługi Azure AD jako dostawcy tożsamości:

    - Zmień zwracany adres URL w aplikacji, aby wskazać nową domenę portalu deweloperów.
    - Zmodyfikuj sufiks zwracanych adresów URL w aplikacji z wartości `/signin-aad` na `/signin` .

- Jeśli używasz usługi Azure AD B2C jako dostawcy tożsamości:

    - Zmień zwracany adres URL w aplikacji, aby wskazać nową domenę portalu deweloperów.
    - Zmodyfikuj sufiks zwracanych adresów URL w aplikacji z `/signin-aad` na `/signin` .
    - Uwzględnij *dane imię,* *nazwisko* i *identyfikator obiektu użytkownika* w oświadczeniach aplikacji.

- Jeśli używasz protokołu OAuth 2.0 w konsoli testów interakcyjnych, zmień zwracany adres URL w aplikacji, aby wskazać nową domenę portalu deweloperów i zmodyfikować sufiks:

    - Od `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` do `/signin-oauth/code/callback/[serverName]` przepływu udzielania kodu autoryzacji.
    - Od `/docs/services/[serverName]/console/oauth2/implicit/callback` do `/signin-oauth/implicit/callback` przepływu niejawnego udzielania.
- Jeśli używasz OpenID Connect konsoli testów interaktywnych, zmień zwracany adres URL w aplikacji, aby wskazać nową domenę portalu deweloperów i zmodyfikować sufiks:

    - Od `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` do `/signin-oauth/code/callback/[serverName]` przepływu udzielania kodu autoryzacji.
    - Od `/docs/services/[serverName]/console/openidconnect/implicit/callback` do `/signin-oauth/implicit/callback` przepływu niejawnego udzielania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o portalu dla deweloperów:

- [Portal deweloperów usługi Azure API Management — omówienie](api-management-howto-developer-portal.md)
- [Uzyskiwanie dostępu do portalu dla deweloperów i dostosowywanie go](api-management-howto-developer-portal-customize.md)