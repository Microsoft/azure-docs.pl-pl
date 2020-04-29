---
title: Tworzenie rozwiązania do zarządzania na platformie Azure | Microsoft Docs
description: Rozwiązania do zarządzania obejmują spakowane scenariusze zarządzania na platformie Azure, które klienci mogą dodać do Log Analytics obszaru roboczego.  Ten artykuł zawiera szczegółowe informacje dotyczące sposobu tworzenia rozwiązań do zarządzania, które mają być używane w Twoim środowisku lub udostępniane klientom.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f1605597c7716ba6a896c7ecdae968f07d66027b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77663219"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure (wersja zapoznawcza)
> [!NOTE]
> Jest to wstępna dokumentacja dotycząca tworzenia rozwiązań do zarządzania na platformie Azure, które są obecnie dostępne w wersji zapoznawczej. Każdy schemat opisany poniżej może ulec zmianie.

[Rozwiązania do zarządzania]( solutions.md) zapewniają spakowane scenariusze zarządzania, które klienci mogą dodawać do swoich log Analytics obszarów roboczych.  W tym artykule przedstawiono podstawowy proces projektowania i tworzenia rozwiązania do zarządzania, które jest odpowiednie dla najbardziej typowych wymagań.  Jeśli dopiero zaczynasz tworzyć rozwiązania do zarządzania, możesz użyć tego procesu jako punktu wyjścia, a następnie wykorzystać koncepcje dla bardziej złożonych rozwiązań w miarę rozwoju wymagań.

## <a name="what-is-a-management-solution"></a>Co to jest rozwiązanie do zarządzania?

Rozwiązania do zarządzania zawierają zasoby platformy Azure, które współpracują ze sobą w celu osiągnięcia określonego scenariusza zarządzania.  Są one implementowane jako [Szablony zarządzania zasobami](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) , które zawierają szczegółowe informacje o sposobie instalowania i konfigurowania zawartych w nich zasobów po zainstalowaniu rozwiązania.

Podstawową strategią jest rozpoczęcie rozwiązania do zarządzania przez utworzenie poszczególnych składników w środowisku platformy Azure.  Po poprawnym działaniu funkcji można rozpocząć pakowanie ich do [pliku rozwiązania do zarządzania]( solutions-solution-file.md). 


## <a name="design-your-solution"></a>Zaprojektuj rozwiązanie
Typowym wzorcem rozwiązania do zarządzania jest pokazano na poniższym diagramie.  Różne składniki w tym wzorcu zostały omówione poniżej.

