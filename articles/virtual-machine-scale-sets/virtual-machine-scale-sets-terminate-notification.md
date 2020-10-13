---
title: Powiadomienie o przerwaniu dla wystąpień zestawu skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak włączyć powiadomienia o zakończeniu dla wystąpień zestawu skalowania maszyn wirtualnych platformy Azure
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: d4b31eb59ed0bae2afe408546ece66eacade9ddb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90603836"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Powiadomienie o przerwaniu dla wystąpień zestawu skalowania maszyn wirtualnych platformy Azure
Wystąpienia zestawu skalowania mogą zrezygnować z otrzymywania powiadomień o zakończeniu wystąpienia i ustawiać wstępnie zdefiniowany limit czasu opóźnienia dla operacji przerywania. Powiadomienie o wygaśnięciu jest wysyłane za pomocą usługi Azure Metadata Service — [Scheduled Events](../virtual-machines/windows/scheduled-events.md), która zapewnia powiadomienia i opóźniać wpływ na operacje, takie jak ponowny rozruch i ponowne wdrażanie. Rozwiązanie dodaje kolejne zdarzenie — Zakończ — do listy Scheduled Events, a skojarzone opóźnienie zdarzenia zakończenia będzie zależeć od limitu opóźnienia określonego przez użytkowników w konfiguracjach modelu zestawu skalowania.

Po zarejestrowaniu w funkcji wystąpienia zestawu skalowania nie muszą czekać, aż określony limit czasu wygaśnie, zanim wystąpienie zostanie usunięte. Po otrzymaniu powiadomienia o przerwaniu wystąpienie może zostać usunięte w dowolnym momencie przed upływem limitu czasu zakończenia.

## <a name="enable-terminate-notifications"></a>Włącz powiadomienia o zakończeniu
Istnieje wiele sposobów włączania powiadomień o zakończeniu dla wystąpień zestawu skalowania, jak opisano w poniższych przykładach.

### <a name="azure-portal"></a>Azure Portal

Poniższe kroki umożliwiają powiadomienie o przerwaniu podczas tworzenia nowego zestawu skalowania. 

1. Przejdź do **zestawu skalowania maszyn wirtualnych**.
1. Wybierz pozycję **+ Dodaj** , aby utworzyć nowy zestaw skalowania.
1. Przejdź do karty **Zarządzanie** . 
1. Znajdź sekcję **zakończenie wystąpienia** .
1. W przypadku **powiadomienia o zakończeniu wystąpienia**wybierz pozycję **włączone**.
1. W przypadku **opóźnienia zakończenia (w minutach)** Ustaw żądany domyślny limit czasu.
1. Po zakończeniu tworzenia nowego zestawu skalowania wybierz pozycję **Recenzja + Utwórz** . 

> [!NOTE]
> Nie można ustawić powiadomień o przerwaniu dla istniejących zestawów skalowania w Azure Portal

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład umożliwia powiadomienie o przerwaniu dla modelu zestawu skalowania.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

Powyższy blok Określa opóźnienie limitu czasu wynoszący 5 minut (wskazywane przez *PT5M*) dla każdej operacji przerywania dla wszystkich wystąpień w zestawie skalowania. Pole *notBeforeTimeout* może przyjmować dowolną wartość z przedziału od 5 do 15 minut w formacie ISO 8601. Można zmienić domyślny limit czasu dla operacji przerwania, modyfikując właściwość *notBeforeTimeout* w obszarze *terminateNotificationProfile* opisany powyżej.

Po włączeniu *scheduledEventsProfile* na modelu zestawu skalowania i ustawieniu *notBeforeTimeout*należy zaktualizować poszczególne wystąpienia do [najnowszego modelu](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , aby odzwierciedlić zmiany.

> [!NOTE]
>Powiadomienia o przerwaniu dla wystąpień zestawu skalowania można włączyć tylko przy użyciu interfejsu API w wersji 2019-03-01 lub nowszej.

### <a name="azure-powershell"></a>Azure PowerShell
Podczas tworzenia nowego zestawu skalowania można włączyć powiadomienia o zakończeniu dla zestawu skalowania za pomocą polecenia cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) .

