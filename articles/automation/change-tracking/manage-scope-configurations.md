---
title: Ograniczenie zakresu wdrożenia Azure Automation Change Tracking i spisu
description: W tym artykule opisano sposób pracy z konfiguracjami zakresu w celu ograniczenia zakresu Change Tracking i wdrożenia spisu.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209990"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Ograniczenie zakresu wdrożenia Change Tracking i spisu

W tym artykule opisano sposób pracy z konfiguracjami zakresu podczas korzystania z funkcji [Change Tracking i spisu](overview.md) w celu wdrażania zmian na maszynach wirtualnych. Aby uzyskać więcej informacji, zobacz [ukierunkowane rozwiązania monitorowania w Azure monitor (wersja zapoznawcza)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>Informacje o konfiguracjach zakresu

Konfiguracja zakresu jest grupą co najmniej jednego zapisanego wyszukiwania (zapytania) służącego do ograniczania zakresu Change Tracking i spisu do określonych komputerów. Konfiguracja zakresu jest używana w obszarze roboczym Log Analytics, aby umożliwić włączanie komputerów. Po dodaniu komputera do zmian z funkcji, komputer zostanie również dodany do zapisanego wyszukiwania w obszarze roboczym.

## <a name="set-the-scope-limit"></a>Ustaw limit zakresu

Aby ograniczyć zakres Change Tracking i wdrożenia spisu:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz **Automation**. Po rozpoczęciu wpisywania lista filtruje sugestie w oparciu o dane wejściowe. Wybierz opcję **Konta automatyzacji**.

3. Na liście kont usługi Automation wybierz konto wybrane podczas włączania Change Tracking i spisu.

4. Na koncie usługi Automation wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

5. Kliknij pozycję **Przejdź do obszaru roboczego**.

6. Wybierz pozycję **konfiguracje zakresu (wersja zapoznawcza)** w obszarze **źródła danych obszaru roboczego**.

7. Wybierz wielokropek z prawej strony  `MicrosoftDefaultScopeConfig-ChangeTracking` konfiguracji zakresu, a następnie kliknij pozycję **Edytuj**.

8. W okienku Edycja wybierz pozycję **Wybierz grupy komputerów**. W okienku grupy komputerów zostaną wyświetlone zapisane wyszukiwania, które są używane do tworzenia konfiguracji zakresu. Zapisane wyszukiwanie używane przez Change Tracking i spis jest następujące:

    |Nazwa     |Kategoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  Śledzenia zmian       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. Wybierz zapisane wyszukiwanie, aby wyświetlić i edytować zapytanie używane do wypełniania grupy. Na poniższej ilustracji przedstawiono zapytanie i jego wyniki:

    ![Zapisane wyszukiwania](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>Następne kroki

* Aby obejść Change Tracking i spis, zobacz [zarządzanie Change Tracking i spisem](manage-change-tracking.md).
* Aby rozwiązać ogólne problemy z funkcjami, zobacz [Rozwiązywanie problemów dotyczących Change Tracking i spisu](../troubleshoot/change-tracking.md).
