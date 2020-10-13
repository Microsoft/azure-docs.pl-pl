---
title: Azure Cloud Services def. LoadBalancerProbe schemat | Microsoft Docs
description: Zdefiniowany przez klienta LoadBalancerProbe to sonda kondycji punktów końcowych w wystąpieniach roli. Łączy się z rolami sieci Web lub procesu roboczego w pliku definicji usługi.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 14
author: georgewallace
ms.author: tagore
ms.openlocfilehash: 6d0e84b6724d9df4162d4be3e06a9952087a53a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79537350"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Schemat LoadBalancerProbe definicji Cloud Services platformy Azure
Sonda modułu równoważenia obciążenia to zdefiniowana przez klienta sonda kondycji punktów końcowych UDP i punktów końcowych w wystąpieniach roli. `LoadBalancerProbe`Nie jest elementem autonomicznym; jest on połączony z rolą sieci Web lub procesu roboczego w pliku definicji usługi. `LoadBalancerProbe`Może być używany przez więcej niż jedną rolę.

Domyślnym rozszerzeniem dla pliku definicji usługi jest. csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Funkcja sondy modułu równoważenia obciążenia
Azure Load Balancer jest odpowiedzialna za kierowanie ruchu przychodzącego do wystąpień roli. Moduł równoważenia obciążenia określa, które wystąpienia mogą odbierać ruch przez regularne badanie każdego wystąpienia w celu określenia kondycji tego wystąpienia. Moduł równoważenia obciążenia sonduje każde wystąpienie wiele razy na minutę. Istnieją dwie różne opcje zapewniające kondycję wystąpienia modułu równoważenia obciążenia — domyślną sondę modułu równoważenia obciążenia lub sondę niestandardowego modułu równoważenia obciążenia, która jest implementowana przez zdefiniowanie LoadBalancerProbe w pliku csdef.

Sonda domyślnego modułu równoważenia obciążenia korzysta z agenta gościa w ramach maszyny wirtualnej, który nasłuchuje i reaguje na odpowiedź HTTP 200 OK tylko wtedy, gdy wystąpienie jest w stanie gotowe (na przykład gdy wystąpienie nie jest zajęte, odtwarzanie, zatrzymywanie itp.). Jeśli Agent gościa nie odpowie przy użyciu protokołu HTTP 200 OK, Azure Load Balancer oznacza wystąpienie jako nieodpowiadające i zatrzymuje wysyłanie ruchu do tego wystąpienia. Azure Load Balancer nadal wysyła polecenie ping do wystąpienia, a agent gościa odpowiada za pomocą protokołu HTTP 200, Azure Load Balancer wyśle ponownie ruch do tego wystąpienia. Gdy używana jest rola sieci Web, kod witryny sieci Web jest zwykle uruchamiany w w3wp.exe, które nie są monitorowane przez sieć szkieletową platformy Azure ani agenta gościa, co oznacza awarie w w3wp.exe (np. Odpowiedzi HTTP 500) nie są raportowane dla agenta gościa, a moduł równoważenia obciążenia nie wie, że to wystąpienie nie jest obracane.

Sonda niestandardowego modułu równoważenia obciążenia zastępuje domyślną sondę agenta gościa i pozwala utworzyć własną logikę niestandardową w celu określenia kondycji wystąpienia roli. Moduł równoważenia obciążenia regularnie sonduje punkt końcowy (co 15 sekund, domyślnie), a wystąpienie jest uznawane za rotacji, jeśli odpowiada za pomocą protokołu TCP ACK lub HTTP 200 w okresie limitu czasu (domyślnie 31 sekund). Może to być przydatne do implementowania własnej logiki w celu usunięcia wystąpień z rotacji modułu równoważenia obciążenia, na przykład zwracając stan inny niż 200, jeśli wystąpienie jest powyżej 90% procesora CPU. W przypadku ról sieci Web korzystających z w3wp.exe oznacza to również automatyczne monitorowanie witryny sieci Web, ponieważ błędy w kodzie witryny sieci Web zwracają stan inny niż 200 do sondy modułu równoważenia obciążenia. Jeśli nie zdefiniujesz LoadBalancerProbe w pliku. csdef, zostanie użyty domyślny sposób działania modułu równoważenia obciążenia (jak opisano wcześniej).

