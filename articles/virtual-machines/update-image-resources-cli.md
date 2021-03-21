---
title: Wyświetlanie listy, aktualizowanie i usuwanie zasobów obrazów przy użyciu interfejsu wiersza polecenia platformy Azure
description: Wyświetlaj, Aktualizuj i usuwaj zasoby obrazów w udostępnionej galerii obrazów przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3554eccf28c5abbe9741af5b9718fb83710dab38
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553633"
---
# <a name="list-update-and-delete-image-resources"></a>Wyświetlanie listy, aktualizowanie i usuwanie zasobów obrazów 

Możesz zarządzać udostępnionymi zasobami galerii obrazów przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [virtual-machines-common-gallery-list-cli.md](../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Następne kroki

[Usługa Azure Image Builder (wersja zapoznawcza)](./image-builder-overview.md) ułatwia automatyzację tworzenia wersji obrazu. można nawet użyć jej do aktualizacji i [tworzenia nowej wersji obrazu z istniejącej wersji obrazu](./linux/image-builder-gallery-update-image-version.md).