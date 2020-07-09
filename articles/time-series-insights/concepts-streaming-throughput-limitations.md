---
title: Ograniczenia przepływności pozyskiwania danych przesyłanych strumieniowo — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się więcej o limitach przepływności danych przychodzących w Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: baa132589f5242ab480c9caa55ca793e0652ad6e
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135011"
---
# <a name="streaming-ingestion-throughput-limits"></a>Limity przepływności pozyskiwania strumieniowego

Poniżej opisano następujące ograniczenia dotyczące transferu danych przychodzących Azure Time Series Insights przesyłania strumieniowego.

> [!TIP]
> Przeczytaj artykuł [Planowanie środowiska w wersji zapoznawczej](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) , aby uzyskać pełną listę wszystkich limitów wersji zapoznawczych.

## <a name="per-environment-limitations"></a>Ograniczenia dotyczące środowiska

Ogólnie rzecz biorąc, stawki za transfer danych przychodzących są wyświetlane jako współczynnik liczby urządzeń w organizacji, częstotliwości emisji zdarzeń i rozmiaru każdego zdarzenia:

*  **Liczba urządzeń** × **częstotliwość emisji zdarzeń** × **rozmiar każdego zdarzenia**.

Domyślnie Time Series Insights może pozyskać dane przychodzące z szybkością **do 1 megabajtów na sekundę (MB/s) na Time Series Insights środowisku**. Istnieją dodatkowe ograniczenia [na partycję centrum](concepts-streaming-throughput-limitations.md#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * Obsługa środowiska w celu pozyskiwania prędkości do 16 MB/s może być świadczona przez żądanie.
> * Skontaktuj się z nami, jeśli potrzebujesz wyższej przepływności przez przesłanie biletu pomocy technicznej za pomocą Azure Portal.
 
* **Przykład 1:**

    Wysyłka firmy Contoso obejmuje 100 000 urządzeń, które emitują wydarzenie trzy razy na minutę. Rozmiar zdarzenia to 200 bajtów. Używają IoT Hub z czterema partycjami jako Time Series Insights źródłem zdarzenia.

    * Szybkość pozyskiwania dla środowiska Time Series Insights: **100 000 urządzeń * 200 bajtów/zdarzenia * (3/60 Event/s) = 1 MB/s**.
    * Szybkość pozyskiwania na partycję 0,25 MB/s.
    * Stawka pozyskiwania kosztów wysyłki firmy Contoso będzie w ramach ograniczenia skali.

* **Przykład 2:**

    Analiza floty firmy Contoso obejmuje 60 000 urządzeń, które emitują zdarzenia co sekundę. Korzystają one z centrum zdarzeń z liczbą partycji 4 jako źródłem zdarzenia Time Series Insights. Rozmiar zdarzenia to 200 bajtów.

    * Szybkość pozyskiwania środowiska: **60 000 urządzeń * 200 bajtów/zdarzenia * 1 zdarzenie/s = 12 MB/s**.
    * Stawka za partycje wynosi 3 MB/s.
    * Wskaźnik pozyskiwania danych z usługi contoso floty jest powyżej limitów środowiska i partycji. Mogą przesłać żądanie Time Series Insights przez Azure Portal, aby zwiększyć szybkość pozyskiwania dla środowiska i utworzyć centrum zdarzeń z większą liczbą partycji w ramach limitów wersji zapoznawczej.

## <a name="hub-partitions-and-per-partition-limits"></a>Partycje centrum i limity na partycję

Planując środowisko Time Series Insights, należy rozważyć konfigurację źródeł zdarzeń, z którymi będziesz się łączyć Time Series Insights. Zarówno IoT Hub platformy Azure, jak i Event Hubs używają partycji, aby włączyć skalowanie w poziomie na potrzeby przetwarzania zdarzeń. 

*Partycja* to uporządkowana sekwencja zdarzeń przechowywanych w centrum. Liczba partycji jest ustawiana podczas fazy tworzenia centrum i nie można jej zmienić.

Aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi partycjonowania Event Hubs, sprawdź, [ile partycji jest potrzebnych?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> Większość centrów IoT używanych z Azure Time Series Insights potrzebuje tylko czterech partycji.

Bez względu na to, czy tworzysz nowe centrum dla środowiska Time Series Insights lub korzystasz z istniejącego, musisz obliczyć współczynnik pozyskiwania partycji, aby określić, czy jest on w ramach limitów. 

Wersja zapoznawcza Azure Time Series Insights ma obecnie **limit na partycję 0,5 MB/s**.

### <a name="iot-hub-specific-considerations"></a>Zagadnienia dotyczące IoT Hub

Gdy urządzenie zostanie utworzone w IoT Hub, zostaje trwale przypisane do partycji. W takim przypadku IoT Hub jest w stanie zagwarantować porządkowanie zdarzeń (ponieważ nigdy nie zmieni się przypisanie).

Stałe przypisanie partycji ma także wpływ na Time Series Insights wystąpieniami, które pobierają dane wysyłane z IoT Hub podrzędnych. Gdy komunikaty z wielu urządzeń są przekazywane do koncentratora przy użyciu tego samego identyfikatora urządzenia bramy, mogą się one pojawić w tej samej partycji, co może spowodować przekroczenie limitów skalowania partycji.

**Wpływ**:

* Jeśli jedna partycja ma stałą częstotliwość pozyskiwania w ramach limitu wersji zapoznawczej, istnieje możliwość, że Time Series Insights nie zsynchronizuje wszystkich danych telemetrycznych urządzenia przed przekroczeniem okresu przechowywania IoT Hub. W związku z tym wysyłane dane mogą zostać utracone w przypadku stałego przekroczenia limitów pozyskiwania.

Aby wyeliminować to okoliczności, zalecamy stosowanie następujących najlepszych rozwiązań:

* Przed wdrożeniem rozwiązania Oblicz swoje licencje na środowisko i przejęcie na partycje.
* Upewnij się, że urządzenia IoT Hub są zrównoważone obciążenia w możliwie najszerszym zakresie.

> [!IMPORTANT]
> W przypadku środowisk, w których IoT Hub jako źródło zdarzeń, Oblicz wskaźnik pozyskiwania przy użyciu liczby używanych urządzeń Hub, aby upewnić się, że stawka spadnie poniżej limitu 0,5 MB/s na partycję.
>
> * Nawet wtedy, gdy kilka zdarzeń dociera jednocześnie, limit wersji zapoznawczej nie zostanie przekroczony.

  ![Diagram partycji IoT Hub](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Zapoznaj się z poniższymi zasobami, aby dowiedzieć się więcej na temat optymalizowania przepływności i partycji centrum:

* [Skalowanie IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Skala centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partycje centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

## <a name="next-steps"></a>Następne kroki

* Przeczytaj o [magazynie](concepts-storage.md) danych