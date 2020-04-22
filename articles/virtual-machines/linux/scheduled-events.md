---
title: Zaplanowane zdarzenia dla maszyn wirtualnych z systemem Linux na platformie Azure
description: Planowanie zdarzeń przy użyciu usługi Azure Metadata Service dla maszyn wirtualnych systemu Linux.
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: mimckitt
ms.openlocfilehash: ee600d7524af27a0e9e2ce0176e7bd4d1f60bc3b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758562"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Usługa metadanych platformy Azure: zaplanowane zdarzenia dla maszyn wirtualnych z systemem Linux

Zaplanowane zdarzenia to usługa metadanych platformy Azure, która daje czas aplikacji na przygotowanie się do konserwacji maszyny wirtualnej. Zawiera informacje o nadchodzących zdarzeniach konserwacji (na przykład ponowne uruchomienie), dzięki czemu aplikacja może przygotować się do nich i ograniczyć zakłócenia. Jest dostępna dla wszystkich typów maszyn wirtualnych platformy Azure, w tym PaaS i IaaS w systemach Windows i Linux. 

Aby uzyskać informacje o zaplanowanych zdarzeniach w systemie Windows, zobacz [Zaplanowane zdarzenia dla maszyn wirtualnych systemu Windows](../windows/scheduled-events.md).