Ten przykładowy skrypt przeprowadzi Cię przez proces tworzenia zestawu skalowania i skojarzonych zasobów przy użyciu pliku konfiguracji: [Utwórz kompletny zestaw skalowania maszyn wirtualnych](./scripts/powershell-sample-create-complete-scale-set.md). Możesz podać powiadomienie o zakończeniu konfigurowania, dodając parametry *TerminateScheduledEvents* i *TerminateScheduledEventNotBeforeTimeoutInMinutes* do obiektu konfiguracji w celu utworzenia zestawu skalowania. Poniższy przykład włącza funkcję z limitem czasu opóźnienia wynoszącym 10 minut.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Użyj polecenia cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) , aby włączyć powiadomienia o zakończeniu dla istniejącego zestawu skalowania.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
Powyższy przykład włącza powiadomienia o przerwaniu w istniejącym zestawie skalowania i ustawia dla zdarzenia przerwania 15-minutowy limit czasu.

Po włączeniu zaplanowanych zdarzeń w modelu zestawu skalowania i ustawieniu limitu czasu należy zaktualizować poszczególne wystąpienia do [najnowszego modelu](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , aby odzwierciedlić zmiany.

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Poniższy przykład dotyczy włączania powiadomień o zakończeniu tworzenia nowego zestawu skalowania.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

W przykładzie powyżej zostanie utworzona grupa zasobów, a następnie zostanie utworzony nowy zestaw skalowania z włączonymi powiadomieniami o przerwaniu dla 10-minutowego limitu czasu.

Poniższy przykład dotyczy włączania powiadomienia o zakończeniu w istniejącym zestawie skalowania.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Otrzymuj powiadomienia o zakończeniu

Powiadomienia o zakończeniu są dostarczane za pomocą [Scheduled Events](../virtual-machines/windows/scheduled-events.md), który jest Metadata Service platformy Azure. Usługa Azure Metadata umożliwia ujawnianie informacji na temat uruchamiania Virtual Machines przy użyciu punktu końcowego REST dostępnego z poziomu maszyny wirtualnej. Informacje są dostępne za pośrednictwem adresu IP bez obsługi routingu, aby nie były widoczne poza maszyną wirtualną.

Scheduled Events jest włączone dla zestawu skalowania przy pierwszym żądaniu dla zdarzeń. Możesz oczekiwać opóźnienia odpowiedzi podczas pierwszego wywołania przez maksymalnie dwie minuty. Należy okresowo wysyłać zapytania do punktu końcowego w celu wykrycia przyszłych zdarzeń konserwacji oraz stanu bieżących działań konserwacyjnych.

Scheduled Events jest wyłączone dla zestawu skalowania, jeśli wystąpienia zestawu skalowania nie zgłaszają żądania przez 24 godziny.

### <a name="endpoint-discovery"></a>Odnajdywanie punktów końcowych
W przypadku maszyn wirtualnych z obsługą sieci wirtualnej Metadata Service jest dostępna ze statycznego adresu IP bez obsługi routingu, 169.254.169.254.

Pełny punkt końcowy dla najnowszej wersji Scheduled Events to:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Odpowiedź na zapytanie
Odpowiedź zawiera tablicę zaplanowanych zdarzeń. Pusta tablica oznacza, że aktualnie nie ma żadnych zaplanowanych zdarzeń.

W przypadku zaplanowanych zdarzeń odpowiedź zawiera tablicę zdarzeń. W przypadku zdarzenia "Przerwij" odpowiedź będzie wyglądać następująco:
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
*DocumentIncarnation* jest elementem ETag i zapewnia łatwy sposób sprawdzenia, czy ładunek zdarzeń został zmieniony od czasu ostatniego zapytania.

Aby uzyskać więcej informacji na temat każdego z powyższych pól, zobacz dokumentację Scheduled Events dla [systemów Windows](../virtual-machines/windows/scheduled-events.md#event-properties) i [Linux](../virtual-machines/linux/scheduled-events.md#event-properties).

### <a name="respond-to-events"></a>Reagowanie na zdarzenia
Po uzyskaniu nadchodzącego zdarzenia i zakończeniu logiki w celu bezpiecznego zamknięcia możesz zatwierdzić zaległe zdarzenie, wykonując wywołanie do usługi metadanych przy użyciu EventId. Wywołanie POST wskazuje na platformę Azure, że może kontynuować usuwanie maszyny wirtualnej.

Poniżej znajduje się kod JSON oczekiwany w treści żądania POST. Żądanie powinno zawierać listę StartRequests. Każdy StartRequest zawiera EventId dla zdarzenia, które chcesz przyspieszyć:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Upewnij się, że każda maszyna wirtualna w zestawie skalowania zatwierdza tylko EventID odpowiednie dla tej maszyny wirtualnej. Maszyna wirtualna może uzyskać własną nazwę maszyny wirtualnej [za pomocą metadanych wystąpienia](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name). Ta nazwa przyjmuje postać "{Scale-Set-Name} _ {instance-ID}" i zostanie wyświetlona w sekcji "Resources" odpowiedzi zapytania opisanej powyżej.

Możesz również zapoznać się z przykładowymi skryptami służącymi do wykonywania zapytań i reagowania na zdarzenia w języku [Python](../virtual-machines/linux/scheduled-events.md#python-sample).

## <a name="tips-and-best-practices"></a>Wskazówki i najlepsze rozwiązania
-   Przerywaj powiadomienia tylko w operacjach "Delete" — wszystkie operacje usuwania (skalowanie ręczne lub automatyczne skalowanie zainicjowane) będą generować zdarzenia przerwania, jeśli zestaw skalowania ma włączone *scheduledEventsProfile* . Inne operacje, takie jak ponowne uruchamianie, odtwarzanie obrazu, ponowne wdrażanie i zatrzymywanie/cofanie alokacji nie generują zdarzeń zakończenia. Nie można włączyć powiadomień o przerwaniu dla maszyn wirtualnych o niskim priorytecie.
-   Brak obowiązkowego oczekiwania na przekroczenie limitu czasu — można uruchomić operację przerwania w dowolnym momencie po odebraniu zdarzenia i upływie czasu *NotBefore* zdarzenia.
-   Obowiązkowe usuwanie po upłynięciu limitu czasu — nie ma możliwości rozszerzenia wartości limitu czasu po wygenerowaniu zdarzenia. Po upływie limitu czasu zdarzenie oczekujące zakończenie zostanie przetworzone, a maszyna wirtualna zostanie usunięta.
-   Modyfikowalna wartość limitu czasu — można w dowolnym momencie zmodyfikować wartość limitu czasu przed usunięciem wystąpienia, modyfikując właściwość *notBeforeTimeout* w modelu zestawu skalowania i aktualizując wystąpienia maszyn wirtualnych do najnowszego modelu.
-   Zatwierdź wszystkie oczekujące usunięcia — Jeśli istnieje oczekujące usunięcie VM_1, które nie zostało zatwierdzone, i zatwierdzono inne zdarzenie przerwania w VM_2, VM_2 nie zostanie usunięte, dopóki nie zostanie zatwierdzone zdarzenie zakończenia dla VM_1 lub upłynął limit czasu. Po zatwierdzeniu zdarzenia zakończenia dla VM_1 zostaną usunięte zarówno VM_1, jak i VM_2.
-   Zatwierdź wszystkie jednoczesne operacje usuwania — rozszerzenie powyższego przykładu, jeśli VM_1 i VM_2 mają taki sam czas *NotBefore* , oba zdarzenia zakończenia muszą być zatwierdzone lub żadna maszyna wirtualna nie zostanie usunięta przed upływem limitu czasu.

## <a name="troubleshoot"></a>Rozwiązywanie problemów
### <a name="failure-to-enable-scheduledeventsprofile"></a>Nie można włączyć scheduledEventsProfile
Jeśli zostanie wyświetlony błąd "nieprawidłowego żądania" z komunikatem o błędzie z informacją o tym, że nie można odnaleźć elementu członkowskiego "scheduledEventsProfile" w obiekcie typu "VirtualMachineProfile" ", sprawdź wersję interfejsu API używaną dla operacji zestawu skalowania. Wymagany jest interfejs API obliczeń w wersji **2019-03-01** lub nowszej. 

### <a name="failure-to-get-terminate-events"></a>Nie można pobrać zdarzeń zakończenia
Jeśli nie otrzymujesz żadnych zdarzeń **przerwania** za pomocą Scheduled Events, sprawdź wersję interfejsu API używaną do pobierania zdarzeń. Do zakończenia zdarzeń jest wymagany interfejs API Metadata Service w wersji **2019-01-01** lub nowszej.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Trwa pobieranie zdarzenia przerwania z nieprawidłowym czasem NotBefore  
Po włączeniu *scheduledEventsProfile* na modelu zestawu skalowania i ustawieniu *notBeforeTimeout*należy zaktualizować poszczególne wystąpienia do [najnowszego modelu](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , aby odzwierciedlić zmiany.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) w zestawach skalowania maszyn wirtualnych.
