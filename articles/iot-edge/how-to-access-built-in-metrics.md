---
title: Uzyskiwanie dostępu do wbudowanych metryk — Azure IoT Edge
description: Dostęp zdalny do wbudowanych metryk ze składników środowiska uruchomieniowego IoT Edge
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 10/05/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 113c8adccc5e8b1c3321569f32ca3fb33423ccd8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562881"
---
# <a name="access-built-in-metrics"></a>Uzyskiwanie dostępu do wbudowanych metryk

Składniki środowiska uruchomieniowego IoT Edge, centrum IoT Edge i Agent IoT Edge tworzą wbudowane metryki w [formacie specyfikacji Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/). Uzyskuj dostęp do tych metryk zdalnie, aby monitorować i zrozumieć kondycję urządzenia IoT Edge.

W programie Release 1.0.10 metryki są automatycznie uwidaczniane domyślnie na **porcie 9600** modułów **edgeHub** i **edgeAgent** ( `http://edgeHub:9600/metrics` i `http://edgeAgent:9600/metrics` ). Nie są one domyślnie mapowane na hosta.

Uzyskaj dostęp do metryk z hosta, ujawniając i mapując port metryk z modułu `createOptions` . Poniższy przykład mapuje domyślny port metryk na port 9601 na hoście:

```
{
  "ExposedPorts": {
    "9600/tcp": {}
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

Wybierz różne i unikatowe numery portów hosta, jeśli mapujesz punkty końcowe metryk edgeHub i edgeAgent.

> [!NOTE]
> Zmienna środowiskowa `httpSettings__enabled` nie powinna być ustawiona na `false` potrzeby wbudowanych metryk, aby można było uzyskać dostęp do kolekcji.
>
> Zmienne środowiskowe, które mogą służyć do wyłączania metryk, są wymienione w [dokumencie Azure/iotedge Repos](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).

## <a name="available-metrics"></a>Dostępne metryki

Metryki zawierają Tagi ułatwiające zidentyfikowanie charakteru zbieranych metryk. Wszystkie metryki zawierają następujące Tagi:

| Tag | Opis |
|-|-|
| iothub | Centrum, z którym rozmawia urządzenie |
| edge_device | Identyfikator bieżącego urządzenia |
| instance_number | Identyfikator GUID reprezentujący bieżące środowisko uruchomieniowe. Po ponownym uruchomieniu wszystkie metryki zostaną zresetowane. Ten identyfikator GUID ułatwia uzgadnianie ponownych uruchomień. |

W formacie specyfikacji Prometheus są cztery podstawowe typy metryk: licznik, miernik, histogram i podsumowanie. Aby uzyskać więcej informacji o różnych typach metryk, zobacz [dokumentację dotyczącą typów metryk Prometheus](https://prometheus.io/docs/concepts/metric_types/).

Quantiles dla wbudowanego histogramu i metryk podsumowujących to 0,1, 0,5, 0,9 i 0,99.

Moduł **edgeHub** generuje następujące metryki:

| Nazwa | Wymiary | Opis |
|-|-|-|
| `edgehub_gettwin_total` | `source` (Źródło operacji)<br> `id` (identyfikator modułu) | Typ: licznik<br> Łączna liczba wywołań getprzędzy |
| `edgehub_messages_received_total` | `route_output` (dane wyjściowe wysyłane przez ten komunikat)<br> `id` | Typ: licznik<br> Całkowita liczba komunikatów odebranych od klientów |
| `edgehub_messages_sent_total` | `from` (źródło wiadomości)<br> `to` (miejsce docelowe komunikatu)<br>`from_route_output`<br> `to_route_input` (dane wejściowe docelowego komunikatu)<br> `priority` (priorytet wiadomości do lokalizacji docelowej) | Typ: licznik<br> Całkowita liczba komunikatów wysłanych do klientów lub nadrzędnych<br> `to_route_input` jest puste, gdy `to` jest $upstream |
| `edgehub_reported_properties_total` | `target`(docelowa aktualizacja)<br> `id` | Typ: licznik<br> Całkowita liczba zgłoszonych wywołań aktualizacji właściwości |
| `edgehub_message_size_bytes` | `id`<br> | Typ: Podsumowanie<br> Rozmiar komunikatu od klientów<br> Wartości mogą być raportowane tak, jakby `NaN` żadne nowe pomiary nie były raportowane przez określony czas (obecnie 10 minut); dla `summary` typu, odpowiadające `_count` i `_sum` liczniki będą emitowane. |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | Typ: Podsumowanie<br> Czas trwania operacji pobierania sznurka |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | Typ: Podsumowanie<br> Czas potrzebny na wysłanie wiadomości |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | Typ: Podsumowanie<br> Czas przetwarzania komunikatu z kolejki |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | Typ: Podsumowanie<br> Czas potrzebny do zaktualizowania raportowanych właściwości |
| `edgehub_direct_method_duration_seconds` | `from` Obiekt wywołujący<br> `to` nadajnik | Typ: Podsumowanie<br> Czas potrzebny na rozwiązanie komunikatu bezpośredniego |
| `edgehub_direct_methods_total` | `from`<br> `to` | Typ: licznik<br> Całkowita liczba wysłanych komunikatów bezpośrednich |
| `edgehub_queue_length` | `endpoint` (źródło wiadomości)<br> `priority` (priorytet kolejki) | Typ: miernik<br> Bieżąca długość kolejki edgeHub dla danego priorytetu |
| `edgehub_messages_dropped_total` | `reason` (no_route, ttl_expiry)<br> `from` <br> `from_route_output` | Typ: licznik<br> Całkowita liczba usuniętych komunikatów z powodu |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | Typ: licznik<br> Całkowita liczba niepotwierdzonych komunikatów z powodu niepowodzenia magazynu |
| `edgehub_offline_count_total` | `id` | Typ: licznik<br> Łączna liczba przypadków, gdy edgeHub przeszedł w tryb offline |
| `edgehub_offline_duration_seconds`| `id` | Typ: Podsumowanie<br> Centrum graniczne czasu w trybie offline |
| `edgehub_operation_retry_total` | `id`<br> `operation` (nazwa operacji) | Typ: licznik<br> Łączna liczba ponownych prób wykonania operacji edgeHub |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (nie uwierzytelniono)<br> | Typ: licznik<br> Łączna liczba przypadków, w których klienci nie mogą połączyć się z usługą edgeHub |

Moduł **edgeAgent** generuje następujące metryki:

| Nazwa | Wymiary | Opis |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | Typ: miernik<br> Czas, przez jaki moduł został określony we wdrożeniu i był w stanie uruchomienia |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | Typ: miernik<br> Czas, przez jaki moduł został określony we wdrożeniu |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | Typ: licznik<br> Liczba edgeAgent zaproszonych przez platformę Docker w celu uruchomienia modułu |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | Typ: licznik<br> Liczba przypadków, w których edgeAgent zażądał zatrzymywania modułu przez platformę Docker |
| `edgeAgent_command_latency_seconds` | `command` | Typ: miernik<br> Jak długo trwało platformę Docker w celu wykonania danego polecenia. Możliwe polecenia to: Tworzenie, aktualizowanie, usuwanie, uruchamianie, zatrzymywanie, ponowne uruchamianie |
| `edgeAgent_iothub_syncs_total` | | Typ: licznik<br> Liczba edgeAgent, które próbowały zsynchronizować swój przędzę z iotHub, zarówno pomyślnie, jak i niepowodzeniem. Ta liczba obejmuje obu agentów żądających dwuosiowej i centrum powiadamiania o aktualizacji z przędzą |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | Typ: licznik<br> Liczba edgeAgent, których nie udało się zsynchronizować z iotHub. |
| `edgeAgent_deployment_time_seconds` | | Typ: licznik<br> Ilość czasu, jaką zajęło wykonanie nowego wdrożenia po odebraniu zmiany. |
| `edgeagent_direct_method_invocations_count` | `method_name` | Typ: licznik<br> Liczba wywoływanych metod wbudowanych edgeAgent Direct, takich jak polecenie ping lub ponowne uruchomienie. |
| `edgeAgent_host_uptime_seconds` | | Typ: miernik<br> Jak długo Host został włączony |
| `edgeAgent_iotedged_uptime_seconds` | | Typ: miernik<br> Jak długo iotedged jest uruchomiony |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | Typ: miernik<br> Ilość miejsca pozostawionego na dysku |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| Typ: miernik<br> Rozmiar dysku |
| `edgeAgent_used_memory_bytes` | `module_name` | Typ: miernik<br> Ilość pamięci RAM używanej przez wszystkie procesy |
| `edgeAgent_total_memory_bytes` | `module_name` | Typ: miernik<br> Dostępna pamięć RAM |
| `edgeAgent_used_cpu_percent` | `module_name` | Typ: histogram<br> Procent użycia procesora CPU przez wszystkie procesy |
| `edgeAgent_created_pids_total` | `module_name` | Typ: miernik<br> Liczba procesów lub wątków utworzonych przez kontener |
| `edgeAgent_total_network_in_bytes` | `module_name` | Typ: miernik<br> Liczba bajtów odebranych z sieci |
| `edgeAgent_total_network_out_bytes` | `module_name` | Typ: miernik<br> Liczba bajtów wysłanych do sieci |
| `edgeAgent_total_disk_read_bytes` | `module_name` | Typ: miernik<br> Liczba bajtów odczytanych z dysku |
| `edgeAgent_total_disk_write_bytes` | `module_name` | Typ: miernik<br> Liczba bajtów zapisywana na dysku |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | Typ: miernik<br> Ogólne metadane dotyczące urządzenia. Wartość jest zawsze równa 0. informacje są kodowane w tagach. Pamiętaj `experimental_features` i `host_information` są obiektami JSON. `host_information` Wygląda na to ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` . Uwaga `ServerVersion` to wersja platformy Docker i `Version` jest to IoT Edge wersja demona zabezpieczeń. |

## <a name="next-steps"></a>Następne kroki

* [Poznaj środowisko uruchomieniowe Azure IoT Edge i jego architekturę](iot-edge-runtime.md)
* [Właściwości agenta IoT Edge i modułu IoT Edge Hub bliźniaczych reprezentacji](module-edgeagent-edgehub.md)
