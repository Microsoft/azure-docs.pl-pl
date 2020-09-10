---
title: Wybierz narzędzie do przemieszczania zasobów platformy Azure między regionami
description: Przegląd opcji i narzędzi do przemieszczania zasobów platformy Azure między regionami
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8af5cd4f961a2138f09bb3e56fccf7aa8f726dd9
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653590"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Wybierz narzędzie do przeniesienia zasobów platformy Azure

Zasoby można przenieść na platformę Azure w następujący sposób:

- **Przenoszenie zasobów między regionami**: Przenieś zasób z poziomu centrum przenoszenia zasobów lub grupy zasobów. 
- **Przenoszenie zasobów między grupami zasobów/subskrypcjami**: przenoszenie z grupy zasobów. 
- **Przenoszenie zasobów między chmurami platformy Azure**: usługa Azure Site Recovery służy do przenoszenia zasobów między chmurami publicznymi i administracyjnymi.
- **Przenoszenie zasobów między strefami dostępności w tym samym regionie**: użyj usługi Azure Site Recovery do przenoszenia zasobów między strefami dostępności w tym samym regionie świadczenia usługi Azure.


## <a name="compare-move-tools"></a>Porównanie narzędzi przenoszenia

**Narzędzie** | **Kiedy stosować**
--- | --- 
**Przenieś w grupie zasobów** | Przenoszenie zasobów do innej grupy zasobów/subskrypcji lub między regionami.<br/><br/> W przypadku przechodzenia między regionami w grupie zasobów wybierasz zasoby, które chcesz przenieść, a następnie przejdź do centrum przenoszenia zasobów, aby sprawdzić zależności i przenieść zasoby do regionu docelowego.
**Przenoszenie z centrum przenoszenia zasobów** | Przenoszenie zasobów między regionami. <br/><br/> W regionie docelowym można przenieść do regionu docelowego lub do określonej strefy dostępności lub zestawu dostępności.
**Przenoszenie maszyn wirtualnych za pomocą Site Recovery** | Służy do przemieszczania maszyn wirtualnych platformy Azure między chmurami publicznymi i prywatnymi.<br/><br/> Użyj, jeśli chcesz przenieść maszyny wirtualne między strefami dostępności w tym samym regionie.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](about-move-process.md) na temat składników przenoszenia zasobów i procesu przenoszenia.
