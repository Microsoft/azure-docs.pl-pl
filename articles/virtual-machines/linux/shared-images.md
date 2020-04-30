---
title: Tworzenie udostępnionych galerii obrazów przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule dowiesz się, jak utworzyć udostępniony obraz maszyny wirtualnej na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: article
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: c75d33f8310cfd143bf201cdac861954d07baf0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758423"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Tworzenie udostępnionej galerii obrazów przy użyciu interfejsu wiersza polecenia platformy Azure

[Galeria obrazów udostępnionych](shared-image-galleries.md) upraszcza udostępnianie obrazów niestandardowych w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom w organizacji, w obrębie lub w różnych regionach, w ramach dzierżawy usługi AAD. Wybierz obrazy, które chcesz udostępnić, które regiony mają być dostępne w programie, oraz użytkowników, którym chcesz je udostępnić. Można utworzyć wiele galerii, aby można było logicznie grupować obrazy udostępnione. 

Galeria jest zasobem najwyższego poziomu, który zapewnia pełną kontrolę dostępu opartą na rolach (RBAC). Obrazy mogą być poddane wersji i można wybrać replikację poszczególnych wersji obrazu do innego zestawu regionów świadczenia usługi Azure. Galeria działa tylko z obrazami zarządzanymi.

Funkcja galerii obrazów udostępnionych ma wiele typów zasobów. Firma Microsoft będzie używać tych aplikacji lub tworzyć je w tym artykule:

| Zasób | Opis|
|----------|------------|
| **Obraz zarządzany** | Jest to podstawowy obraz, którego można użyć samodzielnie lub użyć do utworzenia **wersji obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na podstawie uogólnionych maszyn wirtualnych. Obraz zarządzany jest specjalnym typem dysku VHD, który może służyć do tworzenia wielu maszyn wirtualnych i może być teraz używany do utworzenia wersji obrazu udostępnionego. |
| **Galeria obrazów** | Podobnie jak w przypadku portalu Azure Marketplace, **Galeria obrazów** jest repozytorium do zarządzania i udostępniania obrazów, ale ty kontrolujesz, kto ma dostęp. |
| **Definicja obrazu** | Obrazy są zdefiniowane w galerii i zawierają informacje o obrazie i wymaganiach dotyczących używania go wewnętrznie. Dotyczy to zarówno obrazu systemu Windows, jak i Linux, informacji o wersji oraz minimalnych i maksymalnych wymagań dotyczących pamięci. Jest to definicja typu obrazu. |
| **Wersja obrazu** | **Wersja obrazu** jest używana do tworzenia maszyny wirtualnej w przypadku korzystania z galerii. Dla danego środowiska można mieć wiele wersji obrazu. Podobnie jak w przypadku obrazu zarządzanego, w przypadku tworzenia maszyny wirtualnej przy użyciu **wersji obrazu** wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów można wielokrotnie używać. |



[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną na podstawie najnowszej wersji obrazu za pomocą polecenia [AZ VM Create](/cli/azure/vm#az-vm-create).

```azurecli-interactive 
az vm create\
   --resource-group myGalleryRG \
   --name myVM \
   --image "/subscriptions/subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --generate-ssh-keys
```

Można również użyć określonej wersji, używając identyfikatora wersji obrazu dla `--image` parametru. Na przykład, aby użyć *1.0.0* wersji obrazu typu: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Następne kroki
[Usługa Azure Image Builder (wersja zapoznawcza)](image-builder-overview.md) ułatwia automatyzację tworzenia wersji obrazu. można nawet użyć jej do aktualizacji i [tworzenia nowej wersji obrazu z istniejącej wersji obrazu](image-builder-gallery-update-image-version.md). 

Możesz również tworzyć udostępnione zasoby galerii obrazów przy użyciu szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny wirtualnej z wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Aby uzyskać więcej informacji na temat udostępnionych galerii obrazów, zobacz [Omówienie](shared-image-galleries.md). Jeśli występują problemy, zobacz temat [Rozwiązywanie problemów z udostępnionymi galeriami obrazów](troubleshooting-shared-images.md).
