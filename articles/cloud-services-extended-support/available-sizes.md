---
title: Dostępne rozmiary dla usługi Azure Cloud Services (obsługa rozszerzona)
description: Dostępne rozmiary wdrożeń usługi Azure Cloud Services (obsługa rozszerzona)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cd8011782d134031393731a29594d44aba41b2ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713307"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Dostępne rozmiary dla usługi Azure Cloud Services (obsługa rozszerzona)

W tym artykule opisano dostępne rozmiary maszyn wirtualnych dla wystąpień Cloud Services (obsługa rozszerzona).   

| Rodzina SKU |  ACU/rdzeń | 
|---|---|
| [A5 — 7](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series)| 100 |
|[A8–A11](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](../virtual-machines/av2-series.md) | 100 | 
|[Wykres](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160 – 190 * |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160 – 190 * |
|[EV3](../virtual-machines/ev3-esv3-series.md) | 160 – 190 *
|[G](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180-240 * |
|[C](../virtual-machines/h-series.md) | 290 - 300* | 

>[!NOTE]
> Jednostki ACU oznaczone gwiazdką (*) wykorzystują technologię Intel® Turbo w celu zwiększenia częstotliwości zegara procesora CPU i zapewniania większej wydajności. Skala zwiększenia wydajności może się różnić w zależności od rozmiaru maszyny wirtualnej, obciążenia i innych obciążeń uruchomionych na tym samym hoście.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Skonfiguruj rozmiary dla Cloud Services (obsługa rozszerzona)

Rozmiar maszyny wirtualnej wystąpienia roli można określić jako część modelu usługi w pliku definicji usługi. Rozmiar roli określa liczbę rdzeni procesora CPU, pojemność pamięci i rozmiar lokalnego systemu plików.

Na przykład ustawienie rozmiaru wystąpienia roli sieci Web na `Standard_D2` : 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Zmiana rozmiaru istniejącej roli

Aby zmienić rozmiar istniejącej roli, Zmień rozmiar maszyny wirtualnej w pliku definicji usługi (csdef), ponownie Spakuj usługę w chmurze i Wdróż ją ponownie. 

## <a name="get-a-list-of-available-sizes"></a>Pobierz listę dostępnych rozmiarów 

Aby pobrać listę dostępnych rozmiarów, zobacz [jednostki SKU zasobów — listę](/rest/api/compute/resourceskus/list) i Zastosuj następujące filtry:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona).
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).