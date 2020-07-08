---
ms.topic: include
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 6a3837d01815306e469a684404ab76506f547f43
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77013656"
---
## <a name="business-disaster-recovery"></a>Odzyskiwanie po awarii firmy

W tej sekcji opisano funkcje Azure Time Series Insights, w których działają aplikacje i usługi, nawet jeśli wystąpi awaria (znana jako usługa *odzyskiwania po awarii firmy*).

### <a name="high-availability"></a>Wysoka dostępność

Jako usługa platformy Azure Time Series Insights zapewnia pewne funkcje *wysokiej dostępności* , używając nadmiarowości na poziomie regionu platformy Azure. Na przykład platforma Azure obsługuje funkcje odzyskiwania po awarii za pomocą funkcji *dostępności międzyregionowej* platformy Azure.

Dodatkowe funkcje wysokiej dostępności zapewniane przez platformę Azure (a także dostępne dla każdego wystąpienia Time Series Insights) obejmują:

- **Tryb failover**: platforma Azure zapewnia [replikację geograficzną i równoważenie obciążenia](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Przywracanie** i **odzyskiwanie**danych: platforma Azure oferuje [kilka opcji umożliwiających zachowanie i odzyskiwanie danych](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Azure Site Recovery**: platforma Azure udostępnia funkcje usługi Site Recovery za pomocą [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
- **Azure Backup**: [Azure Backup](https://docs.microsoft.com/azure/backup/backup-architecture) obsługuje kopie zapasowe maszyn wirtualnych platformy Azure zarówno lokalnie, jak i w chmurze.

Upewnij się, że zostały włączone odpowiednie funkcje platformy Azure w celu zapewnienia globalnej wysokiej dostępności dla urządzeń i użytkowników w różnych regionach.

> [!NOTE]
> Jeśli platforma Azure została skonfigurowana do włączania dostępności między regionami, w Azure Time Series Insights nie jest wymagana żadna dodatkowa konfiguracja dostępności między regionami.

### <a name="iot-and-event-hubs"></a>IoT i centra zdarzeń

Niektóre usługi Azure IoT obejmują również wbudowane funkcje odzyskiwania po awarii firmy:

- [Usługa Azure IoT Hub odzyskiwanie awaryjne o wysokiej dostępności](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), które obejmuje nadmiarowość wewnątrz regionu
- [Zasady usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Nadmiarowość usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Integracja Time Series Insights z innymi usługami zapewnia dodatkowe możliwości odzyskiwania po awarii. Na przykład dane telemetryczne wysyłane do centrum zdarzeń mogą zostać utrwalone w kopii zapasowej bazy danych usługi Azure Blob Storage.

### <a name="time-series-insights"></a>Time Series Insights

Istnieje kilka sposobów na zachowanie Time Series Insights danych, aplikacji i usług, nawet jeśli są one zakłócone. 

Można jednak określić, że wymagana jest kompletna kopia zapasowa środowiska Azure Time Series, w następujących celach:

- Jako *wystąpienie trybu failover* przeznaczone dla Time Series Insights do przekierowywania danych i ruchu do
- Aby zachować informacje o danych i inspekcji

Ogólnie rzecz biorąc, najlepszym sposobem duplikowania środowiska Time Series Insights jest utworzenie drugiego środowiska Time Series Insights w ramach kopii zapasowej regionu platformy Azure. Zdarzenia są również wysyłane do tego środowiska pomocniczego ze swojego podstawowego źródła zdarzeń. Upewnij się, że używasz drugiej dedykowanej grupy odbiorców. Postępuj zgodnie ze wskazówkami dotyczącymi odzyskiwania po awarii firmy, zgodnie z wcześniejszym opisem.

Aby utworzyć zduplikowane środowisko:

1. Utwórz środowisko w drugim regionie. Aby uzyskać więcej informacji, przeczytaj artykuł [Tworzenie nowego środowiska Time Series Insights w Azure Portal](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Utwórz drugą dedykowaną grupę odbiorców dla źródła zdarzeń.
1. Połącz to źródło zdarzeń z nowym środowiskiem. Upewnij się, że wyznaczysz drugą dedykowaną grupę odbiorców.
1. Zapoznaj się z dokumentacją [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) Time Series Insights i [Event Hubs](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access) .

Jeśli wystąpi zdarzenie:

1. Jeśli w trakcie zdarzenia awaryjnego wpłynie to na region podstawowy, Przekieruj operacje do środowiska Time Series Insights kopii zapasowej.
1. Użyj drugiego regionu, aby utworzyć kopię zapasową i odzyskać wszystkie Time Series Insights dane telemetryczne i zapytania.

> [!IMPORTANT]
> Jeśli nastąpi przejście w tryb failover:
> 
> * Może również wystąpić opóźnienie.
> * Może wystąpić chwilowe skoki przetwarzania komunikatów, ponieważ operacje są przekierowywane.
> 
> Aby uzyskać więcej informacji, przeczytaj temat [ograniczanie opóźnień w Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