![Przegląd rozwiązania do zarządzania](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Źródła danych
Pierwszym krokiem projektowania rozwiązania jest określenie danych, które są wymagane z repozytorium Log Analytics.  Te dane mogą być zbierane przez [Źródło danych](../../azure-monitor/platform/agent-data-sources.md) lub [inne rozwiązanie]( solutions.md)albo może być konieczne dostarczenie procesu do jego zebrania.

Istnieje wiele sposobów na źródła danych, które mogą być zbierane w repozytorium Log Analytics, zgodnie z opisem w temacie [źródła danych w log Analytics](../../azure-monitor/platform/agent-data-sources.md).  Obejmuje to zdarzenia w dzienniku zdarzeń systemu Windows lub generowane przez dziennik systemowy oprócz liczników wydajności zarówno dla klientów z systemem Windows, jak i Linux.  Możesz również zbierać dane z zasobów platformy Azure zebranych przez Azure Monitor.  

Jeśli wymagane są dane, które nie są dostępne za pośrednictwem żadnego z dostępnych źródeł danych, można użyć [interfejsu API modułu zbierającego dane http](../../azure-monitor/platform/data-collector-api.md) , który umożliwia zapisanie danych do repozytorium log Analytics z dowolnego klienta, który może wywołać interfejs API REST.  Najbardziej typowymi sposobami zbierania danych niestandardowych w ramach rozwiązania do zarządzania jest utworzenie [elementu Runbook w Azure Automation](../../automation/automation-runbook-types.md) , który gromadzi dane wymagane z platformy Azure lub z zasobów zewnętrznych, i używa interfejsu API modułu zbierającego dane do zapisu w repozytorium.  

### <a name="log-searches"></a>Wyszukiwania w dziennikach
[Wyszukiwanie w dziennikach](../../azure-monitor/log-query/log-query-overview.md) służy do wyodrębniania i analizowania danych w repozytorium log Analytics.  Są one używane przez widoki i alerty, a oprócz tego zezwala użytkownikowi na wykonywanie analizy ad hoc danych w repozytorium.  

Należy zdefiniować wszelkie zapytania, które będą przydatne dla użytkownika, nawet jeśli nie są one używane przez żadną z widoków ani alertów.  Będą one dostępne dla nich jako zapisane wyszukiwania w portalu i można je również dołączyć do [listy wizualizacji zapytań](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) w widoku niestandardowym.

### <a name="alerts"></a>Alerty
[Alerty w log Analytics](../../azure-monitor/platform/alerts-overview.md) identyfikują problemy w [przeszukiwaniu dzienników](#log-searches) względem danych w repozytorium.  Powiadamiają użytkownika lub automatycznie uruchamiają akcję w odpowiedzi. Należy określić różne warunki alertu dla aplikacji i uwzględnić odpowiednie reguły alertów w pliku rozwiązania.

Jeśli problem może zostać poprawiony przez proces zautomatyzowany, zazwyczaj utworzysz element Runbook w Azure Automation, aby wykonać to korygowanie.  Większość usług platformy Azure może być zarządzana za pomocą [poleceń cmdlet](/powershell/azure/overview) , które mogą być używane przez element Runbook do wykonywania takich funkcji.

Jeśli rozwiązanie wymaga zewnętrznych funkcji w odpowiedzi na alert, można użyć [odpowiedzi elementu webhook](../../azure-monitor/platform/alerts-metric.md).  Umożliwia to wywołanie zewnętrznej usługi sieci Web wysyłającej informacje z alertu.

### <a name="views"></a>Widoki
Widoki w Log Analytics są używane do wizualizacji danych z repozytorium Log Analytics.  Każde rozwiązanie zwykle zawiera pojedynczy widok z [kafelkiem](../../azure-monitor/platform/view-designer-tiles.md) , który jest wyświetlany na głównym pulpicie nawigacyjnym użytkownika.  Widok może zawierać dowolną liczbę [części wizualizacji](../../azure-monitor/platform/view-designer-parts.md) , aby zapewnić użytkownikowi różne wizualizacje zebranych danych.

[Widoki niestandardowe można tworzyć za pomocą projektanta widoków](../../azure-monitor/platform/view-designer.md) , który można później wyeksportować do dołączenia do pliku rozwiązania.  


## <a name="create-solution-file"></a>Utwórz plik rozwiązania
Po skonfigurowaniu i przetestowaniu składników, które będą częścią rozwiązania, można [utworzyć plik rozwiązania]( solutions-solution-file.md).  Składniki rozwiązania zostaną zaimplementowane w [szablonie Menedżer zasobów](../../azure-resource-manager/templates/template-syntax.md) zawierającym [zasób rozwiązania]( solutions-solution-file.md#solution-resource) z relacjami do innych zasobów w pliku.  


## <a name="test-your-solution"></a>Testowanie rozwiązania
Podczas opracowywania rozwiązania należy zainstalować i przetestować go w obszarze roboczym.  Można to zrobić za pomocą dowolnej z dostępnych metod [testowania i instalowania Menedżer zasobów szablonów](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="publish-your-solution"></a>Publikowanie rozwiązania
Po zakończeniu i przetestowaniu rozwiązania można je udostępnić klientom za pomocą następujących źródeł.

- **Szablony szybkiego startu platformy Azure**.  [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) to zestaw Menedżer zasobów szablonów współtworzonych przez społeczność w serwisie GitHub.  Rozwiązanie można udostępnić, wykonując następujące informacje w [przewodniku](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)udostępniania.
- **Portal Azure Marketplace**.  [Portal Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) pozwala dystrybuować i sprzedawać swoje rozwiązanie innym deweloperom, niezależnym dostawcom oprogramowania i specjalistom IT.  Możesz dowiedzieć się, jak opublikować rozwiązanie w witrynie Azure Marketplace [, w jaki sposób publikować ofertę i zarządzać nią w portalu Azure Marketplace](../../marketplace/marketplace-publishers-guide.md).



## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [utworzyć plik rozwiązania]( solutions-solution-file.md) dla rozwiązania do zarządzania.
* Poznaj szczegóły dotyczące [tworzenia szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
* Przeszukaj [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates) , aby zapoznać się z przykładami różnych szablonów Menedżer zasobów.
