---
title: Wybieranie rozmiarów i obrazów maszyn wirtualnych dla pul
description: Jak wybrać dostępne rozmiary maszyn wirtualnych i wersje systemu operacyjnego dla węzłów obliczeniowych w pulach Azure Batch
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: seodec18
ms.openlocfilehash: 2c3b90d6188dc6660233ae659fb4280dc1d4f2a5
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027384"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Wybieranie rozmiaru i obrazu maszyny wirtualnej dla węzłów obliczeniowych w puli Azure Batch

Po wybraniu rozmiaru węzła dla puli Azure Batch można wybrać spośród niemal wszystkich rozmiarów maszyn wirtualnych dostępnych na platformie Azure. Platforma Azure oferuje różne rozmiary maszyn wirtualnych z systemem Linux i Windows dla różnych obciążeń.

## <a name="supported-vm-series-and-sizes"></a>Obsługiwane serie maszyn wirtualnych i rozmiary

### <a name="pools-in-virtual-machine-configuration"></a>Pule w konfiguracji maszyny wirtualnej

Pule wsadowe w konfiguracji maszyny wirtualnej obsługują niemal wszystkie [rozmiary maszyn](../virtual-machines/sizes.md)wirtualnych. Zapoznaj się z poniższą tabelą, aby dowiedzieć się więcej o obsługiwanych rozmiarach i ograniczeniach.

| Serie maszyn wirtualnych  | Obsługiwane rozmiary |
|------------|---------|
| Podstawowa A | Wszystkie rozmiary *z wyjątkiem* Basic_A0 (a0) |
| A | Wszystkie rozmiary *z wyjątkiem* Standard_A0, Standard_A8, Standard_A9, Standard_A10, Standard_A11 |
| Av2 | Wszystkie rozmiary |
| B | Nieobsługiwane |
| DCsv2 | Wszystkie rozmiary |
| Dv2, DSv2 | Wszystkie rozmiary |
| Dv3, Dsv3 | Wszystkie rozmiary |
| Dav4, Dasv4 | Wszystkie rozmiary |
| Ddv4, Ddsv4 |  Wszystkie rozmiary |
| Dv4, Dsv4 | Nieobsługiwane |
| EV3, Esv3 | Wszystkie rozmiary, z wyjątkiem E64is_v3 |
| Eav4, Easv4 | Wszystkie rozmiary |
| Edv4, Edsv4 |  Wszystkie rozmiary |
| Ev4, Esv4 | Nieobsługiwane |
| F, FS | Wszystkie rozmiary |
| Fsv2 | Wszystkie rozmiary |
| G, GS | Wszystkie rozmiary |
| H | Wszystkie rozmiary |
| HB | Wszystkie rozmiary |
| HBv2 | Wszystkie rozmiary |
| HBv3 | Standard_HB120rs_v3 (inne rozmiary nie są jeszcze dostępne) |
| WĘGLOWODOR | Wszystkie rozmiary |
| Ls | Wszystkie rozmiary |
| Lsv2 | Wszystkie rozmiary |
| M | Wszystkie rozmiary |
| Mv2<sup>1</sup> | Wszystkie rozmiary |
| NC | Wszystkie rozmiary |
| NCv2 | Wszystkie rozmiary |
| Seria NCV3 | Wszystkie rozmiary |
| NCasT4_v3 | Wszystkie rozmiary |
| ND | Wszystkie rozmiary |
| NDv2 | Brak — nie jest jeszcze dostępna |
| NV | Wszystkie rozmiary |
| NVv3 | Wszystkie rozmiary |
| NVv4 | Wszystkie rozmiary |
| SAP HANA | Nieobsługiwane |

<sup>1</sup> te serie maszyn wirtualnych mogą być używane tylko z obrazami maszyn wirtualnych generacji 2.

### <a name="using-generation-2-vm-images"></a>Używanie obrazów maszyn wirtualnych generacji 2

