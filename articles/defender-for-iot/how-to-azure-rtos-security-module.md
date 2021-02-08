---
title: Konfigurowanie i dostosowywanie modułu zabezpieczeń dla usługi Azure RTO
description: Dowiedz się więcej o konfigurowaniu i dostosowywaniu modułu zabezpieczeń dla usługi Azure RTO.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: fb2b7810c0829859f4a104c62b6df2ca0495bac7
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809205"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Konfigurowanie i dostosowywanie modułu zabezpieczeń dla usługi Azure RTO (wersja zapoznawcza)

Użyj poniższego pliku, aby skonfigurować zachowanie urządzenia.

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/Inc/asc_port. h

 Domyślne zachowanie każdej konfiguracji podano w następujących tabelach: 

### <a name="general"></a>Ogólne

| Nazwa | Typ | Domyślne | Szczegóły |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Ciąg | --- | Unikatowy identyfikator urządzenia  |
| ASC_SECURITY_MODULE_PENDING_TIME  | Liczba | 300 | Czas oczekiwania modułu zabezpieczeń (w sekundach). Jeśli czas przekroczy zmianę stanu na wstrzymanie. |

#### <a name="collection"></a>Kolekcja

| Nazwa | Typ | Domyślne | Szczegóły |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | Liczba | 10 | Interwał grupy modułów zbierających o wysokim priorytecie (w sekundach). |
| ASC_MEDIUM_PRIORITY_INTERVAL | Liczba | 30 | Zakres modułów zbierających średni priorytet grupy (w sekundach). |
| ASC_LOW_PRIORITY_INTERVAL | Liczba | 145 440  | Interwał grupy modułów zbierających niski priorytet (w sekundach). |

#### <a name="collector-network-activity"></a>Aktywność sieci modułu zbierającego

Aby dostosować konfigurację działania sieci modułu zbierającego, użyj następujących elementów:

| Nazwa | Typ | Domyślne | Szczegóły |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean | fałsz | Filtrowanie `TCP` aktywności sieciowej |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean | fałsz | Filtruj `UDP` zdarzenia aktywności sieciowej |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean | fałsz | Filtruj `ICMP` zdarzenia aktywności sieciowej |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Wartość logiczna | true | Przechwyć tylko pakiety przychodzące emisji pojedynczej, gdy ustawione na FAŁSZ przechwytywanie również emitują i multiemisję |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Liczba | 64 | Maksymalna liczba zdarzeń sieciowych IPv4 do przechowywania w pamięci |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Liczba | 64  | Maksymalna liczba zdarzeń sieciowych IPv6 do przechowywania w pamięci |


## <a name="compile-flags"></a>Kompiluj flagi
Flagi kompilacji umożliwiają przesłonięcie wstępnie zdefiniowanych konfiguracji.

### <a name="collectors"></a>Moduły zbierające
| Nazwa | Typ | Domyślne | Szczegóły |
| - | - | - | - |
| collector_heartbeat_enabled | Wartość logiczna | ON | Włączanie modułu zbierającego pulsu |
| collector_network_activity_enabled | Wartość logiczna | ON | Włączanie modułu zbierającego działania sieciowe |
| collector_system_information_enabled | Wartość logiczna | ON | Włączanie modułu zbierającego informacje o systemie |

## <a name="supported-security-alerts-and-recommendations"></a>Obsługiwane alerty i zalecenia dotyczące zabezpieczeń

Moduł zabezpieczeń usługi Azure RTO obsługuje określone alerty zabezpieczeń i zalecenia. Pamiętaj, aby [przejrzeć i dostosować odpowiednie wartości alertu i rekomendacji](concept-rtos-security-alerts-recommendations.md) dla usługi.

## <a name="log-analytics-optional"></a>Log Analytics (opcjonalnie)

Chociaż opcjonalne i niewymagane, Włączanie i Konfigurowanie Log Analytics może być przydatne, gdy chcesz dokładniej zbadać zdarzenia i działania urządzeń. Dowiedz się więcej na temat sposobu instalowania i używania [log Analytics z usługą Defender for IoT](how-to-security-data-access.md#log-analytics) , aby dowiedzieć się więcej. 

## <a name="next-steps"></a>Następne kroki

- Przejrzyj i Dostosuj moduł zabezpieczeń dla [alertów zabezpieczeń i zaleceń](concept-rtos-security-alerts-recommendations.md) usługi Azure RTO
- W razie potrzeby zapoznaj się z [modułem zabezpieczeń dla interfejsu API usługi Azure RTO](azure-rtos-security-module-api.md) .

