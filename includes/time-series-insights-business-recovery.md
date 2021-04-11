---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 04/01/2021
ms.openlocfilehash: 6529aa49d06e64947deb5ae54db0c39ad2575569
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106288625"
---
## <a name="business-disaster-recovery"></a>Odzyskiwanie po awarii firmy

W tej sekcji opisano funkcje Azure Time Series Insights, w których działają aplikacje i usługi, nawet jeśli wystąpi awaria (znana jako usługa *odzyskiwania po awarii firmy*).

### <a name="high-availability"></a>Wysoka dostępność

Jako usługa platformy Azure Azure Time Series Insights zapewnia pewne funkcje *wysokiej dostępności* , używając nadmiarowości na poziomie regionu platformy Azure. Na przykład platforma Azure obsługuje funkcje odzyskiwania po awarii za pomocą funkcji *dostępności międzyregionowej* platformy Azure.

Dodatkowe funkcje wysokiej dostępności zapewniane przez platformę Azure (a także dostępne dla każdego wystąpienia Azure Time Series Insights) obejmują:

- **Tryb failover**: platforma Azure zapewnia [replikację geograficzną i równoważenie obciążenia](/azure/architecture/resiliency/recovery-loss-azure-region).
- **Przywracanie** i **odzyskiwanie** danych: platforma Azure oferuje [kilka opcji umożliwiających zachowanie i odzyskiwanie danych](/azure/architecture/resiliency/recovery-data-corruption).
- **Azure Site Recovery**: platforma Azure oferuje funkcje odzyskiwania przez [Azure Site Recovery](../articles/site-recovery/index.yml).
- **Azure Backup**: [Azure Backup](../articles/backup/backup-architecture.md) obsługuje kopie zapasowe maszyn wirtualnych platformy Azure zarówno lokalnie, jak i w chmurze.

Upewnij się, że zostały włączone odpowiednie funkcje platformy Azure w celu zapewnienia globalnej wysokiej dostępności dla urządzeń i użytkowników w różnych regionach.

> [!NOTE]
> Jeśli platforma Azure została skonfigurowana do włączania dostępności między regionami, w Azure Time Series Insights nie jest wymagana żadna dodatkowa konfiguracja dostępności między regionami.

### <a name="iot-and-event-hubs"></a>IoT i centra zdarzeń

Niektóre usługi Azure IoT obejmują również wbudowane funkcje odzyskiwania po awarii firmy:

- [Usługa Azure IoT Hub odzyskiwanie awaryjne o wysokiej dostępności](../articles/iot-hub/iot-hub-ha-dr.md), które obejmuje nadmiarowość wewnątrz regionu
- [Zasady usługi Azure Event Hubs](../articles/event-hubs/event-hubs-geo-dr.md)
- [Nadmiarowość usługi Azure Storage](../articles/storage/common/storage-redundancy.md)

Integracja Azure Time Series Insights z innymi usługami zapewnia dodatkowe możliwości odzyskiwania po awarii. Na przykład dane telemetryczne wysyłane do centrum zdarzeń mogą zostać utrwalone w kopii zapasowej bazy danych usługi Azure Blob Storage.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

Istnieje kilka sposobów na zachowanie Azure Time Series Insights danych, aplikacji i usług, nawet jeśli są one zakłócone.

Można jednak określić, że wymagana jest kompletna kopia zapasowa środowiska Azure Time Series, w następujących celach:

- Jako *wystąpienie trybu failover* przeznaczone dla Azure Time Series Insights do przekierowywania danych i ruchu do
- Aby zachować informacje o danych i inspekcji

Ogólnie rzecz biorąc, najlepszym sposobem duplikowania środowiska Azure Time Series Insights jest utworzenie drugiego środowiska Azure Time Series Insights w ramach kopii zapasowej regionu platformy Azure. Zdarzenia są również wysyłane do tego środowiska pomocniczego ze swojego podstawowego źródła zdarzeń. Upewnij się, że używasz drugiej dedykowanej grupy odbiorców. Postępuj zgodnie ze wskazówkami dotyczącymi odzyskiwania po awarii firmy, zgodnie z wcześniejszym opisem.

Aby utworzyć zduplikowane środowisko:

1. Utwórz środowisko w drugim regionie. Aby uzyskać więcej informacji, przeczytaj artykuł [Tworzenie nowego środowiska Azure Time Series Insights w Azure Portal](../articles/time-series-insights/time-series-insights-get-started.md).
1. Utwórz drugą dedykowaną grupę odbiorców dla źródła zdarzeń.
1. Połącz to źródło zdarzeń z nowym środowiskiem. Upewnij się, że wyznaczysz drugą dedykowaną grupę odbiorców.
1. Zapoznaj się z dokumentacją [IoT Hub](../articles/time-series-insights/how-to-ingest-data-iot-hub.md) Azure Time Series Insights i [Event Hubs](../articles/time-series-insights/concepts-access-policies.md) .

Jeśli wystąpi zdarzenie:

1. Jeśli w trakcie zdarzenia awaryjnego wpłynie to na region podstawowy, Przekieruj operacje do środowiska Azure Time Series Insights kopii zapasowej.
1. Ponieważ numery sekwencji centrów są ponownie wykonywane od 0 po przejściu w tryb failover, należy odtworzyć Źródło zdarzenia w obu regionach/środowiskach z różnymi grupami odbiorców, aby uniknąć tworzenia takich elementów jak zduplikowane zdarzenia.
1. Użyj drugiego regionu, aby utworzyć kopię zapasową i odzyskać wszystkie Azure Time Series Insights dane telemetryczne i zapytania.

> [!IMPORTANT]
> Jeśli nastąpi przejście w tryb failover:
>
> - Może również wystąpić opóźnienie.
> - Może wystąpić chwilowe skoki przetwarzania komunikatów, ponieważ operacje są przekierowywane.
>
> Aby uzyskać więcej informacji, przeczytaj temat [ograniczanie opóźnień w Azure Time Series Insights](../articles/time-series-insights/time-series-insights-environment-mitigate-latency.md).