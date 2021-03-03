---
title: Jak zaktualizować reguły alertów lub reguły akcji, gdy ich zasób docelowy jest przenoszony do innego regionu platformy Azure
description: Tło i instrukcje dotyczące aktualizowania reguł alertów lub reguł akcji, gdy ich zasób docelowy jest przenoszony do innego regionu platformy Azure.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/14/2021
ms.subservice: alerts
ms.openlocfilehash: 435f7b9b585b4effe6b06d78587845bf53a7d051
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714208"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Jak zaktualizować reguły alertów lub reguły akcji, gdy ich zasób docelowy jest przenoszony do innego regionu platformy Azure

W tym artykule opisano, dlaczego mogą ulec wpływ na istniejące [reguły alertów](./alerts-overview.md) i [reguły akcji](./alerts-action-rules.md) podczas przenoszenia innych zasobów platformy Azure między regionami oraz sposobu identyfikowania i rozwiązywania tych problemów. Zapoznaj się z [dokumentacją zasobów](../../azure-resource-manager/management/move-region.md) głównych, aby uzyskać dodatkowe informacje na temat tego, kiedy zasoby są przenoszone między regionami, i listę kontrolną projektowania procesu przenoszenia.

## <a name="why-the-problem-exists"></a>Dlaczego występuje problem

Reguły alertów i reguły akcji odwołują się do innych zasobów platformy Azure. Przykłady obejmują [maszyny wirtualne platformy Azure](../../site-recovery/azure-to-azure-tutorial-migrate.md), [Azure SQL](../../azure-sql/database/move-resources-across-regions.md)i [Azure Storage](../../storage/common/storage-account-move.md). Po przeniesieniu zasobów, do których odwołują się te reguły, reguły mogą przestać działać prawidłowo, ponieważ nie mogą znaleźć zasobów, do których się odwołują.

Istnieją dwa główne przyczyny, dla których reguły mogą przestać działać po przeniesieniu zasobów docelowych:

- Zakres reguły jest jawnie odwołujący się do starego zasobu.
- Reguła alertu jest oparta na metrykach.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>Zakres reguły jawnie odwołuje się do starego zasobu

Podczas przenoszenia zasobu jego identyfikator zasobu zmienia się w większości przypadków. W tle system replikuje zasób w nowym regionie przed usunięciem go ze starego regionu. Ten proces wymaga, aby dwa zasoby i w ten sposób dwa różne identyfikatory zasobów były jednocześnie dostępne przez niewielki czas. Ponieważ identyfikatory zasobów muszą być unikatowe, podczas procesu należy utworzyć nowy identyfikator. 

**Jak przeniesienie zasobu wpływa na istniejące reguły?**

Reguły alertów i reguły akcji mają zakres zasobów, do których mają zastosowanie. Zakresem może być cała subskrypcja, Grupa zasobów lub jeden lub więcej określonych zasobów.
Na przykład poniżej przedstawiono regułę z zakresem zawierającym dwa zasoby (dwie maszyny wirtualne):

![Reguła alertu o wiele zasobów](media/alerts-resource-move/multi-resource-alert-rule.png)

Jeśli zakres reguły jawnie zawiera zasób, a ten zasób został przeniesiony i zmieniony jego identyfikator zasobu, ta reguła będzie szukać nieprawidłowego lub nieistniejącego zasobu, co kończy się niepowodzeniem.

**Jak rozwiązać ten problem?**

Zaktualizuj lub ponownie utwórz regułę, której dotyczy reguła, aby wskazywała na nowy zasób. Proces aktualizowania zakresu znajduje się w dalszej części tego artykułu.

Ten problem dotyczy następujących typów reguł:

