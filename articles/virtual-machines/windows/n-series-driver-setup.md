---
title: Konfiguracja sterowników procesora GPU NVIDIA dla systemu Windows na platformie Azure
description: Jak skonfigurować sterowniki NVIDIA GPU dla maszyn wirtualnych serii N z systemem Windows Server lub Windows na platformie Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: vikancha
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05487db1d0522634eda606dcb2ba22876fc341db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557441"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Instalowanie sterowników NVIDIA GPU na maszynach wirtualnych serii N z systemem Windows 

Aby skorzystać z możliwości procesora GPU dla maszyn wirtualnych serii N platformy Azure, które są obsługiwane przez procesory GPU firmy NVIDIA, należy zainstalować sterowniki procesora GPU NVIDIA. [Rozszerzenie sterownika NVIDIA GPU](../extensions/hpccompute-gpu-windows.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub Grid na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub Zarządzaj nim przy użyciu Azure Portal lub narzędzi, takich jak szablony Azure PowerShell lub Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterownika graficznego procesora NVIDIA](../extensions/hpccompute-gpu-windows.md) dla obsługiwanych systemów operacyjnych i kroków wdrażania.

W przypadku wybrania opcji ręcznego instalowania sterowników procesora GPU w tym artykule przedstawiono obsługiwane systemy operacyjne, sterowniki oraz czynności instalacyjne i weryfikacyjne. Informacje o ręcznej instalacji sterownika są również dostępne dla [maszyn wirtualnych z systemem Linux](../linux/n-series-driver-setup.md).

Aby zapoznać się z podstawowymi danymi, pojemnościami magazynu i szczegółami dysku, zobacz [rozmiary maszyn wirtualnych w systemie Windows GPU](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Instalacja sterownika

1. Połącz się Pulpit zdalny z każdą maszyną wirtualną serii N.

2. Pobierz, Wyodrębnij i zainstaluj obsługiwany sterownik dla systemu operacyjnego Windows.

Po instalacji sterownika siatki na maszynie wirtualnej wymagane jest ponowne uruchomienie. Po zainstalowaniu sterownika CUDA ponowne uruchomienie nie jest wymagane.

## <a name="verify-driver-installation"></a>Weryfikuj instalację sterownika

Należy pamiętać, że Panel sterowania NVIDIA jest dostępny tylko w przypadku instalacji sterownika siatki. Jeśli zainstalowano sterowniki CUDA, Panel sterowania NVIDIA nie będzie widoczny.

Instalację sterownika można sprawdzić w Device Manager. Poniższy przykład przedstawia pomyślne Konfigurowanie karty Tesla K80 na maszynie wirtualnej Azure NC.

![Właściwości sterownika procesora GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Aby wykonać zapytanie o stan urządzenia GPU, uruchom narzędzie wiersza polecenia [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) zainstalowane wraz z sterownikiem.

1. Otwórz wiersz polecenia i przejdź do katalogu **C:\Program Files\NVIDIA Corporation\NVSMI** .

2. Uruchom polecenie `nvidia-smi`. Jeśli sterownik jest zainstalowany, zostaną wyświetlone dane wyjściowe podobne do poniższych. **Procesor GPU** pokazuje **0%** , chyba że obecnie działa obciążenie procesora GPU na maszynie wirtualnej. Informacje o wersji sterownika i procesora GPU mogą różnić się od podanych elementów.

![Stan urządzenia NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Łączność sieciowa RDMA

Łączność sieciową RDMA można włączyć na maszynach wirtualnych z serii N obsługujących funkcję RDMA, takich jak NC24r wdrożone w tym samym zestawie dostępności lub w pojedynczej grupie umieszczania w zestawie skalowania maszyn wirtualnych. Aby zainstalować sterowniki urządzeń sieciowych z systemem Windows, które umożliwiają łączność RDMA, należy dodać rozszerzenie HpcVmDrivers. Aby dodać rozszerzenie maszyny wirtualnej do maszyny wirtualnej z serii N z obsługą funkcji RDMA, użyj [Azure PowerShell](/powershell/azure/) poleceń cmdlet dla Azure Resource Manager.

Aby zainstalować najnowszą wersję 1,1 rozszerzenia HpcVMDrivers na istniejącej maszynie wirtualnej z obsługą funkcji RDMA o nazwie myVM w regionie zachodnie stany USA:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Aby uzyskać więcej informacji, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows](../extensions/features-windows.md).

Sieć RDMA obsługuje ruch MPI (Message Passing Interface) dla aplikacji uruchamianych z programem [Microsoft MPI](/message-passing-interface/microsoft-mpi) lub Intel MPI 5. x. 


## <a name="next-steps"></a>Następne kroki

* Deweloperzy tworzący przyspieszające procesor GPU aplikacje dla procesorów GPU NVIDIA Tesla mogą również pobrać i zainstalować najnowszy [zestaw narzędzi cuda](https://developer.nvidia.com/cuda-downloads). Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji cuda](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).
