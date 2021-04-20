---
title: Integracja Application Insights z portalem dla deweloperów
titleSuffix: Azure API Management
description: Dowiedz się, jak zintegrować Application Insights z portalem deweloperów zarządzanym lub własnym.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 5e1c9caa55d0b3b7820f766a30c878fdc01f5137
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741896"
---
# <a name="integrate-application-insights-to-developer-portal"></a>Integracja Application Insights z portalem dla deweloperów

Popularną funkcją Azure Monitor jest Application Insights. Jest to rozszerzalna usługa zarządzania wydajnością aplikacji (APM) dla deweloperów i specjalistów DevOps. Za jego pomocą możesz monitorować portal dla deweloperów i wykrywać anomalie wydajności. Application Insights oferuje zaawansowane narzędzia analityczne, które ułatwiają naukę tego, co użytkownicy faktycznie robią podczas odwiedzania portalu dla deweloperów.

## <a name="add-application-insights-to-your-portal"></a>Dodawanie Application Insights do portalu

Wykonaj następujące kroki, aby Application Insights do zarządzanego lub samodzielnego portalu deweloperów.

> [!IMPORTANT]
> Kroki 1 i 2 nie są wymagane w przypadku portali zarządzanych. Jeśli masz portal zarządzany, przejdź do kroku 4.

1. Skonfiguruj środowisko [lokalne dla](developer-portal-self-host.md#step-1-set-up-local-environment) najnowszej wersji portalu dla deweloperów.

1. Zainstaluj pakiet **npm,** aby [dodać pakiet Paperbits dla platformy Azure:](https://github.com/paperbits/paperbits-azure)

    ```console
    npm install @paperbits/azure --save
    ```

1. W pliku `startup.publish.ts` w `src` folderze zaimportuj i zarejestruj Application Insights module:

    ```typescript
    import { AppInsightsPublishModule } from "@paperbits/azure";
    ...
    injector.bindModule(new AppInsightsPublishModule());
    ```

1. Pobierz konfigurację portalu:

    ```http
    GET /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": {
                    "title": "Microsoft Azure API Management - developer portal",
                    "description": "Discover APIs, learn how to use them, try them out interactively, and sign up to acquire keys.",
                    "keywords": "Azure, API Management, API, developer",
                    "faviconSourceId": null,
                    "author": "Microsoft Azure API Management"
                }
            }
        ]
    }
    ```

1. Rozszerz konfigurację lokacji z poprzedniego kroku przy użyciu Application Insights konfiguracji:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "appInsights": {
                        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxx"
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o portalu dla deweloperów:

- [Portal deweloperów usługi Azure API Management — omówienie](api-management-howto-developer-portal.md)
- [Automatyzowanie wdrożeń w portalu](automate-portal-deployments.md)
- [Samodzielne hostnie portalu dla deweloperów](developer-portal-self-host.md)
