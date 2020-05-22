---
title: Odłączanie obszaru roboczego od konta usługi Automation dla Change Tracking i spisu
description: W tym artykule opisano sposób rozłączania obszaru roboczego Log Analytics z konta usługi Automation na potrzeby Change Tracking i spisu
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: df2de44c2c8831fa4319b80484a119052434f8fb
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749109"
---
# <a name="unlink-workspace-from-automation-account-for-change-tracking-and-inventory"></a>Odłączanie obszaru roboczego od konta usługi Automation dla Change Tracking i spisu

Podczas włączania operacji [Change Tracking i spisu](change-tracking.md) można zdecydować, aby nie zintegrować konta usługi Automation z obszarem roboczym log Analytics. Ten artykuł zawiera informacje dotyczące rozłączenia obszaru roboczego z Twojego konta.

1. Zaloguj się do platformy Azure pod adresem https://portal.azure.com .

2. Usuń Update Management dla maszyn wirtualnych. Zobacz [usuwanie maszyn wirtualnych z Change Tracking i spisu](automation-remove-vms-from-change-tracking.md).

3. Jeśli Change Tracking i spis obejmuje wcześniejsze wersje usługi Azure SQL monitoring, Instalator funkcji mógł utworzyć zasoby automatyzacji, które należy usunąć. Znajdź te zasoby i usuń je.

4. Otwórz konto usługi Automation i wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby** po lewej stronie.

5. Na stronie Odłącz obszar roboczy kliknij **Odłącz obszar roboczy** i odpowiadaj na polecenia monitujące.

   ![Odłącz stronę obszaru roboczego](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png).

6. Gdy Azure Automation próbuje odłączyć obszar roboczy Log Analytics, można śledzić postęp w obszarze **powiadomienia** z menu.

Alternatywnie możesz odłączyć obszar roboczy Log Analytics z konta usługi Automation z poziomu obszaru roboczego:

1. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**. 
2. Na stronie konto usługi Automation wybierz opcję **Odłącz konto**.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie Change Tracking i spisem](change-tracking-file-contents.md)
* [Usuwanie maszyn wirtualnych z Change Tracking i spisu](automation-remove-vms-from-change-tracking.md)
* [Rozwiązywanie problemów dotyczących zmian na maszynie wirtualnej platformy Azure](automation-tutorial-troubleshoot-changes.md)
* [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md)
