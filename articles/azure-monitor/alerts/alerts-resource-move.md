---
title: Jak zaktualizować reguły alertów lub reguły akcji, gdy ich zasób docelowy zostanie przeniesiony do innego regionu świadczenia usługi Azure
description: Tło i instrukcje dotyczące aktualizowania reguł alertów lub reguł akcji, gdy ich zasób docelowy zostanie przeniesiony do innego regionu świadczenia usługi Azure.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/14/2021
ms.openlocfilehash: d21ee7a60d11a154737c5380ec20d3e9c4490962
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786067"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Jak zaktualizować reguły alertów lub reguły akcji, gdy ich zasób docelowy zostanie przeniesiony do innego regionu świadczenia usługi Azure

W tym artykule opisano, dlaczego istniejące reguły [alertów](./alerts-overview.md) i reguły [akcji](./alerts-action-rules.md) mogą mieć wpływ na przenoszenie innych zasobów platformy Azure między regionami oraz jak identyfikować i rozwiązywać te problemy. Zapoznaj się z główną [dokumentacją przenoszenia](../../azure-resource-manager/management/move-resources-overview.md) zasobów, aby uzyskać dodatkowe informacje na temat przydatnego przenoszenia zasobów między regionami oraz listę kontrolną projektowania procesu przenoszenia.

## <a name="why-the-problem-exists"></a>Dlaczego problem istnieje

Reguły alertów i reguły akcji odwołują się do innych zasobów platformy Azure. Przykłady [obejmują maszyny wirtualne platformy Azure,](../../site-recovery/azure-to-azure-tutorial-migrate.md) [Azure SQL](../../azure-sql/database/move-resources-across-regions.md)i [usługę Azure Storage.](../../storage/common/storage-account-move.md) Po przeniesieniu zasobów, do których odwołują się te reguły, reguły prawdopodobnie przestaną działać prawidłowo, ponieważ nie mogą znaleźć zasobów, do których odwołują się.

Istnieją dwie główne przyczyny, dla których reguły mogą przestać działać po przeniesieniu zasobów docelowych:

- Zakres reguły jawnie odwołuje się do starego zasobu.
- Reguła alertu jest oparta na metrykach.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>Zakres reguły jawnie odwołuje się do starego zasobu

Gdy przenosisz zasób, jego identyfikator zasobu zmienia się w większości przypadków. W tle system replikuje zasób do nowego regionu przed usunięciem go ze starego regionu. Ten proces wymaga, aby dwa zasoby i w związku z tym dwa różne identyfikatory zasobów istniały jednocześnie przez niewielki okres czasu. Ponieważ identyfikatory zasobów muszą być unikatowe, podczas tego procesu należy utworzyć nowy identyfikator. 

**Jak przeniesienie zasobu wpływa na istniejące reguły?**

Reguły alertów i reguły akcji mają zakres zasobów, których dotyczą. Zakresem może być cała subskrypcja, grupa zasobów lub co najmniej jeden określony zasób.
Na przykład poniżej znajduje się reguła z zakresem z dwoma zasobami (dwie maszyny wirtualne):

![Reguła alertu o wielu zasobach](media/alerts-resource-move/multi-resource-alert-rule.png)

Jeśli zakres reguły jawnie wspomina o zasobie, a ten zasób został przeniesiony i zmieniony jego identyfikator zasobu, ta reguła będzie szukać niewłaściwego lub nieistniejącego zasobu i w związku z tym nie powiedzie się.

**Jak rozwiązać ten problem?**

Zaktualizuj lub utwórz ponownie regułę, których dotyczy problem, aby wskazać nowy zasób. Proces aktualizowania zakresu znajduje się w dalszej części tego artykułu.

Problem dotyczy następujących typów reguł:

