---
title: Integracja Menedżer tagów Google z portalem dla deweloperów
titleSuffix: Azure API Management
description: Dowiedz się, jak podłączyć Menedżer tagów Google do zarządzanego lub samodzielnego portalu deweloperów w usłudze Azure API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c209eb782787146d947b4684d41c5d1e9bb6364e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741903"
---
# <a name="integrate-google-tag-manager-to-api-management-developer-portal"></a>Integracja Menedżer tagów Google z API Management portalem deweloperów

[Menedżer tagów Google](https://developers.google.com/tag-manager) to system zarządzania tagami utworzony przez firmę Google. Umożliwia ona zarządzanie tagami JavaScript i HTML używanymi do śledzenia i analizy w witrynach internetowych. Można na przykład użyć usługi Menedżer tagów Google do integracji usługi Google Analytics, map cieplnych lub czatbotów, takich jak LiveChat.

Wykonaj kroki opisane w tym artykule, aby podłączyć Menedżer tagów Google do zarządzanego lub samodzielnego portalu deweloperów w usłudze Azure API Management.

## <a name="add-google-tag-manager-to-your-portal"></a>Dodawanie Menedżer tagów Google do portalu

Wykonaj następujące kroki, aby Menedżer tagów Google do zarządzanego lub samodzielnego portalu dla deweloperów.

> [!IMPORTANT]
> Kroki 1 i 2 nie są wymagane w przypadku portali zarządzanych. Jeśli masz portal zarządzany, przejdź do kroku 4.

1. Skonfiguruj środowisko [lokalne dla](developer-portal-self-host.md#step-1-set-up-local-environment) najnowszej wersji portalu dla deweloperów.

1. Zainstaluj pakiet **npm,** aby dodać [pakiet Paperbits dla Menedżer tagów Google](https://github.com/paperbits/paperbits-gtm):

    ```sh
    npm install @paperbits/gtm --save
    ```

1. W pliku `startup.publish.ts` w `src` folderze zaimportuj i zarejestruj moduł GTM:

    ```typescript
    import { GoogleTagManagerPublishModule } from "@paperbits/gtm/gtm.publish.module";
    ...
    injector.bindModule(new GoogleTagManagerPublishModule());
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

1. Rozszerz konfigurację lokacji z poprzedniego kroku przy użyciu Menedżer tagów Google konfiguracji:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "googleTagManager": {
                        "containerId": "GTM-..."
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Następne kroki

- [Automatyzowanie wdrożeń w portalu](automate-portal-deployments.md)
- [Samodzielne hostnie portalu dla deweloperów](developer-portal-self-host.md)