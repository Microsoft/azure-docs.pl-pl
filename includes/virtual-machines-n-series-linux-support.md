---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 279f4cb1d8891f64fb01f4f8abb6b23a004c2b4d
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84269164"
---
## <a name="supported-distributions-and-drivers"></a>Obsługiwane dystrybucje i sterowniki

### <a name="nvidia-cuda-drivers"></a>Sterowniki NVIDIA CUDA

Sterowniki NVIDIA CUDA dla maszyn wirtualnych z serii NC, NCv2, Seria NCV3, ND i NDv2 (opcjonalne dla serii NV) są obsługiwane tylko w przypadku dystrybucji systemu Linux wymienionych w poniższej tabeli. Informacje o sterowniku CUDA są aktualne w momencie publikacji. Najnowsze sterowniki CUDA i obsługiwane systemy operacyjne można znaleźć w witrynie internetowej [firmy NVIDIA](https://developer.nvidia.com/cuda-zone) . Upewnij się, że instalujesz lub uaktualniasz do najnowszych sterowników CUDA dla dystrybucji. 

> [!TIP]
> Alternatywą dla ręcznej instalacji sterownika CUDA na maszynie wirtualnej z systemem Linux jest wdrożenie obrazu [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) platformy Azure. Wersje DSVM dla Ubuntu 16,04 LTS lub CentOS 7,4 wstępnie instalują sterowniki NVIDIA CUDA, bibliotekę sieciową CUDA głębokiej neuronowych oraz inne narzędzia.


### <a name="nvidia-grid-drivers"></a>Sterowniki sieci NVIDIA

Firma Microsoft redystrybuuje Instalatory sterowników NVIDIA GRID dla maszyn wirtualnych z serii NV i NVv3 używanych jako wirtualne stacje robocze lub aplikacje wirtualne. Zainstaluj tylko te sterowniki siatki na maszynach wirtualnych usługi Azure NV, tylko w systemach operacyjnych wymienionych w poniższej tabeli. Te sterowniki obejmują Licencjonowanie oprogramowania wirtualnej procesora GPU na platformie Azure. Nie trzeba konfigurować serwera licencji oprogramowania NVIDIA vGPU.

| Dystrybucja | Sterownik |
| --- | -- |
|Ubuntu 18,04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,0 do 7,7<br/><br/>CentOS-based 7,0 do 7,7<br/><br/>SUSE Linux Enterprise Server 12 z dodatkiem SP2 | NVIDIA GRID 10,1, gałąź sterownika R440|

> [!WARNING] 
> Instalacja oprogramowania innych firm na produktach systemu Red Hat może wpłynąć na warunki wsparcia systemu Red Hat. Zobacz [artykuł bazy wiedzy systemu Red Hat](https://access.redhat.com/articles/1067).
>
