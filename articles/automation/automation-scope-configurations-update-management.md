---
title: Ogranicz zakres wdrożenia Update Management Azure Automation
description: W tym artykule opisano sposób korzystania z konfiguracji zakresu w celu ograniczenia zakresu wdrożenia Update Management.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 8770762fa2d2ae6bc0584d75397829298a62e8c0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185895"
---
# <a name="limit-update-management-deployment-scope"></a>Ogranicz zakres wdrożenia Update Management

W tym artykule opisano sposób pracy z konfiguracjami zakresu podczas korzystania z funkcji [Update Management](automation-update-management.md) w celu wdrażania aktualizacji i poprawek na maszynach wirtualnych. Aby uzyskać więcej informacji, zobacz [ukierunkowane rozwiązania monitorowania w Azure monitor (wersja zapoznawcza)](../azure-monitor/insights/solution-targeting.md). 

## <a name="about-scope-configurations"></a>Informacje o konfiguracjach zakresu

Konfiguracja zakresu jest grupą co najmniej jednego zapisanego wyszukiwania (zapytania) służącego do ograniczania zakresu Update Management do określonych komputerów. Konfiguracja zakresu jest używana w obszarze roboczym Log Analytics, aby umożliwić włączanie komputerów. Po dodaniu komputera do odbierania aktualizacji z Update Management, komputer zostanie również dodany do zapisanego wyszukiwania w obszarze roboczym.

## <a name="set-the-scope-limit"></a>Ustaw limit zakresu

Aby ograniczyć zakres wdrożenia Update Management:

1. Na koncie usługi Automation wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby**.

2. Kliknij pozycję **Przejdź do obszaru roboczego**.

3. Wybierz pozycję **konfiguracje zakresu (wersja zapoznawcza)** w obszarze **źródła danych obszaru roboczego**.

4. Wybierz wielokropek z prawej strony `MicrosoftDefaultScopeConfig-Updates` konfiguracji zakresu, a następnie kliknij pozycję **Edytuj**. 

5. W okienku Edycja rozwiń **pozycję Wybierz grupy komputerów**. W okienku grupy komputerów zostaną wyświetlone zapisane wyszukiwania, które są używane do tworzenia konfiguracji zakresu. Zapisane wyszukiwanie używane przez Update Management to:

    |Nazwa     |Kategoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Aktualizacje        | Updates__MicrosoftDefaultComputerGroup         |

6. Wybierz zapisane wyszukiwanie, aby wyświetlić i edytować zapytanie używane do wypełniania grupy. Na poniższej ilustracji przedstawiono zapytanie i jego wyniki:

    ![Zapisane wyszukiwania](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Następne kroki

* Aby korzystać z tej funkcji, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
* Aby rozwiązać problemy z błędami funkcji, zobacz [Rozwiązywanie problemów Update Management](troubleshoot/update-management.md).
* Rozwiązywanie problemów z błędami programu Windows Update Agent można znaleźć w temacie [Rozwiązywanie problemów z usługą Windows Update Agent](troubleshoot/update-agent-issues.md).
* Aby rozwiązać problemy z błędami agenta aktualizacji systemu Linux, zobacz [Rozwiązywanie problemów z usługą Linux Update Agent](troubleshoot/update-agent-issues-linux.md).
