---
title: Odłączanie obszaru roboczego od konta usługi Automation dla Update Management
description: Ten artykuł zawiera informacje dotyczące rozłączania obszaru roboczego Log Analytics z konta usługi Automation dla Update Management
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4261b3c3fa7aab830f5f57e86ee25f8ba5894849
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749067"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>Odłączanie obszaru roboczego od konta usługi Automation dla Update Management

Podczas operacji [Update Management](automation-update-management.md) można zdecydować, aby nie zintegrować konta usługi Automation z obszarem roboczym log Analytics. Ten artykuł zawiera informacje dotyczące rozłączenia obszaru roboczego z Twojego konta.

1. Zaloguj się do platformy Azure pod adresem https://portal.azure.com .

2. Usuń Update Management dla maszyn wirtualnych. Zobacz [usuwanie maszyn wirtualnych z Update Management](automation-remove-vms-from-update-management.md).

3. Jeśli Update Management obejmuje wcześniejsze wersje usługi Azure SQL monitoring, Instalator funkcji mógł utworzyć zasoby automatyzacji, które należy usunąć. W przypadku Update Management można chcieć usunąć następujące elementy, które nie są już potrzebne:

   * Harmonogramy aktualizacji — każdy ma nazwę zgodną z utworzonym wdrożeniem aktualizacji.
   * Grupy hybrydowych procesów roboczych utworzone dla Update Management — każda nazwa jest podobna do maszyna1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

4. Otwórz konto usługi Automation i wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby** po lewej stronie.

5. Na stronie Odłącz obszar roboczy kliknij **Odłącz obszar roboczy** i odpowiadaj na polecenia monitujące.

   ![Odłącz stronę obszaru roboczego](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png).

6. Gdy Azure Automation próbuje odłączyć obszar roboczy Log Analytics, można śledzić postęp w obszarze **powiadomienia** z menu.

Alternatywnie możesz odłączyć obszar roboczy Log Analytics z konta usługi Automation z poziomu obszaru roboczego:

1. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**. 
2. Na stronie konto usługi Automation wybierz opcję **Odłącz konto**.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md)
* [Usuń maszyny wirtualne z Update Management](automation-remove-vms-from-update-management.md)
* [Rozwiązywanie problemów dotyczących Update Management](troubleshoot/update-management.md)
