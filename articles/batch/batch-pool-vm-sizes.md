---
title: Wybierz rozmiary maszyn wirtualnych dla pul
description: Jak wybierać dostępne rozmiary maszyn wirtualnych dla węzłów obliczeniowych w pulach Azure Batch
ms.topic: conceptual
ms.date: 06/10/2020
ms.custom: seodec18
ms.openlocfilehash: c1621bbbe6676144176636346f3f5c34db169b7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84693162"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Wybieranie rozmiaru maszyny wirtualnej dla węzłów obliczeniowych w puli Azure Batch

Po wybraniu rozmiaru węzła dla puli Azure Batch można wybrać spośród niemal wszystkich rozmiarów maszyn wirtualnych dostępnych na platformie Azure. Platforma Azure oferuje różne rozmiary maszyn wirtualnych z systemem Linux i Windows dla różnych obciążeń.

Istnieje kilka wyjątków i ograniczeń umożliwiających wybranie rozmiaru maszyny wirtualnej:

* Niektóre maszyny wirtualne lub rozmiary maszyn wirtualnych nie są obsługiwane w usłudze Batch.
* Niektóre rozmiary maszyn wirtualnych są ograniczone i należy je włączyć przed przystąpieniem do przydzielenia.

## <a name="supported-vm-series-and-sizes"></a>Obsługiwane serie maszyn wirtualnych i rozmiary

### <a name="pools-in-virtual-machine-configuration"></a>Pule w konfiguracji maszyny wirtualnej

Pule wsadowe w konfiguracji maszyny wirtualnej obsługują niemal wszystkie rozmiary maszyn wirtualnych ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Zapoznaj się z poniższą tabelą, aby dowiedzieć się więcej o obsługiwanych rozmiarach i ograniczeniach.

| Serie maszyn wirtualnych  | Obsługiwane rozmiary |
|------------|---------|
| Podstawowa A | Wszystkie rozmiary *z wyjątkiem* Basic_A0 (a0) |
| A | Wszystkie rozmiary *z wyjątkiem* Standard_A0 |
| Av2 | Wszystkie rozmiary |
| B | Brak |
| DC | Brak |
| Dv2, DSv2 | Wszystkie rozmiary |
| Dv3, Dsv3 | Wszystkie rozmiary |
| Dav4<sup>1</sup> | Brak — nie jest jeszcze dostępna |
| Dasv4<sup>1</sup> | Wszystkie rozmiary, z wyjątkiem Standard_D48as_v4, Standard_D64as_v4 Standard_D96as_v4 |
| Ddv4, Ddsv4 |  Brak — nie jest jeszcze dostępna |
| EV3, Esv3 | Wszystkie rozmiary, z wyjątkiem E64is_v3 i E64i_v3 |
| Eav4<sup>1</sup> | Wszystkie rozmiary, z wyjątkiem Standard_E48a_v4, Standard_E64a_v4 Standard_E96a_v4 |
| Easv4<sup>1</sup> | Wszystkie rozmiary, z wyjątkiem Standard_E48as_v4, Standard_E64as_v4 Standard_E96as_v4 |
| Edv4, Edsv4 |  Brak — nie jest jeszcze dostępna |
| F, FS | Wszystkie rozmiary |
| Fsv2 | Wszystkie rozmiary |
| G, GS | Wszystkie rozmiary |
| H | Wszystkie rozmiary |
| HB<sup>1</sup> | Wszystkie rozmiary |
| HBv2<sup>1</sup> | Wszystkie rozmiary |
| HC<sup>1</sup> | Wszystkie rozmiary |
| Ls | Wszystkie rozmiary |
| Lsv2<sup>1</sup> | Wszystkie rozmiary |
| M<sup>1</sup> | Wszystkie rozmiary |
| Mv2 | Brak — nie jest jeszcze dostępna |
| NC | Wszystkie rozmiary |
| NCv2<sup>1</sup> | Wszystkie rozmiary |
| Seria NCV3<sup>1</sup> | Wszystkie rozmiary |
| ND<sup>1</sup> | Wszystkie rozmiary |
| NDv2<sup>1</sup> | Brak — nie jest jeszcze dostępna |
| NV | Wszystkie rozmiary |
| NVv3<sup>1</sup> | Wszystkie rozmiary |
| NVv4 | Brak |
| SAP HANA | Brak |

