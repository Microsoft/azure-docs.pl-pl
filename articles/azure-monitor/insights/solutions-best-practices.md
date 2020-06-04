---
title: Rozwiązanie do zarządzania na platformie Azure — najlepsze rozwiązania
description: W tym artykule przedstawiono wskazówki dotyczące tworzenia pliku rozwiązania do zarządzania. Zobacz, jak korzystać ze źródeł danych, elementów Runbook, widoków i alertów.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 7cb300297336edcce4294b800520ad570b12bcde
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84320934"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Najlepsze rozwiązania dotyczące tworzenia rozwiązań do zarządzania na platformie Azure (wersja zapoznawcza)
> [!NOTE]
> Jest to wstępna dokumentacja dotycząca tworzenia rozwiązań do zarządzania na platformie Azure, które są obecnie dostępne w wersji zapoznawczej. Każdy schemat opisany poniżej może ulec zmianie.  

Ten artykuł zawiera najlepsze rozwiązania dotyczące [tworzenia pliku rozwiązania do zarządzania](solutions-solution-file.md) na platformie Azure.  Te informacje zostaną zaktualizowane w miarę identyfikowania dodatkowych najlepszych rozwiązań.

## <a name="data-sources"></a>Źródła danych
- Źródła danych można [skonfigurować przy użyciu szablonu Menedżer zasobów](../../azure-monitor/platform/template-workspace-configuration.md), ale nie powinny być dołączone do pliku rozwiązania.  Przyczyną jest to, że konfigurowanie źródeł danych nie jest obecnie idempotentne, co oznacza, że rozwiązanie może zastąpić istniejącą konfigurację w obszarze roboczym użytkownika.<br><br>Na przykład rozwiązanie może wymagać zdarzeń ostrzeżeń i błędów w dzienniku zdarzeń aplikacji.  Jeśli określisz to jako źródło danych w rozwiązaniu, użytkownik będzie narażony na usuwanie zdarzeń informacji, jeśli użytkownik skonfigurował ją w obszarze roboczym.  Jeśli wszystkie zdarzenia zostały uwzględnione, w obszarze roboczym użytkownika mogą być zbierane nadmierne zdarzenia dotyczące informacji.

- Jeśli rozwiązanie wymaga danych z jednego ze standardowych źródeł danych, należy je zdefiniować jako warunek wstępny.  Stan w dokumentacji, że klient musi samodzielnie skonfigurować źródło danych.  
- Dodaj komunikat [weryfikacyjny przepływu danych](../../azure-monitor/platform/view-designer-tiles.md) do dowolnych widoków w rozwiązaniu, aby nakazać użytkownikowi źródła danych, które muszą zostać skonfigurowane do zbierania wymaganych danych.  Ten komunikat jest wyświetlany na kafelku widoku, gdy nie znaleziono wymaganych danych.


## <a name="runbooks"></a>Elementy Runbook
- Dodaj [harmonogram automatyzacji](../../automation/automation-schedules.md) dla każdego elementu Runbook w rozwiązaniu, który musi być uruchamiany zgodnie z harmonogramem.
- Uwzględnij [moduł IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) w rozwiązaniu, który będzie używany przez elementy Runbook do zapisywania danych w repozytorium log Analytics.  Skonfiguruj rozwiązanie do [odwoływania](solutions-solution-file.md#solution-resource) się do tego zasobu, aby pozostały w przypadku usunięcia rozwiązania.  Pozwala to wielu rozwiązaniom na współużytkowanie modułu.
- Użyj [zmiennych automatyzacji](../../automation/automation-schedules.md) , aby podać wartości rozwiązania, które użytkownicy mogą chcieć zmienić później.  Nawet jeśli rozwiązanie jest skonfigurowane tak, aby zawierało zmienną, można zmienić wartość.

## <a name="views"></a>Widoki
- Wszystkie rozwiązania powinny zawierać jeden widok, który jest wyświetlany w portalu użytkownika.  Widok może zawierać wiele [części wizualizacji](../../azure-monitor/platform/view-designer-parts.md) , aby zilustrować różne zestawy danych.
- Dodaj komunikat [weryfikacyjny przepływu danych](../../azure-monitor/platform/view-designer-tiles.md) do dowolnych widoków w rozwiązaniu, aby nakazać użytkownikowi źródła danych, które muszą zostać skonfigurowane do zbierania wymaganych danych.
- Skonfiguruj rozwiązanie w taki sposób, aby [zawierało](solutions-solution-file.md#solution-resource) widok, aby został usunięty w przypadku usunięcia rozwiązania.

## <a name="alerts"></a>Alerty
- Zdefiniuj listę adresatów jako parametr w pliku rozwiązania, tak aby użytkownik mógł je zdefiniować podczas instalacji rozwiązania.
- Skonfiguruj rozwiązanie do [odwoływania](solutions-solution-file.md#solution-resource) się do reguł alertów, aby użytkownik mógł zmienić konfigurację.  Mogą chcieć wprowadzić zmiany, takie jak modyfikowanie listy adresatów, zmiana progu alertu lub wyłączenie reguły alertu. 


## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z podstawowym procesem [projektowania i tworzenia rozwiązania do zarządzania](solutions-creating.md).
* Dowiedz się, jak [utworzyć plik rozwiązania](solutions-solution-file.md).
* [Dodaj zapisane wyszukiwania i alerty](solutions-resources-searches-alerts.md) do rozwiązania do zarządzania.
* [Dodaj widoki](solutions-resources-views.md) do rozwiązania do zarządzania.
* [Dodaj elementy Runbook usługi Automation i inne zasoby](solutions-resources-automation.md) do rozwiązania do zarządzania.

