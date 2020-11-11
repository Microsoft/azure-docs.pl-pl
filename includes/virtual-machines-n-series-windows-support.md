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
ms.openlocfilehash: 22d2ddc65616bbeb0c511afa6f60bc12c6ec67ef
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94482756"
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

Sterowniki siatki rozpowszechniane przez platformę Azure nie działają na maszynach wirtualnych z serii nietrwałych, takich jak NC, NCv2, Seria NCV3, ND i NDv2. Jedynym wyjątkiem jest seria maszyn wirtualnych NCas_T4_V3, w których sterowniki siatki umożliwią korzystanie z funkcji graficznych podobnie jak w przypadku serii NV.

Należy pamiętać, że rozszerzenie NVIDIA będzie zawsze instalować najnowszy sterownik. Udostępniamy linki do poprzedniej wersji w tym miejscu dla klientów, którzy mają zależność od starszej wersji.

W przypadku systemów Windows Server 2019, Windows Server 2016 i Windows 10 (do kompilacji w wersji 2004):
- [Siatka 11,1 (452,39)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Siatka 11,0 (451,48)](https://download.microsoft.com/download/C/1/4/c147a482-1364-4d12-b9e3-0beda0f00a13/451.48_grid_win10_server2016_server2019_64bit_international.exe) (. exe) 

W przypadku systemu Windows Server 2012 R2: 
- [Siatka 11,0 (451,48)](https://download.microsoft.com/download/C/1/4/c147a482-1364-4d12-b9e3-0beda0f00a13/451.48_grid_win10_server2016_server2019_64bit_international.exe) (. exe) 
- [Siatka 10,1 (442,66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (. exe)  
