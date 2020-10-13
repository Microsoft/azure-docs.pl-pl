---
title: Ogranicz zakres wdrożenia Update Management Azure Automation
description: W tym artykule opisano sposób korzystania z konfiguracji zakresu w celu ograniczenia zakresu wdrożenia Update Management.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: aafd284122ca61ba2b668186095b88003be2775c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87450371"
---
# <a name="limit-update-management-deployment-scope"></a>Ogranicz zakres wdrożenia Update Management

W tym artykule opisano sposób pracy z konfiguracjami zakresu podczas korzystania z funkcji [Update Management](update-mgmt-overview.md) w celu wdrażania aktualizacji i poprawek na maszynach wirtualnych. Aby uzyskać więcej informacji, zobacz [ukierunkowane rozwiązania monitorowania w Azure monitor (wersja zapoznawcza)](../../azure-monitor/insights/solution-targeting.md).

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

    [![Zapisane wyszukiwania](./media/update-mgmt-scope-configuration/logsearch.png)](./media/update-mgmt-scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Możesz [wykonywać zapytania dotyczące dzienników Azure monitor](update-mgmt-query-logs.md) , aby analizować oceny aktualizacji, wdrożenia i inne powiązane zadania zarządzania. Zawiera wstępnie zdefiniowane zapytania ułatwiające rozpoczęcie pracy.
