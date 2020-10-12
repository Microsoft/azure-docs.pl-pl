---
title: Konfigurowanie Start/Stop VMs during off-hours Azure Automation
description: W tym artykule opisano sposób konfigurowania funkcji Start/Stop VMs during off-hours w celu obsługi różnych przypadków użycia lub scenariuszy.
services: automation
ms.subservice: process-automation
ms.date: 06/01/2020
ms.topic: conceptual
ms.openlocfilehash: b0bc23d515bebdd0d943bbad33c5ebba35a35605
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987215"
---
# <a name="configure-startstop-vms-during-off-hours"></a>Konfigurowanie Start/Stop VMs during off-hours

W tym artykule opisano sposób konfigurowania funkcji [Start/Stop VMS during off-hours](automation-solution-vm-management.md) do obsługi opisanych scenariuszy. Możesz również dowiedzieć się, jak:

* [Konfigurowanie powiadomień e-mail](#configure-email-notifications)
* [Dodawanie maszyny wirtualnej](#add-a-vm)
* [Wykluczanie maszyny wirtualnej](#exclude-a-vm)
* [Modyfikowanie harmonogramów uruchamiania i zamykania](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scenariusz 1. Uruchamianie/zatrzymywanie maszyn wirtualnych zgodnie z harmonogramem

Ten scenariusz jest domyślną konfiguracją podczas pierwszego wdrażania Start/Stop VMs during off-hours. Na przykład możesz skonfigurować funkcję, aby zatrzymać wszystkie maszyny wirtualne w ramach subskrypcji, gdy opuszczasz pracę w wieczorie i zaczniesz je w rano, gdy jesteś z powrotem w biurze. Podczas konfigurowania harmonogramów **zaplanowane — StartVM** i **zaplanowany-StopVM** podczas wdrażania, uruchamiają i zatrzymują maszyny wirtualne. 

Konfigurowanie funkcji tylko do zatrzymania maszyn wirtualnych jest obsługiwane. Zobacz [Modyfikowanie harmonogramów uruchamiania i zamykania](#modify-the-startup-and-shutdown-schedules) , aby dowiedzieć się, jak skonfigurować harmonogram niestandardowy.

> [!NOTE]
> Strefa czasowa używana przez funkcję jest bieżącą strefą czasową podczas konfigurowania parametru czasu planowania. Jednak Azure Automation zapisuje je w formacie UTC w Azure Automation. Nie trzeba wykonywać żadnej konwersji strefy czasowej, ponieważ jest ona obsługiwana podczas wdrażania maszynowego.

Aby kontrolować maszyny wirtualne, które znajdują się w zakresie, skonfiguruj zmienne: `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` i `External_ExcludeVMNames` .

Można włączyć opcję określania wartości docelowej dla subskrypcji i grupy zasobów lub przeznaczenie określonej listy maszyn wirtualnych, ale nie obu.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Kierowanie akcji uruchamiania i zatrzymywania względem subskrypcji i grupy zasobów

1. Skonfiguruj `External_Stop_ResourceGroupNames` zmienne i, `External_ExcludeVMNames` Aby określić docelowe maszyny wirtualne.

2. Włącz i zaktualizuj harmonogramy **zaplanowane-StartVM** i **zaplanowane StopVM** .

3. Uruchom **ScheduledStartStop_Parent** element Runbook z polem parametru **akcji** ustawionym na wartość **Start** , a wartością pola parametr **WHATIF** ustawioną na true, aby wyświetlić podgląd zmian.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Kierowanie akcji Rozpocznij i Zatrzymaj według maszyny wirtualnej

1. Uruchom **ScheduledStartStop_Parent** element Runbook z **akcją** ustawioną na **początek**.

2. Dodaj rozdzieloną przecinkami listę maszyn wirtualnych (bez spacji) w polu parametr **VMList** . Przykładową listą jest `vm1,vm2,vm3` .

3. Dla pola parametru **WHATIF** ustaw wartość true.

4. Skonfiguruj `External_ExcludeVMNames` zmienną z rozdzieloną przecinkami listą maszyn wirtualnych (VM1, VM2, VM3), bez spacji między wartościami rozdzielanymi przecinkami.

5. W tym scenariuszu nie są `External_Start_ResourceGroupNames` uwzględniane `External_Stop_ResourceGroupnames` zmienne i. W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać szczegółowe informacje, zobacz [Planowanie elementu Runbook w Azure Automation](shared-resources/schedules.md).

    > [!NOTE]
    > Wartość **docelowej nazwy obiektu** do odniesień jest przechowywana jako wartości dla obu `External_Start_ResourceGroupNames` i `External_Stop_ResourceGroupNames` . Aby uzyskać więcej stopnia szczegółowości, można zmodyfikować każdą z tych zmiennych dla różnych grup zasobów. Dla akcji Rozpocznij akcję, użyj `External_Start_ResourceGroupNames` , i Użyj `External_Stop_ResourceGroupNames` do zatrzymania działania. Maszyny wirtualne są automatycznie dodawane do harmonogramów uruchamiania i zatrzymywania.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scenariusz 2. Uruchamianie/zatrzymywanie maszyn wirtualnych w sekwencji przy użyciu tagów

W środowisku zawierającym co najmniej dwa składniki na wielu maszynach wirtualnych obsługujących obciążenie rozproszone, obsługa sekwencji, w której składniki zostały uruchomione i zatrzymane, jest ważna. 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Kierowanie akcji uruchamiania i zatrzymywania względem subskrypcji i grupy zasobów

1. Dodaj `sequencestart` `sequencestop` tag i z dodatnimi wartościami całkowitymi do maszyn wirtualnych, które są przeznaczone dla `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` zmiennych i. Akcje uruchamiania i zatrzymywania są wykonywane w kolejności rosnącej. Aby dowiedzieć się, jak oznaczyć maszynę wirtualną, zobacz [znakowanie maszyny wirtualnej z systemem Windows na platformie Azure](../virtual-machines/windows/tag.md) i oznaczanie [maszyny wirtualnej z systemem Linux na platformie Azure](../virtual-machines/linux/tag.md).

2. Zmodyfikuj harmonogramy **Sequenced-StartVM** i **Sequenced-StopVM** do daty i godziny, które spełniają Twoje wymagania, i Włącz harmonogram.

3. Uruchom **SequencedStartStop_Parent** element Runbook z **akcją** z ustawioną opcją **Start** i **WHATIF** o wartości true, aby wyświetlić podgląd zmian.

4. Wyświetl podgląd akcji i wprowadź wszelkie niezbędne zmiany przed wdrożeniem na maszynach wirtualnych w środowisku produkcyjnym. Gdy wszystko będzie gotowe, ręcznie wykonaj element Runbook z parametrem ustawionym na **wartość false**lub pozwól, aby harmonogramy usługi Automation **StartVM** i **Sequenced-StopVM** działały automatycznie zgodnie z określonym harmonogramem.

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>Kierowanie akcji uruchamiania i zatrzymywania według maszyny wirtualnej

1. Dodaj `sequencestart` `sequencestop` tag i z dodatnimi wartościami całkowitymi do maszyn wirtualnych, które zamierzasz dodać do `VMList` parametru.

2. Uruchom **SequencedStartStop_Parent** element Runbook z **akcją** ustawioną na **początek**.

3. Dodaj rozdzieloną przecinkami listę maszyn wirtualnych (bez spacji) w polu parametr **VMList** . Przykładową listą jest `vm1,vm2,vm3` .

4. Dla opcji **WHATIF** ustaw wartość true. 

5. Skonfiguruj `External_ExcludeVMNames` zmienną z rozdzieloną przecinkami listą maszyn wirtualnych, bez spacji między wartościami rozdzielonymi przecinkami.

6. W tym scenariuszu nie są `External_Start_ResourceGroupNames` uwzględniane `External_Stop_ResourceGroupnames` zmienne i. W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać szczegółowe informacje, zobacz [Planowanie elementu Runbook w Azure Automation](shared-resources/schedules.md).

7. Wyświetl podgląd akcji i wprowadź wszelkie niezbędne zmiany przed wdrożeniem na maszynach wirtualnych w środowisku produkcyjnym. Gdy wszystko będzie gotowe, ręcznie wykonaj polecenie **monitoring-and-Diagnostics/monitoring-Action-groupsrunbook** z parametrem ustawionym na **wartość false**. Alternatywnie, można zezwolić usłudze Automation Schedules **Sequenced-StartVM** i **Sequenced-StopVM** działać automatycznie zgodnie z określonym harmonogramem.

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scenariusz 3. Uruchamianie lub zatrzymywanie automatyczne na podstawie użycia procesora CPU

Start/Stop VMs during off-hours może pomóc w zarządzaniu kosztami uruchamiania Azure Resource Manager i klasycznych maszyn wirtualnych w ramach subskrypcji przez ocenę maszyn, które nie są używane w okresach poza szczytem, na przykład po godzinach, i ich automatyczne wyłączenie, jeśli użycie procesora jest mniejsze niż określona wartość procentowa.

Domyślnie funkcja jest wstępnie skonfigurowana do obliczania wartości procentowej metryki procesora, aby sprawdzić, czy średnie użycie nie jest równe 5 procent. Ten scenariusz jest kontrolowany przez następujące zmienne i można go zmodyfikować, jeśli wartości domyślne nie spełniają wymagań:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Możesz włączyć akcję i określić jej cel względem subskrypcji i grupy zasobów, lub wskazać określoną listę maszyn wirtualnych.

Po uruchomieniu **AutoStop_CreateAlert_Parent** elementu Runbook sprawdza, czy istnieje docelowa subskrypcja, grupy zasobów i maszyny wirtualne. Jeśli maszyny wirtualne istnieją, element Runbook wywoła **AutoStop_CreateAlert_Child** element Runbook dla każdej maszyny wirtualnej zweryfikowanej przez nadrzędny element Runbook. Ten podrzędny element Runbook:

* Tworzy regułę alertu metryki dla każdej zweryfikowanej maszyny wirtualnej.
* Wyzwala **AutoStop_VM_Child** elementu Runbook dla określonej maszyny wirtualnej, jeśli procesor spadnie poniżej skonfigurowanej wartości progowej przez określony interwał czasu. 
* Próbuje zatrzymać maszynę wirtualną.

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>Przekieruj akcję autozatrzymania do wszystkich maszyn wirtualnych w ramach subskrypcji

1. Upewnij się, że `External_Stop_ResourceGroupNames` zmienna jest pusta lub ma ustawioną wartość * (symbol wieloznaczny).

2. Obowiązkowe Jeśli chcesz wykluczyć niektóre maszyny wirtualne z akcji autozatrzymaj, możesz dodać do zmiennej listę nazw maszyn wirtualnych rozdzielonych przecinkami `External_ExcludeVMNames` .

3. Włącz uruchamianie harmonogramu **Schedule_AutoStop_CreateAlert_Parent** , aby utworzyć wymagane reguły alertu METRYKI maszyny wirtualnej dla wszystkich maszyn wirtualnych w ramach subskrypcji. Uruchomienie tego typu harmonogramu umożliwia tworzenie nowych reguł alertów dotyczących metryk w miarę dodawania nowych maszyn wirtualnych do subskrypcji.

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Wybierz akcję autozatrzymania dla wszystkich maszyn wirtualnych w grupie zasobów lub wielu grup zasobów

1. Dodaj rozdzieloną przecinkami listę nazw grup zasobów do `External_Stop_ResourceGroupNames` zmiennej.

2. Jeśli chcesz wykluczyć niektóre z maszyn wirtualnych z poziomu zatrzymywania, możesz dodać do zmiennej listę nazw maszyn wirtualnych rozdzieloną przecinkami `External_ExcludeVMNames` .

3. Włącz uruchamianie harmonogramu **Schedule_AutoStop_CreateAlert_Parent** , aby utworzyć wymagane reguły alertu METRYKI maszyny wirtualnej dla wszystkich maszyn wirtualnych w grupach zasobów. Uruchomienie tej operacji zgodnie z harmonogramem umożliwia tworzenie nowych reguł alertów dotyczących metryk w miarę dodawania nowych maszyn wirtualnych do grup zasobów.

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>Przekieruj akcję autozatrzymania do listy maszyn wirtualnych

1. Utwórz nowy [harmonogram](shared-resources/schedules.md#create-a-schedule) i podłącz go do **AutoStop_CreateAlert_Parent** elementu Runbook, dodając rozdzieloną PRZECINKAMI listę nazw maszyn wirtualnych do `VMList` parametru.

2. Opcjonalnie, jeśli chcesz wykluczyć niektóre maszyny wirtualne z akcji autozatrzymaj, możesz dodać rozdzieloną przecinkami listę nazw maszyn wirtualnych (bez spacji) do `External_ExcludeVMNames` zmiennej.

## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

Aby zmienić powiadomienia e-mail po wdrożeniu Start/Stop VMs during off-hours, można zmodyfikować grupę akcji utworzoną podczas wdrażania.  

> [!NOTE]
> Subskrypcje w chmurze Azure Government nie obsługują funkcji poczty e-mail tej funkcji.

1. W Azure Portal przejdź do pozycję **monitorowanie**, a następnie pozycję **grupy akcji**. Wybierz grupę akcji o nazwie **StartStop_VM_Notication**.

    :::image type="content" source="media/automation-solution-vm-management/azure-monitor.png" alt-text="Zrzut ekranu strony monitorowanie — grupy akcji.":::

2. Na stronie StartStop_VM_Notification kliknij pozycję **Edytuj szczegóły** w obszarze **szczegóły**. Spowoduje to otwarcie strony wiadomości E-mail/SMS/wypychania/głosu. Zaktualizuj adres e-mail, a następnie kliknij przycisk **OK** , aby zapisać zmiany.

    :::image type="content" source="media/automation-solution-vm-management/change-email.png" alt-text="Zrzut ekranu strony monitorowanie — grupy akcji.":::

    Alternatywnie możesz dodać dodatkowe akcje do grupy akcji, aby dowiedzieć się więcej na temat grup akcji, zobacz [grupy akcji](../azure-monitor/platform/action-groups.md)

Poniżej znajduje się Przykładowa wiadomość e-mail, która jest wysyłana, gdy ta funkcja zamyka maszyny wirtualne.

:::image type="content" source="media/automation-solution-vm-management/email.png" alt-text="Zrzut ekranu strony monitorowanie — grupy akcji.":::

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>Dodawanie lub wykluczanie maszyn wirtualnych

Ta funkcja pozwala dodawać maszyny wirtualne do dołączenia lub wykluczenia. 

### <a name="add-a-vm"></a>Dodawanie maszyny wirtualnej

Istnieją dwa sposoby upewnienia się, że maszyna wirtualna jest dołączona podczas uruchamiania funkcji:

* Każdy z nadrzędnych [elementów Runbook](automation-solution-vm-management.md#runbooks) funkcji ma `VMList` parametr. Można przekazać rozdzieloną przecinkami listę nazw maszyn wirtualnych (bez spacji) do tego parametru podczas planowania odpowiedniego nadrzędnego elementu Runbook dla danej sytuacji, a te maszyny wirtualne zostaną uwzględnione podczas uruchamiania funkcji.

* Aby wybrać wiele maszyn wirtualnych, ustawić `External_Start_ResourceGroupNames` i `External_Stop_ResourceGroupNames` przy użyciu nazw grup zasobów zawierających maszyny wirtualne, które mają zostać uruchomione lub zatrzymane. Możesz również ustawić zmienne na wartość, `*` Aby funkcja była uruchamiana dla wszystkich grup zasobów w subskrypcji.

### <a name="exclude-a-vm"></a>Wykluczanie maszyny wirtualnej

Aby wykluczyć maszynę wirtualną z zatrzymywania/uruchamiania maszyn wirtualnych poza godzinami pracy, możesz dodać jej nazwę do `External_ExcludeVMNames` zmiennej. Ta zmienna jest rozdzielaną przecinkami listą określonych maszyn wirtualnych (bez spacji) do wykluczenia z funkcji. Ta lista jest ograniczona do 140 maszyn wirtualnych. Jeśli dodasz więcej niż 140 maszyn wirtualnych do tej listy, maszyny wirtualne, które zostaną wykluczone, mogą zostać przypadkowo uruchomione lub zatrzymane.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modyfikowanie harmonogramów uruchamiania i zamykania

Zarządzanie harmonogramami uruchamiania i zamykania w tej funkcji odbywa się zgodnie z tymi samymi krokami, które opisano w temacie [Planowanie elementu Runbook w Azure Automation](shared-resources/schedules.md). Do uruchamiania i zatrzymywania maszyn wirtualnych wymagane są osobne harmonogramy.

Konfigurowanie funkcji do tylko zatrzymywania maszyn wirtualnych w określonym czasie jest obsługiwane. W tym scenariuszu po prostu utworzysz harmonogram zatrzymywania i nie ma odpowiedniego harmonogramu uruchamiania. 

1. Upewnij się, że dodano grupy zasobów do maszyn wirtualnych, które mają zostać zamknięte w `External_Stop_ResourceGroupNames` zmiennej.

2. Utwórz własny harmonogram dla czasu, w którym chcesz zamknąć maszyny wirtualne.

3. Przejdź do elementu Runbook **ScheduledStartStop_Parent** i kliknij pozycję **harmonogram**. Umożliwia to wybranie harmonogramu utworzonego w poprzednim kroku.

4. Wybierz **Parametry i Uruchom ustawienia** , a następnie ustaw wartość pola **Akcja** na **Zatrzymaj**.

5. Wybierz przycisk **OK**, aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki

* Aby monitorować funkcję podczas operacji, zobacz [dzienniki zapytań z Start/Stop VMS during off-hours](automation-solution-vm-management-logs.md).
* Aby obsłużyć problemy podczas zarządzania maszyną wirtualną, zobacz [Rozwiązywanie problemów z Start/Stop VMS during off-hours](troubleshoot/start-stop-vm.md).
