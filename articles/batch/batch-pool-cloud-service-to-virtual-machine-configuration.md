---
title: Migruj konfigurację puli partii z Cloud Services do Virtual Machines
description: Dowiedz się, jak zaktualizować konfigurację puli do najnowszej i zalecanej konfiguracji
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: a176c4df1737a340a546b4ab7926447cd821350d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200560"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>Migruj konfigurację puli partii z Cloud Services na maszynę wirtualną

Obecnie pule usługi Batch można tworzyć za pomocą [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) lub [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration). Zalecamy używanie tylko konfiguracji maszyny wirtualnej, ponieważ ta konfiguracja obsługuje wszystkie możliwości usługi Batch.

Cloud Services pule konfiguracji nie obsługują niektórych bieżących funkcji wsadowych i nie obsługują nowo dodanych funkcji. Nie będzie można tworzyć nowych pul "CloudServiceConfiguration" ani dodawać nowych węzłów do istniejących pul [po 29 lutego 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/).

Jeśli Twoje rozwiązania w usłudze Batch używają obecnie pul "cloudServiceConfiguration", zalecamy zmianę na "virtualMachineConfiguration" najszybciej, jak to możliwe. Dzięki temu można korzystać ze wszystkich możliwości usługi Batch, takich jak Rozszerzona funkcja [wybierania maszyn](batch-pool-vm-sizes.md)wirtualnych, maszyn wirtualnych z systemem Linux, [kontenerów](batch-docker-container-workloads.md), [Azure Resource Manager sieci wirtualnych](batch-virtual-network.md)i [szyfrowania dysków węzłowych](disk-encryption.md).

## <a name="create-a-pool-using-virtual-machine-configuration"></a>Tworzenie puli przy użyciu konfiguracji maszyny wirtualnej

Nie można przełączyć istniejącej aktywnej puli korzystającej z elementu "cloudServiceConfiguration", aby używać elementu "virtualMachineConfiguration". Zamiast tego należy utworzyć nowe pule. Po utworzeniu nowych pul "virtualMachineConfiguration" i zreplikowaniu wszystkich zadań i zadań można usunąć stare "cloudServiceConfiguration'pools, które nie są już używane.

Wszystkie interfejsy API usługi Batch, narzędzia wiersza polecenia, Azure Portal i interfejs użytkownika Batch Explorer umożliwiają tworzenie pul przy użyciu opcji "virtualMachineConfiguration".

Aby zapoznać się z przewodnikiem procesu tworzenia pul, które używają elementu "virtualMachineConfiguration", zobacz [Samouczek platformy .NET](tutorial-parallel-dotnet.md) lub [samouczek języka Python](tutorial-parallel-python.md).

## <a name="pool-configuration-differences"></a>Różnice konfiguracji puli

Niektóre kluczowe różnice między obiema konfiguracjami obejmują:

- węzły puli "cloudServiceConfiguration" używają tylko systemu operacyjnego Windows. pule "virtualMachineConfiguration" mogą korzystać z systemu operacyjnego Linux lub Windows.
- W porównaniu do pul "cloudServiceConfiguration" pule "virtualMachineConfiguration" mają bogatszy zestaw funkcji, takich jak obsługa kontenerów, dyski danych i szyfrowanie dysków.
- Czasy uruchamiania i usuwania puli i węzła mogą się nieco różnić między pulami "cloudServiceConfiguration" i "virtualMachineConfiguration".
- węzły puli "virtualMachineConfiguration" wykorzystują zarządzane dyski systemu operacyjnego. [Typ dysku zarządzanego](../virtual-machines/disks-types.md) , który jest używany dla każdego węzła, zależy od rozmiaru maszyny wirtualnej wybranego dla puli. Jeśli dla puli zostanie określony rozmiar maszyny wirtualnej, na przykład "Standard_D2s_v3" zostanie użyty dysk SSD w warstwie Premium. Jeśli jest określony rozmiar maszyny wirtualnej "inny niż s", na przykład "Standard_D2_v3", zostanie użyty standardowy dysk twardy.

   > [!IMPORTANT]
   > Podobnie jak w przypadku Virtual Machines i Virtual Machine Scale Sets, dysk zarządzany systemu operacyjnego używany dla każdego węzła wiąże się z kosztem, który jest dodatkowym kosztem maszyn wirtualnych. Nie ma kosztu dysku systemu operacyjnego dla węzłów "cloudServiceConfiguration", ponieważ dysk systemu operacyjnego jest tworzony na lokalnym dysku SSD.

## <a name="azure-data-factory-custom-activity-pools"></a>Azure Data Factory niestandardowe pule działań

Pule Azure Batch mogą służyć do uruchamiania Data Factory działań niestandardowych. Wszystkie pule "cloudServiceConfiguration" używane do uruchamiania działań niestandardowych muszą zostać usunięte i utworzone nowe pule "virtualMachineConfiguration".

Podczas tworzenia nowej puli do uruchamiania Data Factory działań niestandardowych wykonaj następujące czynności:

- Wstrzymaj wszystkie potoki przed utworzeniem nowych pul i usuń stare, aby upewnić się, że żadne wykonywanie nie zostanie przerwane.
- Tego samego identyfikatora puli można użyć, aby uniknąć zmian w konfiguracji połączonej usługi.
- Wznów potoki po utworzeniu nowych pul.

Aby uzyskać więcej informacji na temat używania Azure Batch do uruchamiania Data Factory działań niestandardowych, zobacz [Azure Batch połączonej usługi](../data-factory/compute-linked-services.md#azure-batch-linked-service) i  [działań niestandardowych w potoku Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [konfiguracjach puli](nodes-and-pools.md#configurations).
- Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących puli](best-practices.md#pools).
- Zapoznaj się z dokumentacją interfejsu API REST w celu [dodania puli](/rest/api/batchservice/pool/add) i [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration).