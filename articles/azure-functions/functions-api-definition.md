---
title: Metadane OpenAPI w usłudze Azure Functions
description: Omówienie obsługi openapi w usłudze Azure Functions
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: cbfd0e36307210851070c22e74acb0a858446ce1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866714"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Obsługa metadanych OpenAPI 2.0 w usłudze Azure Functions (wersja zapoznawcza)
OpenAPI 2.0 (dawniej Swagger) obsługa metadanych w usłudze Azure Functions jest funkcją w wersji zapoznawczej, której można użyć do zapisu definicji OpenAPI 2.0 wewnątrz aplikacji funkcji. Następnie można hostować ten plik przy użyciu aplikacji funkcji.

> [!IMPORTANT]
> Funkcja interfejsu OpenAPI w wersji zapoznawczej jest obecnie dostępna tylko w środowisku uruchomieniowym w wersji 1.x. Informacje na temat tworzenia aplikacji funkcji w wersji 1.x [można znaleźć tutaj](./functions-versions.md#creating-1x-apps).

[Metadane OpenAPI](https://swagger.io/) umożliwia funkcję, która jest hosting interfejsu API REST do korzystania przez wiele różnych innych programów. To oprogramowanie zawiera oferty firmy Microsoft, takie jak PowerApps i [funkcja aplikacji API usługi Azure App Service,](../app-service/overview.md)narzędzia programistyczne innych firm, takie jak [Postman,](https://www.getpostman.com/docs/importing_swagger)i [wiele innych pakietów.](https://swagger.io/tools/)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Zalecamy rozpoczęcie [od samouczka wprowadzenie,](./functions-api-definition-getting-started.md) a następnie powrót do tego dokumentu, aby dowiedzieć się więcej o określonych funkcjach.

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>Włącz obsługę definicji OpenAPI
Wszystkie ustawienia OpenAPI można skonfigurować na stronie **Definicja interfejsu API** w **funkcjach platformy**aplikacji funkcyjnej .

> [!NOTE]
> Funkcja definicji interfejsu API funkcji nie jest obecnie obsługiwana w czasie wykonywania w wersji beta.

Aby włączyć generowanie hostowanych definicji OpenAPI i definicji szybkiego startu, ustaw **źródło definicji interfejsu API** na Function **(Preview)**. **Zewnętrzny adres URL** umożliwia funkcji użycie definicji OpenAPI, która jest hostowana w innym miejscu.

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>Generowanie szkieletu łupu z metadanych funkcji
Szablon może pomóc rozpocząć pisanie pierwszej definicji OpenAPI. Funkcja szablonu definicji tworzy rozrzedzoną definicję OpenAPI przy użyciu wszystkich metadanych w pliku function.json dla każdej funkcji wyzwalacza HTTP. Musisz wypełnić więcej informacji na temat interfejsu API ze [specyfikacji OpenAPI](https://swagger.io/specification/), takich jak szablony żądań i odpowiedzi.

Aby uzyskać instrukcje krok po kroku, zobacz [samouczek wprowadzenie](./functions-api-definition-getting-started.md).

### <a name="available-templates"></a><a name="templates"></a>Dostępne szablony

|Nazwa| Opis |
|:-----|:-----|
|Wygenerowana definicja|Definicja OpenAPI z maksymalną ilością informacji, które można wywnioskować z istniejących metadanych funkcji.|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>Uwzględnione metadane w wygenerowanej definicji

Poniższa tabela przedstawia ustawienia portalu Azure i odpowiednie dane w function.json, ponieważ jest mapowany do wygenerowanego szkieletu Swagger.

|Swagger.json|Interfejs użytkownika portalu|Funkcja.json|
|:----|:-----|:-----|
|[Host](https://swagger.io/specification/#fixed-fields-15)|**Ustawienia** > aplikacji funkcji**Ustawienia** > usługi app**service Omówienie** > **adresu URL**|*Nie obecny*
|[Ścieżki](https://swagger.io/specification/#paths-object-29)|**Integruj** > **wybrane metody HTTP**|Wiązania: Trasa
|[Element ścieżki](https://swagger.io/specification/#path-item-object-32)|**Zintegruj** > **szablon Trasy**|Powiązania: Metody
|[Bezpieczeństwo](https://swagger.io/specification/#security-scheme-object-112)|**Klucze**|*Nie obecny*|
|operationID*|**Trasa + Dozwolone czasowniki**|Trasa + Dozwolone czasowniki|

\*Identyfikator operacji jest wymagany tylko do integracji z programami PowerApps i Flow.
> [!NOTE]
> Rozszerzenie x-ms-summary zawiera nazwę wyświetlaną w aplikacjach logiki, u źródłach PowerApps i flow.
>
> Aby dowiedzieć się więcej, zobacz [Dostosowywanie definicji swaggera dla usługi PowerApps](https://docs.microsoft.com/connectors/custom-connectors/openapi-extensions).

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>Ustawianie definicji interfejsu API za pomocą ciągłej integracji/dysku CD

 Należy włączyć hosting definicji interfejsu API w portalu, zanim włączysz formant źródła, aby zmodyfikować definicję interfejsu API z kontroli źródła. Postępuj zgodnie z poniższymi instrukcjami:

1. Przejdź do **definicji interfejsu API (podgląd)** w ustawieniach aplikacji funkcji.
   1. Ustaw **źródło definicji interfejsu API** na **Function**.
   1. Kliknij **pozycję Generuj szablon definicji interfejsu API,** a następnie **pozycję Zapisz,** aby utworzyć definicję szablonu do późniejszej modyfikacji.
   1. Zanotuj adres URL i klucz definicji interfejsu API.
1. [Konfigurowanie ciągłej integracji/ciągłego wdrażania (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#requirements-for-continuous-deployment).
2. Zmodyfikuj plik swagger.json w\.formancie źródła w folderze \site\wwwroot azurefunctions\swagger\swagger.json.

Teraz zmiany w pliku swagger.json w repozytorium są hostowane przez aplikację funkcji w adresie URL definicji interfejsu API i kluczem, który został odnotowany w kroku 1.c.

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie samouczek](functions-api-definition-getting-started.md). Wypróbuj nasz przewodnik, aby zobaczyć definicję OpenAPI w akcji.
* [Repozytorium Usługi Azure Functions GitHub](https://github.com/Azure/Azure-Functions/). Zapoznaj się z repozytorium funkcji, aby przekazać nam opinię na temat podglądu obsługi definicji interfejsu API. Zrób problem z githubem dla wszystkiego, co chcesz zobaczyć zaktualizowane.
* [Odwołanie do dewelopera usług Azure Functions](functions-reference.md). Dowiedz się więcej o funkcjach kodowania i definiowaniu wyzwalaczy i powiązań.