<sup>1</sup> te rozmiary maszyn wirtualnych można przydzielyć w pulach wsadowym w konfiguracji maszyny wirtualnej, ale należy utworzyć nowe konto wsadowe i zażądać [zwiększenia limitu przydziału](batch-quota-limit.md#increase-a-quota). To ograniczenie zostanie usunięte, gdy przydział vCPU na serię maszyn wirtualnych jest w pełni obsługiwany dla kont usługi Batch.

### <a name="pools-in-cloud-service-configuration"></a>Pule w konfiguracji usługi w chmurze

Pule wsadowe w konfiguracji usługi w chmurze obsługują wszystkie [rozmiary maszyn wirtualnych dla Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **z wyjątkiem** następujących:

| Serie maszyn wirtualnych  | Nieobsługiwane rozmiary |
|------------|-------------------|
| Seria A   | Bardzo małe       |
| Seria Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Istotne zagadnienia dotyczące rozmiaru

* **Wymagania dotyczące aplikacji** — należy wziąć pod uwagę charakterystyki i wymagania aplikacji, które będą uruchamiane w węzłach. Takie czynniki jak to, czy aplikacja jest wielowątkowa oraz ile pamięci zużywa, mogą pomóc w wyborze najbardziej odpowiedniego i ekonomicznego rozmiar węzła. W przypadku [obciążeń MPI](batch-mpi.md) z obsługą wiele wystąpień lub aplikacji cuda należy odpowiednio [rozważyć wyspecjalizowane](../virtual-machines/linux/sizes-hpc.md) rozmiary maszyn wirtualnych lub maszyny wirtualne [obsługujące procesor GPU](../virtual-machines/linux/sizes-gpu.md) . (Zobacz [Używanie wystąpień obsługujących funkcję RDMA lub GPU w pulach wsadowym](batch-pool-compute-intensive-sizes.md)).

* **Zadania na węzeł** — typowym zadaniem jest wybranie rozmiaru węzła, przy założeniu, że jedno zadanie jest uruchamiane w węźle w danym momencie. Jednak może być korzystne wykonywanie wielu zadań (i w związku z tym wiele wystąpień aplikacji) [równolegle](batch-parallel-node-tasks.md) w węzłach obliczeniowych podczas wykonywania zadania. W tym przypadku często należy wybrać rozmiar węzła wielordzeniowego, aby uwzględnić zwiększony popyt wykonywania zadań równoległych.

* **Poziomy obciążenia dla różnych zadań** — ten sam rozmiar wynosi wszystkie węzły w puli. Jeśli planujesz uruchamiać aplikacje z różnymi wymaganiami systemowymi i/lub poziomami obciążenia, zalecamy użycie oddzielnych pul.

* **Dostępność regionu** — seria maszyn wirtualnych lub rozmiar może nie być dostępny w regionach, w których tworzysz konta w usłudze Batch. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/regions/services/).

* **Limity** przydziału — liczba [rdzeni](batch-quota-limit.md#resource-quotas) w ramach konta w usłudze Batch może ograniczać liczbę węzłów o danym rozmiarze, które można dodać do puli wsadowej. Aby zażądać zwiększenia limitu przydziału, zobacz [ten artykuł](batch-quota-limit.md#increase-a-quota). 

* **Konfiguracja puli** — ogólnie rzecz biorąc, masz więcej opcji rozmiaru maszyny wirtualnej podczas tworzenia puli w konfiguracji maszyny wirtualnej w porównaniu z konfiguracją usługi w chmurze.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
* Aby uzyskać informacje o używaniu rozmiarów maszyn wirtualnych intensywnie korzystających z obliczeń, zobacz [Używanie wystąpień z obsługą funkcji RDMA lub procesorów GPU w pulach usługi Batch](batch-pool-compute-intensive-sizes.md).
