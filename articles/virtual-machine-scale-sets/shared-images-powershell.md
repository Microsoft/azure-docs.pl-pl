---
title: Używanie udostępnionych obrazów maszyn wirtualnych do tworzenia zestawu skalowania w Azure PowerShell
description: Dowiedz się, jak za pomocą Azure PowerShell utworzyć udostępnione obrazy maszyn wirtualnych służące do wdrażania zestawów skalowania maszyn wirtualnych na platformie Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: a35413bbc0169e320589459a841db59ab25a7daf
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518694"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Tworzenie i używanie obrazów udostępnionych dla zestawów skalowania maszyn wirtualnych za pomocą Azure PowerShell

Podczas tworzenia zestawu skalowania należy wskazać obraz używany do wdrożenia wystąpień maszyn wirtualnych. Usługa galerii obrazów udostępnionych znacznie upraszcza udostępnianie obrazów niestandardowych w organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom w organizacji, w obrębie lub w różnych regionach, w ramach dzierżawy usługi AAD. Wybierz obrazy, które chcesz udostępnić, które regiony mają być dostępne w programie, oraz użytkowników, którym chcesz je udostępnić. Można utworzyć wiele galerii, aby można było logicznie grupować obrazy udostępnione. 

Galeria jest zasobem najwyższego poziomu, który zapewnia pełną kontrolę dostępu opartą na rolach (Azure RBAC). Obrazy mogą być poddane wersji i można wybrać replikację poszczególnych wersji obrazu do innego zestawu regionów świadczenia usługi Azure. Galeria działa tylko z obrazami zarządzanymi. 

Funkcja galerii obrazów udostępnionych ma wiele typów zasobów. 


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>Przed rozpoczęciem

W poniższych krokach wyjaśniono szczegółowo, jak na podstawie istniejącej maszyny wirtualnej utworzyć obraz niestandardowy do ponownego użycia, za pomocą którego można tworzyć nowe wystąpienia maszyn wirtualnych.

Aby ukończyć ten przykład w tym artykule, musisz mieć istniejący obraz zarządzany. Możesz skorzystać z [samouczka: Utwórz i Użyj obrazu niestandardowego dla zestawów skalowania maszyn wirtualnych z Azure PowerShell](tutorial-use-custom-image-powershell.md) , aby utworzyć je w razie potrzeby. Jeśli zarządzany obraz zawiera dysk z danymi, rozmiar dysku danych nie może być większy niż 1 TB.

Podczas pracy z artykułem należy zastąpić grupę zasobów i nazwy maszyn wirtualnych, w razie konieczności.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]




## <a name="next-steps"></a>Następne kroki

Możesz również utworzyć zasób udostępnionej galerii obrazów przy użyciu szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w usłudze Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny wirtualnej na podstawie wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Aby uzyskać więcej informacji na temat udostępnionych galerii obrazów, zobacz [Omówienie](shared-image-galleries.md). Jeśli występują problemy, zobacz temat [Rozwiązywanie problemów z udostępnionymi galeriami obrazów](../virtual-machines/troubleshooting-shared-images.md).
