---
title: Odłączanie obszaru roboczego od konta usługi Automation dla Change Tracking i spisu
description: W tym artykule opisano sposób rozłączania obszaru roboczego Log Analytics z konta usługi Automation na potrzeby Change Tracking i spisu
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2be702ec6e820fe71dd8d2da7aa4cf831b52402e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83828257"
---
# <a name="unlink-workspace-from-automation-account"></a>Odłączanie obszaru roboczego od konta usługi Automation

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

* Aby obejść Change Tracking i spis, zobacz [zarządzanie Change Tracking i spisem](change-tracking-file-contents.md).
* Aby rozwiązać ogólne problemy z funkcjami, zobacz [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md).
