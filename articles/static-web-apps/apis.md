---
title: Obsługa interfejsów API w Azure Static Web Apps z Azure Functions
description: Dowiedz się, jakie funkcje interfejsu API Azure Static Web Apps obsługuje
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 1fc5e1e6982686e7042e5b8ad55d72a4560b6aee
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737482"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Obsługa interfejsu API w Azure Static Web Apps zapoznawczej z Azure Functions

Azure Static Web Apps punkty końcowe interfejsu API bez serwera za pośrednictwem [Azure Functions](../azure-functions/functions-overview.md). Dzięki wykorzystaniu Azure Functions API dynamicznie skalują się na żądanie i zawierają następujące funkcje:

- **Zintegrowane zabezpieczenia z** bezpośrednim dostępem do uwierzytelniania [użytkowników i danych autoryzacji opartej na rolach.](user-information.md)
- **Bezproblemowy routing,** który sprawia, że trasa interfejsu _API_ jest bezpiecznie dostępna dla aplikacji internetowej bez konieczności użycia niestandardowych reguł CORS.
- **Wyzwalacze HTTP i** powiązania danych wejściowych/wyjściowych.

## <a name="configuration"></a>Konfigurowanie

Punkty końcowe interfejsu API są dostępne dla aplikacji internetowej za pośrednictwem _trasy interfejsu API._ Chociaż ta trasa jest stała, masz kontrolę nad folderem i projektem, w którym znajdujesz skojarzoną Azure Functions aplikację. Tę lokalizację można zmienić, edytując plik [YAML przepływu](github-actions-workflow.md#build-and-deploy) pracy znajdujący się w _folderze github/workflows repozytorium._

## <a name="constraints"></a>Ograniczenia

Azure Static Web Apps udostępnia interfejs API za pośrednictwem Azure Functions. Możliwości usługi Azure Functions są skoncentrowane na określonym zestawie funkcji, które umożliwiają tworzenie interfejsu API dla aplikacji internetowej i bezpieczne łączenie się z interfejsem API przez aplikację internetową. Te funkcje mają pewne ograniczenia, takie jak:

- Prefiks trasy interfejsu API musi być _interfejsem API_.
- Interfejs API musi być aplikacją Node.js 12, .NET Core 3.1 lub Python 3.8 Azure Functions aplikacji.
- Reguły tras dla funkcji interfejsu API obsługują [tylko przekierowywanie](routes.md#redirects) i [zabezpieczanie tras za pomocą ról](routes.md#securing-routes-with-roles).
- Wyzwalacze są ograniczone do [protokołu HTTP](../azure-functions/functions-bindings-http-webhook.md).
  - Powiązania wejściowe i [wyjściowe](../azure-functions/functions-triggers-bindings.md#supported-bindings) są obsługiwane.
- Dzienniki są dostępne tylko po dodaniu [Application Insights](../azure-functions/functions-monitoring.md) do aplikacji usługi Functions.
- Niektóre ustawienia aplikacji są zarządzane przez usługę. Nie można skonfigurować ustawień aplikacji, które zaczynają się od następujących prefiksów: `APPSETTING_` , , , , , , , , `AZUREBLOBSTORAGE_` , , , `AZUREFILESSTORAGE_` , , `AZURE_FUNCTION_` , , , , `CONTAINER_` , `DIAGNOSTICS_` `DOCKER_` , `FUNCTIONS_` `IDENTITY_` `MACHINEKEY_` `MAINSITE_` `MSDEPLOY_` `SCMSITE_` `SCM_` `WEBSITES_` `WEBSITE_` `WEBSOCKET_` `AzureWeb` .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie interfejsu API](add-api.md)
