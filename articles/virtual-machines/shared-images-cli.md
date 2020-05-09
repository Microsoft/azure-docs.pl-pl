---
title: Tworzenie udostępnionych galerii obrazów przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule dowiesz się, jak utworzyć udostępniony obraz maszyny wirtualnej na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: d64b5f94dae7ad0213fa231b5603064ad3647da1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796696"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Tworzenie udostępnionej galerii obrazów przy użyciu interfejsu wiersza polecenia platformy Azure

[Galeria obrazów udostępnionych](./linux/shared-image-galleries.md) upraszcza udostępnianie obrazów niestandardowych w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom. Wybierz obrazy, które chcesz udostępnić, które regiony mają być dostępne w programie, oraz użytkowników, którym chcesz je udostępnić. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Następne kroki

Utwórz wersję obrazu z [maszyny wirtualnej](image-version-vm-cli.md)lub [obrazu zarządzanego](image-version-managed-image-cli.md) przy użyciu interfejsu wiersza polecenia platformy Azure.

Aby uzyskać więcej informacji na temat udostępnionych galerii obrazów, zobacz [Omówienie](./linux/shared-image-galleries.md). Jeśli występują problemy, zobacz temat [Rozwiązywanie problemów z udostępnionymi galeriami obrazów](troubleshooting-shared-images.md).
