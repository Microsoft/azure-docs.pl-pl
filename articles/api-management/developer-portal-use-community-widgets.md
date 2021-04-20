---
title: Korzystanie z widżetów społeczności w portalu dla deweloperów
titleSuffix: Azure API Management
description: Dowiedz się więcej na temat widżetów społeczności API Management portalu dla deweloperów oraz sposobu ich wstrzykiwania i używania w kodzie.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: de6160aa2e556297287ae9e9ecd58a93e54d863f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741824"
---
# <a name="use-community-widgets-in-the-developer-portal"></a>Korzystanie z widżetów społeczności w portalu dla deweloperów

Wszyscy deweloperzy umieszczają swoje widżety udostępnione przez społeczność w `/community/widgets/` folderze repozytorium [GitHub](https://github.com/Azure/api-management-developer-portal)API Management portalu deweloperów . Każdy z nich został zaakceptowany przez zespół portalu deweloperów. Możesz użyć widżetów, wstrzykiwając je do własnej [wersji](developer-portal-self-host.md) portalu. Zarządzana wersja portalu dla deweloperów nie obsługuje obecnie widżetów społeczności.

> [!NOTE]
> Zespół portalu deweloperów dokładnie sprawdza współtwłasne widżety i ich zależności. Jednak zespół nie może zagwarantować, że ładowanie widżetów jest bezpieczne. Podczas podejmowania decyzji o użyciu widżetu współtwomentu przez społeczność skorzystaj z własnej oceny. Zapoznaj się z [naszymi wytycznymi dotyczącymi udziału widżetów,](developer-portal-widget-contribution-guidelines.md#contribution-guidelines) aby dowiedzieć się więcej o naszych działaniach prewencyjnych.

## <a name="inject-and-use-external-widgets"></a>Wstrzykiwanie i używanie zewnętrznych widżetów

1. Skonfiguruj środowisko [lokalne dla](developer-portal-self-host.md#step-1-set-up-local-environment) najnowszej wersji portalu dla deweloperów.

1. Przejdź do folderu widżetu w `/community/widgets` katalogu . Przeczytaj opis widżetu w `readme.md` pliku .

1. Zarejestruj widżet w modułach portalu:

    1. `src/apim.design.module.ts` — moduł, który rejestruje zależności czasu projektowania.
    
        ```typescript
        import { WidgetNameDesignModule } from "../community/widgets/<widget-name>/widget.design.module";
    
        ...
    
            injector.bindModule(new WidgetNameDesignModule());
        ```
    
    1. `src/apim.publish.module.ts` — moduł, który rejestruje zależności czasu publikowania.
    
        ```typescript
        import { WidgetNamePublishModule } from "../community/widgets/<widget-name>/widget.publish.module";
    
        ...
    
            injector.bindModule(new WidgetNamePublishModule());
        ```
    
    1. `src/apim.runtime.module.ts` — moduł, który rejestruje zależności w czasie uruchamiania.
    
        ```typescript
        import { WidgetNameRuntimeModule } from "../community/widgets/<widget-name>/widget.runtime.module";
    
        ...
    
            injector.bindModule(new WidgetNameRuntimeModule());
        ```

1. Sprawdź, czy widżet zawiera `npm_dependencies` plik.

1. Jeśli tak, skopiuj polecenia z pliku i uruchom je w górnym katalogu repozytorium.

    Spowoduje to zainstalowanie zależności widżetu.

1. Uruchom polecenie `npm start`.

Widżet można wyświetlić w kategorii **Community** w selektorze widżetów.

:::image type="content" source="media/developer-portal-use-community-widgets/widget-selector.png" alt-text="Zrzut ekranu przedstawiający selektor widżetów":::


## <a name="next-steps"></a>Następne kroki


Dowiedz się więcej o portalu dla deweloperów:

- [Portal deweloperów usługi Azure API Management — omówienie](api-management-howto-developer-portal.md)

- [Współtwomentuj widżety](developer-portal-widget-contribution-guidelines.md)
