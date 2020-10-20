---
title: Ogranicz zakres wdrożenia Update Management Azure Automation
description: W tym artykule opisano sposób korzystania z konfiguracji zakresu w celu ograniczenia zakresu wdrożenia Update Management.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 76063c479950d12985d5f3f52393f9bb0d5ecd8d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222537"
---
# <a name="limit-update-management-deployment-scope"></a>Ogranicz zakres wdrożenia Update Management

W tym artykule opisano sposób pracy z konfiguracjami zakresu podczas korzystania z funkcji [Update Management](overview.md) w celu wdrażania aktualizacji i poprawek na maszynach wirtualnych. Aby uzyskać więcej informacji, zobacz [ukierunkowane rozwiązania monitorowania w Azure monitor (wersja zapoznawcza)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>Informacje o konfiguracjach zakresu

Konfiguracja zakresu jest grupą co najmniej jednego zapisanego wyszukiwania (zapytania) służącego do ograniczania zakresu Update Management do określonych komputerów. Konfiguracja zakresu jest używana w obszarze roboczym Log Analytics, aby umożliwić włączanie komputerów. Po dodaniu komputera do odbierania aktualizacji z Update Management, komputer zostanie również dodany do zapisanego wyszukiwania w obszarze roboczym.

## <a name="set-the-scope-limit"></a>Ustaw limit zakresu

Aby ograniczyć zakres wdrożenia Update Management:

1. Na koncie usługi Automation wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

2. Wybierz pozycję **Przejdź do obszaru roboczego**.

3. Wybierz pozycję **konfiguracje zakresu (wersja zapoznawcza)** w obszarze **źródła danych obszaru roboczego**.

4. Wybierz wielokropek z prawej strony w obszarze  `MicrosoftDefaultScopeConfig-Updates` Konfiguracja zakresu, a następnie wybierz pozycję **Edytuj**.

5. W okienku Edycja rozwiń **pozycję Wybierz grupy komputerów**. W okienku grupy komputerów zostaną wyświetlone zapisane wyszukiwania, które są używane do tworzenia konfiguracji zakresu. Zapisane wyszukiwanie używane przez Update Management to:

    |Nazwa     |Kategoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Aktualizacje        | Updates__MicrosoftDefaultComputerGroup         |

6. Wybierz zapisane wyszukiwanie, aby wyświetlić i edytować zapytanie używane do wypełniania grupy. Na poniższej ilustracji przedstawiono zapytanie i jego wyniki:

    [![Zapisane wyszukiwania](./media/scope-configuration/logsearch.png)](./media/scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Możesz [wykonywać zapytania dotyczące dzienników Azure monitor](query-logs.md) , aby analizować oceny aktualizacji, wdrożenia i inne powiązane zadania zarządzania. Zawiera wstępnie zdefiniowane zapytania ułatwiające rozpoczęcie pracy.