Jeśli używasz sondy modułu równoważenia obciążenia, musisz upewnić się, że logika bierze pod uwagę metodę RoleEnvironment. OnStop. W przypadku korzystania z sondy domyślnego modułu równoważenia obciążenia wystąpienie jest wychodzące z obrotu przed wywołaniem OnStop, ale sonda niestandardowego modułu równoważenia obciążenia może nadal zwracać 200 OK podczas zdarzenia OnStop. Jeśli używasz zdarzenia OnStop w celu oczyszczenia pamięci podręcznej, zatrzymania usługi lub w inny sposób wprowadzania zmian, które mogą wpływać na zachowanie usługi przez środowisko uruchomieniowe, musisz upewnić się, że logika sondy modułu równoważenia obciążenia usunie wystąpienie z obrotu.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Schemat definicji usługi podstawowej dla sondy modułu równoważenia obciążenia
 Poniżej przedstawiono podstawowy format pliku definicji usługi zawierającego sondę modułu równoważenia obciążenia.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementy schematu
`LoadBalancerProbes`Element pliku definicji usługi zawiera następujące elementy:

- [LoadBalancerProbes, element](#LoadBalancerProbes)
- [LoadBalancerProbe, element](#LoadBalancerProbe)

##  <a name="loadbalancerprobes-element"></a><a name="LoadBalancerProbes"></a> LoadBalancerProbes, element
`LoadBalancerProbes`Element opisuje zbieranie sond modułu równoważenia obciążenia. Ten element jest elementem nadrzędnym [elementu LoadBalancerProbe](#LoadBalancerProbe). 

##  <a name="loadbalancerprobe-element"></a><a name="LoadBalancerProbe"></a> LoadBalancerProbe, element
`LoadBalancerProbe`Element definiuje sondę kondycji dla modelu. Można zdefiniować wiele sond modułu równoważenia obciążenia. 

W poniższej tabeli opisano atrybuty `LoadBalancerProbe` elementu:

|Atrybut|Typ|Opis|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Wymagane. Nazwa sondy modułu równoważenia obciążenia. Nazwa musi być unikatowa.|
| `protocol`          | `string` | Wymagane. Określa protokół punktu końcowego. Możliwe wartości to `http` lub `tcp`. Jeśli `tcp` jest określony, odebrane potwierdzenie jest wymagane do pomyślnego sondowania. Jeśli `http` jest określony, wymagana jest odpowiedź 200 OK od określonego identyfikatora URI, aby sonda zakończyła się pomyślnie.|
| `path`              | `string` | Identyfikator URI używany do żądania stanu kondycji z maszyny wirtualnej. `path` jest wymagana `protocol` , jeśli jest ustawiona na `http` . W przeciwnym razie jest to niedozwolone.<br /><br /> Nie ma żadnej wartości domyślnej.|
| `port`              | `integer` | Opcjonalny. Port do komunikacji z sondą. Jest to opcjonalne dla każdego punktu końcowego, ponieważ ten sam port będzie używany do sondowania. Możesz również skonfigurować inny port dla ich sondowania. Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie.<br /><br /> Wartość domyślna jest ustawiana przez punkt końcowy.|
| `intervalInSeconds` | `integer` | Opcjonalny. Interwał (w sekundach), w którym częstotliwość sondowania punktu końcowego dla stanu kondycji. Zazwyczaj interwał jest nieco krótszy niż połowa przydzielonych przedziałów czasu (w sekundach), co umożliwia uzyskanie dwóch pełnych sond przed przeprowadzeniem obrotu.<br /><br /> Wartość domyślna to 15, a wartość minimalna to 5.|
| `timeoutInSeconds`  | `integer` | Opcjonalny. Przedział czasu (w sekundach) stosowany do sondy, w której nie zostanie zatrzymywany dalsze przesyłanie ruchu do punktu końcowego. Ta wartość umożliwia wypróbowanie punktów końcowych szybciej lub wolniej niż zwykle używane na platformie Azure (które są wartościami domyślnymi).<br /><br /> Wartość domyślna to 31, wartość minimalna to 11.|

## <a name="see-also"></a>Zobacz też
[Schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md)