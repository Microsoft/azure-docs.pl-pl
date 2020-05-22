---
title: Scheduled Events maszyn wirtualnych z systemem Windows na platformie Azure
description: Zaplanowane zdarzenia za pomocą usługi Azure Metadata Service dla maszyn wirtualnych z systemem Windows.
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: mimckitt
ms.openlocfilehash: c8b0d83be0ae464563a06c9307303ee7a5af527f
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779779"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure Metadata Service: Scheduled Events maszyn wirtualnych z systemem Windows

Scheduled Events to Metadata Service platformy Azure, który umożliwia aplikacji przygotowanie do konserwacji maszyn wirtualnych. Zawiera informacje o nadchodzących zdarzeniach konserwacyjnych (np. ponownym uruchomieniu), aby aplikacja mogła je przygotować i ograniczyć zakłócenia. Jest ona dostępna dla wszystkich typów maszyn wirtualnych platformy Azure, w tym PaaS i IaaS w systemach Windows i Linux. 

Aby uzyskać informacje na temat Scheduled Events w systemie Linux, zobacz [Scheduled Events dla maszyn wirtualnych z systemem Linux](../linux/scheduled-events.md).

> [!Note] 
> Scheduled Events jest ogólnie dostępna we wszystkich regionach świadczenia usługi Azure. Informacje o [dostępności wersji i regionu](#version-and-region-availability) znajdują się w temacie dotyczącym najnowszych informacji o wersji.

## <a name="why-scheduled-events"></a>Dlaczego Scheduled Events?

Wiele aplikacji może korzystać z czasu, aby przygotować się do konserwacji maszyn wirtualnych. Czas może służyć do wykonywania zadań specyficznych dla aplikacji, które zwiększają dostępność, niezawodność i możliwości obsługi, w tym: 

- Punkt kontrolny i przywracanie
- Opróżnianie połączeń
- Tryb failover repliki podstawowej 
- Usuwanie z puli modułu równoważenia obciążenia
- Rejestrowanie zdarzeń
- Bezpieczne zamykanie 

Korzystanie Scheduled Events aplikacji może wykryć, gdy nastąpi konserwacja, i wyzwala zadania, aby ograniczyć jego wpływ. Włączenie zaplanowanych zdarzeń powoduje, że maszyna wirtualna jest minimalnym czasem przed wykonaniem działania konserwacji. Aby uzyskać szczegółowe informacje, zobacz sekcję planowanie zdarzeń poniżej.

Scheduled Events udostępnia zdarzenia w następujących przypadkach użycia:
- [Konserwacja inicjowana przez platformę](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (na przykład ponowne uruchomienie maszyny wirtualnej, migracja na żywo lub zachowywanie pamięci na potrzeby hosta)
- Maszyna wirtualna jest uruchomiona na [obniżyć wydajności sprzętu hosta](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) , który przewiduje niepowodzenie wkrótce
- Konserwacja inicjowana przez użytkownika (np. użytkownik uruchamia ponownie lub wdraża maszynę wirtualną)
- Wykluczanie wystąpień [maszyn wirtualnych](spot-vms.md) i [zestawów skalowania](../../virtual-machine-scale-sets/use-spot.md)

## <a name="the-basics"></a>Podstawowe informacje  

Usługa Azure Metadata umożliwia ujawnianie informacji na temat uruchamiania Virtual Machines przy użyciu punktu końcowego REST dostępnego z poziomu maszyny wirtualnej. Informacje są dostępne za pośrednictwem adresu IP bez obsługi routingu, tak aby nie były widoczne poza maszyną wirtualną.

### <a name="endpoint-discovery"></a>Odnajdywanie punktów końcowych
W przypadku maszyn wirtualnych z obsługą sieci wirtualnej usługa metadanych jest dostępna ze statycznego adresu IP bez obsługi routingu `169.254.169.254` . Pełny punkt końcowy dla najnowszej wersji Scheduled Events to: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Jeśli maszyna wirtualna nie zostanie utworzona w ramach Virtual Network, domyślne przypadki usług Cloud Services i klasycznych maszyn wirtualnych są wymagane do odnajdywania adresu IP do użycia. Zapoznaj się z tym przykładem, aby dowiedzieć się [, jak odnaleźć punkt końcowy hosta](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Dostępność wersji i regionu
Usługa Scheduled Events jest w wersji. Wersje są obowiązkowe, a bieżąca wersja to `2019-01-01` .

| Wersja | Typ wydania | Regiony | Informacje o wersji | 
| - | - | - | - |
| 2019-01-01 | Ogólna dostępność | Wszystko | <li> Dodano obsługę funkcji EventType dla zestawów skalowania maszyn wirtualnych |
| 2017-11-01 | Ogólna dostępność | Wszystko | <li> Dodano obsługę elementu EventType punktu wykluczania maszyny wirtualnej<br> | 
| 2017-08-01 | Ogólna dostępność | Wszystko | <li> Usunięto poprzedzony znak podkreślenia z nazw zasobów dla maszyn wirtualnych IaaS<br><li>Wymagania nagłówka metadanych wymuszone dla wszystkich żądań | 
| 2017-03-01 | Wersja zapoznawcza | Wszystko |<li>Wersja początkowa |

> [!NOTE] 
> W poprzedniej wersji zapoznawczej obsługiwane są zaplanowane zdarzenia {Najnowsza} jako wersja interfejsu API. Ten format nie jest już obsługiwany i będzie przestarzały w przyszłości.

### <a name="enabling-and-disabling-scheduled-events"></a>Enabling and Disabling Scheduled Events (Włączanie i wyłączanie usługi Scheduled Events)
Scheduled Events jest włączona dla Twojej usługi podczas pierwszego żądania zdarzeń. Oczekiwana jest opóźniona odpowiedź w pierwszym wywołaniu przez maksymalnie dwie minuty. Należy okresowo wysyłać zapytania do punktu końcowego w celu wykrycia przyszłych zdarzeń konserwacji, jak również stanu wykonywanych działań konserwacyjnych.

Scheduled Events jest wyłączone dla usługi, jeśli nie zostanie wysłane żądanie przez 24 godziny.

### <a name="user-initiated-maintenance"></a>Konserwacja zainicjowana przez użytkownika
Użytkownik zainicjował konserwację maszyny wirtualnej za pośrednictwem Azure Portal, interfejsu API, wiersza polecenia lub programu PowerShell w zaplanowanym zdarzeniu. Pozwala to na przetestowanie logiki przygotowania konserwacji w aplikacji i umożliwia aplikacji przygotowanie się do konserwacji zainicjowanej przez użytkownika.

Ponowne uruchomienie maszyny wirtualnej powoduje zaplanowanie zdarzenia typu `Reboot` . Ponowne wdrożenie maszyny wirtualnej powoduje zaplanowanie zdarzenia typu `Redeploy` .

## <a name="using-the-api"></a>Korzystanie z interfejsu API

### <a name="headers"></a>Nagłówki
Podczas wykonywania zapytania dotyczącego Metadata Service należy podać nagłówek, `Metadata:true` Aby upewnić się, że żądanie nie zostało przypadkowo przekierowane. `Metadata:true`Nagłówek jest wymagany dla wszystkich żądań zaplanowanych zdarzeń. Niepowodzenie dołączenia nagłówka w żądaniu spowoduje nieprawidłową odpowiedź na żądanie z Metadata Service.

### <a name="query-for-events"></a>Zapytanie o zdarzenia
Możesz wykonać zapytanie o Scheduled Events po prostu, wykonując następujące wywołanie:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 -H @{"Metadata"="true"}
```

Odpowiedź zawiera tablicę zaplanowanych zdarzeń. Pusta tablica oznacza, że aktualnie nie ma żadnych zaplanowanych zdarzeń.
W przypadku zaplanowanych zdarzeń odpowiedź zawiera tablicę zdarzeń: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
DocumentIncarnation jest elementem ETag i zapewnia łatwy sposób sprawdzenia, czy ładunek zdarzeń został zmieniony od czasu ostatniego zapytania.

### <a name="event-properties"></a>Właściwości zdarzenia
|Właściwość  |  Opis |
| - | - |
| EventId | Unikatowy identyfikator globalny dla tego zdarzenia. <br><br> Przykład: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Typ zdarzenia | Ma to wpływ na przyczyny tego zdarzenia. <br><br> Wartości: <br><ul><li> `Freeze`: Zaplanowano wstrzymanie maszyny wirtualnej przez kilka sekund. Połączenie procesora i sieci może być zawieszone, ale nie ma wpływu na pamięć lub otwieranie plików. <li>`Reboot`: Zaplanowano ponowne uruchomienie maszyny wirtualnej (pamięć nietrwała zostanie utracona). <li>`Redeploy`: Zaplanowano przeniesienie maszyny wirtualnej do innego węzła (dyski tymczasowe są tracone). <li>`Preempt`: Trwa usuwanie miejsca na maszynie wirtualnej (dyski tymczasowe są tracone). <li> `Terminate`: Zaplanowano usunięcie maszyny wirtualnej. |
| ResourceType | Typ zasobu, którego dotyczy to zdarzenie. <br><br> Wartości: <ul><li>`VirtualMachine`|
| Resources| Lista zasobów, na które ma wpływ zdarzenie. Ma to na celu zapewnienie maszyn z co najwyżej jednej [domeny aktualizacji](manage-availability.md), ale może nie zawierać wszystkich maszyn w ud. <br><br> Przykład: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Stan zdarzenia | Stan tego zdarzenia. <br><br> Wartości: <ul><li>`Scheduled`: To zdarzenie jest zaplanowane do uruchomienia po upływie czasu określonego we `NotBefore` właściwości.<li>`Started`: To zdarzenie zostało uruchomione.</ul> Nie `Completed` jest dostępny żaden ani podobny stan; zdarzenie nie będzie już zwracane po zakończeniu zdarzenia.
| Nie wcześniej niż| Czas, po którym to zdarzenie może się zacząć. <br><br> Przykład: <br><ul><li> PN, 19 wrz 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Planowanie zdarzeń
Każde zdarzenie ma zaplanowaną minimalną ilość czasu w przyszłości w oparciu o typ zdarzenia. Ten czas jest uwzględniany we właściwości zdarzenia `NotBefore` . 

|Typ zdarzenia  | Minimalny komunikat |
| - | - |
| Funkcja| 15 minut |
| Ponowne uruchamianie | 15 minut |
| Ponowne wdrożenie | 10 minut |
| Stępują | 30 sekund |
| Terminate | [Użytkownik konfigurowalny](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications): od 5 do 15 minut |

> [!NOTE] 
> W niektórych przypadkach platforma Azure może przewidzieć awarię hosta z powodu nieprawidłowego sprzętu i podejmuje próbę ograniczenia zakłócenia usługi przez zaplanowanie migracji. Na maszynach wirtualnych, których to dotyczy, zostanie odebrane zaplanowane zdarzenie `NotBefore` , które jest zwykle kilka dni w przyszłości. Rzeczywisty czas zależy od przewidywanej oceny ryzyka niepowodzeń. Platforma Azure podejmuje próbę wydawania z wyprzedzeniem 7 dni, ale rzeczywisty czas jest różny i może być mniejszy, jeśli przewidywane jest, że nastąpi bezpośrednie awaria sprzętu. Aby zminimalizować ryzyko dla usługi na wypadek awarii sprzętu przed zainicjowaniem migracji systemu, zaleca się samodzielnego ponownego wdrożenia maszyny wirtualnej najszybciej, jak to możliwe.

### <a name="event-scope"></a>Zakres zdarzenia     
Zaplanowane zdarzenia są dostarczane do:
 - Autonomiczna Virtual Machines.
 - Wszystkie Virtual Machines w usłudze w chmurze.     
 - Wszystkie Virtual Machines w zestawie dostępności.     
 - Wszystkie Virtual Machines w grupie położenia zestawu skalowania (w tym partii).       

W związku z tym należy zaznaczyć `Resources` pole w zdarzeniu, aby ustalić, które maszyny wirtualne mają mieć wpływ. 

### <a name="starting-an-event"></a>Uruchamianie zdarzenia 

Po uzyskaniu nadchodzącego zdarzenia i zakończeniu logiki w celu bezpiecznego zamknięcia możesz zatwierdzić wydarzenie zaległe, wykonując `POST` wywołanie do usługi metadanych przy użyciu `EventId` . Wskazuje to na platformę Azure, że może skrócić minimalny czas powiadomienia (jeśli to możliwe). 

Poniżej znajduje się kod JSON oczekiwany w `POST` treści żądania. Żądanie powinno zawierać listę `StartRequests` . Każdy z nich `StartRequest` zawiera `EventId` dla zdarzenia, które chcesz przyspieszyć:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Potwierdzenie zdarzenia pozwala na wykonanie zdarzenia dla wszystkich zdarzeń `Resources` w zdarzeniu, a nie tylko do maszyny wirtualnej, która potwierdzi zdarzenie. W związku z tym możesz wybrać lidera, aby koordynował potwierdzenie, które może być proste jako pierwszy komputer w `Resources` polu.


## <a name="powershell-sample"></a>Przykładowy skrypt programu PowerShell 

Poniższy przykład wysyła zapytanie do usługi metadanych o zaplanowanych zdarzeniach i zatwierdza każde wydarzenie zaległe.

```powershell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $uri)
{
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2019-01-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Następne kroki 

- Obejrzyj [Scheduled Events demonstrację](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) w piątek na platformie Azure. 
- Zapoznaj się z przykładami kodu Scheduled Events w [metadanych wystąpienia platformy Azure Scheduled Events repozytorium GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Przeczytaj więcej na temat interfejsów API dostępnych w [usłudze metadanych wystąpienia](instance-metadata-service.md).
- Informacje o [planowanej konserwacji maszyn wirtualnych z systemem Windows na platformie Azure](planned-maintenance.md).
