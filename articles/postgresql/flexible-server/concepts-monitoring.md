---
title: Monitorowanie i metryki — Azure Database for PostgreSQL — serwer elastyczny
description: W tym artykule opisano funkcje monitorowania i metryk w Azure Database for PostgreSQL serwerze elastycznym.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 3d26c585593161ccf4f8ec33f913cc7163531f89
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606661"
---
# <a name="monitor-metrics-on-azure-database-for-postgresql---flexible-server"></a>Monitorowanie metryk na serwerze elastycznym Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Monitorowanie danych dotyczących serwerów ułatwia rozwiązywanie problemów i optymalizację w obciążeniu. Azure Database for PostgreSQL oferuje różne opcje monitorowania w celu zapewnienia wglądu w zachowanie serwera programu.

## <a name="metrics"></a>Metryki
Azure Database for PostgreSQL oferuje różne metryki, które dają wgląd w zachowanie zasobów obsługujących serwer PostgreSQL. Każda Metryka jest emitowana z częstotliwością jednej minuty i ma do [93 dni historii](../../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics). Można skonfigurować alerty dotyczące metryk. Inne opcje obejmują Konfigurowanie zautomatyzowanych akcji, wykonywanie zaawansowanych analiz i archiwizowanie historii. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Metrics](../../azure-monitor/essentials/data-platform-metrics.md).

### <a name="list-of-metrics"></a>Lista metryk
Następujące metryki są dostępne dla PostgreSQL elastyczny serwer:


|Metric|Nazwa wyświetlana metryki|Jednostka|Opis|
|---|---|---|---|
| active_connections | Aktywne połączenia | Liczba | Liczba połączeń z serwerem. | 
| backup_storage_used | Używany magazyn kopii zapasowych | Bajty | Ilość używanych magazynów kopii zapasowych. Ta Metryka przedstawia sumę magazynu zużywanego przez wszystkie pełne kopie zapasowe bazy danych, różnicowe kopie zapasowe i kopie zapasowe dzienników przechowywane na podstawie okresu przechowywania kopii zapasowej ustawionego dla serwera. Częstotliwość wykonywania kopii zapasowych jest zarządzana przez usługę. W przypadku magazynu geograficznie nadmiarowego użycie magazynu kopii zapasowych jest dwa razy większe niż magazyn lokalnie nadmiarowy. |
| connections_failed | Połączenia zakończone niepowodzeniem | Liczba | Połączenia zakończone niepowodzeniem. |
| connections_succeeded | Połączenia zakończone powodzeniem | Liczba | Połączenia zakończone powodzeniem. |
| cpu_credits_consumed | Wykorzystane środki CPU | Liczba | Liczba kredytów używanych przez elastyczny serwer. Dotyczy warstwy możliwej do przetworzenia. |
| cpu_credits_remaining | Pozostałe kredyty procesora CPU | Liczba | Liczba kredytów dostępnych dla serii. Dotyczy warstwy możliwej do przetworzenia. |
| cpu_percent | Procent użycia procesora CPU | Procent | Procent użycia procesora CPU. | 
| disk_queue_depth | Głębokość kolejki dysku | Liczba | Liczba oczekujących operacji we/wy na dysk danych. |
| Wejścia | Liczba operacji we/wy na sekundę | Liczba | Liczba operacji we/wy na dysku na sekundę. |
| maximum_used_transactionIDs | Maksymalna liczba używanych identyfikatorów transakcji | Liczba | Maksymalny identyfikator transakcji jest używany. |
| memory_percent | Procent pamięci | Procent | Procent używanej pamięci. |
| network_bytes_egress | Sieć — wyjście | Bajty | Ilość wychodzącego ruchu sieciowego. |
| network_bytes_ingress | Sieć — wejście | Bajty | Ilość przychodzącego ruchu sieciowego. |
| read_iops | Odczyt operacji we/wy | Liczba | Liczba operacji odczytu we/wy dysku danych na sekundę. |
| read_throughput | Przepływność odczytu | Bajty | Bajty odczytane z dysku na sekundę. |
| storage_free | Wolne miejsce w magazynie | Bajty | Ilość dostępnego miejsca do magazynowania. |
| storage_percent | Procent magazynu | Procent | Procent użytego miejsca do magazynowania. Magazyn używany przez usługę może obejmować pliki bazy danych, dzienniki transakcji i Dzienniki serwera.|
| storage_used | Używany magazyn | Bajty | Procent użytego miejsca do magazynowania. Magazyn używany przez usługę może obejmować pliki bazy danych, dzienniki transakcji i Dzienniki serwera. |
| txlogs_storage_used | Używany magazyn dziennika transakcji | Bajty | Ilość miejsca do magazynowania używanego przez dzienniki transakcji. | 
| write_throughput | Przepływność zapisu | Bajty | Bajty zapisywane na sekundę na dysku. |
| write_iops | Zapisz operacje we/wy | Liczba | Liczba operacji zapisu we/wy dysku danych na sekundę. |

## <a name="server-logs"></a>Dzienniki serwera
Azure Database for PostgreSQL umożliwia konfigurowanie i dostęp do dzienników standardowych Postgres. Aby dowiedzieć się więcej o dziennikach, zapoznaj się z [dokumentem pojęcia dotyczące rejestrowania](concepts-logging.md).
