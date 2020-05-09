---
title: Azure Automation skonfigurować rozwiązanie Start/Stop VMs during off-hours
description: W tym artykule opisano sposób konfigurowania rozwiązania Start/Stop VMs during off-hours w celu obsługi różnych przypadków użycia lub scenariuszy.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 4cceb0d5ada82de73bc74c0ed408f8eb988ea8ec
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864270"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Jak skonfigurować rozwiązanie Start/Stop VMs during off-hours

Rozwiązanie **Start/Stop VMS during off-hours** pozwala:

- [Zaplanuj uruchamianie i zatrzymywanie maszyn wirtualnych](#schedule).
- Zaplanuj uruchamianie i zatrzymywanie maszyn wirtualnych w porządku rosnącym przy [użyciu tagów platformy Azure](#tags) (nieobsługiwanych w przypadku klasycznych maszyn wirtualnych).
- Autozatrzymaj maszyny wirtualne na podstawie [niskiego użycia procesora CPU](#cpuutil).

W tym artykule opisano sposób pomyślnego skonfigurowania rozwiązania do obsługi tych scenariuszy. Możesz również dowiedzieć się, jak wykonywać inne typowe ustawienia konfiguracji dla rozwiązania, na przykład:

* [Konfigurowanie powiadomień e-mail](#configure-email-notifications)

* [Dodawanie maszyny wirtualnej](#add-a-vm)

* [Wykluczanie maszyny wirtualnej](#exclude-a-vm)

* [Modyfikowanie harmonogramów uruchamiania i zamykania](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scenariusz 1. Uruchamianie/zatrzymywanie maszyn wirtualnych zgodnie z harmonogramem

Ten scenariusz jest domyślną konfiguracją podczas pierwszego wdrożenia rozwiązania. Można na przykład skonfigurować go tak, aby zatrzymać wszystkie maszyny wirtualne w ramach subskrypcji, gdy opuszczasz pracę w wieczorie i zaczniesz je od rano, gdy wrócisz do biura. Podczas konfigurowania harmonogramów **zaplanowane — StartVM** i **zaplanowany-StopVM** podczas wdrażania, uruchamiają i zatrzymują maszyny wirtualne. Konfigurowanie tego rozwiązania do tylko zatrzymywania maszyn wirtualnych jest obsługiwane, zobacz [Modyfikowanie harmonogramów uruchamiania i zamykania](#modify-the-startup-and-shutdown-schedules) , aby dowiedzieć się, jak skonfigurować harmonogram niestandardowy.

> [!NOTE]
> Strefa czasowa jest bieżącą strefą czasową podczas konfigurowania parametru czasu planowania. Są one jednak przechowywane w formacie UTC w Azure Automation. Nie trzeba wykonywać żadnej konwersji strefy czasowej, ponieważ jest ona obsługiwana podczas wdrażania.

Możesz kontrolować, które maszyny wirtualne znajdują się w zakresie, konfigurując następujące zmienne: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**i **External_ExcludeVMNames**.

Można włączyć opcję określania wartości docelowej dla subskrypcji i grupy zasobów lub przeznaczenie określonej listy maszyn wirtualnych, ale nie obu.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Kierowanie akcji uruchamiania i zatrzymywania względem subskrypcji i grupy zasobów

1. Skonfiguruj zmienne `External_Stop_ResourceGroupNames` i `External_ExcludeVMNames` , aby określić docelowe maszyny wirtualne.

2. Włącz i zaktualizuj harmonogramy **zaplanowane-StartVM** i **zaplanowane StopVM** .

3. Uruchom **ScheduledStartStop_Parent** element Runbook z polem parametru **akcji** ustawionym na wartość **Start** , a wartością pola parametr **WHATIF** ustawioną na true, aby wyświetlić podgląd zmian.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Kierowanie akcji Rozpocznij i Zatrzymaj według maszyny wirtualnej

1. Uruchom **ScheduledStartStop_Parent** element Runbook z ustawioną **akcją** **Start**, Dodaj rozdzieloną przecinkami listę maszyn wirtualnych w polu parametr **VMList** , a następnie ustaw dla pola parametru **WHATIF** wartość true. Wyświetl podgląd zmian.

2. Skonfiguruj `External_ExcludeVMNames` zmienną z rozdzieloną przecinkami listą maszyn wirtualnych (VM1, VM2, VM3).

3. W tym scenariuszu nie są `External_Start_ResourceGroupNames` uwzględniane `External_Stop_ResourceGroupnames` zmienne i. W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać szczegółowe informacje, zobacz [Planowanie elementu Runbook w Azure Automation](../automation/automation-schedules.md).

    > [!NOTE]
    > Wartość **docelowej nazwy obiektu** do odniesień jest przechowywana jako wartość dla obu `External_Start_ResourceGroupNames` i. `External_Stop_ResourceGroupNames` Aby uzyskać więcej stopnia szczegółowości, można zmodyfikować każdą z tych zmiennych dla różnych grup zasobów. Dla akcji Rozpocznij akcję, `External_Start_ResourceGroupNames`Użyj, i `External_Stop_ResourceGroupNames` Użyj do zatrzymania działania. Maszyny wirtualne są automatycznie dodawane do harmonogramów uruchamiania i zatrzymywania.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scenariusz 2. Uruchamianie/zatrzymywanie maszyn wirtualnych w sekwencji przy użyciu tagów

W środowisku zawierającym co najmniej dwa składniki na wielu maszynach wirtualnych obsługujących obciążenie rozproszone, obsługa sekwencji, w której składniki zostały uruchomione i zatrzymane, jest ważna. Ten scenariusz można wykonać, wykonując następujące czynności:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Kierowanie akcji uruchamiania i zatrzymywania względem subskrypcji i grupy zasobów

1. Dodaj `sequencestart` `sequencestop` tag i z dodatnią wartością całkowitą do maszyn wirtualnych, które są przeznaczone dla `External_Start_ResourceGroupNames` zmiennych `External_Stop_ResourceGroupNames` i. Akcje uruchamiania i zatrzymywania są wykonywane w kolejności rosnącej. Aby dowiedzieć się, jak oznaczyć maszynę wirtualną, zobacz [znakowanie maszyny wirtualnej z systemem Windows na platformie Azure](../virtual-machines/windows/tag.md) i oznaczanie [maszyny wirtualnej z systemem Linux na platformie Azure](../virtual-machines/linux/tag.md).

2. Zmodyfikuj harmonogramy **Sequenced-StartVM** i **Sequenced-StopVM** do daty i godziny, które spełniają Twoje wymagania, i Włącz harmonogram.

3. Uruchom **SequencedStartStop_Parent** element Runbook z **akcją** z ustawioną opcją **Start** i **WHATIF** o wartości true, aby wyświetlić podgląd zmian.

4. Wyświetl podgląd akcji i wprowadź wszelkie niezbędne zmiany przed wdrożeniem na maszynach wirtualnych w środowisku produkcyjnym. Gdy wszystko będzie gotowe, ręcznie wykonaj element Runbook z parametrem ustawionym na **wartość false**lub pozwól `Sequenced-StartVM` , `Sequenced-StopVM` aby harmonogram automatyzacji został uruchomiony automatycznie zgodnie z określonym harmonogramem.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Kierowanie akcji Rozpocznij i Zatrzymaj według maszyny wirtualnej

1. Dodaj `sequencestart` `sequencestop` tag i z dodatnią wartością całkowitą do maszyn wirtualnych, które planujesz dodać do `VMList` parametru.

2. Uruchom **SequencedStartStop_Parent** element Runbook z **akcją** ustawioną na **początek**, Dodaj rozdzieloną przecinkami listę maszyn wirtualnych w polu parametr **VMList** , a następnie ustaw wartość **WHATIF** na true. Wyświetl podgląd zmian.

3. Skonfiguruj `External_ExcludeVMNames` zmienną z rozdzieloną przecinkami listą maszyn wirtualnych (VM1, VM2, VM3).

4. W tym scenariuszu nie są `External_Start_ResourceGroupNames` uwzględniane `External_Stop_ResourceGroupnames` zmienne i. W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać szczegółowe informacje, zobacz [Planowanie elementu Runbook w Azure Automation](../automation/automation-schedules.md).

5. Wyświetl podgląd akcji i wprowadź wszelkie niezbędne zmiany przed wdrożeniem na maszynach wirtualnych w środowisku produkcyjnym. Gdy wszystko będzie gotowe, ręcznie wykonaj polecenie **monitoring-and-Diagnostics/monitoring-Action-groupsrunbook** z parametrem ustawionym na **wartość false**. Alternatywnie możesz zezwolić na harmonogram `Sequenced-StartVM` automatyzacji i `Sequenced-StopVM` uruchamiać go automatycznie zgodnie z określonym harmonogramem.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scenariusz 3. automatyczne uruchamianie/zatrzymywanie na podstawie użycia procesora CPU

To rozwiązanie może ułatwić zarządzanie kosztami uruchamiania Azure Resource Manager i klasycznych maszyn wirtualnych w ramach subskrypcji przez ocenianie maszyn wirtualnych, które nie są używane w okresach poza szczytem, na przykład po godzinach, i ich automatyczne zamykanie, jeśli użycie procesora jest mniejsze niż określona wartość procentowa.

Domyślnie rozwiązanie jest wstępnie skonfigurowane, aby oszacować procentową metrykę procesora CPU, aby sprawdzić, czy średnie użycie nie jest równe 5 procent. Ten scenariusz jest kontrolowany przez następujące zmienne i można go zmodyfikować, jeśli wartości domyślne nie spełniają wymagań:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Możesz włączyć akcję i określić jej cel względem subskrypcji i grupy zasobów, lub wskazać określoną listę maszyn wirtualnych.

Po uruchomieniu **AutoStop_CreateAlert_Parent** elementu Runbook sprawdza, czy istnieje docelowa subskrypcja, grupy zasobów i maszyny wirtualne. Jeśli maszyny wirtualne istnieją, element Runbook następnie wywoła **AutoStop_CreateAlert_Child** element Runbook dla każdej zweryfikowanej maszyny wirtualnej za pomocą nadrzędnego elementu Runbook. Ten podrzędny element Runbook wykonuje następujące czynności:

* Tworzy regułę alertu metryki dla każdej zweryfikowanej maszyny wirtualnej.

* Wyzwala **AutoStop_VM_Child** elementu Runbook dla określonej maszyny wirtualnej, jeśli procesor spadnie poniżej skonfigurowanej wartości progowej przez określony interwał czasu. Następnie ten element Runbook podejmie próbę zatrzymania maszyny wirtualnej.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Aby wykonać akcję autozatrzymania dla wszystkich maszyn wirtualnych w subskrypcji

1. Upewnij się, `External_Stop_ResourceGroupNames` że zmienna jest pusta lub ma ustawioną wartość * (symbol wieloznaczny).

2. [Krok opcjonalny] Jeśli chcesz wykluczyć niektóre maszyny wirtualne z automatycznego zamykania, możesz dodać do `External_ExcludeVMNames` zmiennej listę nazw maszyn wirtualnych rozdzieloną przecinkami.

3. Włącz uruchamianie `Schedule_AutoStop_CreateAlert_Parent` harmonogramu, aby utworzyć wymagane reguły alertu METRYKI maszyny wirtualnej dla wszystkich maszyn wirtualnych w subskrypcji. Uruchomienie tego typu harmonogramu umożliwia tworzenie nowych reguł alertów dotyczących metryk w miarę dodawania nowych maszyn wirtualnych do subskrypcji.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Aby wykonać akcję autozatrzymania dla wszystkich maszyn wirtualnych w grupie zasobów lub w wielu grupach zasobów

1. Dodaj rozdzieloną przecinkami listę nazw grup zasobów do `External_Stop_ResourceGroupNames` zmiennej.

2. Jeśli chcesz wykluczyć niektóre maszyny wirtualne z automatycznego zamykania, możesz dodać do `External_ExcludeVMNames` zmiennej listę nazw maszyn wirtualnych rozdzielonych przecinkami.

3. Włącz uruchamianie harmonogramu **Schedule_AutoStop_CreateAlert_Parent** , aby utworzyć wymagane reguły alertu METRYKI maszyny wirtualnej dla wszystkich maszyn wirtualnych w grupach zasobów. Uruchomienie tej operacji zgodnie z harmonogramem umożliwia tworzenie nowych reguł alertów dotyczących metryk w miarę dodawania nowych maszyn wirtualnych do grup zasobów.

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>Aby przekierować akcję autozatrzymania do listy maszyn wirtualnych

1. Utwórz nowy [harmonogram](shared-resources/schedules.md#create-a-schedule) i podłącz go do **AutoStop_CreateAlert_Parent** elementu Runbook, dodając rozdzieloną PRZECINKAMI listę nazw maszyn wirtualnych do `VMList` parametru.

2. Opcjonalnie, jeśli chcesz wykluczyć niektóre maszyny wirtualne z automatycznego zamykania, możesz dodać do `External_ExcludeVMNames` zmiennej listę nazw maszyn wirtualnych rozdzieloną przecinkami.

## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

Aby zmienić powiadomienia e-mail po wdrożeniu rozwiązania, należy zmodyfikować grupę akcji utworzoną podczas wdrażania.  

> [!NOTE]
> Subskrypcje w chmurze Azure Government nie obsługują funkcji poczty e-mail tego rozwiązania.

1. W Azure Portal przejdź do pozycję **monitorowanie**, a następnie pozycję **grupy akcji**. Wybierz grupę akcji o nazwie **StartStop_VM_Notication**.

    ![Strona rozwiązania Update Management Automation](media/automation-solution-vm-management/azure-monitor.png)

2. Na stronie **StartStop_VM_Notification** kliknij pozycję **Edytuj szczegóły** w obszarze **szczegóły**. Spowoduje to otwarcie strony **wiadomości e-mail/SMS/wypychania/głosu** . Zaktualizuj adres e-mail, a następnie kliknij przycisk **OK** , aby zapisać zmiany.

    ![Strona rozwiązania Update Management Automation](media/automation-solution-vm-management/change-email.png)

    Alternatywnie możesz dodać dodatkowe akcje do grupy akcji, aby dowiedzieć się więcej na temat grup akcji, zobacz [grupy akcji](../azure-monitor/platform/action-groups.md)

Poniżej przedstawiono przykładową wiadomość e-mail, która jest wysyłana, gdy rozwiązanie zamknie maszyny wirtualne.

![Strona rozwiązania Update Management Automation](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Dodawanie/wykluczanie maszyn wirtualnych

Rozwiązanie zapewnia możliwość dodawania maszyn wirtualnych, których celem jest rozwiązanie, lub wykluczanie maszyn z rozwiązania.

### <a name="add-a-vm"></a>Dodawanie maszyny wirtualnej

Dostępne są dwie opcje, których można użyć, aby upewnić się, że maszyna wirtualna jest uwzględniona w rozwiązaniu uruchamiania/zatrzymywania, gdy zostanie uruchomione.

* Każdy z nadrzędnych [elementów Runbook](automation-solution-vm-management.md#runbooks) rozwiązania ma `VMList` parametr. Można przekazać rozdzieloną przecinkami listę nazw maszyn wirtualnych do tego parametru podczas planowania odpowiedniego nadrzędnego elementu Runbook dla danej sytuacji. te maszyny wirtualne zostaną uwzględnione podczas uruchamiania rozwiązania.

* Aby wybrać wiele maszyn wirtualnych, `External_Start_ResourceGroupNames` ustawić `External_Stop_ResourceGroupNames` i przy użyciu nazw grup zasobów zawierających maszyny wirtualne, które mają zostać uruchomione lub zatrzymane. Możesz również ustawić zmienne na wartość, `*` aby rozwiązanie było uruchamiane dla wszystkich grup zasobów w subskrypcji.

### <a name="exclude-a-vm"></a>Wykluczanie maszyny wirtualnej

Aby wykluczyć maszynę wirtualną z rozwiązania, możesz dodać ją do `External_ExcludeVMNames` zmiennej. Ta zmienna jest rozdzielaną przecinkami listą maszyn wirtualnych, które mają zostać wykluczone z rozwiązania uruchamiania/zatrzymywania. Ta lista jest ograniczona do 140 maszyn wirtualnych. Jeśli dodasz więcej niż 140 maszyn wirtualnych do tej listy rozdzielanej przecinkami, maszyny wirtualne, które są wykluczone, mogą zostać przypadkowo uruchomione lub zatrzymane.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modyfikowanie harmonogramów uruchamiania i zamykania

Zarządzanie harmonogramami uruchamiania i zamykania w tym rozwiązaniu odbywa się zgodnie z tymi samymi krokami, które opisano w temacie [Planowanie elementu Runbook w Azure Automation](automation-schedules.md). Musi istnieć osobny harmonogram do uruchamiania i zatrzymywania maszyn wirtualnych.

Obsługiwane jest tylko skonfigurowanie rozwiązania do zatrzymywania maszyn wirtualnych w określonym czasie. W tym scenariuszu po prostu utworzysz harmonogram **zatrzymywania** i nie ma odpowiedniego harmonogramu **uruchamiania** . W tym celu należy:

1. Upewnij się, że dodano grupy zasobów do maszyn wirtualnych, które mają zostać zamknięte `External_Stop_ResourceGroupNames` w zmiennej.

2. Utwórz własny harmonogram dla czasu, w którym chcesz zamknąć maszyny wirtualne.

3. Przejdź do elementu Runbook **ScheduledStartStop_Parent** i kliknij pozycję **harmonogram**. Umożliwia to wybranie harmonogramu utworzonego w poprzednim kroku.

4. Wybierz **Parametry i Uruchom ustawienia** , a następnie ustaw wartość pola **Akcja** na **Zatrzymaj**.

5. Wybierz przycisk **OK**, aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak rozwiązywać problemy z Start/Stop VMs during off-hours, zobacz temat [Rozwiązywanie problemów dotyczących uruchamiania/zatrzymywania maszyn](troubleshoot/start-stop-vm.md)

* [Przejrzyj](automation-solution-vm-management-logs.md) rekordy automatyzacji zapisane w dziennikach Azure monitor i przykładowe zapytania wyszukiwania w dzienniku, aby analizować stan zadań elementu Runbook usługi Automation z uruchamiania/zatrzymywania maszyn wirtualnych.
