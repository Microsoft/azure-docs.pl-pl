---
title: Ograniczenie zakresu wdrożenia Azure Automation Change Tracking i spisu
description: W tym artykule opisano sposób pracy z konfiguracjami zakresu w celu ograniczenia zakresu Change Tracking i wdrożenia spisu.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 353f29d9b4c6599226619d40e4378e21618bcad4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185912"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Ograniczenie zakresu wdrożenia Change Tracking i spisu

W tym artykule opisano sposób pracy z konfiguracjami zakresu podczas korzystania z funkcji [Change Tracking i spisu](change-tracking.md) w celu wdrażania zmian na maszynach wirtualnych. Aby uzyskać więcej informacji, zobacz [ukierunkowane rozwiązania monitorowania w Azure monitor (wersja zapoznawcza)](../azure-monitor/insights/solution-targeting.md). 

## <a name="about-scope-configurations"></a>Informacje o konfiguracjach zakresu

Konfiguracja zakresu jest grupą co najmniej jednego zapisanego wyszukiwania (zapytania) służącego do ograniczania zakresu Change Tracking i spisu do określonych komputerów. Konfiguracja zakresu jest używana w obszarze roboczym Log Analytics, aby umożliwić włączanie komputerów. Po dodaniu komputera do zmian z funkcji, komputer zostanie również dodany do zapisanego wyszukiwania w obszarze roboczym.

## <a name="set-the-scope-limit"></a>Ustaw limit zakresu

Aby ograniczyć zakres Change Tracking i wdrożenia spisu:

1. Na koncie usługi Automation wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

2. Kliknij pozycję **Przejdź do obszaru roboczego**.

3. Wybierz pozycję **konfiguracje zakresu (wersja zapoznawcza)** w obszarze **źródła danych obszaru roboczego**.

4. Wybierz wielokropek z prawej strony  `MicrosoftDefaultScopeConfig-ChangeTracking` konfiguracji zakresu, a następnie kliknij pozycję **Edytuj**. 

5. W okienku Edycja wybierz pozycję **Wybierz grupy komputerów**. W okienku grupy komputerów zostaną wyświetlone zapisane wyszukiwania, które są używane do tworzenia konfiguracji zakresu. Zapisane wyszukiwanie używane przez Change Tracking i spis jest następujące:

    |Nazwa     |Kategoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  Śledzenia zmian       | ChangeTracking__MicrosoftDefaultComputerGroup        |

6. Wybierz zapisane wyszukiwanie, aby wyświetlić i edytować zapytanie używane do wypełniania grupy. Na poniższej ilustracji przedstawiono zapytanie i jego wyniki:

    ![Zapisane wyszukiwania](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Następne kroki

* Aby obejść Change Tracking i spis, zobacz [zarządzanie Change Tracking i spisem](change-tracking-file-contents.md).
* Aby rozwiązać ogólne problemy z funkcjami, zobacz [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md).