Niektóre serie maszyn wirtualnych, takie jak [Mv2](../virtual-machines/mv2-series.md), mogą być używane tylko z [obrazami maszyn wirtualnych generacji 2](../virtual-machines/generation-2.md). Obrazy maszyn wirtualnych generacji 2 są określane jako wszystkie obrazy maszyn wirtualnych przy użyciu właściwości "SKU" konfiguracji ["elementu imagereference"](/rest/api/batchservice/pool/add#imagereference) ; ciąg "SKU" ma sufiks, taki jak "-G2" lub "-Gen2". Aby uzyskać listę obrazów maszyn wirtualnych obsługiwanych przez usługę Batch, w tym obrazów generacji 2, użyj interfejsu API ["list obsługiwanych obrazów"](/rest/api/batchservice/account/listsupportedimages) , [programu PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage)lub [wiersza polecenia platformy Azure](/cli/azure/batch/pool/supported-images).

### <a name="pools-in-cloud-service-configuration"></a>Pule w konfiguracji usługi w chmurze

Pule wsadowe w konfiguracji usługi w chmurze obsługują wszystkie [rozmiary maszyn wirtualnych dla Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **z wyjątkiem** następujących:

| Serie maszyn wirtualnych  | Nieobsługiwane rozmiary |
|------------|-------------------|
| Seria A   | Bardzo mała       |
| Seria Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Istotne zagadnienia dotyczące rozmiaru

- **Wymagania dotyczące aplikacji** — należy wziąć pod uwagę charakterystyki i wymagania aplikacji, które będą uruchamiane w węzłach. Takie czynniki jak to, czy aplikacja jest wielowątkowa oraz ile pamięci zużywa, mogą pomóc w wyborze najbardziej odpowiedniego i ekonomicznego rozmiar węzła. W przypadku [obciążeń MPI](batch-mpi.md) z obsługą wiele wystąpień lub aplikacji cuda należy odpowiednio [rozważyć wyspecjalizowane](../virtual-machines/sizes-hpc.md) rozmiary maszyn wirtualnych lub maszyny wirtualne [obsługujące procesor GPU](../virtual-machines/sizes-gpu.md) . Aby uzyskać więcej informacji, zobacz [Korzystanie z wystąpień obsługujących funkcję RDMA lub GPU w pulach usługi Batch](batch-pool-compute-intensive-sizes.md).

- **Zadania na węzeł** — typowym zadaniem jest wybranie rozmiaru węzła, przy założeniu, że jedno zadanie jest uruchamiane w węźle w danym momencie. Jednak może być korzystne wykonywanie wielu zadań (i w związku z tym wiele wystąpień aplikacji) [równolegle](batch-parallel-node-tasks.md) w węzłach obliczeniowych podczas wykonywania zadania. W tym przypadku często należy wybrać rozmiar węzła wielordzeniowego, aby uwzględnić zwiększony popyt wykonywania zadań równoległych.

- **Poziomy obciążenia dla różnych zadań** — ten sam rozmiar wynosi wszystkie węzły w puli. Jeśli planujesz uruchamiać aplikacje z różnymi wymaganiami systemowymi i/lub poziomami obciążenia, zalecamy użycie oddzielnych pul.

- **Dostępność regionu** — seria maszyn wirtualnych lub rozmiar może nie być dostępny w regionach, w których tworzysz konta w usłudze Batch. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/regions/services/).

- **Limity** przydziału — liczba [rdzeni](batch-quota-limit.md#resource-quotas) w ramach konta w usłudze Batch może ograniczać liczbę węzłów o danym rozmiarze, które można dodać do puli wsadowej. W razie konieczności można [zażądać zwiększenia limitu przydziału](batch-quota-limit.md#increase-a-quota).

- **Konfiguracja puli** — ogólnie rzecz biorąc, masz więcej opcji rozmiaru maszyny wirtualnej podczas tworzenia puli w konfiguracji maszyny wirtualnej w porównaniu z konfiguracją usługi w chmurze.

## <a name="supported-vm-images"></a>Obsługiwane obrazy maszyn wirtualnych

Użyj jednego z następujących interfejsów API, aby zwrócić listę obrazów maszyn wirtualnych z systemami Windows i Linux, które są obecnie obsługiwane przez usługi Batch, w tym identyfikatory jednostki SKU agenta węzła dla każdego obrazu:

- Interfejs API REST usługi Batch: [Lista obsługiwanych obrazów](/rest/api/batchservice/account/listsupportedimages)
- PowerShell: [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Interfejs wiersza polecenia platformy Azure: [AZ Batch Pool Supported-images](/cli/azure/batch/pool/supported-images)

Zdecydowanie zaleca się uniknięcie obrazów z nieoczekiwaną datą zakończenia okresu istnienia (EOL). Te daty można odnaleźć za pośrednictwem [ `ListSupportedImages` interfejsu API](https://docs.microsoft.com/rest/api/batchservice/account/listsupportedimages), [programu PowerShell](https://docs.microsoft.com/powershell/module/az.batch/get-azbatchsupportedimage)lub interfejsu [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/batch/pool/supported-images). Aby uzyskać więcej informacji na temat wyboru obrazu maszyny wirtualnej puli usługi Batch, zobacz [Przewodnik po najlepszych rozwiązaniach](best-practices.md) dotyczących usługi Batch.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Aby uzyskać informacje o używaniu rozmiarów maszyn wirtualnych intensywnie korzystających z obliczeń, zobacz [Używanie wystąpień z obsługą funkcji RDMA lub procesorów GPU w pulach usługi Batch](batch-pool-compute-intensive-sizes.md).
