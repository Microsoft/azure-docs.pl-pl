---
title: Usługa Azure Automation Konfiguruj maszyny wirtualne start/stop podczas pracy poza godzinami pracy
description: W tym artykule opisano sposób konfigurowania maszyn wirtualnych Start/Stop podczas rozwiązania poza godzinami pracy do obsługi różnych przypadków użycia lub scenariuszy.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9842a736cf922e0490f2b0c8acb1d2e5833f3d6c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604757"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Jak skonfigurować maszyny wirtualne Start/Stop w godzinach pracy

Za pomocą rozwiązania **Start/Stop VMs w godzinach wolnych** można:

- [Zaplanuj uruchamianie i zatrzymywać maszyny wirtualne](#schedule).
- Zaplanuj maszyny wirtualne, aby rozpocząć i zatrzymać w kolejności rosnącej [przy użyciu tagów platformy Azure](#tags) (nie są obsługiwane dla klasycznych maszyn wirtualnych).
- Automatyczne zatrzymywania maszyn wirtualnych w oparciu o [niskie zużycie procesora .](#cpuutil)

W tym artykule opisano, jak pomyślnie skonfigurować rozwiązanie do obsługi tych scenariuszy. Można również dowiedzieć się, jak wykonać inne typowe ustawienia konfiguracji rozwiązania, takie jak:

* [Konfigurowanie powiadomień e-mail](#configure-email-notifications)

* [Dodawanie maszyny wirtualnej](#add-a-vm)

* [Wykluczanie maszyny wirtualnej](#exclude-a-vm)

* [Modyfikowanie harmonogramów uruchamiania i zamykania](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scenariusz 1: Uruchamianie/zatrzymywania maszyn wirtualnych zgodnie z harmonogramem

W tym scenariuszu jest konfiguracja domyślna podczas pierwszego wdrożenia rozwiązania. Na przykład można skonfigurować go, aby zatrzymać wszystkie maszyny wirtualne w ramach subskrypcji po opuszczeniu pracy w godzinach wieczornych i uruchomić je rano, gdy jesteś z powrotem w biurze. Podczas konfigurowania harmonogramów **Scheduled-StartVM** i **Scheduled-StopVM** podczas wdrażania, uruchamiają i zatrzymują ukierunkowane maszyny wirtualne. Konfigurowanie tego rozwiązania tak, aby po prostu zatrzymać maszyny wirtualne jest obsługiwane, zobacz [Modyfikowanie harmonogramów uruchamiania i zamykania,](#modify-the-startup-and-shutdown-schedules) aby dowiedzieć się, jak skonfigurować harmonogram niestandardowy.

> [!NOTE]
> Strefa czasowa jest bieżącą strefą czasową podczas konfigurowania parametru czasu harmonogramu. Jednak jest przechowywany w formacie UTC w usłudze Azure Automation. Nie trzeba wykonywać żadnej konwersji strefy czasowej, ponieważ jest to obsługiwane podczas wdrażania.

Można kontrolować, które maszyny wirtualne są w zakresie, konfigurując następujące zmienne: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**i **External_ExcludeVMNames**.

Można włączyć kierowanie akcji względem subskrypcji i grupy zasobów lub kierowanie na określoną listę maszyn wirtualnych, ale nie obie.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Kierowanie akcji początkowych i zatrzymania względem subskrypcji i grupy zasobów

1. Skonfiguruj `External_Stop_ResourceGroupNames` zmienne i, `External_ExcludeVMNames` aby określić maszyny wirtualne docelowe.

2. Włącz i zaktualizuj **harmonogramy Scheduled-StartVM** i **Scheduled-StopVM.**

3. Uruchom **ScheduledStartStop_Parent** runbook z ustawionym polem parametru **ACTION** **i** polem parametru **WHATIF** ustawionym na True, aby wyświetlić podgląd zmian.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Kierowanie akcji początkowej i stop przez listę maszyn wirtualnych

1. Uruchom **ScheduledStartStop_Parent** runbook z ustawioną ustawą **ACTION,** aby **rozpocząć,** dodaj listę maszyn wirtualnych oddzieloną przecinkami w polu parametru **VMList,** a następnie ustaw pole parametru **WHATIF** na True. Podgląd zmian.

2. Skonfiguruj `External_ExcludeVMNames` zmienną za pomocą listy maszyn wirtualnych rozdzielonych przecinkami (VM1, VM2, VM3).

3. Ten scenariusz nie `External_Start_ResourceGroupNames` honoruje i `External_Stop_ResourceGroupnames` zmiennych. W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać szczegółowe informacje, zobacz [Planowanie uruchomieniu księgi ą w usłudze Azure Automation](../automation/automation-schedules.md).

    > [!NOTE]
    > Wartość **nazw grup zasobów docelowych** jest przechowywana jako wartość dla obu `External_Start_ResourceGroupNames` i `External_Stop_ResourceGroupNames`. Aby uzyskać dalszą szczegółowość, można zmodyfikować każdą z tych zmiennych, aby kierować reklamy na różne grupy zasobów. Aby rozpocząć akcję, `External_Start_ResourceGroupNames`użyć `External_Stop_ResourceGroupNames` i użyć do zatrzymania akcji. Maszyny wirtualne są automatycznie dodawane do harmonogramów uruchamiania i zatrzymywania.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scenariusz 2: Uruchamianie/zatrzymywać maszyny wirtualne w sekwencji przy użyciu tagów

W środowisku, które zawiera dwa lub więcej składników na wielu maszynach wirtualnych obsługujących obciążenie rozproszone, obsługa sekwencji, w której składniki są uruchamiane i zatrzymywane w kolejności, jest ważne. Ten scenariusz można wykonać, wykonując następujące kroki:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Kierowanie akcji początkowych i zatrzymania względem subskrypcji i grupy zasobów

1. Dodaj `sequencestart` i `sequencestop` tag z dodatnią wartością całkowitą do maszyn `External_Start_ResourceGroupNames` wirtualnych, które są ukierunkowane i `External_Stop_ResourceGroupNames` zmienne. Akcje start i stop są wykonywane w porządku rosnącym. Aby dowiedzieć się, jak oznaczyć maszynę wirtualną, zobacz [Oznaczanie maszyny wirtualnej systemu Windows na platformie Azure](../virtual-machines/windows/tag.md) i [oznaczanie maszyny wirtualnej systemu Linux na platformie Azure](../virtual-machines/linux/tag.md).

2. Zmodyfikuj harmonogramy **Sequenced-StartVM** i **Sequenced-StopVM** do daty i godziny, które spełniają twoje wymagania i włącz harmonogram.

3. Uruchom **SequencedStartStop_Parent** runbook z zestawem **ACTION,** aby **rozpocząć** i **ustawieniem WHATIF** na True, aby wyświetlić podgląd zmian.

4. Podgląd akcji i wprowadzić wszelkie niezbędne zmiany przed wdrożeniem przeciwko maszynom wirtualnym produkcji. Gdy będzie to gotowe, ręcznie wykonaj runbook z parametrem `Sequenced-StartVM` **ustawionym**na False lub pozwól harmonogramowi automatyzacji i `Sequenced-StopVM` uruchomić automatycznie zgodnie z określonym harmonogramem.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Kierowanie akcji początkowej i stop przez listę maszyn wirtualnych

1. Dodaj `sequencestart` i `sequencestop` tag z dodatnią wartością całkowitą do maszyn wirtualnych, które zamierzasz dodać do parametru. `VMList`

2. Uruchom **SequencedStartStop_Parent** runbook z ustawionym zestawem **ACTION,** aby **rozpocząć,** dodaj listę maszyn wirtualnych oddzielonych przecinkami w polu parametru **VMList,** a następnie ustaw **wartość WHATIF** na True. Podgląd zmian.

3. Skonfiguruj `External_ExcludeVMNames` zmienną za pomocą listy maszyn wirtualnych rozdzielonych przecinkami (VM1, VM2, VM3).

4. Ten scenariusz nie `External_Start_ResourceGroupNames` honoruje i `External_Stop_ResourceGroupnames` zmiennych. W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać szczegółowe informacje, zobacz [Planowanie uruchomieniu księgi ą w usłudze Azure Automation](../automation/automation-schedules.md).

5. Podgląd akcji i wprowadzić wszelkie niezbędne zmiany przed wdrożeniem przeciwko maszynom wirtualnym produkcji. Gdy jest to gotowe, ręcznie wykonaj **książkę monitorowania i diagnostyki/monitorowania akcji** z parametrem ustawionym na **Fałsz**. Alternatywnie niech automatyzacji harmonogram `Sequenced-StartVM` i `Sequenced-StopVM` uruchomić automatycznie zgodnie z harmonogramem określonych.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scenariusz 3: Automatyczne uruchamianie/zatrzymywania na podstawie wykorzystania procesora

To rozwiązanie może pomóc w zarządzaniu kosztami uruchamiania usługi Azure Resource Manager i klasycznych maszyn wirtualnych w ramach subskrypcji, oceniając maszyny wirtualne, które nie są używane w okresach innych niż szczyt, na przykład po godzinach, i automatycznie zamykając je, jeśli wykorzystanie procesora jest mniejsze niż określona wartość procentowa.

Domyślnie rozwiązanie jest wstępnie skonfigurowane do oceny procentowej metryki procesora CPU, aby sprawdzić, czy średnie wykorzystanie wynosi 5 procent lub mniej. Ten scenariusz jest kontrolowany przez następujące zmienne i może być modyfikowany, jeśli wartości domyślne nie spełniają wymagań:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Można włączyć i kierować akcję na subskrypcję i grupę zasobów lub kierować określoną listę maszyn wirtualnych.

Po uruchomieniu **AutoStop_CreateAlert_Parent** runbook, sprawdza, czy istnieje docelowa subskrypcja, grupy zasobów i maszyny wirtualne. Jeśli maszyny wirtualne istnieją, element runbook następnie wywołuje **AutoStop_CreateAlert_Child** element runbook dla każdej zweryfikowanej maszyny wirtualnej przez nadrzędny element runbook. Ten podrzędny system runbook wykonuje następujące czynności:

* Tworzy regułę alertu metryki dla każdej zweryfikowanej maszyny Wirtualnej.

* Wyzwala **AutoStop_VM_Child** runbook dla określonej maszyny Wirtualnej, jeśli procesor spadnie poniżej skonfigurowanego progu dla określonego przedziału czasu. Ten projekt runbook następnie próbuje zatrzymać maszynę wirtualną.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Aby skierować akcję automatycznego zatrzymania na wszystkie maszyny wirtualne w ramach subskrypcji

1. Upewnij się, że zmienna jest pusta `External_Stop_ResourceGroupNames` lub ustawiona na * (symbol wieloznaczny).

2. [Opcjonalny krok] Jeśli chcesz wykluczyć niektóre maszyny wirtualne z automatycznego zamykania, możesz dodać do `External_ExcludeVMNames` zmiennej listę nazw maszyn wirtualnych oddzieloną przecinkami.

3. Włącz `Schedule_AutoStop_CreateAlert_Parent` harmonogram do uruchomienia, aby utworzyć wymagane reguły alertów zatrzymania metryki maszyny Wirtualnej dla wszystkich maszyn wirtualnych w ramach subskrypcji. Uruchamianie tego typu harmonogramu umożliwia tworzenie nowych reguł alertów metryki, jak nowe maszyny wirtualne są dodawane do subskrypcji.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Aby skierować akcję automatycznego zatrzymania na wszystkie maszyny wirtualne w grupie zasobów lub wielu grupach zasobów

1. Do tej zmiennej należy dodać listę nazw `External_Stop_ResourceGroupNames` grup zasobów oddzieloną przecinkami.

2. Jeśli chcesz wykluczyć niektóre maszyny wirtualne z automatycznego zamykania, możesz dodać do `External_ExcludeVMNames` zmiennej listę nazw maszyn wirtualnych oddzieloną przecinkami.

3. Włącz **harmonogram Schedule_AutoStop_CreateAlert_Parent** do uruchomienia, aby utworzyć wymagane reguły alertów zatrzymania metryki maszyny Wirtualnej dla wszystkich maszyn wirtualnych w grupach zasobów. Uruchomienie tej operacji zgodnie z harmonogramem umożliwia tworzenie nowych reguł alertów metryki, gdy nowe maszyny wirtualne są dodawane do grup zasobów.

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>Aby skierować akcję autostopu na listę maszyn wirtualnych

1. Utwórz nowy [harmonogram](shared-resources/schedules.md#creating-a-schedule) i połącz go z **AutoStop_CreateAlert_Parent** runbook, dodając do `VMList` parametru listę nazw maszyn wirtualnych oddzieloną przecinkami.

2. Opcjonalnie, jeśli chcesz wykluczyć niektóre maszyny wirtualne z automatycznego zamykania, można dodać listę rozdzielonych przecinkami nazw maszyn wirtualnych do zmiennej. `External_ExcludeVMNames`

## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

Aby zmienić powiadomienia e-mail po wdrożeniu rozwiązania, zmodyfikuj grupę akcji utworzoną podczas wdrażania.  

> [!NOTE]
> Subskrypcje w chmurze azure dla instytucji rządowych nie obsługują funkcji poczty e-mail tego rozwiązania.

1. W witrynie Azure portal przejdź do **pozycji Monitor,** a następnie **do grupy akcji**. Wybierz grupę akcji o nazwie **StartStop_VM_Notication**.

    ![Strona rozwiązania do zarządzania aktualizacjami automatyzacji](media/automation-solution-vm-management/azure-monitor.png)

2. Na **stronie StartStop_VM_Notification** kliknij pozycję **Edytuj szczegóły** w obszarze **Szczegóły**. Spowoduje to otwarcie strony **E-mail/SMS/Push/Voice.** Zaktualizuj adres e-mail i kliknij przycisk **OK,** aby zapisać zmiany.

    ![Strona rozwiązania do zarządzania aktualizacjami automatyzacji](media/automation-solution-vm-management/change-email.png)

    Alternatywnie można dodać dodatkowe akcje do grupy akcji, aby dowiedzieć się więcej o grupach akcji, zobacz [grupy akcji](../azure-monitor/platform/action-groups.md)

Poniżej przedstawiono przykładową wiadomość e-mail, która jest wysyłana, gdy rozwiązanie zamyka maszyny wirtualne.

![Strona rozwiązania do zarządzania aktualizacjami automatyzacji](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Dodawanie/wykluczanie maszyn wirtualnych

Rozwiązanie zapewnia możliwość dodawania maszyn wirtualnych, które mają być kierowane przez rozwiązanie lub w szczególności wykluczyć maszyny z rozwiązania.

### <a name="add-a-vm"></a>Dodawanie maszyny wirtualnej

Istnieją dwie opcje, których można użyć, aby upewnić się, że maszyna wirtualna jest uwzględniona w rozwiązaniu Start/Stop po uruchomieniu.

* Każdy z nadrzędnych [obrażeń runbook](automation-solution-vm-management.md#runbooks) rozwiązania ma `VMList` parametr. Można przekazać listę rozdzielonych przecinkami nazw maszyn wirtualnych do tego parametru podczas planowania odpowiedniego nadrzędnego uruchomieniu dla danego przypadku, a te maszyny wirtualne zostaną uwzględnione podczas uruchamiania rozwiązania.

* Aby wybrać wiele maszyn `External_Start_ResourceGroupNames` wirtualnych, należy ustawić i `External_Stop_ResourceGroupNames` nazwy grup zasobów, które zawierają maszyny wirtualne, które chcesz uruchomić lub zatrzymać. Można również ustawić zmienne na `*` wartość, aby rozwiązanie było uruchamiane dla wszystkich grup zasobów w ramach subskrypcji.

### <a name="exclude-a-vm"></a>Wykluczanie maszyny wirtualnej

Aby wykluczyć maszynę wirtualną z rozwiązania, `External_ExcludeVMNames` można dodać ją do zmiennej. Ta zmienna jest oddzieloną przecinkami listą określonych maszyn wirtualnych do wykluczenia z rozwiązania Start/Stop. Ta lista jest ograniczona do 140 maszyn wirtualnych. Jeśli dodasz więcej niż 140 maszyn wirtualnych do tej listy oddzielone przecinkami, maszyny wirtualne, które są ustawione do wykluczenia, mogą zostać przypadkowo uruchomione lub zatrzymane.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modyfikowanie harmonogramów uruchamiania i zamykania

Zarządzanie harmonogramami uruchamiania i zamykania w tym rozwiązaniu przebiega zgodnie z tymi samymi krokami, które opisano w [obszarze Planowanie wiązki uruchomieniu w usłudze Azure Automation](automation-schedules.md). Musi istnieć oddzielny harmonogram uruchamiania i zatrzymywania maszyn wirtualnych.

Konfigurowanie rozwiązania, aby po prostu zatrzymać maszyny wirtualne w określonym czasie jest obsługiwane. W tym scenariuszu po prostu utworzyć **harmonogram zatrzymania** i nie odpowiednie **harmonogram start.** W tym celu należy:

1. Upewnij się, że dodano grupy zasobów dla maszyn `External_Stop_ResourceGroupNames` wirtualnych, aby zamknąć w zmiennej.

2. Utwórz własny harmonogram dla czasu, który chcesz zamknąć maszyny wirtualne.

3. Przejdź do **ScheduledStartStop_Parent** uruchomieniu i kliknij pozycję **Zaplanuj**. Dzięki temu można wybrać harmonogram utworzony w poprzednim kroku.

4. Wybierz **parametry i uruchom ustawienia** i ustaw pole **AKCJA** na **Stop**.

5. Aby zapisać zmiany, wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak rozwiązywać problemy z maszynami wirtualnymi start/stop poza godzinami pracy, zobacz [Rozwiązywanie problemów z maszynami wirtualnymi Start/Stop](troubleshoot/start-stop-vm.md).

* [Przejrzyj](automation-solution-vm-management-logs.md) rekordy automatyzacji zapisane w dziennikach usługi Azure Monitor i przykładowe zapytania wyszukiwania dziennika, aby przeanalizować stan zadań uruchomieniu automatyzacji z maszyn wirtualnych Start/Stop.
