---
title: OpenAPI metadanych w Azure Functions
description: Omówienie obsługi OpenAPI w Azure Functions
ms.topic: conceptual
ms.date: 03/23/2017
ms.openlocfilehash: 499d4f685e3802fcc37c8a3050ae367207f192d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87385827"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Obsługa metadanych OpenAPI 2,0 w Azure Functions (wersja zapoznawcza)
Obsługa metadanych w programie OpenAPI 2,0 (dawniej Swagger) w Azure Functions jest funkcją w wersji zapoznawczej, której można użyć do zapisania definicji OpenAPI 2,0 w aplikacji funkcji. Następnie można hostować ten plik za pomocą aplikacji funkcji.

> [!IMPORTANT]
> Funkcja interfejsu OpenAPI w wersji zapoznawczej jest obecnie dostępna tylko w środowisku uruchomieniowym w wersji 1.x. Informacje na temat tworzenia aplikacji funkcji w wersji 1.x [można znaleźć tutaj](./functions-versions.md#creating-1x-apps).

[Metadane openapi](https://swagger.io/) umożliwiają funkcję, która obsługuje interfejs API REST do użycia przez szeroką gamę innych programów. To oprogramowanie obejmuje oferty firmy Microsoft, takie jak PowerApps i [API Apps funkcja Azure App Service](../app-service/overview.md), narzędzia deweloperskie innych firm, takie jak program [Poster](https://www.getpostman.com/docs/importing_swagger)i [wiele innych pakietów](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Zalecamy rozpoczęcie od [samouczka wprowadzającego](./functions-openapi-definition.md) , a następnie powrót do tego dokumentu, aby dowiedzieć się więcej o konkretnych funkcjach.

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>Włącz obsługę definicji OpenAPI
Wszystkie ustawienia OpenAPI można skonfigurować na stronie **definicji interfejsu API** w **funkcjach platformy**aplikacji funkcji.

> [!NOTE]
> Funkcja definicji interfejsu API funkcji nie jest obecnie obsługiwana w przypadku środowiska uruchomieniowego beta.

Aby włączyć generowanie definicji hostowanej OpenAPI i definicji szybkiego startu, ustaw **źródło definicji interfejsu API** na **funkcję (wersja zapoznawcza)**. **Zewnętrzny adres URL** umożliwia funkcji używanie definicji openapi, która jest hostowana w innym miejscu.

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>Generuj szkielet struktury Swagger na podstawie metadanych funkcji
Szablon może ułatwić rozpoczęcie pisania pierwszej definicji OpenAPI. Funkcja szablonu definicji tworzy rozrzedzoną definicję OpenAPI przy użyciu wszystkich metadanych w function.jsw pliku dla każdej funkcji wyzwalacza HTTP. Musisz podać więcej informacji na temat interfejsu API ze [specyfikacji openapi](https://swagger.io/specification/), takich jak szablony żądań i odpowiedzi.

Aby uzyskać instrukcje krok po kroku, zobacz [samouczek wprowadzający](./functions-openapi-definition.md).

### <a name="available-templates"></a><a name="templates"></a>Dostępne szablony

|Nazwa| Opis |
|:-----|:-----|
|Wygenerowana definicja|Definicja OpenAPI z maksymalną ilością informacji, które można wywnioskować na podstawie istniejących metadanych funkcji.|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>Uwzględnione metadane w wygenerowanej definicji

Poniższa tabela przedstawia ustawienia Azure Portal i odpowiadające im dane w function.jsw miarę mapowania do wygenerowanego szkieletu struktury Swagger.

|Swagger.jsna|Interfejs użytkownika portalu|Function.jsna|
|:----|:-----|:-----|
|[Host](https://swagger.io/specification/#fixed-fields-15)|**Ustawienia**  >  aplikacji funkcji **Ustawienia App Service**  >  **Przegląd**  >  **Adres URL**|*Nieobecne*
|[Ścieżki](https://swagger.io/specification/#paths-object-29)|**Integracja**  >  **Wybrane metody http**|Powiązania: Route
|[Element ścieżki](https://swagger.io/specification/#path-item-object-32)|**Integracja**  >  **Szablon trasy**|Powiązania: metody
|[Zabezpieczenia](https://swagger.io/specification/#security-scheme-object-112)|**Klucze**|*Nieobecne*|
|operationId|**Trasy + dozwolone czasowniki**|Trasy + dozwolone czasowniki|

\*Identyfikator operacji jest wymagany tylko w przypadku integracji z usługą PowerApps i przepływem.
> [!NOTE]
> Rozszerzenie x-MS-Summary zawiera nazwę wyświetlaną w Logic Apps, PowerApps i Flow.
>
> Aby dowiedzieć się więcej, zobacz [Dostosowywanie definicji struktury Swagger dla usługi powerapps](/connectors/custom-connectors/openapi-extensions).

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>Używanie ciągłej integracji/ciągłego ustawiania definicji interfejsu API

 Należy włączyć hosting definicji interfejsu API w portalu przed włączeniem kontroli źródła w celu zmodyfikowania definicji interfejsu API z kontroli źródła. Wykonaj następujące instrukcje:

1. Przejdź do **definicji interfejsu API (wersja zapoznawcza)** w ustawieniach aplikacji funkcji.
   1. Ustaw **źródło definicji interfejsu API** do **działania**.
   1. Kliknij pozycję **Generuj szablon definicji interfejsu API** , a następnie **Zapisz** , aby utworzyć definicję szablonu do modyfikacji później.
   1. Zanotuj adres URL definicji interfejsu API i klucz.
1. [Konfigurowanie ciągłej integracji/ciągłego wdrażania (Ci/CD)](./functions-continuous-deployment.md#requirements-for-continuous-deployment).
2. Zmodyfikuj swagger.jsw kontroli źródła w \site\wwwroot \.azurefunctions\swagger\swagger.jsna.

Teraz zmiany swagger.jsw repozytorium są hostowane przez aplikację funkcji pod adresem URL definicji interfejsu API i kluczem zanotowanym w kroku 1. c.

## <a name="next-steps"></a>Następne kroki
* [Samouczek z wprowadzeniem](./functions-openapi-definition.md). Wypróbuj nasze wskazówki, aby zobaczyć definicję OpenAPI w akcji.
* [Azure Functions repozytorium GitHub](https://github.com/Azure/Azure-Functions/). Zapoznaj się z repozytorium funkcji, aby przekazać nam swoją opinię na temat obsługi definicji interfejsu API w wersji zapoznawczej. Zapoznaj się z tematem problemu z usługą GitHub, który ma zostać zaktualizowany.
* [Azure Functions Dokumentacja dla deweloperów](functions-reference.md). Dowiedz się więcej o funkcjach kodowania i definiowaniu wyzwalaczy i powiązań.
