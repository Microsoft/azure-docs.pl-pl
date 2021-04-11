---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b3e097f1c41f3047dc4e9d6cae2a05a6b19dea9d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554778"
---
## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

### <a name="nvidia-tesla-cuda-drivers"></a>Sterowniki NVIDIA Tesla (CUDA)

Sterowniki NVIDIA Tesla (CUDA) dla maszyn wirtualnych z serii NC, NCv2, Seria NCV3, NCasT4_v3, ND i NDv2 (opcjonalne dla serii NV) są obsługiwane tylko w systemach operacyjnych wymienionych w poniższej tabeli. Linki pobierania sterowników są aktualne w momencie publikacji. Aby uzyskać najnowsze sterowniki, odwiedź witrynę internetową firmy [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Alternatywą dla ręcznej instalacji sterownika CUDA na maszynie wirtualnej z systemem Windows Server jest wdrożenie obrazu [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) platformy Azure. Wersje DSVM dla systemu Windows Server 2016 wstępnie instalują sterowniki NVIDIA CUDA, bibliotekę sieciową CUDA głęboki neuronowych oraz inne narzędzia.


| System operacyjny | Sterownik |
| -------- |------------- |
| Windows Server 2019 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |
| Windows Server 2016 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>Sterowniki sieci NVIDIA

Firma Microsoft redystrybuuje Instalatory sterowników NVIDIA GRID dla maszyn wirtualnych z serii NV i NVv3 używanych jako wirtualne stacje robocze lub aplikacje wirtualne. Zainstaluj tylko te sterowniki siatki na maszynach wirtualnych z serii NV platformy Azure, tylko w systemach operacyjnych wymienionych w poniższej tabeli. Te sterowniki obejmują Licencjonowanie oprogramowania wirtualnej procesora GPU na platformie Azure. Nie trzeba konfigurować serwera licencji oprogramowania NVIDIA vGPU.

Sterowniki siatki rozpowszechniane przez platformę Azure nie działają na maszynach wirtualnych z serii nietrwałych, takich jak NCv2, Seria NCV3, ND i NDv2. Jedynym wyjątkiem jest seria maszyn wirtualnych NCas_T4_V3, w których sterowniki siatki umożliwią korzystanie z funkcji graficznych podobnie jak w przypadku serii NV.

NC-Series z procesorami GPU NVIDIA K80 nie obsługują aplikacji SIATKowych i graficznych.  

Należy pamiętać, że rozszerzenie NVIDIA będzie zawsze instalować najnowszy sterownik. Udostępniamy linki do poprzedniej wersji w tym miejscu dla klientów, którzy mają zależność od starszej wersji.

W przypadku systemów Windows Server 2019, Windows Server 2016 1607, 1709 i Windows 10 (do kompilacji 20H2):
- [Siatka 12,1 (461,33)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Siatka 12,0 (461,09)](https://download.microsoft.com/download/4/8/d/48d2d45b-bebc-44ad-9c58-e0b79a9d4ff2/461.09_grid_win10_server2016_server2019_64bit_azure_swl.exe) (. exe) 

W przypadku systemu Windows Server 2012 R2: 
- [Siatka 12,1 (461,33)](https://download.microsoft.com/download/9/9/c/99caf5c6-af9f-48b2-bcb0-af5ec64b8592/461.33_grid_server2012R2_64bit_azure_swl.exe) (. exe)
- [Siatka 12,0 (461,09)](https://download.microsoft.com/download/c/5/e/c5e7df99-364d-45f5-bff7-c253d59121f1/461.09_grid_server2012R2_64bit_azure_swl.exe) (. exe) 


Aby zapoznać się z pełną listą poprzednich linków sterownika NVIDIA GRID, odwiedź witrynę [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json)