> [!Note] 
> Zaplanowane zdarzenia są ogólnie dostępne we wszystkich regionach platformy Azure. Aby uzyskać najnowsze informacje o wersji, zobacz [Dostępność wersji i regionu.](#version-and-region-availability)

## <a name="why-use-scheduled-events"></a>Dlaczego warto korzystać z zaplanowanych wydarzeń?

Wiele aplikacji może korzystać z czasu, aby przygotować się do konserwacji maszyny Wirtualnej. Czas może służyć do wykonywania zadań specyficznych dla aplikacji, które zwiększają dostępność, niezawodność i łatwość serwisowania, w tym: 

- Punkt kontrolny i przywracanie.
- Opróżnianie połączenia.
- Podstawowego trybu failover repliki.
- Usunięcie z puli modułu równoważenia obciążenia.
- Rejestrowanie zdarzeń.
- Wdzięczne zamknięcie.

W przypadku zaplanowanych zdarzeń aplikacja może odnajdywać, kiedy będzie płynąć konserwacja, i wyzwalać zadania, aby ograniczyć jej wpływ.  

Zaplanowane zdarzenia udostępnia zdarzenia w następujących przypadkach użycia:

- [Konserwacja inicjowana przez platformę](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (na przykład ponowne uruchomienie maszyny Wirtualnej, migracja na żywo lub przechowywanie aktualizacji pamięci dla hosta)
- Maszyna wirtualna jest uruchomiona na [zdegradowanym sprzęcie hosta,](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) który wkrótce zakończy się niepowodzeniem
- Konserwacja inicjowana przez użytkownika (na przykład użytkownik uruchamia ponownie lub ponownie wdroży maszynę wirtualną)
- Eksmisje wystąpienia w skali [punktowej](spot-vms.md) i [skali punktowej.](../../virtual-machine-scale-sets/use-spot.md)

## <a name="the-basics"></a>Podstawy  

  Usługa metadanych udostępnia informacje o uruchamianiu maszyn wirtualnych przy użyciu punktu końcowego REST, który jest dostępny z poziomu maszyny Wirtualnej. Informacje są dostępne za pośrednictwem nierozerwalnego adresu IP, dzięki czemu nie są udostępniane poza maszyną wirtualną.

### <a name="scope"></a>Zakres
Zaplanowane wydarzenia są dostarczane do:

- Autonomiczne maszyny wirtualne.
- Wszystkie maszyny wirtualne w usłudze w chmurze.
- Wszystkie maszyny wirtualne w zestawie dostępności.
- Wszystkie maszyny wirtualne w grupie umieszczania zestawu skalowania. 

W rezultacie sprawdź `Resources` pole w zdarzeniu, aby zidentyfikować, których maszyn wirtualnych dotyczy.

### <a name="endpoint-discovery"></a>Odnajdowanie punktu końcowego
W przypadku maszyn wirtualnych z włączoną siecią wirtualną usługa metadanych jest dostępna ze statycznego nierozerwalnego adresu IP. `169.254.169.254` Pełny punkt końcowy dla najnowszej wersji zaplanowanych zdarzeń jest: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Jeśli maszyna wirtualna nie jest tworzona w sieci wirtualnej, domyślne przypadki dla usług w chmurze i klasycznych maszyn wirtualnych, dodatkowa logika jest wymagana do odnajdywać adres IP do użycia. Aby dowiedzieć się, jak [odkryć punkt końcowy hosta,](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)zobacz ten przykład.

### <a name="version-and-region-availability"></a>Dostępność wersji i regionu
Usługa Zaplanowane zdarzenia jest wersjona. Wersje są obowiązkowe; aktualna wersja `2019-01-01`jest .

| Wersja | Typ wydania | Regiony | Informacje o wersji | 
| - | - | - | - | 
| 2019-01-01 | Ogólna dostępność | Wszystkie | <li> Dodano obsługę zestawów skalowania maszyny wirtualnej EventType "Terminate" |
| 2017-11-01 | Ogólna dostępność | Wszystkie | <li> Dodano obsługę spot vm eksmisji EventType "Preempt"<br> | 
| 2017-08-01 | Ogólna dostępność | Wszystkie | <li> Usunięto poprzedzane podkreślenie z nazw zasobów maszyn wirtualnych IaaS<br><li>Wymagania nagłówka metadanych wymuszane dla wszystkich żądań | 
| 2017-03-01 | Wersja zapoznawcza | Wszystkie | <li>Wersja początkowa |


> [!NOTE] 
> Poprzednie wersje w wersji zapoznawczej zaplanowanych zdarzeń obsługiwane {latest} jako wersja interfejsu api. Ten format nie jest już obsługiwany i zostanie przestarzały w przyszłości.

### <a name="enabling-and-disabling-scheduled-events"></a>Enabling and Disabling Scheduled Events (Włączanie i wyłączanie usługi Scheduled Events)
Zaplanowane zdarzenia są włączone dla usługi przy pierwszym zwładniu żądania zdarzeń. Należy się spodziewać opóźnionej odpowiedzi w pierwszym wywołaniu do dwóch minut.

Zaplanowane zdarzenia są wyłączone dla usługi, jeśli nie złóż żądania przez 24 godziny.

### <a name="user-initiated-maintenance"></a>Konserwacja inicjowana przez użytkownika
Inicjowana przez użytkownika konserwacja maszyn wirtualnych za pośrednictwem portalu Azure, interfejsu API, interfejsu wiersza polecenia lub programu PowerShell powoduje zaplanowane zdarzenie. Następnie można przetestować logikę przygotowania konserwacji w aplikacji, a aplikacja może przygotować się do konserwacji zainicjowanej przez użytkownika.

Po ponownym uruchomieniu maszyny Wirtualnej zaplanowane `Reboot` jest zdarzenie o typie. Jeśli ponownie wdrożyć maszynę wirtualną, zdarzenie `Redeploy` z typem jest zaplanowane.

## <a name="use-the-api"></a>Używanie interfejsu API

### <a name="headers"></a>Nagłówki
Podczas kwerendy usługi metadanych, `Metadata:true` należy podać nagłówek, aby upewnić się, że żądanie nie zostało przypadkowo przekierowane. Nagłówek `Metadata:true` jest wymagany dla wszystkich żądań zaplanowanych zdarzeń. Nieuwzłaczanie nagłówka w żądaniu powoduje odpowiedź "Nieprawidłowe żądanie" z usługi metadanych.

### <a name="query-for-events"></a>Kwerenda o zdarzenia
Można wyszukiwać zaplanowane zdarzenia, dokonując następującego połączenia:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

Odpowiedź zawiera tablicę zaplanowanych zdarzeń. Pusta tablica oznacza, że obecnie nie są planowane żadne zdarzenia.
W przypadku, gdy istnieją zaplanowane zdarzenia, odpowiedź zawiera tablicę zdarzeń. 
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

### <a name="event-properties"></a>Właściwości zdarzenia
|Właściwość  |  Opis |
| - | - |
| Eventid | Globalnie unikatowy identyfikator dla tego zdarzenia. <br><br> Przykład: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Typ zdarzenia | Wpływ powoduje to zdarzenie. <br><br> Wartości: <br><ul><li> `Freeze`: Maszyna wirtualna jest zaplanowana do wstrzymania na kilka sekund. Łączność procesora i sieci może zostać zawieszona, ale nie ma to wpływu na pamięć lub otwarte pliki.<li>`Reboot`: Maszyna wirtualna jest zaplanowana do ponownego uruchomienia (pamięć nietrwała zostanie utracona). <li>`Redeploy`: Maszyna wirtualna jest zaplanowane do przeniesienia do innego węzła (dyski efemeryczne są tracone). <li>`Preempt`: Maszyna wirtualna punktowa jest usuwana (dyski efemeryczne są tracone). <li> `Terminate`: Maszyna wirtualna jest zaplanowana do usunięcia. |
| ResourceType | Typ zasobu, którego dotyczy to zdarzenie. <br><br> Wartości: <ul><li>`VirtualMachine`|
| Resources| Lista zasobów, których dotyczy to zdarzenie. Lista jest gwarantowana zawierać maszyny z co najwyżej jednej [domeny aktualizacji,](manage-availability.md)ale może nie zawierać wszystkich maszyn w UD. <br><br> Przykład: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Stan tego zdarzenia. <br><br> Wartości: <ul><li>`Scheduled`: To zdarzenie ma się rozpocząć po `NotBefore` czasie określonym w właściwości.<li>`Started`: To wydarzenie zostało rozpoczęte.</ul> Nigdy `Completed` nie podano żadnego statusu podobnego. Zdarzenie nie jest już zwracane po zakończeniu zdarzenia.
| Nie wcześniej niż| Czas, po którym można rozpocząć to zdarzenie. <br><br> Przykład: <br><ul><li> pon,19 wrz 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Planowanie zdarzeń
Każde zdarzenie jest zaplanowane przez co najmniej czas w przyszłości na podstawie typu zdarzenia. Ten czas znajduje odzwierciedlenie w `NotBefore` właściwości zdarzenia. 

|Typ zdarzenia  | Powiadomienie minimalne |
| - | - |
| Zamrozić| 15 minut |
| Ponowne uruchamianie | 15 minut |
| Ponowne wdrożenie | 10 minut |
| Wywłaszczyć | 30 sekund |
| Terminate | [Możliwość konfiguracji użytkownika:](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)od 5 do 15 minut |

> [!NOTE] 
> W niektórych przypadkach platforma Azure jest w stanie przewidzieć awarię hosta z powodu awarii sprzętu i podejmie próbę złagodzenia zakłóceń w usłudze przez zaplanowanie migracji. Których dotyczy problem maszyny wirtualne otrzymają zaplanowane zdarzenie z `NotBefore` zwykle kilku dni w przyszłości. Rzeczywisty czas różni się w zależności od przewidywanej oceny ryzyka awarii. Platforma Azure próbuje powiadomić z 7-dniowym wyprzedzeniem, jeśli jest to możliwe, ale rzeczywisty czas jest różny i może być mniejszy, jeśli przewidywanie jest, że istnieje duże prawdopodobieństwo, że sprzęt nie działa w najbliższym czasie. Aby zminimalizować ryzyko związane z usługą w przypadku awarii sprzętu przed migracją zainicjowaną przez system, zaleca się jak najszybsze samoreneponowanie maszyny wirtualnej.

### <a name="start-an-event"></a>Rozpoczynanie wydarzenia 

Po zapoznaniu się z nadchodzącym wydarzeniem i zakończeniu logiki bezpiecznego zamykania, możesz zatwierdzić zdarzenie, wywołując `POST` usługę metadanych za pomocą pliku `EventId`. To wywołanie wskazuje platformie Azure, że może skrócić minimalny czas powiadamiania (jeśli to możliwe). 

Następujące próbki JSON oczekuje `POST` się w treści żądania. Żądanie powinno zawierać listę `StartRequests`. Każdy `StartRequest` `EventId` zawiera dla zdarzenia, które chcesz przyspieszyć:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash próbki
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Potwierdzanie zdarzenia umożliwia zdarzenie, aby `Resources` przejść dla wszystkich w przypadku, a nie tylko maszyny Wirtualnej, która potwierdza zdarzenie. W związku z tym można wybrać lidera do koordynowania potwierdzenia, które mogą być `Resources` tak proste, jak pierwsza maszyna w terenie.

## <a name="python-sample"></a>Przykład pythona 

Następujące przykładowe zapytania Usługi metadanych dla zaplanowanych zdarzeń i zatwierdza każde zdarzenie zaległe:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01"
this_host = socket.gethostname()


def get_scheduled_events():
    req = urllib2.Request(metadata_url)
    req.add_header('Metadata', 'true')
    resp = urllib2.urlopen(req)
    data = json.loads(resp.read())
    return data


def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ", "_")
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Następne kroki 
- Obejrzyj [zaplanowane wydarzenia w piątek platformy Azure,](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) aby zobaczyć demo. 
- Przejrzyj przykłady kodu zaplanowanych zdarzeń w [repozytorium GitHub zaplanowane zdarzenia](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)wystąpienia platformy Azure.
- Dowiedz się więcej o interfejsach API dostępnych w [usłudze metadanych wystąpienia](instance-metadata-service.md).
- Dowiedz się więcej o [planowanej konserwacji maszyn wirtualnych systemu Linux na platformie Azure](planned-maintenance.md).
