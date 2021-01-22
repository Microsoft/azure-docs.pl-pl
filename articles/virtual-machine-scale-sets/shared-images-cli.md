---
title: Używanie udostępnionych obrazów maszyn wirtualnych do tworzenia zestawu skalowania w interfejsie wiersza polecenia platformy Azure
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć udostępnione obrazy maszyn wirtualnych służące do wdrażania zestawów skalowania maszyn wirtualnych na platformie Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 418258c498f768697aefebd5df0cc64b7255076f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676082"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Tworzenie i używanie obrazów udostępnionych dla zestawów skalowania maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure 2,0

Podczas tworzenia zestawu skalowania należy wskazać obraz używany do wdrożenia wystąpień maszyn wirtualnych. [Galeria obrazów udostępnionych](../virtual-machines/shared-image-galleries.md) upraszcza udostępnianie obrazów niestandardowych w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie obrazów innym osobom. Wybierz obrazy, które chcesz udostępnić, które regiony mają być dostępne w programie, oraz użytkowników, którym chcesz je udostępnić. 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Następne kroki

Utwórz wersję obrazu z [maszyny wirtualnej](../virtual-machines/image-version-vm-cli.md)lub [obrazu zarządzanego](../virtual-machines/image-version-managed-image-cli.md).

Aby uzyskać więcej informacji na temat udostępnionych galerii obrazów, zobacz [Omówienie](../virtual-machines/shared-image-galleries.md). Jeśli występują problemy, zobacz temat [Rozwiązywanie problemów z udostępnionymi galeriami obrazów](../virtual-machines/troubleshooting-shared-images.md).