- Reguły alertów dziennika aktywności
- Reguły akcji
- Alerty metryk — Aby uzyskać więcej informacji, zobacz następną sekcję [reguły alertów w oparciu o metryki](#alert-rules-based-on-metrics).

> [!NOTE]
> Reguły alertów wyszukiwania dzienników i reguły alertów inteligentnego wykrywania nie mają zastosowania, ponieważ ich zakres jest obszarem roboczym lub Application Insights. Żaden z tych zakresów nie obsługuje obszaru przenoszenia.

## <a name="alert-rules-based-on-metrics"></a>Reguły alertów w oparciu o metryki

Metryki, które są emitowane przez zasoby platformy Azure, są regionalne. Za każdym razem, gdy zasób jest przenoszony do nowego regionu, rozpocznie emitowanie metryk w tym nowym regionie. W związku z tym wszystkie reguły alertów oparte na metrykach muszą zostać zaktualizowane lub odtworzone, aby wskazywały na bieżący strumień metryk w poprawnym regionie.

To wyjaśnienie dotyczy zarówno [reguł alertów metryk](alerts-metric-overview.md) , jak i [reguł alertów dotyczących testów dostępności](../app/monitor-web-app-availability.md).

Jeśli **wszystkie** zasoby w zakresie zostały przeniesione, nie trzeba ponownie tworzyć reguły. Można po prostu zaktualizować dowolne pola reguły alertu, takie jak opis reguły alertu, i zapisać je.
Jeśli **tylko część** zasobów w zakresie została przeniesiona, należy usunąć przenoszone zasoby z istniejącej reguły i utworzyć nową regułę obejmującą tylko przeniesione zasoby.

## <a name="procedures-to-fix-problems"></a>Procedury rozwiązywania problemów

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Identyfikowanie reguł skojarzonych z przenoszonym zasobem z Azure Portal

- **Aby uzyskać reguły alertów** , przejdź do alertów > zarządzanie regułami alertów > filtrowanie według zawierającej ją subskrypcji i przeniesionego zasobu.
> [!NOTE]
> Reguły alertów dziennika aktywności nie obsługują tego procesu. Nie można zaktualizować zakresu reguły alertu dziennika aktywności i ma ona wskazywać na zasób w innej subskrypcji. Zamiast tego można utworzyć nową regułę, która zastąpi stary.

- **W przypadku reguł akcji** — przejdź do alertów > zarządzanie akcjami > reguły akcji (wersja zapoznawcza) > filtr według zawierającej ją subskrypcji i przeniesionego zasobu.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Zmień zakres reguły z Azure Portal

1. Otwórz regułę zidentyfikowaną w poprzednim kroku, klikając ją.
2. W obszarze **zasób** kliknij opcję **Edytuj** i Dostosuj zakres, zgodnie z wymaganiami.
3. Dostosuj inne właściwości reguły zgodnie z wymaganiami.
4. Kliknij pozycję **Zapisz**.

![Zmień zakres reguły alertu](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Zmiana zakresu reguły przy użyciu szablonów Azure Resource Manager

1. Uzyskaj szablon Azure Resource Manager reguły.  Aby wyeksportować szablon reguły z Azure Portal:
   1. Przejdź do sekcji grupy zasobów w portalu i otwórz grupę zasobów zawierającą regułę.
   2. W sekcji Przegląd zaznacz pole wyboru **Pokaż typ ukryty** i przefiltruj według odpowiedniego typu reguły.
   3. Wybierz odpowiednią regułę, aby wyświetlić jej szczegóły.
   4. W obszarze **Ustawienia** wybierz pozycję **Eksportuj szablon**.
2. Zmodyfikuj szablon. W razie potrzeby Podziel na dwie reguły (odpowiednie dla niektórych przypadków alertów metryk, jak wspomniano powyżej).
3. Wdróż ponownie szablon.

### <a name="change-scope-of-a-rule-using-rest-api"></a>Zmień zakres reguły przy użyciu interfejsu API REST

1. Pobierz istniejącą regułę ([alerty metryki](/rest/api/monitor/metricalerts/get), [alerty dziennika aktywności](/rest/api/monitor/activitylogalerts/get))
2. Modyfikuj zakres ([alerty dziennika aktywności](/rest/api/monitor/activitylogalerts/update))
3. Ponowne wdrażanie reguły ([alerty metryk](/rest/api/monitor/metricalerts/createorupdate), [alerty dziennika aktywności](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>Zmień zakres reguły przy użyciu programu PowerShell

1. Pobierz istniejącą regułę ([alerty metryk](/powershell/module/az.monitor/get-azmetricalertrulev2), [alerty dziennika aktywności](/powershell/module/az.monitor/get-azactivitylogalert), [reguły akcji](/powershell/module/az.alertsmanagement/get-azactionrule)).
2. Zmodyfikuj zakres. W razie potrzeby Podziel na dwie reguły (odpowiednie dla niektórych przypadków alertów metryk, jak wspomniano powyżej).
3. Ponownie Wdróż regułę ([alerty metryk](/powershell/module/az.monitor/add-azmetricalertrulev2), [alerty dziennika aktywności](/powershell/module/az.monitor/enable-azactivitylogalert), [reguły akcji](/powershell/module/az.alertsmanagement/set-azactionrule)).

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Zmiana zakresu reguły przy użyciu interfejsu wiersza polecenia platformy Azure

1.  Pobierz istniejącą regułę ([alerty metryki](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-show), [alerty dziennika aktywności](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)).
2.  Aktualizuj zakres reguły bezpośrednio ([alerty metryczne](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update), [alerty dziennika aktywności](/cli/azure/monitor/activity-log/alert/scope))
3.  W razie potrzeby Podziel na dwie reguły (odpowiednie dla niektórych przypadków alertów metryk, jak wspomniano powyżej).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o rozwiązywaniu innych problemów z [powiadomieniami o alertach](alerts-troubleshoot.md), [alertami metrykami](alerts-troubleshoot-metric.md)i [alertami dzienników](alerts-troubleshoot-log.md).