---
title: Ograniczenia przepływności pozyskiwania danych przesyłanych strumieniowo — Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się więcej o limitach przepływności danych przychodzących w Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 4d1d071a36531ed5f159543e33e9ac043160cd70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650769"
---
# <a name="streaming-ingestion-throughput-limits"></a>Limity przepływności pozyskiwania strumieniowego

Poniżej opisano ograniczenia dotyczące transferu danych przychodzących w Azure Time Series Insights Gen2 Streaming.

> [!TIP]
> Przeczytaj artykuł [Planowanie środowiska Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) , aby uzyskać pełną listę wszystkich limitów.

## <a name="per-environment-limitations"></a>Ograniczenia dotyczące środowiska

Ogólnie rzecz biorąc, stawki za transfer danych przychodzących są wyświetlane jako współczynnik liczby urządzeń w organizacji, częstotliwości emisji zdarzeń i rozmiaru każdego zdarzenia:

* **Liczba urządzeń** × **częstotliwość emisji zdarzeń** × **rozmiar każdego zdarzenia**.

Domyślnie Azure Time Series Insights Gen2 może pozyskać dane przychodzące z szybkością **do 1 megabajtów na sekundę (MB/s) na Azure Time Series Insights środowisku Gen2**. Istnieją dodatkowe ograniczenia [na partycję centrum](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * Obsługa środowiska w celu pozyskiwania prędkości do 8 MB/s może być świadczona przez żądanie.
> * Skontaktuj się z nami, jeśli potrzebujesz większej przepływności przez przesłanie biletu pomocy technicznej za pomocą Azure Portal.

* **Przykład 1:**

    Wysyłka firmy Contoso obejmuje 100 000 urządzeń, które emitują wydarzenie trzy razy na minutę. Rozmiar zdarzenia to 200 bajtów. Używają IoT Hub z czterema partycjami jako źródłem zdarzeń Azure Time Series Insights Gen2.

  * Szybkość pozyskiwania dla środowiska Azure Time Series Insights Gen2: **100 000 urządzeń * 200 bajtów/zdarzenia * (3/60 Event/s) = 1 MB/s**.
    * Przy założeniu, że zrównoważona partycja, szybkość pozyskiwania na partycję 0,25 MB/s.
    * Stawka pozyskiwania kosztów wysyłki firmy Contoso będzie w ramach ograniczeń skalowania.

* **Przykład 2:**

    Analiza floty firmy Contoso obejmuje 40 000 urządzeń, które emitują zdarzenia co sekundę. Korzystają one z centrum zdarzeń z liczbą partycji 2 jako źródłem zdarzenia Azure Time Series Insights Gen2. Rozmiar zdarzenia to 200 bajtów.

  * Szybkość pozyskiwania środowiska: **40 000 urządzeń * 200 bajtów/zdarzenia * 1 zdarzenie/s = 8 MB/s**.
    * Przy założeniu, że zrównoważona partycja, ich stawka na partycje wynosi 4 MB/s.
    * Wskaźnik pozyskiwania danych z usługi contoso floty jest powyżej limitów środowiska i partycji. Mogą przesłać żądanie Azure Time Series Insights Gen2 przez Azure Portal, aby zwiększyć szybkość pozyskiwania dla środowiska i utworzyć centrum zdarzeń z większą liczbą partycji, które mają być objęte limitami.

## <a name="hub-partitions-and-per-partition-limits"></a>Partycje centrum i limity na partycję

Podczas planowania środowiska Azure Time Series Insights Gen2 należy wziąć pod uwagę konfigurację źródeł zdarzeń, z którymi będziesz się łączyć Azure Time Series Insights Gen2. Zarówno IoT Hub platformy Azure, jak i Event Hubs używają partycji, aby włączyć skalowanie w poziomie na potrzeby przetwarzania zdarzeń.

*Partycja* to uporządkowana sekwencja zdarzeń przechowywanych w centrum. Liczba partycji jest ustawiana podczas fazy tworzenia centrum i nie można jej zmienić.

Aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi partycjonowania Event Hubs, sprawdź, [ile partycji jest potrzebnych?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> Większość centrów IoT używanych z programem Azure Time Series Insights Gen2 potrzebuje tylko czterech partycji.

Bez względu na to, czy tworzysz nowe centrum dla środowiska Azure Time Series Insights Gen2, czy korzystasz z istniejącego, musisz obliczyć współczynnik pozyskiwania partycji, aby określić, czy jest on w ramach limitów.

Azure Time Series Insights Gen2 ma obecnie limit na **partycję 0,5 MB/s**.

### <a name="iot-hub-specific-considerations"></a>Zagadnienia dotyczące IoT Hub

Gdy urządzenie zostanie utworzone w IoT Hub, zostaje trwale przypisane do partycji. W takim przypadku IoT Hub jest w stanie zagwarantować porządkowanie zdarzeń (ponieważ nigdy nie zmieni się przypisanie).

Stałe przypisanie partycji ma także wpływ na Azure Time Series Insights wystąpienia Gen2, które pobierają dane wysyłane z IoT Hub podrzędnych. Gdy komunikaty z wielu urządzeń są przekazywane do koncentratora przy użyciu tego samego identyfikatora urządzenia bramy, mogą się one pojawić w tej samej partycji, co może spowodować przekroczenie limitów skalowania partycji.

**Wpływ**:

* Jeśli jedna partycja osiągnęła stałą częstotliwość pozyskiwania w ramach limitu, możliwe jest, że Azure Time Series Insights Gen2 nie zsynchronizuje wszystkich danych telemetrycznych urządzenia przed przekroczeniem okresu IoT Hub. W związku z tym wysyłane dane mogą zostać utracone w przypadku stałego przekroczenia limitów pozyskiwania.

Aby wyeliminować to okoliczności, zalecamy stosowanie następujących najlepszych rozwiązań:

* Przed wdrożeniem rozwiązania Oblicz swoje licencje na środowisko i przejęcie na partycje.
* Upewnij się, że urządzenia IoT Hub są zrównoważone obciążenia w możliwie najszerszym zakresie.

> [!IMPORTANT]
> W przypadku środowisk, w których IoT Hub jako źródło zdarzeń, Oblicz wskaźnik pozyskiwania przy użyciu liczby używanych urządzeń Hub, aby upewnić się, że stawka spadnie poniżej limitu 0,5 MB/s na partycję.
>
> * Nawet wtedy, gdy kilka zdarzeń dociera jednocześnie, limit nie zostanie przekroczony.

  ![Diagram partycji IoT Hub](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Zapoznaj się z poniższymi zasobami, aby dowiedzieć się więcej na temat optymalizowania przepływności i partycji centrum:

* [Skalowanie IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Skala centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partycje centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

## <a name="next-steps"></a>Następne kroki

* Przeczytaj o [magazynie](./concepts-storage.md) danych
