---
title: Tworzenie udostępnionej galerii obrazów przy użyciu Azure PowerShell
description: Dowiedz się, jak używać Azure PowerShell do tworzenia galerii obrazów udostępnionych na platformie Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 1d135c6fe4e867a9b270e2a5c1b986e1957e7012
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843846"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Tworzenie udostępnionej galerii obrazów przy użyciu Azure PowerShell 

[Galeria obrazów udostępnionych](./windows/shared-image-galleries.md) upraszcza udostępnianie obrazów niestandardowych w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Niestandardowe obrazy mogą służyć do uruchamiania zadań wdrażania, takich jak wstępne ładowanie aplikacji, konfiguracje aplikacji i inne konfiguracje systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom w organizacji, w obrębie lub w różnych regionach, w ramach dzierżawy usługi AAD. Wybierz obrazy, które chcesz udostępnić, które regiony mają być dostępne w programie, oraz użytkowników, którym chcesz je udostępnić. Można utworzyć wiele galerii, aby można było logicznie grupować obrazy udostępnione. 

Galeria jest zasobem najwyższego poziomu, który zapewnia pełną kontrolę dostępu opartą na rolach (Azure RBAC). Obrazy mogą być poddane wersji i można wybrać replikację poszczególnych wersji obrazu do innego zestawu regionów świadczenia usługi Azure. Galeria działa tylko z obrazami zarządzanymi.

Funkcja galerii obrazów udostępnionych ma wiele typów zasobów. 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


[!INCLUDE [virtual-machines-common-shared-images-powershell](../../includes/virtual-machines-common-shared-images-powershell.md)]


## <a name="next-steps"></a>Następne kroki

Utwórz obraz z [maszyny wirtualnej](image-version-vm-powershell.md), [obrazu zarządzanego](image-version-managed-image-powershell.md)lub [obrazu w innej galerii](image-version-another-gallery-powershell.md).

[Usługa Azure Image Builder (wersja zapoznawcza)](./windows/image-builder-overview.md) ułatwia automatyzację tworzenia wersji obrazu. można nawet użyć jej do aktualizacji i [tworzenia nowej wersji obrazu z istniejącej wersji obrazu](./windows/image-builder-gallery-update-image-version.md). 

Możesz również utworzyć zasób udostępnionej galerii obrazów przy użyciu szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny wirtualnej na podstawie wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


