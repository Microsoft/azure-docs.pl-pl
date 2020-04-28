---
title: Konfiguracja sterownika procesora GPU platformy Azure n dla systemu Windows
description: Jak skonfigurować sterowniki procesorów graficznych NVIDIA dla maszyn wirtualnych z serii N z systemem Windows Server lub Windows na platformie Azure
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: vikancha
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bc11937410bf0307a00895e0ebd1f01a58bd1b1b
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865777"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Instalowanie sterowników procesorów graficznych NVIDIA na maszynach wirtualnych z systemem Windows z systemem Windows 

Aby korzystać z możliwości procesorów GPU maszyn wirtualnych z systemem Windows z systemem Windows, należy zainstalować sterowniki procesora graficznego NVIDIA. [Rozszerzenie sterownika GPU NVIDIA](../extensions/hpccompute-gpu-windows.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub GRID na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub zarządzaj nim za pomocą witryny Azure portal lub narzędzi, takich jak szablony programu Azure PowerShell lub Usługi Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterowników procesora graficznego NVIDIA, aby](../extensions/hpccompute-gpu-windows.md) zapoznać się z obsługiwanymi systemami operacyjnymi i krokami wdrażania.

Jeśli zdecydujesz się zainstalować sterowniki GPU ręcznie, ten artykuł zawiera obsługiwane systemy operacyjne, sterowniki oraz kroki instalacji i weryfikacji. Ręczne informacje o konfiguracji sterownika są również dostępne dla [maszyn wirtualnych z systemem Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Aby uzyskać podstawowe specyfikacje, pojemność pamięci masowej i szczegóły dysku, zobacz [Rozmiary maszyn wirtualnych systemu GPU systemu Windows](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Instalacja sterownika

1. Połącz się za pomocą pulpitu zdalnego z każdą maszyną wirtualną z serii N.

2. Pobierz, wyodrębnij i zainstaluj obsługiwany sterownik dla systemu operacyjnego Windows.

Po zainstalowaniu sterownika GRID na maszynie wirtualnej wymagane jest ponowne uruchomienie. Po zainstalowaniu sterownika CUDA ponowne uruchomienie nie jest wymagane.

## <a name="verify-driver-installation"></a>Weryfikowanie instalacji sterownika

Należy pamiętać, że panel Nvidia Control jest dostępny tylko po zainstalowaniu sterownika GRID. Jeśli masz zainstalowane sterowniki CUDA, panel sterowania Nvidia nie będzie widoczny.

Instalację sterownika można zweryfikować w Menedżerze urządzeń. W poniższym przykładzie przedstawiono pomyślną konfigurację karty Tesla K80 na maszynie wirtualnej platformy Azure NC.

![Właściwości sterownika GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Aby zbadać stan urządzenia GPU, uruchom narzędzie wiersza polecenia [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) zainstalowane ze sterownikiem.

1. Otwórz wiersz polecenia i zmień katalog **C:\Program Files\NVIDIA Corporation\NVSMI.**

2. Uruchom polecenie `nvidia-smi`. Jeśli sterownik jest zainstalowany, zobaczysz dane wyjściowe podobne do następujących. **Gpu-Util** pokazuje **0%,** chyba że aktualnie jest uruchomione obciążenie gpu na maszynie wirtualnej. Wersja sterownika i szczegóły gpu mogą się różnić od podanych.

![Stan urządzenia NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Łączność sieciowa RDMA

Łączność sieciowa RDMA może być włączona na maszynach wirtualnych z serii N obsługujących technologię RDMA, takich jak NC24r wdrożonych w tym samym zestawie dostępności lub w jednej grupie umieszczania w zestawie skalowania maszyny wirtualnej. Rozszerzenie HpcVmDrivers należy dodać, aby zainstalować sterowniki urządzeń sieciowych systemu Windows, które umożliwiają łączność RDMA. Aby dodać rozszerzenie maszyny Wirtualnej do maszyny wirtualnej z obsługą technologii RDMA z obsługą N, należy użyć poleceń cmdlet [programu Azure PowerShell](/powershell/azure/overview) dla usługi Azure Resource Manager.

Aby zainstalować najnowszą wersję 1.1 Rozszerzenie HpcVMDrivers na istniejącej maszynie wirtualnej obsługującej rdma o nazwie myVM w regionie Zachodnie stany USA:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Aby uzyskać więcej informacji, zobacz [Rozszerzenia i funkcje maszyn wirtualnych dla systemu Windows](extensions-features.md).

Sieć RDMA obsługuje ruch mpi (Message Passing Interface) dla aplikacji działających z [systemem Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) lub Intel MPI 5.x. 


## <a name="next-steps"></a>Następne kroki

* Deweloperzy budujące aplikacje z akceleratorem GPU dla procesorów graficznych NVIDIA Tesla mogą również pobrać i zainstalować najnowszy [zestaw narzędzi CUDA.](https://developer.nvidia.com/cuda-downloads) Aby uzyskać więcej informacji, zobacz [Podręcznik instalacji CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