- Reguły alertów dziennika aktywności
- Reguły akcji
- Alerty dotyczące metryk — aby uzyskać więcej informacji, zobacz następną [sekcję Reguły alertów oparte na metrykach.](#alert-rules-based-on-metrics)

> [!NOTE]
> Nie ma to wpływu na reguły alertów przeszukiwania dzienników i reguły alertów inteligentnego wykrywania, ponieważ ich zakres to obszar roboczy lub Application Insights. Żaden z tych zakresów nie obsługuje obecnie przenosi regionów.

## <a name="alert-rules-based-on-metrics"></a>Reguły alertów oparte na metrykach

Metryki emitowane przez zasoby platformy Azure są regionalne. Za każdym razem, gdy zasób zostanie przeniesiony do nowego regionu, rozpoczyna emitowanie metryk w tym nowym regionie. W związku z tym wszystkie reguły alertów oparte na metrykach muszą zostać zaktualizowane lub ponownie utworzone, aby wskazać bieżący strumień metryk w poprawnym regionie.

To wyjaśnienie dotyczy zarówno reguł [alertów dotyczących metryk,](alerts-metric-overview.md) jak [i reguł alertów testów dostępności.](../app/monitor-web-app-availability.md)

Jeśli **wszystkie** zasoby w zakresie zostały przeniesione, nie trzeba ponownie tworzyć reguły. Wystarczy zaktualizować dowolne pole reguły alertu, takie jak opis reguły alertu, i zapisać je.
Jeśli **tylko niektóre** zasoby w zakresie zostały przeniesione, musisz usunąć przeniesione zasoby z istniejącej reguły i utworzyć nową regułę, która będzie dotyczyć tylko przeniesionych zasobów.

## <a name="procedures-to-fix-problems"></a>Procedury naprawiania problemów

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Identyfikowanie reguł skojarzonych z przeniesionym zasobem z Azure Portal

- **W przypadku reguł alertów** — przejdź do > Alerty i Zarządzaj regułami alertów> filtruj według zawierającej subskrypcji i przeniesionego zasobu.
> [!NOTE]
> Reguły alertów dziennika aktywności nie obsługują tego procesu. Nie można zaktualizować zakresu reguły alertu dziennika aktywności i wskazać zasobu w innej subskrypcji. Zamiast tego można utworzyć nową regułę, która zastąpi starą regułę.

- **W przypadku reguł akcji** — przejdź do tematu Alerty > Zarządzaj akcjami > Reguły akcji (wersja zapoznawcza) > filtru według zawierającej subskrypcji i przeniesionego zasobu.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Zmienianie zakresu reguły z Azure Portal

1. Otwórz regułę zidentyfikowaną w poprzednim kroku, klikając ją.
2. W **obszarze Zasób** kliknij **pozycję Edytuj** i dostosuj zakres zgodnie z potrzebami.
3. Dostosuj inne właściwości reguły zgodnie z potrzebami.
4. Kliknij pozycję **Zapisz**.

![Zmienianie zakresu reguły alertu](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Zmienianie zakresu reguły przy użyciu Azure Resource Manager szablonów

1. Uzyskaj Azure Resource Manager szablonu reguły.   Aby wyeksportować szablon reguły z Azure Portal:
   1. Przejdź do sekcji Grupy zasobów w portalu i otwórz grupę zasobów zawierającą regułę.
   2. W sekcji Przegląd zaznacz pole wyboru **Pokaż** typ ukryty i filtruj według odpowiedniego typu reguły.
   3. Wybierz odpowiednią regułę, aby wyświetlić jej szczegóły.
   4. W **obszarze Ustawienia** wybierz pozycję **Eksportuj szablon**.
2. Zmodyfikuj szablon. W razie potrzeby podziel na dwie reguły (odpowiednie dla niektórych przypadków alertów dotyczących metryk, jak wspomniano powyżej).
3. Ponownie wdychaj szablon.

### <a name="change-scope-of-a-rule-using-rest-api"></a>Zmienianie zakresu reguły przy użyciu interfejsu API REST

1. Pobierz istniejącą regułę[(alerty metryk,](/rest/api/monitor/metricalerts/get) [alerty dziennika aktywności)](/rest/api/monitor/activitylogalerts/get)
2. Modyfikowanie zakresu[(alerty dziennika aktywności](/rest/api/monitor/activitylogalerts/update))
3. Ponownie wdychaj regułę[(alerty metryk,](/rest/api/monitor/metricalerts/createorupdate) [alerty dziennika aktywności](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>Zmienianie zakresu reguły przy użyciu programu PowerShell

1. Pobierz istniejącą regułę[(alerty metryk,](/powershell/module/az.monitor/get-azmetricalertrulev2) [alerty dziennika aktywności,](/powershell/module/az.monitor/get-azactivitylogalert) [reguły akcji).](/powershell/module/az.alertsmanagement/get-azactionrule)
2. Zmodyfikuj zakres. W razie potrzeby podziel na dwie reguły (odpowiednie dla niektórych przypadków alertów dotyczących metryk, jak wspomniano powyżej).
3. Ponownie wdychaj regułę[(alerty metryk,](/powershell/module/az.monitor/add-azmetricalertrulev2) [alerty dziennika aktywności,](/powershell/module/az.monitor/enable-azactivitylogalert) [reguły akcji).](/powershell/module/az.alertsmanagement/set-azactionrule)

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Zmienianie zakresu reguły przy użyciu interfejsu wiersza polecenia platformy Azure

1.  Pobierz istniejącą regułę[(alerty metryk,](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_show) [alerty dziennika aktywności).](/cli/azure/monitor/activity-log/alert#az_monitor_activity_log-alert_list)
2.  Bezpośrednie aktualizowanie zakresu reguły[(alerty metryk,](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_update) [alerty dziennika aktywności)](/cli/azure/monitor/activity-log/alert/scope)
3.  W razie potrzeby podziel na dwie reguły (odpowiednie dla niektórych przypadków alertów dotyczących metryk, jak wspomniano powyżej).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o rozwiązywaniu innych problemów z [powiadomieniami o alertach,](alerts-troubleshoot.md) [alertami metryk](alerts-troubleshoot-metric.md)i [alertami dzienników.](alerts-troubleshoot-log.md)