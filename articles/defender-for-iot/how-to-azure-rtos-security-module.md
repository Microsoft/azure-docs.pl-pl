---
title: Konfigurowanie i dostosowywanie usługi Defender-IoT-Micro-Agent for Azure RTO
description: Dowiedz się więcej o konfigurowaniu i dostosowywaniu usługi Defender-IoT-Micro-Agent dla usługi Azure RTO.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2021
ms.author: shhazam
ms.openlocfilehash: 874a783763882a28f2fe7078e3a264d09107808a
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495100"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos-preview"></a>Konfigurowanie i dostosowywanie usługi Defender-IoT-Micro-Agent for Azure RTO (wersja zapoznawcza)

W tym artykule opisano sposób konfigurowania usługi Defender-IoT-Micro-Agent dla urządzenia z systemem Azure RTO w celu spełnienia wymagań dotyczących sieci, przepustowości i pamięci.

Musisz wybrać docelowy plik dystrybucji `*.dist` z rozszerzeniem z `netxduo/addons/azure_iot/azure_iot_security_module/configs` katalogu.  

W przypadku korzystania ze środowiska kompilacji CMake należy ustawić parametr wiersza polecenia na `IOT_SECURITY_MODULE_DIST_TARGET` dla wybranej wartości. Na przykład `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`.

W IAR lub innym środowisku kompilacji innym niż CMake, należy dodać `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` ścieżkę do wszystkich znanych ścieżek uwzględnionych. Na przykład `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`.

Użyj poniższego pliku, aby skonfigurować zachowanie urządzenia.

**netxduo/Dodatki/azure_iot/azure_iot_security_module/Inc/configs/ \<target distribution> /asc_config. h**

W środowisku kompilacji CMake należy zmienić konfigurację domyślną, edytując `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` plik. Użyj następującego formatu CMake `set(ASC_XXX ON)` lub poniższego pliku `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` dla wszystkich innych środowisk. Na przykład `#define ASC_XXX`.

Domyślne zachowanie każdej konfiguracji podano w następujących tabelach: 

## <a name="general"></a>Ogólne

| Nazwa | Typ | Domyślne | Szczegóły |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Ciąg | Defender-IoT-Micro-Agent | Unikatowy identyfikator urządzenia.  |
| SECURITY_MODULE_VERSION_ (GŁÓWNY) (POMOCNICZY) (POPRAWKA)  | Liczba | 3.2.1 | Wersja. |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Liczba  | 3 | Czas, przez jaki usługa Defender-IoT-Micro-agent będzie wysyłać komunikat zabezpieczeń po awarii. (w sekundach) |
| ASC_SECURITY_MODULE_PENDING_TIME  | Liczba | 300 | Czas oczekiwania na usługę Defender-IoT-Micro-Agent (w sekundach). Jeśli czas zostanie przekroczony, stan zmieni się na wstrzymanie. |

## <a name="collection"></a>Kolekcja

| Nazwa | Typ | Domyślne | Szczegóły |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | Liczba  | 30  | Przesunięcie interwału kolekcji początkowej modułu zbierającego. Podczas uruchamiania wartość zostanie dodana do kolekcji systemu w celu uniknięcia jednoczesnego wysyłania komunikatów z wielu urządzeń.  |
| ASC_HIGH_PRIORITY_INTERVAL | Liczba | 10 | Interwał grupy wysokiego priorytetu modułu zbierającego (w sekundach). |
| ASC_MEDIUM_PRIORITY_INTERVAL | Liczba | 30 | Interwał grupy średnich priorytetów modułu zbierającego (w sekundach). |
| ASC_LOW_PRIORITY_INTERVAL | Liczba | 145 440  | Interwał grupy o niskim priorytecie modułu zbierającego (w sekundach). |

#### <a name="collector-network-activity"></a>Aktywność sieci modułu zbierającego

Aby dostosować konfigurację działania sieci modułu zbierającego, użyj następujących elementów:

| Nazwa | Typ | Domyślne | Szczegóły |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean | fałsz | Filtruje `TCP` aktywność sieci. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean | fałsz | Filtruje `UDP` zdarzenia działania sieci. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean | fałsz | Filtruje `ICMP` zdarzenia działania sieci. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Wartość logiczna | true | Przechwytuje tylko pakiety przychodzące emisji pojedynczej. Ustawienie wartości false spowoduje również przechwycenie emisji i multiemisji. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Boolean  | fałsz  | Wysyła puste zdarzenia modułu zbierającego. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Liczba | 64 | Maksymalna liczba zdarzeń sieciowych IPv4 do przechowywania w pamięci. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Liczba | 64  | Maksymalna liczba zdarzeń sieciowych IPv6 do przechowywania w pamięci. |

### <a name="collectors"></a>Moduły zbierające
| Nazwa | Typ | Domyślne | Szczegóły |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Wartość logiczna | ON | Włącza moduł zbierający puls. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Wartość logiczna | ON | Włącza moduł zbierający działania sieciowe. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Wartość logiczna | ON | Włącza moduł zbierający informacje o systemie.  |

Inne flagi konfiguracji są zaawansowane i mają Nieobsługiwane funkcje. Skontaktuj się z pomocą techniczną, aby to zmienić, lub aby uzyskać więcej informacji.
 
## <a name="supported-security-alerts-and-recommendations"></a>Obsługiwane alerty i zalecenia dotyczące zabezpieczeń

Defender-IoT-Micro-Agent for Azure RTO obsługuje określone alerty zabezpieczeń i zalecenia. Pamiętaj, aby [przejrzeć i dostosować odpowiednie wartości alertu i rekomendacji](concept-rtos-security-alerts-recommendations.md) dla usługi.

## <a name="log-analytics-optional"></a>Log Analytics (opcjonalnie)

Log Analytics można włączać i konfigurować w celu zbadania zdarzeń i działań związanych z urządzeniami. Dowiedz się więcej na temat sposobu instalacji i używania [log Analytics z usługą Defender for IoT](how-to-security-data-access.md#log-analytics) , aby dowiedzieć się więcej. 

## <a name="next-steps"></a>Następne kroki


- Przejrzyj i Dostosuj usługę Defender-IoT-Micro-Agent na potrzeby [alertów zabezpieczeń i zaleceń](concept-rtos-security-alerts-recommendations.md) usługi Azure RTO
- W razie potrzeby zapoznaj się z [usługą Defender-IoT-Micro-Agent dla interfejsu API RTO](azure-rtos-security-module-api.md) .
