---
title: Tworzenie udostępnionych galerii obrazów za pomocą interfejsu wiersza polecenia platformy Azure
description: W tym artykule dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia udostępnionego obrazu maszyny Wirtualnej na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: axayjo
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: ab0a4b98e562cda544800f2938d0b829fb1d6ef7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458645"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Tworzenie udostępnionej galerii obrazów za pomocą interfejsu wiersza polecenia platformy Azure

[Udostępniona galeria obrazów](shared-image-galleries.md) upraszcza niestandardowe udostępnianie obrazów w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom w organizacji, w obrębie lub między regionami, w dzierżawie usługi AAD. Wybierz obrazy, które chcesz udostępnić, regiony, w których chcesz je udostępnić i komu chcesz je udostępnić. Można utworzyć wiele galerii, aby logicznie grupować udostępnione obrazy. 

Galeria jest zasobem najwyższego poziomu, który zapewnia pełną kontrolę dostępu opartą na rolach (RBAC). Obrazy mogą być wersjona i można wybrać do replikacji każdej wersji obrazu do innego zestawu regionów platformy Azure. Galeria działa tylko z zarządzanymi obrazami.

Funkcja Galeria obrazów udostępnionych ma wiele typów zasobów. Będziemy używać lub budować te w tym artykule:

| Zasób | Opis|
|----------|------------|
| **Obraz zarządzany** | Jest to podstawowy obraz, który może być używany samodzielnie lub używany do tworzenia **wersji obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na uogólnionych maszynach wirtualnych. Obraz zarządzany jest specjalnym typem dysku VHD, który może służyć do tworzenia wielu maszyn wirtualnych i może być teraz używany do tworzenia wersji obrazów udostępnionych. |
| **Galeria zdjęć** | Podobnie jak w portalu Azure **Marketplace, galeria obrazów** jest repozytorium do zarządzania i udostępniania obrazów, ale można kontrolować, kto ma dostęp. |
| **Definicja obrazu** | Obrazy są definiowane w galerii i zawierają informacje o obrazie i wymaganiach dotyczących używania go wewnętrznie. Obejmuje to, czy obraz jest Windows lub Linux, informacje o wersji i minimalne i maksymalne wymagania dotyczące pamięci. Jest to definicja typu obrazu. |
| **Wersja obrazu** | **Wersja obrazu** jest to, czego używasz do tworzenia maszyny Wirtualnej podczas korzystania z galerii. W zależności od środowiska może być dostępnych wiele wersji obrazu. Podobnie jak w przypadku obrazu zarządzanego, podczas tworzenia maszyny Wirtualnej przy użyciu **wersji obrazu** wersja obrazu jest używana do tworzenia nowych dysków dla maszyny Wirtualnej. Wersje obrazów mogą być używane wiele razy. |



[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną z najnowszej wersji obrazu przy użyciu [programu az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive 
az vm create\
   --resource-group myGalleryRG \
   --name myVM \
   --image "/subscriptions/subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --generate-ssh-keys
```

Można również użyć określonej wersji przy użyciu identyfikatora wersji obrazu dla parametru. `--image` Na przykład, aby użyć obrazu w wersji *1.0.0* typu: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Następne kroki
[Usługa Azure Image Builder (wersja zapoznawcza)](image-builder-overview.md) może pomóc w automatyzacji tworzenia wersji obrazu, można nawet użyć go do aktualizacji i [utworzenia nowej wersji obrazu z istniejącej wersji obrazu](image-builder-gallery-update-image-version.md). 

Zasoby udostępnionej galerii obrazów można również tworzyć przy użyciu szablonów. Dostępnych jest kilka szablonów szybki start platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny Wirtualnej z wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Aby uzyskać więcej informacji na temat galerii obrazów udostępnionych, zobacz [Omówienie](shared-image-galleries.md). Jeśli napotkasz problemy, zobacz [Rozwiązywanie problemów z udostępnionymi galeriami obrazów](troubleshooting-shared-images.md).
