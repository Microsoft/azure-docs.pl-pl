---
title: Skalowalność — Event Hubs platformy Azure | Microsoft Docs
description: Ten artykuł zawiera informacje na temat skalowania Event Hubs platformy Azure przy użyciu partycji i jednostek przepływności.
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: f258ee2a3b4162dabf7a8e615db82b9b889d628b
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601296"
---
# <a name="scaling-with-event-hubs"></a>Skalowanie za pomocą Event Hubs

Istnieją dwa czynniki wpływające na skalowanie w Event Hubs.
*   Jednostki przepływności
*   Partycje

## <a name="throughput-units"></a>Jednostki przepływności

Pojemność przepływności usługi Event Hubs jest kontrolowana przez *jednostki przepływności*. Jednostki przepływności to zakupione wcześniej jednostki pojemności. Pojedyncza przepływność pozwala:

* Ruch przychodzący: maksymalnie 1 MB na sekundę lub 1000 zdarzeń na sekundę (w zależności od tego, co nastąpi wcześniej).
* Ruch wychodzący: maksymalnie 2 MB na sekundę lub 4096 zdarzeń na sekundę.

Po przekroczeniu pojemności zakupionych jednostek przepływności ruch przychodzący jest ograniczany i jest zwracany wyjątek [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). Ruch wychodzący nie generuje wyjątków ograniczania, ale też jest ograniczony do pojemności zakupionych jednostek przepływności. Jeśli wystąpią wyjątki szybkości publikowania lub oczekiwany będzie większy transfer danych wychodzących, należy sprawdzić liczbę jednostek przepływności zakupionych dla przestrzeni nazw. Jednostkami przepływności można zarządzać w bloku **Skala** przestrzeni nazw w witrynie [Azure Portal](https://portal.azure.com). Jednostki przepływności można także zarządzać programowo przy użyciu [interfejsów api Event Hubs](./event-hubs-samples.md).

Jednostki przepływności są wstępnie kupowane i są rozliczane za godzinę. Po zakupieniu jednostki przepływności są rozliczane za co najmniej jedną godzinę. Dla przestrzeni nazw Event Hubs można kupić maksymalnie 20 jednostek przepływności i są one współużytkowane przez wszystkie Centra zdarzeń w tej przestrzeni nazw.

Funkcja **automatycznego** rozszerzania Event Hubs automatycznie skaluje się w górę przez zwiększenie liczby jednostek przepływności w celu spełnienia wymagań dotyczących użycia. Zwiększenie jednostek przepływności uniemożliwia scenariusze ograniczania, w którym:

- Szybkość transferu danych przychodzących przekracza ustaloną liczbę jednostek przepływności.
- Szybkości żądań danych wychodzących przekraczają ustawioną jednostkę przepływności.

Usługa Event Hubs zwiększa przepływność, gdy obciążenie przekracza minimalny próg, bez żądań zakończonych niepowodzeniem z błędami ServerBusy. 

Aby uzyskać więcej informacji na temat funkcji automatycznego rozszerzania, zobacz [Automatyczne skalowanie jednostek przepływności](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partycje
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]




## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

- [Automatyczne skalowanie jednostek przepływności](event-hubs-auto-inflate.md)
- [Przegląd usługi Event Hubs](./event-hubs-about.md)
